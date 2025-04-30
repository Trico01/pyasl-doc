utils
=====

read_data_description
---------------------

Reads the data description file from the specified root directory. This function ensures that the necessary data description is available for further processing steps.

.. code-block:: python

    read_data_description(root: str)

**Parameters:**

- ``root`` (str): The root directory of the dataset.

**Returns:**

- ``data_descrip`` (dict): A dictionary containing the data description read from the ``data_description.json`` file.

**Raises:**

- ``ValueError``: If the ``data_description.json`` file is not found in the specified root directory.
- ``ValueError``: If the ``data_description.json`` file cannot be decoded due to JSON formatting issues.

load_img
--------

Loads an MRI image using the specified file path. This function ensures that any NaN values in the image data are replaced with zeros.

.. code-block:: python

    load_img(P: str)

**Parameters:**

- ``P`` (str): The file path to the MRI image.

**Returns:**

- ``V`` (nibabel.Nifti1Image): The loaded NIfTI image object.
- ``data`` (numpy.ndarray): The image data as a NumPy array with NaN values replaced by zeros.
