asl_mricloud module
===================

mricloud_pipeline
-----------------

Executes the complete ASL MRI processing pipeline, including data rescaling, motion correction, difference map calculation, M0 calculation, and CBF estimation. The pipeline handles both single-delay and multi-delay ASL data.

.. code-block:: python

    mricloud_pipeline(root: str, flag_t1: bool, t1_tissue: float, t1_blood: float, part_coef: float, bgs_eff=None)

**Parameters:**

- ``root`` (str): The root directory of the dataset.
- ``flag_t1`` (bool): Flag indicates whether to apply multi-atlas analysis.
- ``t1_tissue`` (float): T1 relaxation time of the tissue.
- ``t1_blood`` (float): T1 relaxation time of the blood.
- ``part_coef`` (float): Partition coefficient.
- ``bgs_eff`` (float): Background suppression efficiency.

**Outputs:**

Stores all generated files under the ``derivatives`` directory, such as CBF and ATT images.

img_rescale
-----------

Rescales an MRI image based on the scaling slope provided in the image header and saves the rescaled image to the specified target path.

.. code-block:: python

    img_rescale(source_path: str, target_path: str)

**Parameters:**

- ``source_path`` (str): The file path to the source MRI image.
- ``target_path`` (str): The file path where the rescaled MRI image will be saved.

**Outputs:**

Saves the rescaled MRI image to the specified ``target_path``.

mricloud_rescale
----------------

Rescales ASL and M0 images specified in the data description and saves the rescaled images to the derivatives folder.

.. code-block:: python

    mricloud_rescale(data_descrip: dict)

**Parameters:**

- ``data_descrip`` (dict): A dictionary containing the data description, which includes information about the images and their paths.

**Outputs:**

Rescaled ASL and M0 images are saved in the derivatives folder corresponding to their original paths.

mricloud_realign
----------------

Realigns ASL data using SPM. This function adjusts the ASL images to correct for motion by realigning them to the first time point.

.. code-block:: python

    mricloud_realign(data_descrip: dict)

**Parameters:**

- ``data_descrip`` (dict): A dictionary containing the data description, which includes information about the images and their paths.

**Outputs:**

The realigned ASL images (with prefix "r") are saved in the derivatives folder corresponding to their original paths.

mricloud_calculate_diffmap
--------------------------

Calculates the difference volume (control - label) for ASL data. This function averages control and label volumes, then computes the difference, and saves the resulting images.

.. code-block:: python

    mricloud_calculate_diffmap(data_descrip: dict)

**Parameters:**

- ``data_descrip`` (dict): A dictionary containing the data description, which includes information about the images and their paths.

**Outputs:**

Saves the following 3D images in the derivatives folder corresponding to their original paths:

- ``r<asl_file>_ctrl.nii``: The averaged control image.
- ``r<asl_file>_labl.nii``: The averaged label image.
- ``r<asl_file>_diff.nii``: The difference image (control - label).

img_coreg
---------

Coregisters the source image to the target image using SPM.

.. code-block:: python

    img_coreg(target: str, source: str, other=[])

**Parameters:**

- ``target`` (str): The file path to the target image.
- ``source`` (str): The file path to the source image that will be coregistered to the target.
- ``other`` (list): Other images to be coregistered.

**Outputs:**

The coregistered source image is saved with the prefix "r" added to the original filename.

mricloud_inbrain
----------------

Generates a brain mask from an MRI volume using a thresholding and morphological operations approach. This function identifies brain tissue and creates a binary mask.

.. code-block:: python

    mricloud_inbrain(imgvol: np.ndarray, thre: float, ero_lyr: int, dlt_lyr: int)

**Parameters:**

- ``imgvol`` (numpy.ndarray): The 3D MRI volume from which the brain mask is generated.
- ``thre`` (float): Threshold multiplier for initial brain tissue segmentation.
- ``ero_lyr`` (int): Number of erosion layers applied to refine the mask.
- ``dlt_lyr`` (int): Number of dilation layers applied after erosion to finalize the mask.

**Returns:**

- ``brainmask`` (numpy.ndarray): A binary 3D array of the same shape as ``imgvol``, where brain tissue is marked with 1s and non-brain tissue with 0s.

mricloud_getBrainMask
---------------------

Generates a brain mask for ASL images using tissue probability maps. This function handles both small and large field-of-view datasets and provides calculated and display brain masks.

.. code-block:: python

    mricloud_getBrainMask(imgtpm: str, imgfile: str)

**Parameters:**

- ``imgtpm`` (str): Path to the tissue probability map file.
- ``imgfile`` (str): Path to the ASL image file.

**Returns:**

- ``brnmsk_dspl`` (numpy.ndarray): The display brain mask.
- ``brnmsk_clcu`` (numpy.ndarray): The calculated brain mask.

mricloud_bgs_factor
-------------------

Calculates the longitudinal magnetization factor for background suppression. This function iterates through the flip angles and timing slots to update the magnetization.

.. code-block:: python

    mricloud_bgs_factor(mz0: float, t1_tissue: float, flip: list, timing:list, inv_eff:float)

**Parameters:**

- ``mz0`` (float): Initial longitudinal magnetization.
- ``t1_tissue`` (float): T1 relaxation time of the tissue.
- ``flip`` (list): List of flip angles in radians.
- ``timing`` (list): List of timing intervals corresponding to the flip angles.
- ``inv_eff`` (float): Inversion efficiency.

**Returns:**

- ``mz`` (float): The final longitudinal magnetization after applying the background suppression.

mricloud_calculate_M0
---------------------

Calculates the M0 map for ASL data, handling different M0 types and field-of-view sizes. This function creates M0 maps and brain masks and saves them in the appropriate directories.

.. code-block:: python

    mricloud_calculate_M0(data_descrip: dict, t1_tissue: float, bgs_eff)

**Parameters:**

- ``data_descrip`` (dict): A dictionary containing the data description, which includes information about the images and their paths.
- ``t1_tissue`` (float): T1 relaxation time of the tissue.
- ``bgs_eff`` (float): Background suppression efficiency.

**Outputs:**

Saves the following files in the derivatives ``perf`` subdirectory:

- ``M0map.nii``: The final M0 map.
- ``brnmsk_dspl.nii``: The display brain mask.
- ``brnmsk_clcu.nii``: The calculated brain mask.

mricloud_calculate_CBF
----------------------

Calculates the Cerebral Blood Flow (CBF) map from ASL difference images, M0 maps, and brain masks. This function supports both pCASL and PASL acquisition types and saves the absolute and relative CBF maps.

.. code-block:: python

    mricloud_calculate_CBF(data_descrip: dict, t1_blood: float, part_coef: float, bgs_eff)

**Parameters:**

- ``data_descrip`` (dict): A dictionary containing the data description, which includes information about the images and their paths.
- ``t1_blood`` (float): T1 relaxation time of the blood.
- ``part_coef`` (float): Partition coefficient.
- ``bgs_eff`` (float): Background suppression efficiency.

**Outputs:**

Saves the following files in the derivatives ``perf`` subdirectory:

- ``r<asl_file>_aCBF_native.nii``: The absolute CBF map.
- ``r<asl_file>_rCBF_native.nii``: The relative CBF map.

mricloud_func_recover
---------------------

Computes the longitudinal magnetization recovery for ASL data using either the Look-Locker T1 recovery model or the multi-delay T1 recovery model.

.. code-block:: python

    mricloud_func_recover(m0: float, t1: float, tp: np.ndarray, flip_angle=None, m_init=None)

**Parameters:**

- ``m0`` (float): Initial magnetization value.
- ``t1`` (float): T1 relaxation time of the tissue.
- ``tp`` (numpy.ndarray): Array of post-label delays (PLDs) or inversion times.
- ``flip_angle`` (float, optional): Flip angle in degrees for the Look-Locker model.
- ``m_init`` (float, optional): Initial magnetization for the Look-Locker model.

**Returns:**

- ``mm`` (numpy.ndarray): The recovered magnetization values at different times ``tp``.

mricloud_multidelay_calculate_M0
--------------------------------

Calculates the M0 map for multi-delay ASL data, handling different M0 types and field of view sizes. This function creates M0 maps and brain masks and saves them in the appropriate directories.

.. code-block:: python

    mricloud_multidelay_calculate_M0(data_descrip: dict)

**Parameters:**

- ``data_descrip`` (dict): A dictionary containing the data description, which includes information about the images and their paths.

**Outputs:**

Saves the following files in the derivatives ``perf`` subdirectory:

- ``M0map.nii``: The final M0 map.
- ``brnmsk_dspl.nii``: The display brain mask.
- ``brnmsk_clcu.nii``: The calculated brain mask.

mricloud_func_gkm_pcasl_multidelay
-----------------------------------

Calculates the generalized kinetic model for multi-delay pCASL data. This function computes the expected ASL signal for a given cerebral blood flow (CBF) and arterial transit time (ATT) across multiple post-labeling delays (PLDs).

.. code-block:: python

    mricloud_func_gkm_pcasl_multidelay(cbf: float, att: float, casl_dur: float, plds: np.ndarray, paras: dict)

**Parameters:**

- ``cbf`` (float): Cerebral blood flow in mL/100g/min.
- ``att`` (float): Arterial transit time in seconds.
- ``casl_dur`` (float): Duration of the CASL labeling in seconds.
- ``plds`` (numpy.ndarray): Array of post-labeling delays in seconds.
- ``paras`` (dict): Dictionary of parameters containing:
  - ``t1_blood`` (float): T1 relaxation time of blood in seconds.
  - ``part_coef`` (float): Partition coefficient.
  - ``labl_eff`` (float): Labeling efficiency.

**Returns:**

- ``mm`` (numpy.ndarray): The ASL signal for the given CBF and ATT across the specified PLDs.

mricloud_func_gkm_pasl_looklocker
---------------------------------

Calculates the generalized kinetic model for PASL data using the Look-Locker acquisition method. This function computes the expected ASL signal for a given cerebral blood flow (CBF) and arterial transit time (ATT) across multiple inversion times (TIs).

.. code-block:: python

    mricloud_func_gkm_pasl_looklocker(cbf: float, att: float, pasl_dur: float, tis: np.ndarray, flip_angle: float, paras: dict)

**Parameters:**

- ``cbf`` (float): Cerebral blood flow in mL/100g/min.
- ``att`` (float): Arterial transit time in seconds.
- ``pasl_dur`` (float): Duration of the PASL labeling in seconds.
- ``tis`` (numpy.ndarray): Array of inversion times in seconds.
- ``flip_angle`` (float): Flip angle in degrees for the Look-Locker readout.
- ``paras`` (dict): Dictionary of parameters containing:
  - ``t1_blood`` (float): T1 relaxation time of blood in seconds.
  - ``part_coef`` (float): Partition coefficient.
  - ``labl_eff`` (float): Labeling efficiency.

**Returns:**

- ``mm`` (numpy.ndarray): The ASL signal for the given CBF and ATT across the specified TIs.

mricloud_multidelay_calculate_CBFATT
-------------------------------------

Calculates the Cerebral Blood Flow (CBF) and Arterial Transit Time (ATT) maps from multi-delay ASL data. This function supports both pCASL and PASL acquisition types and saves the absolute CBF, relative CBF, and ATT maps.

.. code-block:: python

    mricloud_multidelay_calculate_CBFATT(data_descrip: dict, t1_blood: float, part_coef: float)

**Parameters:**

- ``data_descrip`` (dict): A dictionary containing the data description, which includes information about the images and their paths.
- ``t1_blood`` (float): T1 relaxation time of the blood in milliseconds.
- ``part_coef`` (float): Partition coefficient.

**Outputs:**

Saves the following files in the derivatives ``perf`` subdirectory:

- ``r<asl_file>_aCBF_native.nii``: The absolute CBF map.
- ``r<asl_file>_rCBF_native.nii``: The relative CBF map.
- ``r<asl_file>_ATT_native.nii``: The ATT map.

mricloud_read_mpr
-----------------

Read the T1 multi-atlas segmentation folder and update the data description dictionary.

.. code-block:: python

    mricloud_read_mpr(data_descrip: dict)

**Parameters:**

- ``data_descrip`` (dict): A dictionary containing information about images, including their paths and types.

**Returns:**

- ``data_descrip`` (dict): The updated dictionary with added keys ``mpr_folder`` and ``mpr_name`` for each image entry.

**Raises:**

- ``ValueError``: If structural images required for multi-atlas analysis are missing.
- ``ValueError``: If the parcellation folder required for multi-atlas analysis is missing.

mricloud_read_roi_lookup_table
------------------------------

Reads ``multilevel_lookup_table.txt`` under T1 multi-atlas segmentation folder.

.. code-block:: python

    mricloud_read_roi_lookup_table(roi_lookup_file: str)

**Parameters:**

- ``roi_lookup_file`` (str): The path to the ROI lookup table file.

**Returns:**

- ``data`` (list of list of str): A list containing the ROI lookup table data, where each sublist represents a line from the file, split into individual components.

mricloud_skullstrip
-------------------

Performs skull stripping on the provided anatomical image using a specified multi-atlas ROI lookup table.

.. code-block:: python

    asl_skullstrip(path_mpr: str, name_mpr: str)

**Parameters:**

- ``path_mpr`` (str): The directory path where the anatomical images and lookup table are located.
- ``name_mpr`` (str): The base name of the anatomical image file (without the extension).

**Returns:**

- ``mpr_brain_file`` (str): The file path of the skull-stripped anatomical image saved as a NIfTI file.

**Outputs:**

Saves the skull-stripped anatomical image ``<asl_file>_brain.nii`` under ``path_mpr``.

mricloud_coreg_mpr
------------------

Coregisters ASL images with the skull-stripped anatomical images.

.. code-block:: python

    mricloud_coreg_mpr(data_descrip: dict)

**Parameters:**

- ``data_descrip`` (dict): A dictionary containing information about the images, including their paths and types.

**Outputs:**

Updates the filenames of the coregistered ASL images in the derivatives ``perf`` directory, renaming them to include the ``_mpr`` suffix.

mricloud_read_roi_lists_info
----------------------------

Reads ROI statistics from a specified file and organizes the information into a structured format based on specified ROI types.

.. code-block:: python

    mricloud_read_roi_lists_info(roi_stats_file: str, roitypes: list)

**Parameters:**

- ``roi_stats_file`` (str): The path to the ROI statistics file.
- ``roitypes`` (list of str): A list of ROI types to look for in the statistics file.

**Returns:**

- ``tmpinfo`` (list of dict): A list of dictionaries, each containing the type of ROI, the count of corresponding entries, and a list of brain regions of that ROI type.



mricloud_t1roi_CBFaverage
-------------------------

Computes and writes the average CBF for different ROI segments based on T1 anatomical images.

.. code-block:: python

    mricloud_t1roi_CBFaverage(data_descrip: dict)

**Parameters:**

- ``data_descrip`` (dict): A dictionary containing information about the images, including their paths and types.

**Outputs:**

Writes the CBF analysis results for each ROI segment to a text file in the derivatives ``perf`` directory. This includes the ROI index, ROI name, regional CBF, regional relative CBF, and number of voxels for each segment.
