preclinical_pCASL module
========================

preclinical_pCASL_pipeline
--------------------------

Executes a complete pipeline for single-delay preclinical pCASL data processing, including data selection, parameter extraction, motion artifact checking, M0 map calculation, and CBF calculation.

.. code-block:: python

    preclinical_pCASL_pipeline(
        root: str,
        keyword="asl",
        control_first=True,
        SGap=31,
        T1blood=2800,
        relCBF_vmax=10,
        mask_thres=0.2,
    )

**Parameters:**

- ``root`` (str): The path to the dataset.
- ``keyword`` (str, optional): Keyword to identify pCASL scans within the dataset. The protocol string in ``acqp`` file of eligible scans should include ``keyword``. Default is ``"asl"``.
- ``control_first`` (bool, optional): Determines the ordering of control and label images. Default is ``True``.
- ``SGap`` (int, optional): The delay time between two adjacent EPI acquisitions. Unit is ``ms``. Default is ``31``.
- ``T1blood`` (int, optional): T1 relaxation time of blood. Unit is ``ms``. Default is ``2800``.
- ``relCBF_vmax`` (int, optional): Maximum value for relative CBF in the perfusion map display. Unit is ``%``. Default is ``10``.
- ``mask_thres`` (float, optional): Threshold for brain mask generation. Default is ``0.2``. Higher value indicates stricter mask.

**Raises:**

- ``FileNotFoundError``: If the dataset or pCASL scan files are not found.
- ``ValueError``: If no pCASL scans are identified or invalid inputs are given during the selection process.

**Outputs:**

- Saves processed ASL data as both picture and numpy array, such as extracted image, difference image, M0 map, and CBF map. The result folder ``Results_<name of dataset>`` locates in the same directory as the dataset folder. The subfolders in the result folder are named by ``<expno>_<run_index>``.

read_nmr_par
------------

Reads BRUKER parameter files and stores information into a dictionary.

.. code-block:: python

    read_nmr_par(filename: str)

**Parameters:**

- ``filename`` (str): The name of the parameter file.

**Returns:**

- ``dict``: A dictionary containing parameter-value pairs extracted from the file.

**Raises:**

- ``FileNotFoundError``: If the specified file cannot be found or opened.

read_2dseq_v3
-------------

Reads image data from ``2dseq`` file that has been reconstructed from Bruker ParaVision 5. This function extracts various acquisition and reconstruction parameters from associated parameter files and processes the ``2dseq`` file accordingly.

.. code-block:: python

    read_2dseq_v3(fname: str, expno: int, procno: int)

**Parameters:**

- ``fname`` (str): Path to the dataset.
- ``expno`` (int): Experiment number within the dataset.
- ``procno`` (int): Process number.

**Returns:**

- A tuple containing the following:
   - ``img`` (numpy array): The reconstructed image data array.
   - ``Ncolumns`` (int): Number of columns in the image.
   - ``Nrows`` (int): Number of rows in the image.
   - ``NI`` (int): Slice number multiplied by echo number.
   - ``NR`` (int): Repetition time (dynamics).
   - ``RecoNumInputChan`` (int): Number of reconstruction input channels.

**Raises:**

- ``FileNotFoundError``: If the ``2dseq`` file or any of the parameter files (``method``, ``acqp``, ``reco``) are not found.

W_Expno
-------

Extracts and sorts experiment numbers (expno) from the dataset.

.. code-block:: python

    W_Expno(root: str)

**Parameters:**

- ``root`` (str): The path to the dataset.

**Returns:**

- ``list``: A list of integers representing the experiment numbers found in the dataset, sorted in descending order.

W_ImgParaAbs
------------

Abstracts critical imaging parameters from BRUKER data files for subsequent analysis.

.. code-block:: python

    W_ImgParaAbs(path: str)

**Parameters:**

- ``path`` (str): The path to an expno folder in the dataset.

**Returns:**

- ``dict``: A dictionary containing various imaging parameters such as slice number, slice thickness, repetition time (TR), echo time (TE), ROI dimensions, and more, extracted from different parameter files (``acqp``, ``method``, ``visu_pars``).

**Raises:**

- ``FileNotFoundError``: If any of the expected parameter files (``acqp``, ``method``, ``visu_pars``) are not found.

W_FindScan
----------

Searches through dataset directory to find scans that contain a specified keyword within their protocol parameter. This function is used to identify pCASL scans.

.. code-block:: python

    W_FindScan(root: str, keyword: str)

**Parameters:**

- ``root`` (str): The path to the dataset.
- ``keyword`` (str): The keyword to look for within the protocol parameter of each experiment.

**Returns:**

- ``list``: A list of expno where the keyword was found in the protocol parameter.

W_ParaWrite
-----------

Writes extracted imaging parameters from a dictionary into a text file within a given directory.

.. code-block:: python

    W_ParaWrite(dir: str, Para: dict)

**Parameters:**

- ``dir`` (str): The path to an expno folder.
- ``Para`` (dict): A dictionary containing imaging parameters to be written into the file.

**Outputs:**

- Creates or overwrites ``Parameters.txt`` in the specified directory.

get_plot_array
--------------

Transforms a 3D NumPy array into a 2D array layout suitable for visualizing multi-slice data in a single image plot.

.. code-block:: python

    get_plot_array(data: np.ndarray, winf: list)

**Parameters:**

- ``data`` (numpy array): A 3D NumPy array representing the image data with dimensions corresponding to [height, width, number of slices].
- ``winf`` (list): A list of two integers [rows, columns] specifying the window layout to arrange the slices in the 2D plot array.

**Returns:**

- ``np.ndarray``: A 2D NumPy array where each slice from the input 3D array is placed into a specified position in a grid layout.

plot_save_fig
-------------

Creates and saves a visualization of a 2D NumPy array as an image file, with options for adjusting the display range.

.. code-block:: python

    plot_save_fig(data: np.ndarray, fig_title: str, fig_path: str, range=None)

**Parameters:**

- ``data`` (np.ndarray): A 2D NumPy array containing the data to be visualized.
- ``fig_title`` (str): The title of the figure.
- ``fig_path`` (str): The file path where the image will be saved.
- ``range`` (list, optional): A list of two elements specifying the display range as ``[min, max]``.

**Outputs:**

- Saves a figure to the specified path.
