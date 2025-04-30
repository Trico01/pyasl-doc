JSON Parameters
================

This page lists all supported fields in the JSON parameter file used by the ``load_data`` function in PyASL.

The parameters are grouped into three categories:

- ``ASL``: Acquisition parameters for perfusion scans
- ``M0``: Parameters for the M0 scan
- ``anat``: (Optional) Parameters for anatomical images

Each group should be provided as a dictionary in the top-level JSON structure.

Example JSON structure:

.. code-block:: none

    {
        "ASL": { ... },
        "M0": { ... },
        "anat": { ... }
    }

Field Descriptions
------------------

+-----------------------------------+-----------------------------------------------------------------+--------------------------------------------------+
| Name                              | Description                                                     | Defaults                                         |
+===================================+=================================================================+==================================================+
| Manufacturer                      | Manufacturer                                                    | Required                                         |
+-----------------------------------+-----------------------------------------------------------------+--------------------------------------------------+
| ManufacturersModelName            | Manufacturers model name                                        | Required                                         |
+-----------------------------------+-----------------------------------------------------------------+--------------------------------------------------+
| MagneticFieldStrength             | Magnetic field strength                                         | Required                                         |
+-----------------------------------+-----------------------------------------------------------------+--------------------------------------------------+
| RepetitionTime                    | Repetition time (sec)                                           | Required                                         |
+-----------------------------------+-----------------------------------------------------------------+--------------------------------------------------+
| EchoTime                          | Echo time (sec)                                                 | Required                                         |
+-----------------------------------+-----------------------------------------------------------------+--------------------------------------------------+
| FlipAngle                         | Flip angle (degree)                                             | Required                                         |
+-----------------------------------+-----------------------------------------------------------------+--------------------------------------------------+
| ArterialSpinLabelingType          | ``PCASL``, ``CASL``, or ``PASL``                                | Required                                         |
+-----------------------------------+-----------------------------------------------------------------+--------------------------------------------------+
| PostLabelingDelay                 | A value (sec) for single-delay data when ``M0Type`` is not      | Required                                         |
|                                   | ``Included``; Otherwise should be a vector containing           |                                                  |
|                                   | corresponding PLDs for control/label pairs and 0 for M0 scan    |                                                  |
+-----------------------------------+-----------------------------------------------------------------+--------------------------------------------------+
| LabelingDuration                  | Labeling duration (sec)                                         | Optional, required when                          |
|                                   |                                                                 | ``ArterialSpinLabelingType`` = ``PCASL`` or      |
|                                   |                                                                 | ``CASL``                                         |
+-----------------------------------+-----------------------------------------------------------------+--------------------------------------------------+
| BolusCutOffDelayTime              | Bolus cutOff delay time (TI1) (sec)                             | Optional, required when                          |
|                                   |                                                                 | ``ArterialSpinLabelingType`` = ``PASL``          |
+-----------------------------------+-----------------------------------------------------------------+--------------------------------------------------+
| Looklocker                        | Flip angle of looklocker acquisition (degree)                   | Optional, required when using mutli-delay PASL   |
+-----------------------------------+-----------------------------------------------------------------+--------------------------------------------------+
| M0Type                            | ``Separate`` means there is a separate M0 file in the ``perf``  | Required                                         |
|                                   | folder; ``Included`` means that the M0 scan is included in the  |                                                  |
|                                   | ASL image file; ``Estimate`` means there is no M0 scan          |                                                  |
+-----------------------------------+-----------------------------------------------------------------+--------------------------------------------------+
| MRAcquisitionType                 | ``2D`` or ``3D``                                                | Required                                         |
+-----------------------------------+-----------------------------------------------------------------+--------------------------------------------------+
| SliceDuration                     | For multi-slice 2D acquisition, the time interval between       | Optional, required when                          |
|                                   | excitations of adjacent slices (sec)                            | ``MRAcquisitionType`` = ``2D``                   |
+-----------------------------------+-----------------------------------------------------------------+--------------------------------------------------+
| BackgroundSuppression             | ``True`` or ``False``                                           | Required                                         |
+-----------------------------------+-----------------------------------------------------------------+--------------------------------------------------+
| BackgroundSuppressionNumberPulses | Background suppression number of pulses                         | Optional, required when                          |
|                                   |                                                                 | ``BackgroundSuppressionNumberPulses`` = ``True`` |
+-----------------------------------+-----------------------------------------------------------------+--------------------------------------------------+
| BackgroundSuppressionPulseTime    | Vector containing timing (sec) of the background suppression    | Optional, required when                          |
|                                   | pulses                                                          | ``BackgroundSuppressionNumberPulses`` = ``True`` |
+-----------------------------------+-----------------------------------------------------------------+--------------------------------------------------+
| LabelingEfficiency                | Labeling efficiency                                             | Required                                         |
+-----------------------------------+-----------------------------------------------------------------+--------------------------------------------------+