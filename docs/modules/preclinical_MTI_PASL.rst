preclinical_MTI_PASL module
===========================

preclinical_MTI_PASL_pipeline
-----------------------------

Calculates CBF with multi-TI preclinical PASL data. This function calculates CBF based on T1 relaxation differences between selective and global images.

.. code-block:: python

    preclinical_MTI_PASL_pipeline(
        data: np.ndarray,
        sel_index: list,
        glo_index: list,
        TI_list: list,
        mask_array=None,
        save_dir=None,
    )

**Parameters:**

- ``data`` (np.ndarray): The 3D image data array with dimensions corresponding to ``[height, width, number of selective and global images]``.
- ``sel_index`` (list): Indices for selective images in the third dimension of the data array.
- ``glo_index`` (list): Indices for global images in the third dimension of the data array.
- ``TI_list`` (list): List of inversion times corresponding to ``sel_index`` and ``glo_index``.
- ``mask_array`` (np.ndarray, optional): A 2D binary mask array to isolate regions of interest in the calculation. Must match the first two dimensions of ``data``.
- ``save_dir`` (str, optional): Directory where the calculated CBF map and fitting curves are saved. If not provided, the CBF map is returned without saving.

**Returns:**

- ``CBF`` (np.ndarray): The calculated CBF map.

**Outputs:**

- If ``save_dir`` is provided, saves the CBF map as a NumPy array file (``CBF.npy``) and a figure illustrating the curve fitting (``curvefit.png``) in the specified directory.

**Raises:**

- ``ValueError``: If ``mask_array`` is not a NumPy array or its shape does not match the required dimensions.

T1fit_function
--------------

This function models T1 relaxation signal based on given parameters.

.. code-block:: python

    T1fit_function(xdata: np.ndarray, a: float, T1: float, A: float)

**Parameters:**

- ``xdata`` (np.ndarray): An array of time points (usually TIs) at which the signal was measured.
- ``a`` (float): Baseline offset of the signal.
- ``T1`` (float): T1 relaxation time.
- ``A`` (float): Amplitude of the recovery signal.

**Returns:**

- ``np.ndarray``: An array representing the signal intensity calculated at each point in ``xdata`` using the given T1 model parameters.

T1fit
-----

Performs non-linear curve fitting to derive T1 relaxation parameters from experimental data using the T1 relaxation model function.

.. code-block:: python

    T1fit(xdata: np.ndarray, ydata: np.ndarray)

**Parameters:**

- ``xdata`` (np.ndarray): An array of time points (usually TIs) at which the MRI signal was measured.
- ``ydata`` (np.ndarray): An array of signal intensities corresponding to each time point in ``xdata``.

**Returns:**

- A list containing the fitted parameters ``[a, T1, A]``, where:
   - ``a`` is the baseline offset of the signal.
   - ``T1`` is the estimated T1 relaxation time.
   - ``A`` is the amplitude of the recovery signal.
