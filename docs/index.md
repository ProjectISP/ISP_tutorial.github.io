# Welcome to Integrated Seismic Program (ISP) Documentation

![Screenshot](img/logo.png)


Integrated Seismic Program is an amenable toolbox that joins functionality and standard algorithms commonly used in seismology. The complete software is separated in several modules and each module is designed to overcome a specific task.

Cabieces et al. [**Integrated Seismic Program (ISP)**](https://pubs.geoscienceworld.org/ssa/srl/article-abstract/93/3/1895/612376/Integrated-Seismic-Program-ISP-A-New-Python-GUI?redirectedFrom=fulltext): A New Python GUI‐Based Software for Earthquake Seismology and Seismic Signal Processing. Seismological Research Letters 2022;; 93 (3): 1895–1908. doi: https://doi.org/10.1785/0220210205


<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Links Grid</title>
    <style>
        .links-container {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 15px;
            max-width: 800px;
            margin: auto;
            background: white;
            padding: 20px;
            border-radius: 8px;
        }
        .link-item {
            text-decoration: none;
            display: flex;
            align-items: center;
            background: #f8f8f8;
            padding: 10px;
            border-radius: 8px;
        }
        .link-item img {
            margin-right: 10px;
        }
        .link-item span {
            font-size: 16px;
            color: black;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <div class="links-container">
        <a href="https://github.com/ProjectISP/ISP/tree/master" target="_blank" class="link-item">
            <img src="img/github.png" width="40">
            <span>See ISP open-source code</span>
        </a>
        <a href="https://twitter.com/ProgramSeismic" target="_blank" class="link-item">
            <img src="img/twitter.png" width="40">
            <span>Follow us on Twitter</span>
        </a>
        <a href="https://www.youtube.com/@robertocabiecesdiaz3775/videos" target="_blank" class="link-item">
            <img src="img/youtube.png" width="40">
            <span>Subscribe on YouTube</span>
        </a>
        <a href="https://github.com/ProjectISP/ISP/issues" target="_blank" class="link-item">
            <img src="img/questions.png" width="60">
            <span>Questions and Issues</span>
        </a>
        <a href="mailto:isp@roa.es?subject=Subscription Request&body=Hello, I would like to subscribe to ISP news and future workshops" class="link-item">
            <img src="img/mail.png" width="60">
            <span>Send us an email to subscribe to news and future workshops</span>
        </a>
    </div>
</body>
</html>

## New Signal Processing Tutorial
We have included a new tutorial with nice theory explanations and analysis examples in "Signal Processing"!. You can also run an [**automatic process**](https://projectisp.github.io/surfquaketutorial.github.io/processing_overview/) using.

## surfQuake

[**surfQuake**](https://projectisp.github.io/surfquaketutorial.github.io/) core has been totally joined to ISP, now either the library and the command line interface are installed all together! This new software is designed to streamline the workflow of estimating seismic source parameters:

- [<span style="color:black">*surfQuake paper*</span>](https://pubs.geoscienceworld.org/ssa/srl/article-abstract/doi/10.1785/0220240186/654285/surfQuake-A-New-Python-Toolbox-for-the-Workflow?redirectedFrom=fulltext)

---

```bash
> conda activate isp

# show all comands
> (isp) surfquake -h

# example
> (isp) surfquake project -h
```

---

[![Screenshot](img/logo_surf.png)](https://projectisp.github.io/surfquaketutorial.github.io/)

## Software Structure
### Index: Modules

- [<span style="color:#8E44AD">Earthquake Seismology </span>](https://projectisp.github.io/ISP_tutorial.github.io/el/)

- [<span style="color:#8E44AD">Time-Frequency Analysis</span>](https://projectisp.github.io/ISP_tutorial.github.io/tf/)

- [<span style="color:#8E44AD">Focal Mechanism  / Moment Tensor Inversion</span>](https://projectisp.github.io/ISP_tutorial.github.io/mti/)

- [<span style="color:#8E44AD">Array Seismology </span>](https://projectisp.github.io/ISP_tutorial.github.io/aa/)

- [<span style="color:#8E44AD">Receiver Functions </span>](https://projectisp.github.io/ISP_tutorial.github.io/rf/)

- [<span style="color:#8E44AD">Ambient Noise Tomography </span>](https://projectisp.github.io/ISP_tutorial.github.io/ant/)

### Index: ToolBoxes
- [<span style="color:#8E44AD">Database </span>](https://projectisp.github.io/ISP_tutorial.github.io/db/)

- [<span style="color:#8E44AD">Retrieve Data (FDSN) </span>](https://projectisp.github.io/ISP_tutorial.github.io/rd/)

- [<span style="color:#8E44AD">Probability Power Spectral Density Function (PPSD) </span>](https://projectisp.github.io/ISP_tutorial.github.io/ppsds/)

- [<span style="color:#8E44AD">Synthetic Generator </span>](https://projectisp.github.io/ISP_tutorial.github.io/synth/)

- [<span style="color:#8E44AD">Near-Real Time Adquisition </span>](https://projectisp.github.io/ISP_tutorial.github.io/nrt/)


## Multimedia Tutorials

- [<span style="color:darkgreen">Managing your Project</span>](https://projectisp.github.io/ISP_tutorial.github.io/basics/#project-video-tutorial)

- [<span style="color:darkgreen">Seismogram Analysis</span>](https://projectisp.github.io/ISP_tutorial.github.io/el/#multimedia-material)

- [<span style="color:darkgreen">Explore and Search in your Catalog</span>](https://projectisp.github.io/ISP_tutorial.github.io/el/#multimedia-material)

- [<span style="color:darkgreen">Manual Pick, Locate Event, Fault Plane and Magnitude</span>](https://projectisp.github.io/ISP_tutorial.github.io/el/#multimedia-material)

- [<span style="color:darkgreen">Automatic Picking and Associate</span>](https://projectisp.github.io/ISP_tutorial.github.io/el/#multimedia-material)

- [<span style="color:darkgreen">Time-Frequency Analysis</span>](https://projectisp.github.io/ISP_tutorial.github.io/tf/#multimedia-material-time-frequency-analysis)
- [<span style="color:darkgreen">Array Seismology </span>](https://projectisp.github.io/ISP_tutorial.github.io/aa/#multimedia-material-array-analysis)

- [<span style="color:darkgreen">Moment Tensor Inversion</span>](https://projectisp.github.io/ISP_tutorial.github.io/mti/#multimedia-material-mti)


## Data examples repository

[<span style="color:#5DADE2;">**Case of study**</span>](https://github.com/rcabdia/test_surfquake), contains a full example of using surfQuake with core Library Python scripts, Core Library bash script and expected results. Of course you can try to run the example using the GUI.

We have also created a repository with an example of [<span style="color:#5DADE2;">**3D model**</span>](https://github.com/rcabdia/ProjectISP_3Dmodels)








