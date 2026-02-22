## News 22-Nov-2024

We have upgraded modules Moment Tensor Inversion and Array Analysis. Now they are perfectly connected with Earthquake analysis and are much more flexible and intuitive.


## News 1-Nov-2024

- We have renew the [<span style="color:#8E44AD">Synthetic Toolbox</span>](https://projectisp.github.io/ISP_tutorial.github.io/synth/).

![Screenshot](img/Synthetic_Visulization.png)

- We have included an option to run your own Python script from a seismogram processed with ISP. You can see how to use this option at [<span style="color:#8E44AD">Earthquake Seismology - Run Script </span>](https://projectisp.github.io/ISP_tutorial.github.io/el/).

- Now you can read ObsPy catalogs using ISP [<span style="color:#8E44AD">Earthquake Seismology - Search in Catalog </span>](https://projectisp.github.io/ISP_tutorial.github.io/el/).

The ISP team has recently developed:

- <span style="color:red">Earthquake Analysis module Upgraded</span>

In this upgrade you can create your project from your individual files or from your sds structure. We have also included a fast way to search your waveforms from a catalog (./isp/examples/catalog/). 

Now it is created a connection Earthquake Analysis to TF Analysis to allow the user a rapid deep signal processing investigation.

Moreover, it is included a very nice way of sort tyour waveforms by distance and showing the theoretical Travel-Times.

![Screenshot](img/plot_record_section.png)

![Screenshot](img/TFconnection.png)

![Screenshot](img/catalog.png)


- <span style="color:red">Seismic Ambient Noise Tomography</span> 

For now, It is available the Empirical Green Functions retrieval of vertical and Horizontal components, synchronize clocks using EGFs ,a tool to measure Group and Phase velocity from dispersion curves and create dispersion maps.

An example with the results of Cabieces et al., 2022 "Upper lithospheric structure of northeastern Venezuela from joint inversion of surface wave dispersion and receiver functions" can be found in ./isp/ant

![Screenshot](img/dispersion_maps.png)

We have implemented the conexion between Erthquake Location and [<span style="color:red">**Time-Frequency Anlysis**</span>](https://projectisp.github.io/ISP_tutorial.github.io/tf/#connect-with-earthquake-analysis)