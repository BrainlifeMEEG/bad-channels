# Detect Bad Channels Using Maxwell Filtering

[![Run on Brainlife.io](https://img.shields.io/badge/Brainlife-bl.app.494-blue.svg)](https://doi.org/10.25663/brainlife.app.494)

## Description

This Brainlife App automatically detects bad (noisy and flat) MEG channels using Signal Space Separation, via [`mne.preprocessing.find_bad_channels_maxwell`](https://mne.tools/stable/generated/mne.preprocessing.find_bad_channels_maxwell.html). Detection is performed without actually applying Maxwell filtering to the data, so that:

1. bad channels can be found thanks to SSS without removing external components, and
2. artifacts in bad channels are prevented from spreading once Maxwell filtering (MaxFilter) is subsequently applied.

The entry script is `find_bad_channels.py` (invoked by the `main` bash script; this app has not yet been migrated to the standard `main.py` entrypoint/shared-utilities structure used by newer apps in this repo).

The app generates:
- A BIDS-compliant `channels.tsv` with the newly detected bad channels marked `bad`
- An HTML report with diagnostic figures and a summary of the parameters used
- A `product.json` summary for the Brainlife.io UI

## Inputs

- **`fif`** (`neuro/meg/fif`): MEG recording in FIF format in which to detect bad channels (required)
- **`calibration`** (`neuro/meg/fif`): fine calibration coefficients file (`.dat`); machine/site-specific (optional)
- **`crosstalk`** (`neuro/meg/fif`): cross-talk correction information (`.fif`) (optional)
- **`headshape`** (`neuro/meg/fif`): digitized head position file (`.pos`), enabling movement compensation (optional)
- **`channels`** (`neuro/meg/fif`): BIDS-compliant channels table (`.tsv`); if provided, it must include a `status` column and any channels already marked bad there are carried over (optional)
- **`destination`** (`neuro/meg/fif`): MEG device-to-head transformation destination (`.fif`) used during Maxwell-filter-based detection (optional)

If no `channels` file is supplied, the app builds a temporary BIDS dataset (via `mne_bids.write_raw_bids`) purely to obtain a BIDS-compliant `channels.tsv` to fill in; the temporary `bids/` folder is removed by the `main` script afterwards.

## Outputs

- **`out_dir_bad_channels/channels.tsv`**: BIDS-compliant channels table with the automatically detected bad channels marked `bad` in the `status` column
- **`out_dir_report/report_bad_channels.html`**: HTML report with data-info tables, noisy/flat channel score heatmaps, power spectral density plots, time-domain plots of the flagged channels, and the parameter values used
- **`product.json`**: success/warning/info messages for the Brainlife.io UI

## Configuration Parameters

| key | type | default | description |
|---|---|---|---|
| `param_duration` | float | `5` | Duration of the segments into which to slice the data for processing, in seconds. |
| `param_min_count` | int | `5` | Minimum number of times a channel must show up as bad in a chunk. |
| `param_limit` | float | `7` | Detection limit for noisy segments. Smaller values find more bad channels at increased risk of including good ones. |
| `param_h_freq` | float or `null` | `40.0` | Cutoff frequency (Hz) of the low-pass filter applied before processing the data. |
| `param_origin` | `"auto"` or list of 3 floats | `"auto"` | Origin of internal and external multipolar moment space, in meters. |
| `param_return_scores` | bool | `true` | Whether to return per-segment scoring information; must be `true` for this app (MNE's own default is `false`). |
| `param_int_order` | int | `8` | Order of the internal component of the spherical expansion. |
| `param_ext_order` | int | `3` | Order of the external component of the spherical expansion. |
| `param_coord_frame` | string (`"meg"` or `"head"`) | `"head"` | Coordinate frame in which `param_origin` is specified. |
| `param_regularize` | string or `null` | `"in"` | Basis regularization type; `"in"` or `null`. |
| `param_ignore_ref` | bool | `false` | If `true`, do not include reference channels in compensation. |
| `param_bad_condition` | string (`"error"`, `"warning"`, `"info"`, `"ignore"`) | `"error"` | How to deal with ill-conditioned SSS matrices. |
| `param_mag_scale` | float or `"auto"` | `100.0` | Magnetometer scale-factor used to bring magnetometers to approximately the same order of magnitude as gradiometers (they have different units, T vs T/m). |
| `param_skip_by_annotation` | string or list of strings | `["edge", "bad_acq_skip"]` | Any annotation segment beginning with one of these strings is excluded, and the segments on either side of it are processed separately. |
| `param_extended_proj` | list | `[]` | Empty-room projection vectors used to extend the external SSS basis (eSSS). |

This list, along with the default values, corresponds to the parameters of the MNE-Python 0.22.0 `find_bad_channels_maxwell` function (except for `return_scores`).

## Usage

### Running on Brainlife.io

1. Select a MEG recording (`.fif`) as the `fif` input, and optionally supply a fine calibration, cross-talk, head position, channels, or destination file.
2. Set the detection parameters, or keep the defaults.
3. Submit the task.
4. Review the resulting `channels.tsv` and the HTML report in the output viewer.

### Local Testing

```bash
git clone <this repo>
cd bad-channels
cp config.json.example config.json
# edit config.json with paths to your input files and desired parameter values
./main
```

## Technical Details

- **Method**: `mne.preprocessing.find_bad_channels_maxwell` (SSS-based automated bad- and flat-channel detection).
- **Guard**: the app raises an error if the input data has already been processed with Maxwell filtering (SSS/tSSS), since this detection method should not be run on already-filtered data.
- If a `channels` file is supplied, it must be BIDS-compliant and include a `status` column.

## Authors
- [Aurore Bussalb](mailto:aurore.bussalb@icm-institute.org)

### Contributors
- [Aurore Bussalb](mailto:aurore.bussalb@icm-institute.org)
- [Maximilien Chaumon](mailto:maximilien.chaumon@icm-institute.org)

## Citations

We kindly ask that you cite the following articles when publishing papers and code using this app:

Hayashi, S., Caron, B.A., Heinsfeld, A.S. et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. Nat Methods 21, 809–813 (2024). https://doi.org/10.1038/s41592-024-02237-2

Gramfort, A. et al. MEG and EEG data analysis with MNE-Python. Front. Neurosci. 7, 267 (2013). https://doi.org/10.3389/fnins.2013.00267

Taulu S. and Kajola M. Presentation of electromagnetic multichannel data: The signal space separation method. Journal of Applied Physics, 97 (2005). https://doi.org/10.1063/1.1935742

Taulu S. and Simola J. Spatiotemporal signal space separation method for rejecting nearby interference in MEG measurements. Physics in Medicine and Biology, 51 (2006). https://doi.org/10.1088/0031-9155/51/7/008

Appelhoff, S., Sanderson, M., Brooks, T., Vliet, M., Quentin, R., Holdgraf, C., Chaumon, M., Mikulan, E., Tavabi, K., Höchenberger, R., Welke, D., Brunner, C., Rockhill, A., Larson, E., Gramfort, A., & Jas, M. MNE-BIDS: Organizing electrophysiological data into the BIDS format and facilitating their analysis. Journal of Open Source Software, 4:1896 (2019). https://doi.org/10.21105/joss.01896

Avesani, P., McPherson, B., Hayashi, S. et al. The open diffusion data derivatives, brain data upcycling via integrated publishing of derivatives and reproducible open cloud services. Sci Data 6, 69 (2019). https://doi.org/10.1038/s41597-019-0073-y

## Funding Acknowledgement

brainlife.io is publicly funded and for the sustainability of the project we kindly ask that you acknowledge the use of the platform by including the following funding sources in your code and publications:

[![NSF-BCS-1734853](https://img.shields.io/badge/NSF_BCS-1734853-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1734853)
[![NSF-BCS-1636893](https://img.shields.io/badge/NSF_BCS-1636893-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1636893)
[![NSF-ACI-1916518](https://img.shields.io/badge/NSF_ACI-1916518-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1916518)
[![NSF-IIS-1912270](https://img.shields.io/badge/NSF_IIS-1912270-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1912270)
[![NIH-NIBIB-R01EB029272](https://img.shields.io/badge/NIH_NIBIB-R01EB029272-green.svg)](https://grantome.com/grant/NIH/R01-EB029272-01)
[![NIH-NIBIB-R01EB030896](https://img.shields.io/badge/NIH_NIBIB-R01EB030896-green.svg)](https://grantome.com/grant/NIH/R01-EB030896-01)

## License

Copyright (c) 2021 AuroreBussalb. Licensed under the MIT License, see [LICENSE](LICENSE).
