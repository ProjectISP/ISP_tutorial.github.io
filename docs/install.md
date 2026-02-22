# Installation

First of all, clone the repository from [ISP master branch](https://github.com/ProjectISP/ISP). Just open your terminal and navigate whenever you want to place **ISP** and type in the terminal

	git clone --depth 1 --branch master --single-branch https://github.com/ProjectISP/ISP.git

To be updated, from ./ISP type in your terminal, 

	git pull --depth 1

The installation file will install the requirements and will compile the source packages (advanced). At the end of the installation the user will be asked for an alias "isp". Following the directions **Installation for end users** , it is needed to have previously installed [anaconda](https://www.anaconda.com/).

Additionally, [<span style="color:#5DADE2">*Rosetta*</span>](https://support.apple.com/en-us/102527#:~:text=Rosetta%20is%20not%20an%20app,it%20like%20you%20normally%20would.) allows Mac machines with Apple chips to run applications meant for Mac systems with Intel processors. If this is your case, please install Rosseta AFTER the ISP installation, if you need either Locate Events, First Polarity Focal Mechanism or to use MTI module.


## Installation for end users (Mac and Linux)

To run the installation go to ./ISP/install and type:
	
	chmod u+x ISP_installer.sh

	./ISP_installer.sh

You will be ask for the type of installation, in this case your answer must be, conventional

	Which type of installation would you prefer, conventional or advanced ? conventional

Now the installation process is finished. Open a terminal and type:

	>> isp

Just in case the alias isp do not open the software (because the alias has not been written in your shell), go to ./ISP and type:

	conda activate isp or source activate isp
	python start_isp.py

WARNING! For Linux users it is recommendable to install futher dependencies:

	sudo apt-get update
	sudo apt-get install -y build-essential
	sudo apt-get install -y pkg-config
	sudo apt-get install xdg-utils
	sudo apt install --reinstall libxcb-xinerama0
	sudo apt install --reinstall libxcb1 libx11-xcb1 libxext6 libxkbcommon-x11-0


## Installation for developers (Mac and Linux)


Before run this type of installation be sure you have installed and in your PATH system variables [cmake](https://cmake.org/install/) & [Intel Fortran Stand Alone version](https://www.intel.com/content/www/us/en/developer/tools/oneapi/fortran-compiler.html#gs.u6wzcf) with useful directions for [Ubuntu users](https://gist.github.com/SomajitDey/aeb6eb4c8083185e06800e1ece4be1bd)
To run the installation go to ./ISP/install and type:
	
	chmod u+x ISP_installer.sh

	./ISP_installer.sh

You will be ask for the type of installation, in this case your answer must be, advanced

	Which type of installation would you prefer, conventional or advanced ? advanced

Just in case the alias isp do not open the software, go to ./ISP and type:

	conda activate isp or source activate isp
	python start_isp.py

## Installation for Windows 10/11 users

In the following text, we briefly explain how to install ISP in windows 10/11 via **WSL**. Please, <a href="https://raw.githubusercontent.com/ProjectISP/ISP/master/install/win_directions/Running_WSL_ISP.pdf" download> download the directions here</a>
## Uninstall ISP

First remove the folder ISP, then type in your terminal:

	conda remove --name isp --all

Good luck with the process, and report any issue to the [ISP github repository](https://github.com/ProjectISP/ISP/tree/ISP_2.0_development)
