.. _getting_started:

Getting Started
===============

Calibrate VCOM
--------------

EPaper displays use electrical fields to drive colored particles.
One of the required voltages, VCOM (Common Voltage) is display-dependent
and must be calibrated for each display individually.

Fortunately, the VCOM voltage is usually printed on the display, similar to this:

.. image:: img/vcom.jpg

The VCOM value is usually between -1V and -3V. 

To tune the controller output voltage, use the trimmer marked :code:`RV1`.
You can measure the VCOM on the VCOM test pad (if your board has one) or directly
at the amplifier:

.. image:: img/vcom_opamp.jpg

Flashing the demo
-----------------

First, connect you board with the computer. In the output of :code:`lsusb` you should find something like:
::

    Bus 001 Device 048: ID 1a86:7523 QinHeng Electronics HL-340 USB-Serial adapter

This means the serial adapter is working and there a serial like :code:`/dev/ttyUSB0` should appear.

Next, make sure you have the `Espressif IoT Development Framework <https://github.com/espressif/esp-idf>`_ installed. 
The current stable (and tested) version is 4.0.
For instructions on how to get started with the IDF, please refer to their `documentation <https://docs.espressif.com/projects/esp-idf/en/stable/get-started/>`_.

Then, clone the :code:`epdiy` git repository (and its submodules):
::

    git clone --recursive https://github.com/vroland/epdiy

Now, (after activating the IDF environment) you should be able to build the demo:
::

    cd examples/demo/
    idf.py build

Hold down the :code:`BOOT` button on your board, while quickly pressing the :code:`RESET` button. 
The ESP module is now in boot mode. 
Upload the demo program to the board with
::

    idf.py build && idf.py flash -b 921600 && idf.py monitor

Pressing :code:`RESET` a second time should start the demo program, which will
output some information on the serial monitor.

With the **board power turned off**, connect your display. 
Power on the board.
You should now see the demo output on your display.

Use with esp-idf
----------------

The neccessary functionality for driving an EPD display is encapsulated in the :code:`components/epd_driver` IDF component.
To use it in you own project, simply copy the :code:`epd_driver` folder to your project-local :code:`components` directory.
The component sould be automatically detected by the framework, you can now use
::

    #include "epd_driver.h"

to use the EPD driver's :ref:`pub_api`.

Selecting a Display Type
~~~~~~~~~~~~~~~~~~~~~~~~

To select the display type you want to use for the project (see :ref:`display_types`), run
::

    idf.py menuconfig

And navigate to :code:`Component config -> E-Paper driver -> Display Type`, select the appropriate option and save the configuration. You can use the defines
::

    CONFIG_EPD_DISPLAY_TYPE_ED097OC4
    CONFIG_EPD_DISPLAY_TYPE_ED060SC4
    CONFIG_EPD_DISPLAY_TYPE_ED097TC2

to make your code portable.

Use with Arduino
----------------

Using the library from arduino framework is currently not recommended,
as it uses a pre-compiled ESP-IDF, which only allows slower SPI RAM speeds and is not 
sufficiently configurable. 

However, it is possible to use the `Arduino APIs as an IDF component <https://github.com/espressif/arduino-esp32/blob/master/docs/esp-idf_component.md>`_,
which allows you to use the Arduino ecosystem (Except for a different build process).

Generating Font Files
---------------------

Fonts are provided in a special header format (inspired by the Adafruit GFX library), which need to be generated from TTF fonts.
For this purpose, the :code:`scripts/fontconvert.py` utility is provided.
The following example generates a header file for Fira Code at size 10, where glyphs that are not found in Fira Code will be taken from Symbola:
::
    ./fontconvert.py FiraCode 10 /usr/share/fonts/TTF/FiraCode-Regular.ttf /usr/share/fonts/TTF/Symbola.ttf > ../examples/terminal/main/firacode.h

You can change which unicode character codes are to be exported by modifying :code:`intervals` in :code:`fontconvert.py`.
