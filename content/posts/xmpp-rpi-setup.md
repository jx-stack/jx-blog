+++
title = "Using a Raspberry Pi as a XMPP chat server"
date = 2021-09-14
tags = ["guides","selfhosting","raspberry pi"]
+++

I recently setup a Raspberry Pi with Prosody XMPP server and I thought that it worked rather well for such a small computer, so I decided I'd share how I did it on here.

Requirments:
------------

*   Raspberry Pi (I used a Raspberry Pi 1 but any will work)
*   SD Card (the larger the better)
*   External Hard Drive (Optional: for storing user chat files and images)

Step 1: Setting up the Raspberry Pi
-----------------------------------

I used Raspberry Pi OS Lite as the operating system for my Raspberry Pi, if you're using Ubuntu or some other debian based distrobution I'd imagine it wouldn't be too different. I'm using the lite version simply because we don't need a desktop environment and a bunch of the included applications to run the XMPP chat server. I downloaded the the file from the official Raspberry Pi website, [here's](https://www.raspberrypi.org/software/operating-systems/) a link to the page for your convenience.  
  
I used USBImager to flash the image onto the SD card, you can download USBImager from [here](https://gitlab.com/bztsrc/usbimager). After you have finished flashing the downloaded ISO file you can slot the SD card back into the Raspberry Pi

Step 2: Setting up the software
-------------------------------

When you plug the Raspberry Pi in it should automatically load into the login screen, for me this is in the terminal login because I selected the Raspbian lite iso. The default login on Raspbian is:

**Default Username:** pi

**Default Password:** raspberry

### Installing Prosody

I chose to go with the Prosody XMPP server because of how light it is on system resources. Before we install it we should update the system as sometimes the Raspbian images are a bit out of date.

    sudo apt update
  
Now we are going to install Prosody XMPP server

    sudo apt install prosody

### Configuring Prosody

Now that Prosody is installed we need to set some settings in the configuration files. Navigate to:

    /etc/prosody/

In this folder there will be a file called:

    prosody.cfg.lua

At the top of the file there is an admins section, you can put a user into the quotation marks, so I put:

    user@example.com

We will create this user later on in the guide so make sure to remember what you put here. Down towards the bottom of the file you will find the VirtualHost section, enter your domain into this field, in my case I would put:

    example.com

### Adding a new user

Remember how we specified a admin account back in the last section, well now we are going to create that user. This is the username that you will use to login and that other people will use to contact you. Type the command:

    sudo prosodyctl adduser username@yourdomain.com

### Generating Certificates

In order to have end to end encryption we need to use Certbot to generate certificates.  
  
You can install it with:

    sudo apt install certbot
  
You will need to forward some ports to allow certbot to verify your certificates

    Certbot HTTP: Port 80 TCP
  
While you are there the ports for the XMPP server are:

    XMPP: Port 5222, 5322 TCP

Now we are going to run Certbot in standealone mode because we aren't running a webserver already on the RPi, this will tell Certbot to spin up a webserver temporarily to verify.

    sudo certbot --standalone --preferred-challenges http -d example.com
  
Now that we have generated the certificates we can import them into Prosody.

    sudo prosodyctl --root cert import /etc/letsencrypt/live/

### Apply changes

After you are happy with the changes that you have made to the config file, you can restart the service to apply the changes.

    sudo systemctl restart prosody

### Connecting to your XMPP server

Now that everything is setup you can contect to the XMPP server using a client application, just use the username and password you created eariler!

#### XMPP Clients

*   blabber.im (Android)
*   Profanity (Windows, macOS, Linux)
*   Adium (macOS)

  
There are plenty of other clients but these are the ones I've tried.  
  
I hope this guide has been somewhat helpful thanks for reading :)
