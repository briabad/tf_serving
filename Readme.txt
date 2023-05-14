--activate environment
cd .env 
. Scripts/activate

--download tf serving image
docker pull tensorflow/serving

--If you are running Docker on an instance with GPU, you can install the GPU version as well:
docker pull tensorflow/serving:latest-gpu

-- install all dependencies
pip install -r Requirements.txt

-- run the model
python src\training\model.py


Once you have your model saved, and Tensorflow Serving correctly installed with Docker, you are going to serve it as an API Endpoint. 

It is worth mentioning that Tensorflow Serving allows two types of API Endpoint — REST and gRPC.

REST is a communication “protocol” used by web applications. It defines a communication style on how clients communicate with web services. REST clients communicate with the server using the standard HTTP methods like GET, POST, DELETE, etc. The payloads of the requests are mostly encoded in JSON format
gRPC on the other hand is a communication protocol initially developed at Google. The standard data format used with gRPC is called the protocol buffer. gRPC provides low- latency communication and smaller payloads than REST and is preferred when working with extremely large files during inference. 
In this tutorial, you’ll use a REST Endpoint, since it is easier to use and inspect. It should also be noted that Tensorflow Serving will provision both Endpoints when you run it, so you do not need to worry about extra configuration and setup. 

Follow the steps below to serve your model:

--First, in your project folder, open a terminal, and add the Docker command below, you need to spacify the path of the model artefact  in source parameter:

docker run -p 8501:8501 --name tfserving_classifier --mount type=bind,source=/mnt/c/Users/brian/OneDrive/Escritorio/PROJECTS/serving_tf_model/src/models/img_classifier/,target=/models/img_classifier -e MODEL_NAME=img_classifier -t tensorflow/serving
N

Note:

If you encounter the path error:


docker: Error response from daemon: invalid mount config for type “bind”: bind source path does not exist: /User/tf-server/img_classifier/.


Then specify the full path to the model folder. Remember, not the model itself, but the model folder.


The prediction URL is made up of a few important parts. A general structure may look like the one below:

http://{HOST}:{PORT}/v1/models/{MODEL_NAME}:{VERB}

HOST: The domain name or IP address of your model server
PORT: The server port for your URL. By default, TF Serving uses 8501 for REST Endpoint.
MODEL_NAME: The name of the model you’re serving. 
VERB: The verb has to do with your model signature. You can specify one of predict, classify or regress. 

Note:


To run the predict.py file, ensure the TF Serving container is still active, before running python predict.py in a new terminal window.

--run predict

python src/predict/predict.py


