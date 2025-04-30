data_import module
==================

load_data
---------

Reads and optionally converts dataset into ASL-BIDS format. It ensures data adherence to the BIDS format and handles conversion using user-defined parameters.

.. code-block:: python

    load_data(root: str, params_json: str, convert=True, is_singledelay=True, is_labelcontrol=True)

**Parameters:**

- ``root`` (str): The root directory of the dataset.
- ``params_json`` (str): Path to the JSON file containing user-defined parameters (see JSON Parameters section in Tutorials).
- ``convert`` (bool, optional): Flag indicating whether to convert data to ASL-BIDS format. Default is ``True``.
- ``is_singledelay`` (bool, optional): Flag indicating whether the ASL data is single-delay.
- ``is_labelcontrol`` (bool, optional): Flag indicating whether the ASL data is arranged in a label-first order.

**Outputs:**

The original dataset will be renamed as ``rawdata_user`` and the converted ASL-BIDS dataset will be stored as ``rawdata`` under ``root``. A ``data_description.json`` file is also generated under ``root`` to store some basic information about the dataset.

check_bids_format
-----------------

Verifies if a given root path follows the pre-required BIDS format. It checks the presence and structure of necessary directories and files within the dataset.

.. code-block:: python

    check_bids_format(root: str)

**Parameters:**

- ``root`` (str): The root directory of the dataset.

**Returns:**

- ``valid`` (bool): Flag indicating whether this function runs without error.
- ``error`` (str): Error information.
- ``img_type`` (str): Image file format (``.nii`` or ``.img``).
- ``has_structural`` (bool): Flag indicating whether the dataset has structural images.

read_params
-----------

Reads and validates user-defined parameters from a JSON file. It ensures that all necessary parameters for ASL data processing are present and correctly formatted.

.. code-block:: python

    read_params(params_json: str, has_structural: bool, is_singledelay: bool)

**Parameters:**

- ``params_json`` (str): Path to the JSON file containing user-defined parameters.
- ``has_structural`` (bool): Whether the directory has structural images.
- ``is_singledelay`` (bool): Flag indicating whether the ASL data is single-delay.

**Returns:**

- ``valid`` (bool): Flag indicating whether this function runs without error.
- ``error`` (str): Error information.
- ``params`` (dict): Parameters read from the JSON file.

make_sidecar
------------

Generates JSON sidecar files for ASL and structural MRI data, as well as a TSV file containing ``aslcontext`` information. It processes user-defined parameters and organizes them into the appropriate formats for ASL-BIDS compliance.

.. code-block:: python

    make_sidecar(params: dict, num_volumes: int, is_singledelay: bool, is_labelcontrol: bool)

**Parameters:**

- ``params`` (dict): Parameters read from the user-input JSON file.
- ``num_volumes`` (int): Number of volumes in the ASL image.
- ``is_singledelay`` (bool): Flag indicating whether the ASL data is single-delay.
- ``is_labelcontrol`` (bool): Flag indicating whether the ASL data is arranged in a label-first order.

**Returns:**

- ``asl_json_data`` (dict): Sidecar information for ASL images.
- ``structural_json_data`` (dict): Sidecar information for structural images.
- ``tsv_data`` (dict): ASL context.

convert2bids
------------

Converts MRI data from its original format into the ASL-BIDS format. It processes ASL and structural images, converting them to NIfTI format if necessary, and generates associated sidecar files.

.. code-block:: python

    convert2bids(root: str, params: dict, img_type: str, has_structural: bool, is_singledelay: bool, is_labelcontrol: bool)

**Parameters:**

- ``root`` (str): The root directory of the dataset.
- ``params`` (dict): Parameters read from the user-input JSON file.
- ``img_type`` (str): Image file format (``.nii`` or ``.img``).
- ``has_structural`` (bool): Flag indicating whether the dataset has structural images.
- ``is_singledelay`` (bool): Flag indicating whether the ASL data is single-delay.
- ``is_labelcontrol`` (bool): Flag indicating whether the ASL data is arranged in a label-first order.

**Returns:**

- ``valid`` (bool): Flag indicating whether this function runs without error.
- ``error`` (str): Error information.

**Outputs:**

The original dataset will be renamed as ``rawdata_user``. The converted ASL-BIDS dataset with NIFTI images and sidecar files will be stored as ``rawdata`` under ``root``. A ``data_description.json`` file is also generated under ``root`` to store some basic information about the dataset.

read_asl_bids
-------------

Reads data from an ASL-BIDS-compliant dataset. It extracts relevant metadata and organizes it into a data description JSON file.

.. code-block:: python

    read_asl_bids(root: str, img_type: str, has_structural: bool)

**Parameters:**

- ``root`` (str): The root directory of the dataset.
- ``img_type`` (str): Image file format (``.nii`` or ``.img``).
- ``has_structural`` (bool): Flag indicating whether the dataset has structural images.

**Outputs:**

A ``data_description.json`` file under ``root`` to store some basic information about the dataset.

create_derivatives_folders
--------------------------

Creates the derivatives folders for the ASL MRI dataset based on the provided data description.

.. code-block:: python

    create_derivatives_folders(data_descrip: dict)

**Parameters:**

- ``data_descrip`` (dict): A dictionary containing the data description, which includes information about the images and their paths.

**Outputs:**

Creates the ``derivatives`` folder under ``root``, with the same subject, session, and modality subfolders as ``rawdata``.
