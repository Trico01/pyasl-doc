oxford_asl module
=================

run_oxford_asl
--------------

Executes the ``oxford_asl`` command-line tool to process ASL MRI data, incorporating various options for calibration, motion correction, and other image processing features.

.. code-block:: python

    run_oxford_asl(
        root: str,
        useStructural=False,
        useCalibration=False,
        cmethod="voxel",
        wp=False,
        mc=False,
        bat=None,
        t1=None,
        t1b=None,
        sliceband=None,
    )

**Parameters:**

- ``root`` (str): The root directory where the dataset is located.
- ``useStructural`` (bool, optional): If ``True``, uses structural images for processing. Default is ``False``.
- ``useCalibration`` (bool, optional): If ``True``, includes calibration in the processing steps. Default is ``False``.
- ``cmethod`` (str, optional): Calibration method, either ``"voxel"`` or ``"single"``. Default is ``"voxel"``.
- ``wp`` (bool, optional): If ``True``, do analysis in 'White Paper Mode'. Default is ``False``.
- ``mc`` (bool, optional): If ``True``, applies motion correction. Default is ``False``.
- ``bat`` (float, optional): Bolus arrival time (in seconds).
- ``t1`` (float, optional): T1 value of tissue (in seconds).
- ``t1b`` (float, optional): T1 value of arterial blood (in seconds).
- ``sliceband`` (int, optional): Number of slices per band in a multi-band acquisition.

**Raises:**

- ``ValueError``: If using ``"single"`` calibration method without structural images, or if required structural or calibration images are missing.

**Outputs:**

Output folders are stored in each session under ``derivatives``. For output file details, please refer to the `Oxford ASL documentation <https://asl-docs.readthedocs.io/en/latest/oxford_asl_userguide.html#output>`_.

split_asl_m0
------------

When ``M0Type=Included``, this function splits ASL and M0 images from a single file based on ASL context information, saving them as individual NIfTI images.

.. code-block:: python

    split_asl_m0(file_path: str, asl_context: list)

**Parameters:**

- ``file_path`` (str): The path to the 4D MRI file containing combined ASL and M0 scans.
- ``asl_context`` (list): A list indicating the context of each volume within the file, specifying which are ASL scans and which are M0 scans.

**Returns:**

- A tuple containing two elements:
   - ``asl_path`` (str): The file path where the separated ASL data is saved.
   - ``m0_path`` (str): The file path where the separated M0 data is saved.
