# Welcome to the 'observium installation script' repository!

This repository contains an installation script for the Observium network monitoring tool, compatible with versions 7, 8, and 9 of CentOS.

Installing Observium can sometimes be a tedious and time-consuming process, but this script aims to simplify the process by automating the most common steps.

How to use the script
To use the script, simply clone this repository on your CentOS machine using the command git clone, and then execute the script as the root user. Make sure you understand the implications of the installation before running the script.

Here's how to use the script in a few simple steps:

1) Open a terminal on your CentOS machine.

2) install git on centos

```sh
  yum install git
```
  
3) Download the repository using the command git clone.

 ```sh
  git clone https://github.com/bryanmauv/script-installation-observium.git
 ```

4) Change directory to the repository using the command cd.

```sh
  cd script-installation-observium
```

5) Change the permissions of the installation script using the command chmod.

```sh
  chmod +x installation-observium-centos-789.sh
```

6) Execute the installation script using the command sudo.

```sh
  sudo ./installation-observium-centos-789.sh
```

7) Follow the instructions of the installation script to complete the installation of Observium.

If you encounter any issues during installation, feel free to open an issue on this repository. I will do my best to help you resolve any problems you encounter.

We hope you find this script helpful in simplifying the installation process of Observium on your CentOS machine!
