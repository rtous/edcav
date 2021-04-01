# Setup your computer for the DRCAV Part 2 labs/project

There's no compulsory development environment for doing the DRCAV Part 2 labs/project. In fact, everything can be done on any platform (Windows, macOS, Linux). However, **it's recommended working over a Linux-based development environment**, as most professional developers do (because most real server-side software run on Linux-based severs).

Following we provide some recommendations to setup a Linux-based development environment on the different platforms. 

*NOTE: For students wanting to try a more advanced setup, at the end we talk about Docker containers (only recommended if you have a minimal experience developing server-side applications).*

## If you already have a Linux platform

The documentation is designed for Ubuntu, you will just need to adapt some installation instructions. 

## If you have macOS

As macOS is Unix-based, you can directly work there. You will just need to adapt some installation instructions.

## If you have Windows 10 (Home or any other edition)

a. [WSL2](./wsl.md) may be a very practical way (recommended).

b. A [virtual machine](./virtualmachine.md) may be not so practical but it will provide a more similar environment. 

## (OPTIONAL) Docker

Independently of your operating system, you may try using Docker containers for some tasks. Docker containers are lightweight isolated virtual Linux-based systems. The main goal of containers is to facilitate application deployment, but they can be very useful for application development too. On the one hand, as containers are lighter than virtual machines, they become a practical alternative to play with a disposable Linux system. On the other hand, as there're repositories of pre-cooked containers for almost anything, they become a practical way to run some applications without the need to install them (e.g. a MongoDB database, a Deep Learning classifier, etc.). For those wanting to try we provide some help [here](./docker.md).



