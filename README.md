# AI CART

![Cover_Pic.jpg](https://usercdn.edgeimpulse.com/project126099/a9eac90e03ea0f1ddbadf45cd0b9988b25b95497817fd74ef1c86d1e58922249)

The AI cart uses a combination of object detection algorithms to verify each item placed in the cart. The AI cart's screen shows a real-time receipt of all items in the cart, and when shoppers are ready to check out, simply press the chekout button. A QR code for payment is generated and users can pay the bill by scanning the QR code. No queues, no waiting time, skip the line and checkout easily.

## Physical Setup
![Tear_Down.jpg](https://usercdn.edgeimpulse.com/project126099/885d12ade355aa7098b2ecdfe97072deaf9cf9e612e26dc9fad675e1cd2afb16)

## Work Flow
Let's have a look at the logical flow.
![flow.png](https://usercdn.edgeimpulse.com/project126099/3ab4cc6548a6387d6f11c64b3829a81da553e8b16adb8bc9078fd48cc6185b25)

## Object detection

![Logo.png](https://usercdn.edgeimpulse.com/project126099/0b8afe15c51b7a319c7ac3848eeedf625dcffe756662dfc8f8bb2fdf20b81432)

Edge Impulse is one of the leading development platforms for machine learning on edge devices, free for developers and trusted by enterprises. Here we are using object detection to build a system that can recognize the products. Then we deploy the system on the Raspberry Pi 4B.

## Data acquisition
It is crucial to have a large number of product images while creating the machine learning model. These product images are used to train the model and allow it to differentiate between them. Make sure you have a wide range of perspectives and zoom levels of the items that are offered in the stores. You may take data from any device or development board, or upload your own datasets, for data acquisition. So we're adding our existing datasets here.

![Data Acquisition.png](https://usercdn.edgeimpulse.com/project126099/803fad7d10504fe6ad6db61a48d6802c7f1e32b1bbaa4695b8d547101dfd3898)

Simply navigate to the Data acquisition tab and select a file to upload. After that, give it a label and upload it to the training area. The Edge Impulse will only accept JPG or PNG image files. Convert it to JPG or PNG format using the internet converters if you have any other formats.

So, using the three separate labels—Popcorn, Lays, and Coke—we uploaded all the data. Therefore, the computer will only recognize these items when you check out. You must submit the dataset of other objects if you wish to recognize any more objects except these. For each object, we have put about 50 pictures here. The more data that neural networks have access to, the better their ability to recognize the object.

## Labelling Data
You may view all of your dataset's unlabeled data in the labeling queue. Adding a label to an object is as simple as dragging a box around it. We attempt to automate this procedure by running an object tracking algorithm in the background in order to make life a little easier. If you have the same object in multiple photos we thus can move the boxes for you and you just need to confirm the new box. Drag the boxes, then click Save labels. Continue doing this until your entire dataset has been labeled.

![Label.png](https://usercdn.edgeimpulse.com/project126099/068ce6b99375edaff4c211b2b8cf7dc40bec3cd408f69666974795185141c0e6)

## Designing an Impulse

![Create Impulse.png](https://usercdn.edgeimpulse.com/project126099/436f3958aed13e476a52cfdc4a0e8faeb5ccaf8af61140f737bf29501331a6fa)

With the training set in place, you can design an impulse. An impulse takes the raw data, adjusts the image size, uses a preprocessing block to manipulate the image, and then uses a learning block to classify new data. Preprocessing blocks always return the same values for the same input (e.g. convert a color image into a grayscale one), while learning blocks learn from past experiences.

For this system, we'll use the 'Images' preprocessing block. This block takes in the color image, optionally makes the image grayscale, and then turns the data into a features array. Then we'll use a 'Transfer Learning' learning block, which takes all the images in and learns to distinguish between the two ('popcorn', 'coke') classes.

In the studio go to Create impulse, set the image width and image height to 320px, the 'resize mode' to Fit the shortest axis, and add the 'Images' and 'Object Detection (Images)' blocks. Then click Save impulse.

Then in the image tab, you can see the raw and processed features of every image. You can use the options to switch between 'RGB' and 'Grayscale' mode, but for now, leave the color depth on 'RGB' and click Save parameters.

This will send you to the Feature generation screen. Here you'll
* Resize all the data
* Apply the processing block on all this data.
* Create a visualization of your complete dataset.
* Click Generate features to start the process.

Afterward the 'Feature explorer' will load. This is a plot of all the data in your dataset. Because images have a lot of dimensions (here: 320x320x3=307200 features) we run a process called 'dimensionality reduction on the dataset before visualizing this. Here the 307200 features are compressed down to just 3 and then clustered based on similarity. Even though we have little data you can already see the clusters forming and can click on the dots to see which image belongs to which dot.

![Feature Generation.png](https://usercdn.edgeimpulse.com/project126099/c7affddcb92ca09992dd74e886efa59291030cb6d1697d4165e7a4479bb69a97)

With all data processed it's time to start training a neural network. Neural networks are a set of algorithms, modeled loosely after the human brain, that is designed to recognize patterns. The network that we're training here will take the image data as an input, and try to map this to one of the three classes.

It's very hard to build a good working computer vision model from scratch, as you need a wide variety of input data to make the model generalize well, and training such models can take days on a GPU. To make this easier and faster we are using transfer learning. This lets you piggyback on a well-trained model, only retraining the upper layers of a neural network, leading to much more reliable models that train in a fraction of the time and work with substantially smaller datasets.

To configure the transfer learning model, click Object detection in the menu on the left. Here you can select the base model (the one selected by default will work, but you can change this based on your size requirements), and set the rate at which the network learns.

Leave all settings as it is, and click Start training. After the model is done you'll see accuracy numbers below the training output. We have now trained our model.

![Accuracy.png](https://usercdn.edgeimpulse.com/project126099/65b3ceda3e7739478c79a9a8d391fa378b56c8b5c04c26802effc3fafce24b57)

With the model trained let's try it out on some test data. When collecting the data we split the data up between training and a testing dataset. The model was trained only on the training data, and thus we can use the data in the testing dataset to validate how well the model will work in the real world. This will help us ensure the model has not learned to overfit the training data, which is a common occurrence.

To validate your model, go to Model testing and select Classify all. Here we hit 85.71% accuracy, which is great for a model with so little data.

To see classification in detail, click the three dots next to an item, and select Show classification. This brings you to the Live classification screen with much more details on the file (you can also capture new data directly from your development board from here). This screen can help you determine why items were misclassified.

With the impulse designed, trained, and verified you can deploy this model back to your device. This makes the model run without an internet connection, minimizes latency, and runs with minimum power consumption. Edge Impulse can package up the complete impulse - including the preprocessing steps, neural network weights, and classification code - in a single C++ library or model file that you can include in your embedded software.

## RASPBERRY PI 4B
The Raspberry Pi 4B is the powerful development of the extremely successful credit card-sized computer system. The brain of the device is Raspberry Pi. All major processes are carried out by this device.

![IMG_1508_1.jpg](https://usercdn.edgeimpulse.com/project126099/43fc0be61a70a339008c63d88ad6f0264d01a75113ec40706ec5017b8ee00a93)

If you don't know how to set up the Pi just go [here](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up).
To set this device up in Edge Impulse, run the following commands:

```
curl -sL https://deb.nodesource.com/setup_12.x | sudo bash -
sudo apt install -y gcc g++ make build-essential nodejs sox gstreamer1.0-tools gstreamer1.0-plugins-good gstreamer1.0-plugins-base gstreamer1.0-plugins-base-apps
npm config set user root && sudo npm install edge-impulse-linux -g --unsafe-perm
```
If you have a Raspberry Pi Camera Module, you also need to activate it first. Run the following command:

``sudo raspi-config``

Use the cursor keys to select and open Interfacing Options, and then select Camera and follow the prompts to enable the camera. Then reboot the Raspberry.
With all software set up, connect your camera to Raspberry Pi and run.

``edge-impulse-linux``

This will start a wizard which will ask you to log in and choose an Edge Impulse project. If you want to switch projects run the command with --clean.
That's all! Your device is now connected to Edge Impulse. To verify this, go to your Edge Impulse project, and click Devices. The device will be listed here.

![Devices.png](https://usercdn.edgeimpulse.com/project126099/ca34ffafabd2299037af836463902a65137c93b7f7fc11ce05700a0f8e2c3737)

To run your impulse locally, just connect to your Raspberry Pi again, and run.

``edge-impulse-linux-runner``

This will automatically compile your model with full hardware acceleration, download the model to your Raspberry Pi, and then start classifying.

Here we are using the Linux Python SDK for integrating the model with the system. For working with the Python SDK you need to have a recent version of Python(>=3.7)
For installing the SDK for the Raspberry pi, you need to run the following commands.
```
sudo apt-get install libatlas-base-dev libportaudio0 libportaudio2 libportaudiocpp0 portaudio19-dev
pip3 install edge_impulse_linux -i https://pypi.python.org/simple
```
To classify data, you'll need a model file. We have already trained our model. This model file contains all signal processing code, classical ML algorithms, and neural networks - and typically contains hardware optimizations to run as fast as possible. To download the model file run the below command.

``edge-impulse-linux-runner --download modelfile.eim``

This downloads the file into ~/modelfile.eim.

## Camera Module

This Raspberry Pi Camera Module is a custom-designed add-on for Raspberry Pi. It attaches to Raspberry Pi by way of one of the two small sockets on the board's upper surface. This interface uses the dedicated CSI interface, which was designed especially for interfacing with cameras. The CSI bus is capable of extremely high data rates, and it exclusively carries pixel data.

![IMG_1510_1.jpg](https://usercdn.edgeimpulse.com/project126099/3832dbaf30a038b2786f17a7d27feea450a3880bd578960d0870c1e88e30f483)

The board itself is tiny, at around 25mm x 23mm x 8mm. It also weighs just over 3g, making it perfect for mobile or other applications where size and weight are important. It connects to Raspberry Pi by way of a short flexible ribbon cable. The camera connects to the BCM2835 processor on the Pi via the CSI bus, a higher bandwidth link that carries pixel data from the camera back to the processor. This bus travels along the ribbon cable that attaches the camera board to the Pi.

The sensor itself has a native resolution of 5 megapixels and has a fixed focus lens onboard. In terms of still images, the camera is capable of 2592 x 1944 pixel static images, and also supports 1080p30, 720p60 and 640x480p60/90 video.

## Power Unit

For making the cart an independent mobile system we are using a power bank to power up the Raspberry Pi.

![IMG_1514_1.jpg](https://usercdn.edgeimpulse.com/project126099/1c529f06a307321de489f6b5ac08ccef00b69e6b761da5bb118577a029a287ee)

## Checkout Interface

The checkout interface has two parts,
1. Front-end developed using HTML, JS
2. Backend API developed using NodeJS and Express

### 1. Front-end developed using HTML, JS
The front-end continuously checks for the changes happening in the back-end API and displays the changes to the user. Once an item is added to the API, the front-end displays it as an item added to the cart.

![Home.png](https://usercdn.edgeimpulse.com/project126099/4b84c4c2baf49e78ba7b9dab8767edacbd00f5d11f0ab086830ede666baf8047)

![Cart.png](https://usercdn.edgeimpulse.com/project126099/6d2c5226a2dd4573228537c89bb3ac47eb8b97495ac3027429f2102eede6ec44)

### 2. Backend API developed using NodeJS and Express
The backend REST API is developed using NodeJS and Express. ExpressJS is one of the most popular HTTP server libraries for Node.js, which ships with very basic functionalities. The backend API keeps the details of the products that are visually identified.
For setting our interface we have used a small tablet which is having a touch interface with a small stand.

### Deploy API on Heroku
In order to deploy API on Heroku, you must have Git and the Heroku CLI installed to deploy with Git.
* [Git installation instructions](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Heroku CLI installation instructions](https://devcenter.heroku.com/articles/heroku-cli#install-the-heroku-cli)

Once the prerequisites and installed, now you can deploy your app to Heroku.
* Download the code from the Github Repository.
* Navigate to the ~/CheckoutUI/server directory
* Now follow the given steps
```
git init
git add .
git commit -m "My first commit"
heroku create -a nameofyourapi
git push heroku master
```

This will result in the creation of an api with a URL in the format ``https://nameofyourapi.herokuapp.com/``

### Note
To verify your API is working fine, go to  https://nameofyourapi.herokuapp.com/product , if you get the following response, the API is working flawlessly.


## Integrating API in the Code
### In Python Code
* Replace the "URL" in billing.py with your API URL

### In JS Code
* Navigate to ~/CheckoutUI/client/asset/scripts
* Replace all occurrences of "https://automaticbilling.herokuapp.com/" with your API URL

## Code

The entire code and related assets can be found [here](https://github.com/CodersCafeTech/AI-Cart/)
## What's in the Future?
* Generate Digital Receipt
* Keeps Transaction History
* Screen Promotions and Offers
