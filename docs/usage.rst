How to use
==========

PyASL supports two types of workflows based on the type of ASL data:

- 🧠 **Human Data Processing**
- 🐭 **Preclinical Data Processing**

Each workflow consists of multiple modules for data conversion, preprocessing, and feature map generation.

-----

Human Data Processing
---------------------

Step 1: Import and Convert Raw Data
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Before processing, you need to convert your dataset into ASL-BIDS format using the ``load_data`` function. This step also generates a ``data_description.json`` file that stores key metadata for further processing.

📁 Prepare Your Dataset
^^^^^^^^^^^^^^^^^^^^^^^

Your raw data should follow this basic structure:

::

    ExampleStudy/
      rawdata/
          sub001/
            ses1/
              anat/
                sub001_ses1_t1w.hdr
                sub001_ses1_t1w.img
              perf/
                sub001_ses1_run1.hdr
                sub001_ses1_run1.img
                sub001_ses1_run2.hdr
                sub001_ses1_run2.img
                sub001_ses1_M0.hdr
                sub001_ses1_M0.img
            ses2/
              ...
          sub002/
            ...

✅ Notes:

- Data can be in **Analyze** (``.hdr`` + ``.img``) or **NIfTI** (``.nii``) format  
- The directory **must** contain a ``rawdata/`` folder and ``perf/`` subfolders  
- The ``anat/`` folder is optional  
- The **M0 file name must contain "M0"**  
- You may rename subject/session folders as needed  

⚙️ Define ASL metadata
^^^^^^^^^^^^^^^^^^^^^^^

Create a JSON file containing acquisition parameters. This will be used to generate valid BIDS sidecar files.

.. note::

   For a full list of available fields, see :doc:`json_parameters`.

.. code-block:: json

    {
        "anat": {
            "Manufacturer": "Siemens",
            "ManufacturersModelName": "Prisma",
            "MagneticFieldStrength": 3.0,
            "RepetitionTime": 4.38,
            "EchoTime": 0.0204,
            "FlipAngle": 90
        },
        "M0": {
            "Manufacturer": "Siemens",
            "ManufacturersModelName": "Prisma",
            "MagneticFieldStrength": 3.0,
            "RepetitionTime": 4.38,
            "EchoTime": 0.0204,
            "FlipAngle": 90
        },
        "ASL": {
            "Manufacturer": "Siemens",
            "ManufacturersModelName": "Prisma",
            "MagneticFieldStrength": 3.0,
            "RepetitionTime": 4.38,
            "EchoTime": 0.0204,
            "FlipAngle": 90,
            "ArterialSpinLabelingType": "PCASL",
            "PostLabelingDelay": 2,
            "LabelingDuration": 1.8,
            "M0Type": "Separate",
            "MRAcquisitionType": "3D",
            "BackgroundSuppression": true,
            "BackgroundSuppressionNumberPulses": 2,
            "BackgroundSuppressionPulseTime": [
                1,
                1
            ],
            "LabelingEfficiency": 0.85,
        }
    }

🚀 Run the Conversion
^^^^^^^^^^^^^^^^^^^^^

Use the following command to convert your dataset into ASL-BIDS format:

.. code-block:: python

    from pyasl import load_data

    load_data(
        "path-to-your-dataset/ExampleStudy",
        "path-to-your-jsonfile/example_parameters.json",
        convert=True,
        is_singledelay=True,
        is_labelcontrol=False
    )

After conversion, all images are converted to ``.nii`` format, with accompanying ``.json`` and ``.tsv`` sidecars. A ``data_description.json`` file is also generated at the root of the dataset.

Example output structure:

::

    ExampleStudy/
      rawdata/
          sub001/
            ses1/
              anat/
                sub001_ses1_t1w.nii
                sub001_ses1_t1w.json
              perf/
                sub001_ses1_run1.nii
                sub001_ses1_run1.json
                sub001_ses1_run1_aslcontext.tsv
                sub001_ses1_run2.nii
                sub001_ses1_run2.json
                sub001_ses1_run2_aslcontext.tsv
                sub001_ses1_M0.nii
                sub001_ses1_M0.json
            ses2/
              ...
          sub002/
            ...

🗃️ Load Existing BIDS Dataset (Skip Conversion)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If your dataset is already in ASL-BIDS format, you can skip the conversion step and simply generate the ``data_description.json`` file:

.. code-block:: python

    load_data("path-to-your-dataset/ExampleStudy", None, convert=False)


Step 2: Run processing functions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

After converting your data to ASL-BIDS format (see Step 1), you can run any of the following processing functions. All outputs will be saved in the ``derivatives/`` folder under your dataset root.

🧠 ASL_MRICloud
^^^^^^^^^^^^^^^^

You can process single- or multi-delay PCASL/PASL data using  
`ASL_MRICloud <https://doi.org/10.1002/nbm.4051>`_:

.. code-block:: python

    from pyasl import mricloud_pipeline

    mricloud_pipeline(root, flag_t1, t1_tissue, t1_blood, part_coef, bgs_eff)

This function calculates CBF (and optionally ATT) maps. If T1-MultiAtlas segmentation is provided, it can coregister the CBF maps to anatomical space and extract ROI-level CBF.

To generate T1-MultiAtlas segmentations, visit the  
`MRICloud <https://braingps.mricloud.org/>`_ website and run *Segmentation-T1MultiAtlas* for all structural images. Unzip the output and move the ``output/`` or ``targetx/`` folders to ``derivatives/sub-*/ses-*/anat/``.

**Input Parameters**:

- ``root`` (str): Path to the dataset root.
- ``flag_t1`` (bool): Whether to use T1-MultiAtlas results.
- ``t1_tissue`` (float): T1 value of tissue (msec).
- ``t1_blood`` (float): T1 value of blood (msec).
- ``part_coef`` (float): Brain-blood partition coefficient.
- ``bgs_eff`` (float): Background suppression efficiency.

🧠 ASLtbx
^^^^^^^^^

Process single-delay PCASL/CASL/PASL data using `ASLtbx <https://www.cfn.upenn.edu/zewang/ASLtbx.php>`_:

.. code-block:: python

    from pyasl import asltbx_pipeline

    asltbx_pipeline(
        root,
        smooth_fwhm,
        mask_thres,
        QuantFlag,
        M0wmcsf,
        MaskFlag,
        MeanFlag,
        BOLDFlag,
        PerfFlag,
        SubtractionType,
        SubtrationOrder,
        Timeshift,
    )

**Input Parameters**:

- ``root`` (str): Path to the dataset root.
- ``smooth_fwhm`` (list of int, optional): The full-width half-maximum (FWHM) smoothing parameter. Defaults to ``[6, 6, 6]``.
- ``mask_thres`` (float, optional): The coefficient for thresholding to calculate the brain mask.
- ``QuantFlag`` (int, optional): Flag for quantification using a unique M0 value. ``0``: use M0 map for quantification; ``1``: use mean CSF M0 value; ``2``: use mean WM M0 value. Defaults to ``1``.
- ``M0wmcsf`` (optional): Mean M0 value in WM or CSF. Must be provided when using unique M0 value for quantification. Defaults to ``None``.
- ``MaskFlag`` (bool, optional): Flag indicating whether perfusion images will be masked or not. Defaults to ``True``.
- ``MeanFlag`` (bool, optional): Flag to compute mean images. Defaults to ``True``.
- ``BOLDFlag`` (bool, optional): Flag to extract pseudo BOLD images. Defaults to ``False``.
- ``PerfFlag`` (bool, optional): Flag to save the perfusion difference images. Defaults to ``False``.
- ``SubtractionType`` (int, optional): Type of subtraction for perfusion calculation. ``0``: simple subtraction; ``1``: surround subtraction; ``2``: sinc subtraction. Defaults to ``0``.
- ``SubtrationOrder`` (int, optional): Order of subtraction for perfusion calculation. ``1``: control-label; ``0``: label-control. Defaults to ``1``.
- ``Timeshift`` (float, optional): A value between 0 and 1 to shift the labeled image forward or backward; only valid for sinc interpolation. Defaults to ``0.5``.

🧠 DLASL (Denoising)
^^^^^^^^^^^^^^^^^^^^

Use `DLASL <https://doi.org/10.1002/jmri.27984>`_ to denoise CBF maps:

.. code-block:: python

    from pyasl import dlasl_pipeline

    dlasl_pipeline(root, model_selection, pattern)

To use this function, you must have CBF maps (3D or 4D, with filenames matching ``pattern``) and segmentation images (names containing ``c1``, ``c2``, or ``c3``). These files should be placed in the ``perf/`` folders under ``derivatives/``. This function can be run using only CPU resources.

Denoised CBF images will be saved in the same directory as the originals, with filenames prefixed by ``denoised_``.

**Input Parameters**:

- ``root`` (str): Path to the dataset root.
- ``model_selection`` (int, optional): Selector for the neural network model. ``0``: model trained on healthy young adult PCASL data; ``1``: model fine-tuned on PASL data from normal controls and Alzheimer's disease subjects. Defaults to ``1``.
- ``pattern`` (str, optional): Regular expression pattern to match filenames of CBF images. Defaults to ``.*_CBF\.(nii|nii\.gz)$``.

🧠 Oxford_ASL
^^^^^^^^^^^^^

Process your ASL data using `Oxford_ASL <https://asl-docs.readthedocs.io/en/latest/oxford_asl.html>`_:

.. code-block:: python

    from pyasl import run_oxford_asl

    run_oxford_asl(
        root,
        useStructural,
        useCalibration,
        cmethod,
        wp,
        mc,
        bat,
        t1,
        t1b,
        sliceband,
    )

This function calculates CBF maps with optional structural image registration and optional M0 image calibration.

**Input Parameters**:

- ``root`` (str): Path to the dataset root.
- ``useStructural`` (bool, optional): Whether to use structural images for processing. Defaults to ``False``.
- ``useCalibration`` (bool, optional): Whether to include calibration steps. Defaults to ``False``.
- ``cmethod`` (str, optional): Calibration method. Must be either ``"voxel"`` or ``"single"``. Defaults to ``"voxel"``.
- ``wp`` (bool, optional): Whether to use White Paper Mode. Defaults to ``False``.
- ``mc`` (bool, optional): Whether to apply motion correction. Defaults to ``False``.
- ``bat`` (str, optional): Bolus arrival time.
- ``t1`` (str, optional): T1 value of tissue.
- ``t1b`` (str, optional): T1 value of arterial blood.
- ``sliceband`` (str, optional): Number of slices per band in a multi-band acquisition.

-----

Preclinical Data Processing
---------------------------

🐭 Single-Delay PCASL
~~~~~~~~~~~~~~~~~~~~~

Use the following function to process single-delay preclinical PCASL data  
(`Hirschler et al. 2018 <https://doi.org/10.1002/mrm.26767>`_,  
`Wei et al. 2022 <https://doi.org/10.1002/mrm.29353>`_):

.. code-block:: python

    from pyasl import preclinical_pCASL_pipeline

    preclinical_pCASL_pipeline(
        root,
        keyword,
        control_first,
        SGap,
        T1blood,
        relCBF_vmax,
        mask_thres,
    )

This function takes a BRUKER dataset as input and allows the user to select which experiment number to process. The pipeline performs parameter extraction, motion artifact checking, and automatic masking, followed by M0 and CBF calculation.  

The result folder is created in the same directory as the input dataset and includes both NumPy arrays and image figures of the resulting maps.

**Input Parameters**:

- ``root`` (str): Path to the BRUKER dataset.
- ``keyword`` (str, optional): Keyword to identify PCASL scans from the protocol string in the ``acqp`` file. Defaults to ``"asl"``.
- ``control_first`` (bool, optional): Whether control images come before label images. Defaults to ``True``.
- ``SGap`` (int, optional): Time delay between adjacent EPI slices (msec). Defaults to ``31``.
- ``T1blood`` (int, optional): T1 value of blood (msec). Defaults to ``2800``.
- ``relCBF_vmax`` (int, optional): Max value for displaying relative CBF (%). Defaults to ``10``.
- ``mask_thres`` (float, optional): Brain mask threshold. Higher values result in stricter masks. Defaults to ``0.2``.

🐭 Multi-TI PASL
~~~~~~~~~~~~~~~~

Use the following function to process multi-TI preclinical PASL data  
(`Kim 1995 <https://doi.org/10.1002/mrm.1910340303>`_,  
`Kindler et al. 2022 <https://doi.org/10.1177/0271678x211062274>`_):

.. code-block:: python

    from pyasl import preclinical_MTI_PASL_pipeline

    preclinical_MTI_PASL_pipeline(
        data,
        sel_index,
        glo_index,
        TI_list,
        mask_array,
        save_dir,
    )

This function fits the input ASL data to a T1 relaxation model and estimates CBF values based on signal differences between selective and global images.

**Input Parameters**:

- ``data`` (np.ndarray): 3D ASL image array with dimensions [height, width, number of images].
- ``sel_index`` (list): Indices for selective images in ``data``.
- ``glo_index`` (list): Indices for global images in ``data``.
- ``TI_list`` (list): List of inversion times (msec), matching the indices above.
- ``mask_array`` (np.ndarray, optional): 2D binary mask for calculation. Must match the first two dimensions of ``data``.
- ``save_dir`` (str, optional): Directory to save results. If not provided, results are returned in memory.
