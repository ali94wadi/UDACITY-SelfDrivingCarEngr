## Submission (for grading)

### Project overview
In this project, a resnet50 pretrained model is used as a starting point for classification and object detection of cars, pedestrains and cyclists in a an autonomous vehicle dataset. The task is of crucial importance to realizing full autonomy in passenger vehicles or otherwise. The trained model is to be optimized to work with the data provided through data-spesific decisions. 

### Set up
The files in this repo are taken for the most part from the UDACITY-provided virtual environment. The algorithms should run there. Alternatively, steps to use these files on a local machine are provided down below after the resutls. 
PS **YOUR MILAGE MAY VARY** 

### Dataset
#### Dataset analysis
The training data was analyzed and here are some rough figures (analysis had to be repeated as a consequence of data loss due to the VM apps crashing frequently)

![index](https://user-images.githubusercontent.com/23568809/155496149-b0e9c8b4-4ab6-46ba-8fdf-e42be7bc00a5.png)

As can be seen, cars are most frequent in the dataset, followed by pedistriants and finally cyclists. this will affect the performance in relation to how much data of each category is there.

Here are some random samples taken from the video stream

![sss](https://user-images.githubusercontent.com/23568809/155496427-7756b331-e96b-4f8f-af2f-870872138bb4.png)
![sssss](https://user-images.githubusercontent.com/23568809/155496433-e5fcb1bd-5cca-4285-829c-06db02a34df3.png)


#### Cross validation
First, the dataset is randomly shuffled. Then, an 80-20 split is utilized for the 100 sequences available to work with. 80 sequences are used for training, and 20 for validation. The higher percentage and the shuffling is done to promote diversity and try to minimize the chance of a dominant class in the data (which is also an issue with the data). This ultimately shuold serve to reduce overfitting as much as possible.

### Training
#### Reference experiment
First, the provided resnet50 refrence model is trained on the provided dataset then validated and evaluated to show very poor performance of the network. The data includes nighttime streams and it is evident augmentations to the training data are required to get better performance.

Training

![ref_1](https://user-images.githubusercontent.com/23568809/155835593-539d5cf1-a92b-4e82-82b2-4ea92034a5c8.png)

Looking at the training results, loss, while being extremely noisy, was on average decreasing and it does reach some steady state. Preformance was also not great in terms of precision and recall. 

**PS: only 5k steps were used as the VM is scarce in memory resources and eval/train parallel sessions are VERY BUGGY. They cannot be run together all the time, and it was necessary to stop the training to do evaluation to delete checkpoints, and then rrepeat...**

Validation

![s1](https://user-images.githubusercontent.com/23568809/155073998-078ad3ea-b865-4cd1-b564-31421c2bcfc8.png)
![s2](https://user-images.githubusercontent.com/23568809/155074011-036e5f17-c7be-4af3-907b-80be85c6e077.png)

animation

https://user-images.githubusercontent.com/23568809/155206048-53854bef-0688-4e51-977b-c405af273592.mp4

#### Improve on the reference
After messing around so much and training for a few hours, "better" results were realized after augmentations to the data. This served to mimic nighttime data to artificially inflate their presense in the training dataset, and it also served to circumvent from any overfitting that an approach like SSD results in. Results did improve quite a bit, and some classification is successfull in comparison with the reference training. 

A summary of the augmentations inserted into `pipeline_new.config`:
```
data_augmentation_options {
    random_horizontal_flip {
    probability: 0.5
    }
  }
  data_augmentation_options {
    random_crop_image {
      min_object_covered: 0.0
      min_aspect_ratio: 0.75
      max_aspect_ratio: 3.0
      min_area: 0.75
      max_area: 1.0
      overlap_thresh: 0.0
    }
  }
  data_augmentation_options {
    random_rgb_to_gray {
    probability: 0.5
    }
  }
  data_augmentation_options {
    random_adjust_contrast {
    min_delta: 0.6
    max_delta: 1.0
    }
  }
  data_augmentation_options {
    random_adjust_brightness {
    max_delta: 0.3
    }
    }
```

Here are some results:

Training

![exp](https://user-images.githubusercontent.com/23568809/155880457-5774ac57-42a0-472e-8a2a-e09e2bf7f849.png)

Looking at the training results, loss is consistantly moving in the right direction after augmentation. Preformance was also much better in terms of precision and recall. Under the limitations posed by the VM, good results are achieved after training for more time and inflating both the learning rate and the warmup time. It seems like there is more performance to be had by further tuning and perhaps more augmentation and/or model pipeline changes, but total loss is around ~1.5, classificaiton is happening, and training did reach a plateau. 


Validation

![es1](https://user-images.githubusercontent.com/23568809/155267842-58bce3dd-749f-4e22-bfeb-7be704d6bf3a.png)
![es3](https://user-images.githubusercontent.com/23568809/155267852-65aa1ffa-47f7-48e1-98bb-5f22ffec640c.png)

Animation

https://user-images.githubusercontent.com/23568809/155880451-0b69311c-3353-4421-8229-64b1551eae12.mp4



### Comments on Training

While the training losses are heading the right direction, it is evident the improvement to the pipeline resulted in better performance as shown in the steepest trend describing training after augmentation. 
It seems that we can still make improvements to th pipeline, but that requires more time finetuning the augmentations. Another approach could be to change the network to something other than the suggested resnet50, but this would require much more time as well. I think the realized improvement at least shows promise in terms of possible future performance. Moreover, this is an SSD that is aimed at quick computation to generate results on behalf of some loss in performance.
-----------------------------------------------------------------------------------------------------------------





# Object Detection in an Urban Environment

## Data

For this project, we will be using data from the [Waymo Open dataset](https://waymo.com/open/).

[OPTIONAL] - The files can be downloaded directly from the website as tar files or from the [Google Cloud Bucket](https://console.cloud.google.com/storage/browser/waymo_open_dataset_v_1_2_0_individual_files/) as individual tf records. We have already provided the data required to finish this project in the workspace, so you don't need to download it separately.

## Structure

### Data

The data you will use for training, validation and testing is organized as follow:
```
/home/workspace/data/waymo
    - training_and_validation - contains 97 files to train and validate your models
    - train: contain the train data (empty to start)
    - val: contain the val data (empty to start)
    - test - contains 3 files to test your model and create inference videos
```

The `training_and_validation` folder contains file that have been downsampled: we have selected one every 10 frames from 10 fps videos. The `testing` folder contains frames from the 10 fps video without downsampling.

You will split this `training_and_validation` data into `train`, and `val` sets by completing and executing the `create_splits.py` file.

### Experiments
The experiments folder will be organized as follow:
```
experiments/
    - pretrained_model/
    - exporter_main_v2.py - to create an inference model
    - model_main_tf2.py - to launch training
    - reference/ - reference training with the unchanged config file
    - experiment0/ - create a new folder for each experiment you run
    - experiment1/ - create a new folder for each experiment you run
    - experiment2/ - create a new folder for each experiment you run
    - label_map.pbtxt
    ...
```

## Prerequisites

### Local Setup

For local setup if you have your own Nvidia GPU, you can use the provided Dockerfile and requirements in the [build directory](./build).

Follow [the README therein](./build/README.md) to create a docker container and install all prerequisites.

### Download and process the data

**Note:** ”If you are using the classroom workspace, we have already completed the steps in the section for you. You can find the downloaded and processed files within the `/home/workspace/data/preprocessed_data/` directory. Check this out then proceed to the **Exploratory Data Analysis** part.

The first goal of this project is to download the data from the Waymo's Google Cloud bucket to your local machine. For this project, we only need a subset of the data provided (for example, we do not need to use the Lidar data). Therefore, we are going to download and trim immediately each file. In `download_process.py`, you can view the `create_tf_example` function, which will perform this processing. This function takes the components of a Waymo Tf record and saves them in the Tf Object Detection api format. An example of such function is described [here](https://tensorflow-object-detection-api-tutorial.readthedocs.io/en/latest/training.html#create-tensorflow-records). We are already providing the `label_map.pbtxt` file.

You can run the script using the following command:
```
python download_process.py --data_dir {processed_file_location} --size {number of files you want to download}
```

You are downloading 100 files (unless you changed the `size` parameter) so be patient! Once the script is done, you can look inside your `data_dir` folder to see if the files have been downloaded and processed correctly.

### Classroom Workspace

In the classroom workspace, every library and package should already be installed in your environment. You will NOT need to make use of `gcloud` to download the images.

## Instructions

### Exploratory Data Analysis

You should use the data already present in `/home/workspace/data/waymo` directory to explore the dataset! This is the most important task of any machine learning project. To do so, open the `Exploratory Data Analysis` notebook. In this notebook, your first task will be to implement a `display_instances` function to display images and annotations using `matplotlib`. This should be very similar to the function you created during the course. Once you are done, feel free to spend more time exploring the data and report your findings. Report anything relevant about the dataset in the writeup.

Keep in mind that you should refer to this analysis to create the different spits (training, testing and validation).


### Create the training - validation splits
In the class, we talked about cross-validation and the importance of creating meaningful training and validation splits. For this project, you will have to create your own training and validation sets using the files located in `/home/workspace/data/waymo`. The `split` function in the `create_splits.py` file does the following:
* create three subfolders: `/home/workspace/data/train/`, `/home/workspace/data/val/`, and `/home/workspace/data/test/`
* split the tf records files between these three folders by symbolically linking the files from `/home/workspace/data/waymo/` to `/home/workspace/data/train/`, `/home/workspace/data/val/`, and `/home/workspace/data/test/`

Use the following command to run the script once your function is implemented:
```
python create_splits.py --data-dir /home/workspace/data
```

### Edit the config file

Now you are ready for training. As we explain during the course, the Tf Object Detection API relies on **config files**. The config that we will use for this project is `pipeline.config`, which is the config for a SSD Resnet 50 640x640 model. You can learn more about the Single Shot Detector [here](https://arxiv.org/pdf/1512.02325.pdf).

First, let's download the [pretrained model](http://download.tensorflow.org/models/object_detection/tf2/20200711/ssd_resnet50_v1_fpn_640x640_coco17_tpu-8.tar.gz) and move it to `/home/workspace/experiments/pretrained_model/`.

We need to edit the config files to change the location of the training and validation files, as well as the location of the label_map file, pretrained weights. We also need to adjust the batch size. To do so, run the following:
```
python edit_config.py --train_dir /home/workspace/data/train/ --eval_dir /home/workspace/data/val/ --batch_size 2 --checkpoint /home/workspace/experiments/pretrained_model/ssd_resnet50_v1_fpn_640x640_coco17_tpu-8/checkpoint/ckpt-0 --label_map /home/workspace/experiments/label_map.pbtxt
```
A new config file has been created, `pipeline_new.config`.

### Training

You will now launch your very first experiment with the Tensorflow object detection API. Move the `pipeline_new.config` to the `/home/workspace/experiments/reference` folder. Now launch the training process:
* a training process:
```
python experiments/model_main_tf2.py --model_dir=experiments/reference/ --pipeline_config_path=experiments/reference/pipeline_new.config
```
Once the training is finished, launch the evaluation process:
* an evaluation process:
```
python experiments/model_main_tf2.py --model_dir=experiments/reference/ --pipeline_config_path=experiments/reference/pipeline_new.config --checkpoint_dir=experiments/reference/
```

**Note**: Both processes will display some Tensorflow warnings, which can be ignored. You may have to kill the evaluation script manually using
`CTRL+C`.

To monitor the training, you can launch a tensorboard instance by running `python -m tensorboard.main --logdir experiments/reference/`. You will report your findings in the writeup.

### Improve the performances

Most likely, this initial experiment did not yield optimal results. However, you can make multiple changes to the config file to improve this model. One obvious change consists in improving the data augmentation strategy. The [`preprocessor.proto`](https://github.com/tensorflow/models/blob/master/research/object_detection/protos/preprocessor.proto) file contains the different data augmentation method available in the Tf Object Detection API. To help you visualize these augmentations, we are providing a notebook: `Explore augmentations.ipynb`. Using this notebook, try different data augmentation combinations and select the one you think is optimal for our dataset. Justify your choices in the writeup.

Keep in mind that the following are also available:
* experiment with the optimizer: type of optimizer, learning rate, scheduler etc
* experiment with the architecture. The Tf Object Detection API [model zoo](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf2_detection_zoo.md) offers many architectures. Keep in mind that the `pipeline.config` file is unique for each architecture and you will have to edit it.

**Important:** If you are working on the workspace, your storage is limited. You may to delete the checkpoints files after each experiment. You should however keep the `tf.events` files located in the `train` and `eval` folder of your experiments. You can also keep the `saved_model` folder to create your videos.


### Creating an animation
#### Export the trained model
Modify the arguments of the following function to adjust it to your models:

```
python experiments/exporter_main_v2.py --input_type image_tensor --pipeline_config_path experiments/reference/pipeline_new.config --trained_checkpoint_dir experiments/reference/ --output_directory experiments/reference/exported/
```

This should create a new folder `experiments/reference/exported/saved_model`. You can read more about the Tensorflow SavedModel format [here](https://www.tensorflow.org/guide/saved_model).

Finally, you can create a video of your model's inferences for any tf record file. To do so, run the following command (modify it to your files):
```
python inference_video.py --labelmap_path label_map.pbtxt --model_path experiments/reference/exported/saved_model --tf_record_path /data/waymo/testing/segment-12200383401366682847_2552_140_2572_140_with_camera_labels.tfrecord --config_path experiments/reference/pipeline_new.config --output_path animation.gif
```


