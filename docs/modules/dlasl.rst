dlasl module
============

dlasl_pipeline
--------------

Denoises CBF maps using `DLASL <https://doi.org/10.1002/jmri.27984>`_ model. It involves loading data descriptions, selecting models, resampling, and calculating denoised maps.

.. code-block:: python

    dlasl_pipeline(root: str, model_selection=1, pattern=".*_CBF\.(nii|nii\.gz)$")

**Parameters:**

- ``root`` (str): The root directory where the dataset is located.
- ``model_selection`` (int, optional): Selector for the neural network model. ``0`` selects a model trained on young healthy adults' PCASL data, ``1`` selects a model fine-tuned on PASL data of normal controls and Alzheimer's disease groups. Default is ``1``.
- ``pattern`` (str, optional): Regular expression pattern to match the filenames of CBF images. Default is ``.*_CBF\.(nii|nii\.gz)$``. CBF images should be located in the perfusion folders under ``derivatives``.

**Raises:**

- ``FileNotFoundError``: If no CBF files are found that match the specified pattern in the given directories.

**Outputs:**

Generates denoised versions of CBF images using the specified deep learning model and saves them in the same directory as the original files, prefixed with ``denoised_``.

dlasl_resample
--------------

Resamples MRI data to ``(64, 64, 24)`` for neural network inputs.

.. code-block:: python

    dlasl_resample(v: nibabel.Nifti1Image, data: np.ndarray)

**Parameters:**

- ``v`` (nibabel.Nifti1Image): A Nifti1Image object.
- ``data`` (numpy.ndarray): The image data array from the Nifti1Image object that needs to be resampled.

**Returns:**

- A tuple containing two elements:
  - ``resampled_img`` (nibabel.Nifti1Image): The resampled image object.
  - ``resampled_data`` (numpy.ndarray): The array of the resampled image data.

dlasl_get_subj_c123
-------------------

Loads white matter, gray matter, and cerebrospinal fluid segmentation images from a specified directory, combining them into a single mask.

.. code-block:: python

    get_subj_c123(dir: str)

**Parameters:**

- ``dir`` (str): The directory from which to load the segmentation files. This directory should have segmented images with names containing ``c1``, ``c2``, and ``c3``.

**Returns:**

- A tuple containing two elements:
  - ``v`` (nibabel.Nifti1Image): The Nifti image object from the first found file (typically ``c1``), used as the mask image object.
  - ``c123`` (numpy.ndarray): An array representing the combined mask data.

**Raises:**

- ``FileNotFoundError``: If no segmentation files (``c1``, ``c2``, or ``c3``) are found in the specified directory, an error is raised indicating which specific segmentation file is missing.
