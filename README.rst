Pylinac
=======

.. image:: https://badges.gitter.im/Join%20Chat.svg
   :alt: Join the chat at https://gitter.im/jrkerns/pylinac
   :target: https://gitter.im/jrkerns/pylinac?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge

.. image:: https://img.shields.io/pypi/v/pylinac.svg
    :target: https://pypi.python.org/pypi/pylinac
    :alt: Latest Version

.. image:: https://anaconda.org/jrkerns/pylinac/badges/installer/conda.svg
    :target: https://anaconda.org/jrkerns/pylinac

.. image:: https://img.shields.io/pypi/l/pylinac.svg
    :target: https://pypi.python.org/pypi/pylinac/
    :alt: MIT

.. image:: https://travis-ci.org/jrkerns/pylinac.svg?branch=master
    :target: https://travis-ci.org/jrkerns/pylinac
    :alt: Travis CI

.. image:: https://coveralls.io/repos/jrkerns/pylinac/badge.svg?branch=master
    :target: https://coveralls.io/r/jrkerns/pylinac
    :alt: Code Coverage

.. image:: https://www.quantifiedcode.com/api/v1/project/25e3cefc2cc84052bbd127c3386adf69/badge.svg
    :target: https://www.quantifiedcode.com/app/project/25e3cefc2cc84052bbd127c3386adf69
    :alt: Code issues

.. image:: https://readthedocs.org/projects/pylinac/badge/?version=stable
    :target: http://pylinac.readthedocs.org/en/stable/?badge=stable
    :alt: Documentation Status


Pylinac provides TG-142 quality assurance (QA) tools to Python programmers as well as non-programmers in the field of 
therapy medical physics. The package comes in two flavors: source-level and `web app <https://assuranceqa.herokuapp.com>`_. The source-level
allows programmers and those familiar with Python to create custom tests with pylinac while the web app is for
those who don't want or don't know how to program.

Pylinac contains high-level modules for automatically analyzing images and data generated by linear accelerators.
Most scripts can be utilized with <10 lines of code.

The library also contains lower-level `hackable modules & tools <http://pylinac.readthedocs.org/en/latest/pylinac_core_hacking.html>`_
for creating your own radiation therapy algorithms.

Below are the high-level tools currently available:

* `Continuous Directory Watching <http://pylinac.readthedocs.org/en/stable/watcher.html>`_ -
   True, one-time "set it and forget it" service that analyzes any file that enters the folder. Files with
   keywords and certain file types are analyzed automatically.
   Only three lines of code: 1) install the base package, 2) install the console requirements, 3) run::

     $ conda install pylinac  # base package
     $ pip install pylinac[console]  # directory watching dependencies
     $ pylinac watch "dir/to/watch"  # start watching!

   And that's it! No code to write; no fuss, no muss.
   Analysis settings are `customizable <http://pylinac.readthedocs.org/en/latest/watcher.html#configuration>`_ and includes optional email service upon analysis.

* `Planar Phantom Analysis (Leeds TOR, PipsPro QC-3) <http://pylinac.readthedocs.org/en/stable/planar_imaging.html>`_ -
   The planar imaging module analyzes 2D phantoms. Currently only the LeedsTOR kV phantom and PipsPro QC-3 MV phantom.

   Features:

   * **Automatic phantom localization** - Set up your phantom any way you like; automatic positioning,
     angle, and inversion correction mean you can set up how you like, nor will setup variations give you headache.
   * **High and low contrast determination** - Analyze both low and high contrast ROIs. Set thresholds
     as you see fit.

   Example script:

   .. code-block:: python

        from pylinac import LeedsTOR, PipsProQC3

        path = 'path/to/leeds.dcm`
        leeds = LeedsTOR(path)
        leeds.analyze()
        leeds.plot_analyzed_image()

        path = 'path/to/pipspro.dcm'
        pp = PipsProQC3(path)
        pp.analyze()
        pp.plot_analyzed_image()

* `Winston-Lutz Analysis <http://pylinac.readthedocs.org/en/stable/winston_lutz.html>`_ -
    The Winston-Lutz module analyzes EPID images taken of a small radiation field and BB to determine the 2D
    distance from BB to field CAX. Additionally, the isocenter size of the gantry, collimator, and couch can
    all be determined *without the BB being at isocenter*. Analysis is based on
    `Winkler et al <http://iopscience.iop.org/article/10.1088/0031-9155/48/9/303/meta;jsessionid=269700F201744D2EAB897C14D1F4E7B3.c2.iopscience.cld.iop.org>`_
    and `Du et al <http://scitation.aip.org/content/aapm/journal/medphys/37/5/10.1118/1.3397452>`_.

    Features:

    * **Automatic field & BB positioning** - When an image or directory is loaded, the field CAX and the BB
      are automatically found, along with the vector and scalar distance between them.
    * **Isocenter size determination** - Using backprojections of the EPID images, the 3D gantry isocenter size
      and position can be determined *independent of the BB position*. Additionally, the 2D planar isocenter size
      of the collimator and couch can also be determined.
    * **Image plotting** - WL images can be plotted separately or together, each of which shows the field CAX, BB and
      scalar distance from BB to CAX.
    * **Gantry sag** - The sag of the gantry is also quantified and can be plotted.

    Example script:

    .. code-block:: python

        from pylinac import WinstonLutz

        path = 'path/to/image/directory`
        wl = WinstonLutz(path)  # images are analyzed upon loading
        wl.plot_summary()
        print(wl.results())

* `Starshot Analysis <http://pylinac.readthedocs.org/en/stable/starshot_docs.html>`_ -
    The Starshot module analyses a starshot image made of radiation spokes, whether gantry, collimator, MLC or couch.
    It is based on ideas from `Depuydt et al <http://iopscience.iop.org/0031-9155/57/10/2997>`_
    and `Gonzalez et al <http://dx.doi.org/10.1118/1.1755491>`_.

    Features:

    * **Analyze scanned film images, single EPID images, or a set of EPID images** -
      Any image that you can load in can be analyzed, including 1 or a set of EPID DICOM images and
      films that have been digitally scanned.
    * **Any image size** - Have machines with different EPIDs? Scanned your film at different resolutions? No problem.
    * **Dose/OD can be inverted** - Whether your device/image views dose as an increase in value or a decrease, pylinac
      will detect it and invert if necessary.
    * **Automatic noise detection & correction** - Sometimes there's dirt on the scanned film; sometimes there's a dead pixel on the EPID.
      Pylinac will detect these spurious noise signals and can avoid or account for them.
    * **Accurate, FWHM star line detection** - Pylinac uses not simply the maximum value to find the center of a star line,
      but analyzes the entire star profile to determine the center of the FWHM, ensuring small noise or maximum value bias is avoided.
    * **Adaptive searching** - If you passed pylinac a set of parameters and a good result wasn't found, pylinac can recover and
      do an adaptive search by adjusting parameters to find a "reasonable" wobble.

    Example script:

    .. code-block:: python

        from pylinac import Starshot

        star = Starshot("mystarshot.tif")
        star.analyze(radius=60, tolerance=0.75)
        print(star.return_results())  # prints out wobble information
        star.plot_analyzed_image()  # shows a matplotlib figure

* `VMAT QA <http://pylinac.readthedocs.org/en/stable/vmat_docs.html>`_ -
    The VMAT module consists of the class VMAT, which is capable of loading an EPID DICOM Open field image and MLC field image and analyzing the
    images according to the Varian RapidArc QA tests and procedures, specifically the Dose-Rate & Gantry-Speed (DRGS) and MLC speed (MLCS) tests.

    Features:

    * **Do both tests** - Pylinac can handle either DRGS or DRMLC tests.
    * **Adjust for offsets** - Older VMAT patterns were off-center. Easily account for the offset by passing it in.
    * **Automatic identification using file names** - If your file names are clear, the image type and test type don't even
      have to be specified; just load and analyze.

    Example script:

    .. code-block:: python

        from pylinac import VMAT

        vmat = VMAT.from_zip("myvmatimages.zip")
        vmat.analyze(test='mlcs', tolerance=1.5)
        print(vmat.return_results())  # prints out ROI information
        vmat.plot_analyzed_image()  # shows a matplotlib figure

* `CT & CBCT QA <http://pylinac.readthedocs.org/en/stable/cbct_docs.html>`_ -
    The CBCT module automatically analyzes DICOM images of a CatPhan 504 or 503 acquired when doing CBCT or regular CT quality assurance. It can load a folder or zip file that
    the images are in and automatically correct for phantom setup in 6 degrees.
    It can analyze the HU regions and image scaling (CTP404), the high-contrast line pairs (CTP528) to calculate the modulation transfer function (MTF), and the HU
    uniformity (CTP486) on the corresponding slice.

    Features:

    * **Automatic phantom registration** - Your phantom can be tilted, rotated, or translated--pylinac will register the phantom.
    * **Automatic testing of 4 major modules** - Major modules are automatically registered and analyzed.
    * **Any scan protocol** - Scan your CatPhan with any protocol; or even scan it in a regular CT scanner.
      Any field size or field extent is allowed.

    Example script:

    .. code-block:: python

        from pylinac import CBCT

        cbct = CBCT("my/cbct_image_folder")
        cbct.analyze(hu_tolerance=40)
        print(cbct.return_results())
        cbct.plot_analyzed_image()

* `Log Analysis <http://pylinac.readthedocs.org/en/stable/log_analyzer.html>`_ -
    The log analyzer module reads and parses Varian linear accelerator machine logs, both Dynalogs and Trajectory logs. The module also
    calculates actual and expected fluences as well as performing gamma evaluations. Data is structured to be easily accessible and
    easily plottable.

    Unlike most other modules of pylinac, the log analyzer module has no end goal. Data is parsed from the logs, but what is done with that
    info, and which info is analyzed is up to the user.

    Features:

    * **Analyze Dynalogs or Trajectory logs** - Either platform is supported. Tlog versions 2.1 and 3.0 supported.
    * **Save Trajectory log data to CSV** - The Trajectory log binary data format does not allow for easy export of data. Pylinac lets you do
      that so you can use Excel or other software that you use with Dynalogs.
    * **Plot or analyze any axis** - Every data axis can be plotted: the actual, expected, and even the difference.
    * **View actual or expected fluences & calculate gamma** - View fluences and gamma maps for any log.
    * **Anonymization** - Anonymize your logs so you can share them with others.

    Example script:

    .. code-block:: python

        from pylinac import MachineLog

        log = MachineLog("tlog.bin")
        # after loading, explore any Axis of the Varian structure
        log.axis_data.gantry.plot_actual()  # plot the gantry position throughout treatment
        log.fluence.gamma.calc_map(doseTA=1, distTA=1, threshold=10, resolution=0.1)
        log.fluence.gamma.plot_map()  # show the gamma map as a matplotlib figure

* `Picket Fence MLC Analysis <http://pylinac.readthedocs.org/en/stable/picketfence.html>`_ -
    The picket fence module is meant for analyzing EPID images where a "picket fence" MLC pattern has been made.
    Physicists regularly check MLC positioning through this test. This test can be done using film and one can
    "eyeball" it, but this is the 21st century and we have numerous ways of quantifying such data. This module
    attains to be one of them. It will load in an EPID dicom image and determine the MLC peaks, error of each MLC
    pair to the picket, and give a few visual indicators for passing/warning/failing.

    Features:

    * **Analyze either HD or regular MLCs** - Just pass a flag and tell pylinac whether it's HD or not.
    * **Easy-to-read pass/warn/fail overlay** - Analysis gives you easy-to-read tools for determining the status of an MLC pair.
    * **Any Source-to-Image distance** - Whatever your clinic uses as the SID for picket fence, pylinac can account for it.
    * **Account for panel translation** - Have an off-CAX setup? No problem. Translate your EPID and pylinac knows.
    * **Account for panel sag** - If your EPID sags at certain angles, just tell pylinac and the results will be shifted.

    Example script:

    .. code-block:: python

        from pylinac import PicketFence

        pf = PicketFence("mypf.dcm")
        pf.analyze(tolerance=0.5, action_tolerance=0.25)
        print(pf.return_results())
        pf.plot_analyzed_image()

* `Flatness/Symmetry Analysis <http://pylinac.readthedocs.org/en/stable/flatsym.html>`_ -
    Analysis of Flatness & Symmetry of film or EPID images. Multiple equation definitions, in/cross plane.

Documentation
-------------
To get started, install the package, run the demos, view the API docs, and learn the module design, visit the
`Full Documentation <http://pylinac.readthedocs.org/>`_ on Read The Docs.

Discussion
----------
Have questions? Ask them here on the `pylinac forum <https://groups.google.com/forum/#!forum/pylinac>`_.

Contributing
------------

Contributions to pylinac can be many. The most useful things a non-programmer can contribute are images to analyze and bug reports. If
you have VMAT images, starshot images, machine log files, CBCT DICOM files, or anything else you want analyzed, email or share them via Dropbox, Google Drive, etc: jkerns at gmail.com



