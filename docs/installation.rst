Getting started
===============

Installation
------------

Install the package using pip:

.. code-block:: bash

   pip install pyasl-osipi

Requirements
------------

To use all features, make sure the following dependencies are installed:

- **MATLAB** (R2019b or later)
- **SPM12** (added to MATLAB path)
- **FSL** (v6.0 or later)

Check that ``matlab`` and ``fsl`` are available in your system path:

.. code-block:: bash

   matlab -batch "disp('MATLAB ready')"
   fslmaths -h

That's it — you're ready to start using PyASL!