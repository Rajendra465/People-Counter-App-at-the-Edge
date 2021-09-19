# Project Write-Up

In investigating potential people counter models, I found following model:
Model: SSD Mobile Net V2 COCO

Command used to download model:
wget http://download.tensorflow.org/models/object_detection/ssd_mobilenet_v2_coco_2018_03_29.tar.gz

extracted model files from tar file using below command-

tar -xvf ssd_mobilenet_v2_coco_2018_03_29.tar.gz

Converted the model to an Intermediate Representation:

python /opt/intel/openvino/deployment_tools/model_optimizer/mo.py --input_model ssd_mobilenet_v2_coco_2018_03_29/frozen_inference_graph.pb --tensorflow_object_detection_api_pipeline_config ssd_mobilenet_v2_coco_2018_03_29/pipeline.config --reverse_input_channels --tensorflow_use_custom_operations_config /opt/intel/openvino/deployment_tools/model_optimizer/extensions/front/tf/ssd_v2_support.json -o optimized/ssd_mobilenet_v2_coco_2018_03_29


Source the env by following command-

source /opt/intel/openvino/bin/setupvars.sh -pyver 3.5

To run the inferance on model:

python main.py -i resources/Pedestrian_Detect_2_1_1.mp4 -m /home/workspace/optimized/ssd_mobilenet_v2_coco_2018_03_29/frozen_inference_graph.xml -l /opt/intel/openvino/deployment_tools/inference_engine/lib/intel64/libcpu_extension_sse4.so -d CPU -pt 0.1 | ffmpeg -v warning -f rawvideo -pixel_format bgr24 -video_size 768x432 -framerate 24 -i - http://0.0.0.0:3004/fac.ffm

## Explaining Custom Layers
We need custom layers when existing layers are not enough to provide the desired output. Generally When we need an extra feature to be added in the model for prediction which is already not there in the existing trained model. We can combine two or more existing layers to form a new layer.
OpenVINO offers different options depending on model original framework (TensorFlow, Caffe, MXNet, etc), available devices and user preferences to handle those not recognized operations as Custom Layers.
At the moment the Model Optimizer tries to convert a model, it will give more relevance to preserve any defined Custom Layer, which means that if a layer is defined as custom (with some internal logic), it will be used even if such layer is already supported.
## Comparing Model Performance

My method(s) to compare models before and after conversion to Intermediate Representations
were 'Inference Time' and 'Accuracy'.

The difference between model accuracy pre- and post-conversion was 0.77 and 0.87.

The size of the model pre- and post-conversion was 3557ms and 72ms.

## Assess Model Use Cases

# To avoid gathering in Covid-19 situation
Cameras can be installed where there is a high chance of people's gathering, So, It can notify police by sending an alert on phone with location to avoid such large gatherings.

# Lift's overweight
It can also used to avoid large crowd in a lift which can prevent from overloading alarm in lift an can save time.


# Security places
Security places like ATMs, where maximum one or two peoples are allowed, It will help by alerting them to avoid more than required people inside ATM.

## Assess Effects on End User Needs

Lighting, model accuracy, and camera focal length/image size have different effects on a
deployed edge model. The potential effects of each of these are as follows...

Lighting is required to work model effectively. In low light or no light, It may not give accurate output. So this can affect the performance of the model but it can be overcome by keeping lights on and also we can use cameras that can capture videos or images in dark also(Night Vision Cameras).

Model Accuracy and speed are the factor that combining affect the end-user. One must choose either one based on the requirements.

Focal length or image size is also important to factor for model accuracy. The focal length is also related to light. So better the focal length better will be the result of image and model output. And Smaller image will decrease the model accuracy so image size should be good enough to detect people.

