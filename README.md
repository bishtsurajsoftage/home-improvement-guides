# home-improvement-guides

# INTRODUCTION 
A couple of months ago I have started a little project  that involved some Raspberry Pis and some of that type of equipment. One of the items we 've purchase is a small [E-Ink display, 2.13",](https://www.waveshare.com/wiki/2.13inch_e-Paper))
to be used as a diagnostics pane. So about a week ago I was cleaning up my work space to remove all those fancy useless bits from the work surface coming upon this little E-Ink display. That night I was trying to think about what to do 
with it.

This is what I came up with and how to build one yourself really fast:

Take a photo frame you already have of your loved ones and a little caption to it with changing texts. In this case I took our family's photo and added some slternating quotes.

**Disclaimer** - This is **not** a sponsored post neither are any of the products promoted in anyway. Other than the Raspberry Pi because I love it.

<img src="https://www.morirt.com/Kindler/7.png" alt="" data-canonical-src="https://www.morirt.com/Kindler/7.png" width="512"/>

## Built Details
Since the display is only 2.13 it really can't be too much in and of itself. Since it's also not that durable and I 'm pretty sure that after 10 minutes with our kids this thing will turn into a colored useless strip I was going from
something more decorative. So I have commenced building a picture frame with our own favourite picture that will have an alternative text on it.
For the text I  chose quotes.

This is how it looks:

<img src="https://morirt.com/Kindler/6.jpg" alt="" data-canonical-src="https://www.morirt.com/Kindler/6.jpg" width="512"/>

### What You 'll need
I really wanted to be durable, resilient and long lasting. We have a few picture frames in our house and some just died, some just reboot occassionally and some just shut-off from time to time waiting for someone to start them.
Hence I wanted something that once plugged in, even with no internet, will continue working.

#### Equipment:
1. Raspberry Pi of any kind. I started with a Raspberry Pi W Zero but for some reason it chose not to work with my display (although it should have) so I have switched to a Raspberry Pi 3.
2. E ink display using SPI. I have used WaveShare 2.13 D but really any would do.
3. Some duct tape
4. A photo frame.
5. Your Photo.


**Project Work Time**: ~15 minutes if your Raspberry Pi is ready, ~1 hour if it needs flushing.

<img src="https://www.morirt.com/Kindler/0.jpg" alt="" data-canonical-src="https://www.morirt.com/Kindler/0.jpg" width="512"/>

## Instructions

### Step 1 - Get Your Raspberry Pi ready
No need for me here.
https://www.raspberry.org/documentation/installation/installing-images/

Remember to 'touch ssh' and edit your network configurations to have a way to deploy codeon it. [This](https://raspberrypi.stackexange.com/a/57023) woeked just fine.

### Step 2 - Prep your Pi for Display
Depending on your display. I have worked by [this](https://www.waveshare.com/wiki/2.13inch_e-Paper_HAT_(D)) and here's a summary:

```
# Enable SPI
sudo raspi-config
#Choose Interfacing Options -> SPI -> Yes to enable SPI interface
# Restart

# Install BCM2835 libraries
wget https://www.airspayce.com/mikem/bcm2835/bcm2835-1.60.tar.gz

tar zxvf bcm2835-1.60.tar.gz

cd bcm2835-1.60.tar.gz
sudo ./configure

sudo make
sudo make check
sudo make install

# Wiring Pi
sudo apt-get install wiringpi


# Python2 setup
sudo apt-get update
sudo apt-get install python-pip
sudo apt-get install python-pil

sudo apt-get install python-numpy

sudo pip install RPi.GPIO

sudo pip install spidev


# e-Paper libs
sudo git clone https://github.com/waveshare/e-Paper

cd e-Paper/RaspberryPi\&JetsonNano/
cd Python

sudo python setup.py install

# Image Libraries
sudo apt-get install python3-pil
sudo apt-get install python3-numpy
```


### Step 3 - Connection Setup
Now that we have the tools and everything needed to have the code communicate with the display we need to connect it. This part is relatively simple - the adapter to the display should come with a full IDE female connector to connect
to the Raspberry Pi's male IDE connector. Just snap that in on top of yout RaspberryPi and should be ready to go.

In my case, I also wanted a small fan to circulate some air in the case of the device. Since all of the IDE pins are locked I have used an 8 pin connector from the adapter (came in the box for me) and then took pin-0 (VNN) amd pin-1 (GND)
and connected the fan to that.

<img src="https://www.morirt.com/Kindler/2.jpg" alt="" data-canonical-src="https://www.morirt.com/Kindler/2.jpg" width="512"/>

<img src="https://www.morirt.com/Kindler/3.jpg" alt="" data-canonical-src="https://www.morirt.com/Kindler/3.jpg" width="512"/>

### Step 3.5 - [Optional] Configuring WIFI

This step is optional and the 'kindler' will be operating the same way regardless if you take this step or not. This is just if you want to have an option to update your quotes remotely or SSH into the RaspberryPi.

Edit the file 'wifi_conf' to have the same Wifi configurations and the network you want it connecting to. After that, upload this configuration to 'wpa_supplicant.conf' on the root directory of the RaspberryPi and create a file named 
'ssh' on the same directory.

The 'ssh' empty file on the root directory will instruct the RaspberryPi to open up the SSH Server when booting, and a parsing of the 'wpa_supplicant.conf' will have the RaspberryPi load the Wifi network configurations and attempt
to connect to the network.

### Step 3.7 - [Optional] Editing the Text

You can edit the texts to be displayed however you want. Just keep the first line (header) of the CSV and make sure you 're inputting two elements even if the second one is going to be an empty one. For example, if you want to have just
one or two lines displayed the 'quotes.csv' file should look like this:

```csv
quote,author
"This is line #1 with no 'Author'", ""
"Line #2 with Author", "Tony"
```

### Step 4- Upload the Code
At this point you want to upload your code to the Raspberry Pi. You can so that with 'scp'. This should be **after** you have put your lovely quotes in 'quotes.csv'.

'scp -r ~/Kindler/. pi@pi_ip:/home/pi/Kindler/'

### Step 5 - Adding to Boot
At this point you want to make sure that even if the device reboots for some reason, power break, accidentally disconnecting it or anything else, when it starts up again it will continue operating as if nothing happened. Reiterating
that the main point here is not to have another device you need to maintain but rather have it work without any maintenance.

Edit the 'ec.local' file like this:

`sudo nano /etc/rc.local`

Add the following line before the `exit' line at the end:

`sudo python /home/pi/Kindler/main.py &`
To exit 'nano' use 'Ctrl+O' and 'Return/Enter' to save and 'CTRL+X' to edit. Be sure to keep the '&' at the end or your raspberryPi will be stuck on boot loop. You need it to make sure the 'rc.local' does not struct waiting on input
but continue to the next line.

At this point you have added 'Kindler' to the start up of the device and it should execute on the next boot.

### Step 6 - Casing in the Photo
When I measured this particular eink display the size was 2[cm] on 3.5[cm].  Yours might be different depending on the EInk display you are using. Use a box cutter to cut the image and paste your display on the spot.
Use some duct tape to join the RaspberryPi with the back of the photo. Make sure it has some clear surface space to disperse of aggregated heat.

























































