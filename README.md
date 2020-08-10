# Action-Based Representation Learning for Autonomous Driving

-------------------------------------------------------------
This repository is for running the experiments of paper: Action-Based Representation Learning for Autonomous Driving

The processes can be defined as four types:

 * Train an encoder model (Behaviour Cloning (BC), Inverse, Forward, ST-DIM)
 * Train a MLP for affordances outputs. The pre-trained encoder model will be used.
 * Validation on affordances prediction.
 * Actual drive using controller tuned with affordances prediction.

-------------------------------------------------------------
### Setting Environments & Getting Datasets

1. Download the dataset with this [link]().

2. Define the path of the dataset folder with SRL_DATASET_PATH:

        export SRL_DATASET_PATH = <Path to where your datasets are>

3. Download this repository

        git clone https://github.com/yixiao1/Action-Based-Representation-Learning.git

4. Download the CARLA version we used with this [link]().

5. Add packages to your PYTHONPATH:

    - Path to carla
    - Path to carla .egg fil
    - Path to scenario_runner
    - Path to carl

    example:

        export PYTHONPATH=/<repo dir>/Carla96ped4/PythonAPI/carla:/<repo dir>/Carla96ped4/PythonAPI/carla/dist/carla-0.9.6-py3.5-linux-x86_64.egg:/<repo dir>/scenario_runner:/<repo dir>/carl

    where `repo dir` is the directory of the downloaded `Action-Based-Representation-Learning` repository

-------------------------------------------------------------
### Training Encoder

1. Define configuration files for training. Refer to files in [configs folder]()

2. Run the main.py file with "train_encoder" process:

        python3 main.py --single-process train_encoder --gpus 0 --encoder-folder ENCODER --encoder-exp BC_im_5Hours_seed1

    where `--single-process` defines the process type, `--gpus` defines the gpu to be used, `--encoder-folder` is the experiment folder of your config file for the encoder, `--encoder-exp` is the experiment name of your config file for the encoder

-------------------------------------------------------------
### Training MLP for affordances

1. Define configuration files for training. Refer to files in [configs folder]()

2. Run the main.py file with "train" process:

        python3 main.py --single-process train --gpus 0 --encoder-folder ENCODER --encoder-exp BC_im_5Hours_seed1 --encoder-checkpoint 100000 -f EXP -e BC_im_5Hours_seed1_encoder_frozen_1FC_30mins

   where `--single-process` defines the process type, `--gpus` defined the gpu to be used, `--encoder-folder` is the experiment folder in `_logs` of the encoder to be used, `--encoder-exp` is the experiment of encoder to be used, `--encoder-checkpoint` is the specific encoder checkpoint to be used, `-f` is the folder to save experiments of affordances prediction, `-e` is the experiment of affordance training

-------------------------------------------------------------
### Validate on affordances prediction

1. Run the main.py file with "validation" process. You will need to define the path to the json file of validation dataset:

        python3 main.py --single-process validation --gpus 0 --encoder-folder ENCODER --encoder-exp BC_im_5Hours_seed1 --encoder-checkpoint 100000 -f EXP -e BC_im_5Hours_seed1_encoder_frozen_1FC_30mins -vj /<repo dir>/carl/database/CoRL2020/dataset_dynamic_Town01_1Hour_valid.json

    where `-vj` defines the path to your validation json file

-------------------------------------------------------------
### Driving on CARLA benchmark

1. The driving results will be saved to your SRL_DATASET_PATH, you could re-define if you want to save to another path

2. Build a docker with your carla version

3. Set up your CARLA drivng PYTHONPATH:

        export PYTHONPATH=/<repo dir>:/<repo dir>/cad:/<repo dir>/Carla96ped4/PythonAPI/carla:/<repo dir>/PythonAPI/carla:/<repo dir>/scenario_runner

    where `repo dir` is the directory of the downloaded `Action-Based_Representation_Learning` repository


4. Define a config.json for using a specific model, and put it inside the logs folder of that model: _logs/(exp folder)/(exp exp)

   check on this [config.json]() example

5. To run the benchmark, go under [driving-benchmarks]() folder, and run:

        python3 benchmark_runner.py -b NoCrash -a /home/yixiao/Action-Based_Representation_Learning/drive/AffordancesAgent.py -d carlaped -c /home/yixiao/Action-Based_Representation_Learning/_logs/EXP/BC_im_5Hours_seed1_encoder_finetuning_3FC_30mins_s1_100000/config.json --gpu 2

    where `-b` is the benchmark, `-a` is the path to the agent class, `-c` is the [configuration file] () for driving
