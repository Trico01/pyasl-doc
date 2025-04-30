asltbx module
=============

asltbx_pipeline
---------------

Executes a complete ASL processing pipeline, including steps such as reading data descriptions, resetting orientations, realigning, coregistering, smoothing, creating masks, and performing perfusion subtraction.

.. code-block:: python

    asltbx_pipeline(
        root: str,
        smooth_fwhm=[6, 6, 6],
        mask_thres=0.1,
        QuantFlag=1,
        M0wmcsf=None,
        MaskFlag=True,
        MeanFlag=True,
        BOLDFlag=False,
        PerfFlag=False,
        SubtractionType=0,
        SubtrationOrder=1,
        Timeshift=0.5,
    )

**Parameters:**

- ``root`` (str): The root directory of the dataset.
- ``smooth_fwhm`` (list of int, optional): The full-width half-maximum (FWHM) smoothing parameters for the smoothing step. Defaults to ``[6, 6, 6]``.
- ``mask_thres`` (float, optional): The coefficient for thresholding to calculate the brain mask.
- ``QuantFlag`` (int, optional): Flag for quantification using a unique M0 value. ``0`` means using M0 map for quantification. ``1`` means using mean CSF M0 value. ``2`` means using mean WM M0 value. Defaults to ``1``.
- ``M0wmcsf`` (optional): Mean M0 value in WM or CSF. Must be provided when using unique M0 value for quantification. Defaults to ``None``.
- ``MaskFlag`` (bool, optional): Flag indicating whether perfusion images will be masked or not. Defaults to ``True``.
- ``MeanFlag`` (bool, optional): Flag to compute mean images. Defaults to ``True``.
- ``BOLDFlag`` (bool, optional): Flag to extract pseudo BOLD images. Defaults to ``False``.
- ``PerfFlag`` (bool, optional): Flag to save the perfusion difference images. Defaults to ``False``.
- ``SubtractionType`` (int, optional): Type of subtraction for perfusion calculation. ``0``: simple subtraction; ``1``: surround subtraction; ``2``: sinc subtraction. Defaults to ``0``.
- ``SubtrationOrder`` (int, optional): Order of subtraction for perfusion calculation. ``1``: control-label; ``0``: label-control. Defaults to ``1``.
- ``Timeshift`` (float, optional): A value between ``0`` and ``1`` to shift the labeled image forward or backward; only valid for sinc interpolation. Defaults to ``0.5``.

**Outputs:**

Stores all generated files under the ``derivatives`` directory, such as CBF and BOLD images.

img_reset_orientation
---------------------

Resets the orientation of a given image by updating its affine matrix and saves the reoriented image to a specified target path.

.. code-block:: python

    img_reset_orientation(source_path: str, target_path: str)

**Parameters:**

- ``source_path`` (str): The file path of the source image to be reoriented.
- ``target_path`` (str): The file path where the reoriented image will be saved.

**Outputs:**

The orientation-reset image will be stored in ``target_path``.

asltbx_reset_orientation
------------------------

Resets the orientation of ASL, M0, and anatomical images based on the provided data description dictionary.

.. code-block:: python

    asltbx_reset_orientation(data_descrip: dict)

**Parameters:**

- ``data_descrip`` (dict): A dictionary containing information about the images, including their paths and types.

**Raises:**

- ``ValueError``: If structural images (``anat``) are missing.

asltbx_realign
--------------

Realigns ASL data based on the provided data description dictionary.

.. code-block:: python

    asltbx_realign(data_descrip: dict)

**Parameters:**

- ``data_descrip`` (dict): A dictionary containing information about the images, including their paths and types.

**Outputs:**

Saves realigned files ``r<asl_file>.nii`` and mean image ``mean_<asl_file>.nii`` to their corresponding ``derivatives`` directories.

asltbx_coregister
-----------------

Coregisters ASL, mean ASL, and M0 images to anatomical images based on the provided data description dictionary.

.. code-block:: python

    asltbx_coregister(data_descrip: dict)

**Parameters:**

- ``data_descrip`` (dict): A dictionary containing information about the images, including their paths and types.

**Outputs:**

Saves the coregistered ASL (with prefix ``rr``), mean ASL (with prefix ``rmean``), and M0 images (with prefix ``r``) to their corresponding ``derivatives`` directories.

asltbx_smooth
-------------

Applies smoothing to ASL, mean ASL, and M0 images based on the provided data description dictionary and specified full-width half-maximum (FWHM) parameters.

.. code-block:: python

    asltbx_smooth(data_descrip: dict, fwhm: list)

**Parameters:**

- ``data_descrip`` (dict): A dictionary containing information about the images, including their paths and types.
- ``fwhm`` (list of int): The FWHM smoothing parameters for the smoothing step.

**Outputs:**

Saves the smoothed ASL (with prefix ``srr``), mean ASL (with prefix ``srmean``), and M0 images (with prefix ``sr``) to their corresponding ``derivatives`` directories.

asltbx_create_mask
------------------

Creates a mask for ASL images based on a threshold of the mean ASL image.

.. code-block:: python

    asltbx_create_mask(data_descrip: dict, thres: float)

**Parameters:**

- ``data_descrip`` (dict): A dictionary containing information about the images, including their paths and types.
- ``thres`` (float): The coefficient for thresholding to calculate the brain mask.

**Outputs:**

Creates a binary mask image for each ASL file and saves it to the ``derivatives`` directory with the suffix ``_mask_perf_cbf.nii``.

asltbx_sinc_interpVec
---------------------

Performs sinc interpolation on a given 2D array along the specified interpolation points.

.. code-block:: python

    asltbx_sinc_interpVec(x: np.ndarray, u: float)

**Parameters:**

- ``x`` (np.ndarray): A 2D array of shape ``(dim, lenx)`` where ``dim`` is the number of dimensions and ``lenx`` is the length of each dimension.
- ``u`` (float): The interpolation point, which will be applied to each dimension.

**Returns:**

- ``y`` (np.ndarray): A 1D array of interpolated values for each dimension.

asltbx_perf_subtract
--------------------

Performs perfusion subtraction on ASL images and calculates various quantitative maps based on the provided parameters.

.. code-block:: python

    asltbx_perf_subtract(
        data_descrip: dict,
        QuantFlag: int,
        M0wmcsf: float,
        MaskFlag: bool,
        MeanFlag: bool,
        BOLDFlag: bool,
        PerfFlag: bool,
        SubtractionType: int,
        SubtrationOrder: int,
        Timeshift: float,
    )

**Parameters:**

- ``data_descrip`` (dict): A dictionary containing information about the images, including their paths and types.
- Other parameters can be found under ``asltbx_pipeline``.

**Outputs:**

Saves various quantitative maps, including CBF, BOLD, and perfusion images, to the specified paths under the ``derivatives`` directory. Additionally, saves global signal values to text files.
