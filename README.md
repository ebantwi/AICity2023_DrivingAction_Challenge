
# NOTES

## 7th [AICITY](https://www.aicitychallenge.org/2023-challenge-tracks/) (2023) Challenge Track 3 - Naturalistic Driving Action Recognition.


### Running environment

Please refer to the [INSTALL](Train/INSTALL.md) to prepare the running environment.

### Preprocessing
Please run command below to go into the preprocessing folder:
```bash
cd Preprocessing
```
First, download the training data and annotation files, which are provided by the 2023 AICity Challenge. Then put the downloaded data in **YOUR_DATA_PATH**.
There are some errors in the official annotation file 'user_id_86356.csv', replace it with the modified files in [Preprocessing/A1_new_annotation](Preprocessing/A1_new_annotation).

modify the __data_homepath__ parameter in [Preprocessing/cut_video.py](Preprocessing/cut_video.py#L31) and [Preprocessing/create_csv.py](Preprocessing/create_csv.py#L6) as user data path.

Proceed to run the commands below to cut the original long training videos into meaningful segments (It needs about 04 hours.):
```bash
python cut_video.py
```
Then, generate the training files (*.csv) by running:
```bash
python create_csv.py
```
Finally, the total structure of YOUR_DATA_PATH should be the same as follows:
>   * YOUR_DATA_PATH
>     * A1
>       * user_id_*
>         * CAMERAVIEW_user_id_*.MP4
>         * user_id_*.csv
>       * ...
>     * A1_cut_video
>       * new_spllit_by_id_A1_total
>         * expand_zero
>           * train_dashboard.csv
>           * val_dashboard.csv
>           * test_dashboard.csv
>           * ...
>         * original_zero
>       * 0
>         * *.MP4 
>       * 1
>       * ...

### Train
Uses 2 RTX3090 GPUS ( totally 48G ) to perform the training experiments.
First, run this command to go into the training folder.
```bash
cd Train
```
Download [X3D_l weights](https://dl.fbaipublicfiles.com/pyslowfast/x3d_models/x3d_l.pyth) pretrained on kinetics-400 and put it in [Train/pretrain](Train/pretrain).
In the [Train/exp/aicity3](Train/exp/aicity3) folder, there are experiment folders to train all of the used models.
To train a model, remember to first specify the **DATA.PATH_TO_DATA_DIR** in the **run.sh** files as your path (should be YOUR_DATA_PATH/A1_cut_video).

Then please run:
```
CUDA_VISIBLE_DEVICES=[your_GPU_indexes] bash exp/aicity3/[EXPERIMENTS_FOLDER]/run.sh
```
The training results are in the  **EXPERIMENTS_FOLDER**, structured as follows:
>   * EXPERIMENTS_FOLDER
>     * checkpoint_[VIEW]_[TRAIN_MANNER]_zero
>       * checkpoints
>         * checkpoint_epoch_000**.pyth
>         * ...
>       * stdout.log
>       * ...
>     * run.sh
>     * config.yaml

Used the weights of the last epoch to predict the snippet-level action probabilities.


### Inference
### General folder structure for Inference
Please put the videos to be tested in the **YOUR_TEST_DATA** folder. And organize the folder as follows:
>   * YOUR_TEST_DATA
>     * user_id_*
>       * CAMERAVIEW_user_id_*.MP4
>       * CAMERAVIEW_user_id_*.MP4
>       * CAMERAVIEW_user_id_*.MP4
>       * ...
>     * video_ids.csv


### Acknowledgement

Build based on [UniFormerv2](https://github.com/OpenGVLab/UniFormerV2) and [Winner of 2022](https://github.com/VTCC-uTVM/2022AICityChallenge-Track3) repository.


