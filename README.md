# app-bad-channels

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

> **Detects noisy and flat MEG channels with MNE-Python find_bad_channels_maxwell (Signal Space Separation without applying the filter) and outputs a BIDS channels.tsv marking them bad plus an HTML report.**

Runs mne.preprocessing.find_bad_channels_maxwell on a FIF recording, optionally using the site fine-calibration and cross-talk files, head position and destination files, and carries over channels already marked bad in an input channels.tsv. Use it before Maxwell filtering (MaxFilter/SSS) so that artefacts in bad channels do not spread. Elekta/MEGIN (Neuromag) MEG data are expected; param_return_scores must stay true for the report.

| | |
|---|---|
| 📥 **Inputs** | `neuro/meg/fif` · `neuro/meg/fif-override` (optional) |
| 📤 **Outputs** | `neuro/meg/fif-override` · `report/html` |
| 🐳 **Container** | `brainlife/mne:0.23dev` |
| ⚙️ **Resources** | 1 CPUs |
| 🔗 **Run it** | [doi.org/10.25663/brainlife.app.494](https://doi.org/10.25663/brainlife.app.494) |

#### 📥 Inputs

| Input | Datatype | Required | Description |
|---|---|---|---|
| `fif01` | `neuro/meg/fif` | yes | MEG data in fif format in which we want to find bad channels. |
| `opt01` | `neuro/meg/fif-override` | no | Optional head positions stored in a headshape.pos file and destination file in fif format, containing the mean transformation matrix. The headshape.pos file is obtained with app-head-pos and the destination.fif file is obtained with app-mean-transformation-matrix. This headshape.pos will override the headshape.pos linked to the neuro/meg/fif datatype. |

#### 📤 Outputs

| Output | Datatype | Description |
|---|---|---|
| `out_dir_bad_channels` | `neuro/meg/fif-override` | channels.tsv file (in a BIDS compliant structure) in which bad channels detected by the App are marked as bad. |
| `out_dir_report` | `report/html` | HTML report with plots of the signals in time and frequency domains. |

<details>
<summary><b>⚙️ Parameters</b> (15)</summary>

| Parameter | Type | Default | Description |
|---|---|---|---|
| `param_origin` | string | `auto` | Origin of internal and external multipolar moment space in meters. Can be "auto" or three numbers separated by a comma (example: 0, 0, 0). |
| `param_duration` | number | `5` | Duration of the segments into which to slice the data for processing, in seconds. It can be a float. |
| `param_min_count` | number | `5` | Minimum number of times a channel must show up as bad in a chunk. This value must be an integer. |
| `param_limit` | number | `7` | Detection limit for noisy segments. This value can be a float. |
| `param_h_freq` | number | `40` | Optional the cutoff frequency (in Hz) of the low-pass filter that will be applied before processing the data. This value can be a float. |
| `param_return_scores` | boolean | `true` | If True, return a dictionary with scoring information for each evaluated segment of the data. Default in MNE is False but here it must be True. |
| `param_int_order` | number | `8` | Order of internal component of spherical expansion. This value is an integer. |
| `param_ext_order` | number | `3` | order of external component of spherical expansion. This value is an integer. |
| `param_coord_frame` | enum | `head` | The coordinate frame that the origin is specified in, either 'meg' or 'head'. Options: `meg`, `head`. |
| `param_regularize` | enum | `in` | The destination location for the head, either in or None. Options: `in`. |
| `param_ignore_ref` | boolean | `false` | If True, do not include reference channels in compensation. |
| `param_mag_scale` | string | `100` | The magenetometer scale-factor used to bring the magnetometers to approximately the same order of magnitude as the gradiometers, as they have different units (T vs T/m). This value can be a float or "auto". |
| `param_bad_condition` | enum | `error` | How to deal with ill-conditioned SSS matrices, either 'error', 'warning', 'info' , 'ignore'. Options: `error`, `warning`, `info`, `ignore`. |
| `param_skip_by_annotation` | string | `[edge, bad acq skip]` | Any annotation segment that begins with the given string will not be included in filtering, and segments on either side of the given excluded annotated segment will be filtered separately. Can be an empty list []. |
| `param_extended_proj` | string | `[]` | The empty-room projection vectors used to extend the external SSS basis (i.e., use eSSS). |

</details>

<!-- brainlife-app:end -->

Repository of a Brainlife App that detects bad channels in MEG recordings using [`mne.preprocessing.find_bad_channels_maxwell`](https://mne.tools/stable/generated/mne.preprocessing.maxwell_filter.html#mne.preprocessing.maxwell_filter).

# app-bad-channels documentation

1) Detect bad channels thanks to SSS without removing external components
2) Prevent artifacts in bad channels from spreading when MaxFilter is applied
3) Input files are:
    * a MEG file in `.fif` format,
    * an optional fine calibration file in `.dat`,
    * an optional crosstalk compensation file in `.fif`,
    * an optional head position file in `.pos`,
    * an optional destination file in `.fif`,
    * an optional events file in `.tsv`,
    * an optional channels file in `.tsv`.
4) Input parameters are:
    * `param_duration`: `float`, duration of the segments into which to slice the data for processing, in seconds. Default is 5.
    * `param_min_count`: `int`, minimum number of times a channel must show up as bad in a chunk. Default is 5.
    * `param_limit`: `float`, detection limit for noisy segments. Default is 7.
    * `param_h_freq`: `float`, optional the cutoff frequency (in Hz) of the low-pass filter that will be applied before processing the data. Default is 40.
    * `param_origin`: `str` or list of three `float`, origin of internal and external multipolar moment space in meters. Default is 'auto'. 
    * `param_return_scores`: `bool`, if True, return a dictionary with scoring information for each evaluated segment of the data. Default in MNE is False but here it must be True.
    * `param_int_order`: `int`, order of internal component of spherical expansion. Default is 8.
    * `param_ext_order`: `int`, order of external component of spherical expansion. Default is 3.
    * `param_coord_frame`: `str`, the coordinate frame that the origin is specified in, either 'meg' or 'head'. Default is 'head'.
    * `param_regularize`: `str`, optional, the destination location for the head, either 'in' or `None`. Default is 'in'.
    * `param_ignore_ref`: `bool`, if `True`, do not include reference channels in compensation. Default is `False`.
    * `param_bad_condition`: `str`, how to deal with ill-conditioned SSS matrices, either 'error', 'warning', 'info' , 'ignore'. Default is 'error'.
    * `param_mag_scale`: `float` or `str`, the magnetometer scale-factor used to bring the magnetometers to approximately the same order of magnitude as the gradiometers, as they have different units (T vs T/m). Can be "auto". 
Default is 100. 
    * `param_skip_by_annotation`, `str` or `list of str`, any annotation segment that begins with the given string will not be included in filtering, and segments on either side of the given excluded annotated segment will be filtered separately.
Default is `["edge, "bad acq skip"]`.
    * `param_extended_proj`: `list`, the empty-room projection vectors used to extend the external SSS basis (i.e., use eSSS). Default is an empty list.
      
This list along with the default values correspond to the parameters of MNE Python version 0.22.0 find_bad_channels_maxwell function (except for return_scores).

5) Ouput files are:
    * a BIDS compliant `.tsv` channels file with channels info,
    * an `.html` report containing figures.

### Authors
- [Aurore Bussalb](aurore.bussalb@icm-institute.org)

### Contributors
- [Aurore Bussalb](aurore.bussalb@icm-institute.org)
- [Maximilien Chaumon](maximilien.chaumon@icm-institute.org)

### Funding Acknowledgement
brainlife.io is publicly funded and for the sustainability of the project it is helpful to Acknowledge the use of the platform. We kindly ask that you acknowledge the funding below in your code and publications. Copy and past the following lines into your repository when using this code.

[![NSF-BCS-1734853](https://img.shields.io/badge/NSF_BCS-1734853-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1734853)
[![NSF-BCS-1636893](https://img.shields.io/badge/NSF_BCS-1636893-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1636893)
[![NSF-ACI-1916518](https://img.shields.io/badge/NSF_ACI-1916518-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1916518)
[![NSF-IIS-1912270](https://img.shields.io/badge/NSF_IIS-1912270-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1912270)
[![NIH-NIBIB-R01EB029272](https://img.shields.io/badge/NIH_NIBIB-R01EB029272-green.svg)](https://grantome.com/grant/NIH/R01-EB029272-01)

### Citations
1. Avesani, P., McPherson, B., Hayashi, S. et al. The open diffusion data derivatives, brain data upcycling via integrated publishing of derivatives and reproducible open cloud services. Sci Data 6, 69 (2019). [https://doi.org/10.1038/s41597-019-0073-y](https://doi.org/10.1038/s41597-019-0073-y)
2. Appelhoff, S., Sanderson, M., Brooks, T., Vliet, M., Quentin, R., Holdgraf, C., Chaumon, M., Mikulan, E., Tavabi, K., Höchenberger, R., Welke, D., Brunner, C., Rockhill, A., Larson, E., Gramfort, A., & Jas, M. MNE-BIDS: Organizing electrophysiological data into the BIDS format and facilitating their analysis. Journal of Open Source Software, 4:1896 (2019). [https://doi.org/10.21105/joss.01896](https://doi.org/10.21105/joss.01896)
3. Taulu S. and Kajola M. Presentation of electromagnetic multichannel data: The signal space separation method. Journal of Applied Physics, 97 (2005). [https://doi.org/10.1063/1.1935742](https://doi.org/10.1063/1.1935742)
4. Taulu S. and Simola J. Spatiotemporal signal space separation method for rejecting nearby interference in MEG measurements. Physics in Medicine and Biology, 51 (2006). [https://doi.org/10.1088/0031-9155/51/7/008](https://doi.org/10.1088/0031-9155/51/7/008)

## Running the App 

### On Brainlife.io

This App is still private on Brainlife.io.

### Running Locally (on your machine)

1. git clone this repo
2. Inside the cloned directory, create `config.json` with the same keys as in `config.json.example` but with paths to your input 
   files and values of the input parameters.

```json
{
  "fif": "rest1-raw.fif"
}
```

3. Launch the App by executing `main`

```bash
./main
```

## Output

The output file is a channels.tsv BIDS compliant with channels marked bad and an `html` report.
