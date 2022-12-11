---

Golem in the Box DIY Guide
\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

.. highlight:: sh

.. todo:: paragraph about reasons why: - official version ran out of
stock - bigger ssd - newer not yet released rpi - experimentation

# Requirements

- Some Raspberry Pi 4: either RPi 4B, 8 GiB RAM \[TBC\]; or CM4. You
  can also try other SBC with Cortex-A72 SoC, but those we didn't test
  so YMMV.

- Storage: NVMe SSD 2 TB

  (we don't recommend USB SSD because of low transfer \[TBC\], but you
  have to use them if you use RPi 4B)

- appropriate 5 V power supply

  .. todo:: reference official RPI recommendation about pwr supply,
  iiuc min 2 A

  .. caution::

        It's really important to have a reliable power supply! Some USB phone
        chargers do not in fact supply 5.0 V, but slightly less (for example,
        4.9 V) and while this is enough for charging a smartphone, it's not
        enough for Raspberry Pi. If you encounter kernel logs in dmesg about
        "undervoltage", that's definitely the case. If in doubt, just measure
        the voltage under load. Use a standard multimeter or buy cheap USB power
        meters for about 5 € on AliExpress.

  .. todo:: zdjęcie: USB power meter .. todo:: zdjęcie: IKEA pwr
  supply scribbled

- For CM4:

  - carrier board capable of connecting NVMe/SATA SSD
  - USB-C to USB-A cable

- For rpi4b:

  - SD card (min 32 GB)
  - USB SD card reader
  - external SSD enclosure

- consider enclosure (optional, but recommended)

- last but not least, a computer running reasonably recent GNU/Linux
  distribution, preferably Debian testing or latest Ubuntu.

# Before starting

.. caution::

    - Please observe ESD precautions. You can damage integrated circuits (on
      RPi, CM4, SSD and to a lesser extent the carrier board) just by touching
      their pin or exposed traces by hand. Ground yourself before handling the
      boards (for example by touching uninsulated metal parts on radiator in
      your room if you think it's grounded to your local earth) and handle the
      boards by the edges or big metal parts (like Ethernet port on RPi). If
      you're laying boards on the desk, you can rest them on the plastic bags,
      in which the boards arrived.

    - (For RPi 4B) Shorting +5V pins to any other pin on P5 header while powered
      up WILL damage the board. If you're not planning to power RPi via those
      pins (and you shoudn't if you don't know what you're doing), we recommend
      you put short pieces of insulation from 0,75 mm² cable covering them
      completely to prevent from accidentally touching anything metallic in the
      vincinity.

# Instructions for CM4

.. todo:: HW assembly instructions

1.  Put CM4 in its carrier board.

    .. todo:: image

    .. caution:: Do not repeatedly plug and unplug CM4 module. Mezzanine
    connectors (those long multi-pin connectors) are rated for 30
    cycles.

2.  Connect SSD to the carrier board and secure with a screw. The SSD
    goes into the connector at an angle and then pivots towards the nut,
    which is soldered to the PCB. It should be parallel to PCB when
    screwed.

3.  Download the image available at FIXME. Optionally, you can
    :ref:`compile the    image yourself (see below) <compile>`.

    .. todo:: link

4.  Optionally, install `bmap` flashing tool.

         sudo apt-get install bmap-tools

5.  Download and build `usbboot` tool from Raspberry Pi foundation::

         git clone https://github.com/raspberrypi/usbboot
         cd usbboot
         make

6.  Prepare for flashing the eMMC: hold the BOOT button (or short the
    respective jumper) and connect the USB-C port with a USB-C/A cable.

7.  Execute `./rpiboot`::

         ./rpiboot

    You should see that CM4 was discovered and attached as `/dev/sdX`.
    It should also be linked as something like
    `/dev/disk/by-id/usb-RPi-MSD-_0001_????????-0:0`.

8.  Flash the image to CM4::

         sudo bmaptool copy core-image-full-cmdline-raspberrypi4-64.wic.bmap /dev/disk/by-id/usb-RPi-MSD-_0001_????????-0:0

    Or if you don't have bmaptool::

         sudo dd if=core-image-full-cmdline-raspberrypi4-64.wic.bmap of=/dev/disk/by-id/usb-RPi-MSD-_0001_????????-0:0 bs=4M iflag=fullblock oflag=direct conv=sparse,fsync status=progress

9.  Remove the USB cable and plug the board to power supply. It should
    start Golem in the Box. Now proceed according to official
    instructions.

.. todo::

ssd formatting?

# Instructions for RPi 4B

1.  Assemble RPi 4B and it's components: put the board in the enclosure,
    connect SSD.

2.  Download the image available at FIXME. Optionally, you can
    :ref:`compile the    image yourself (see below) <compile>`.

    .. todo:: link

3.  Put SD card in the reader (and plug the reader to the computer if
    it's an external reader). Flash the image to the SD card (assuming
    the card is called `/dev/mmcblk0`)::

         sudo bmaptool copy core-image-full-cmdline-raspberrypi4-64.wic.bmap /dev/mmcblk0

    Or if you don't have bmaptool::

         sudo dd if=core-image-full-cmdline-raspberrypi4-64.wic.bmap of=/dev/mmcblk0 bs=4M iflag=fullblock oflag=direct conv=sparse,fsync status=progress

4.  When the reader LED stops blinking, take the card from the reader
    and put in RPi. Plug the RPi to power supply and proceed according
    to official instructions.

.. todo::

ssd formatting?

.. \_compile:

# Compiling the image yourself

.. todo:: paragraph If you want to

    - have more trusted image (we didn't include anything wrong there, but you
      might want to check yourself),
    - update sw to newer versions,
    - add other SW not included in official image,
    - customise your image in some other way,
    - run a development version of the image, with ssh daemon and other
      debugging tools,
    - contribute your changes back to our repo,
    - or just tinkering

    However, if you're compiling, you're on your own and we most likely can't
    help you, because most problems encoutered here are related to local
    machine's configuration, and we're not in business of debugging people's
    computers.

1.  Download `kas` and `kas-container`. If you use at least Debian 12
    (testing at the time of writing) or Ubuntu 22.10 (kinetic), you can
    install them from apt::

         apt-get install kas

    Otherwise you need to download the `kas-container` tool from
    https://github.com/siemens/kas. (Older versions of packages don't
    ship `kas-container` script).

2.  Add yourself to `docker` group::

         sudo gpasswd -a username docker

    This is required for kas-container to work.

3.  Clone the
    `official Golem in the Box repository    <https://github.com/golemfactory/golem-in-the-box>`\_\_::

         git clone https://github.com/golemfactory/golem-in-the-box
         cd golem-in-the-box

4.  Build the images::

         kas-container --repo-rw build kas-project-rpi4.yml

    The build process can take several hours, depending on your machine.

    If everything went well, the images are available at
    `build/tmp/deploy/images/raspberrypi4-64/core-image-full-cmdline-raspberrypi4-64.wic`,
    `build/tmp/deploy/images/raspberrypi4-64/core-image-full-cmdline-raspberrypi4-64.wic.bmap`,
    and
    `build/tmp/deploy/images/raspberrypi4-64/core-image-full-cmdline-raspberrypi4-64.wic.bz2`.

# Debugging official board with serial

.. todo:: describe soldering serial, recompilation with debug commit

.. vim: tw=80 ts=4 sts=4 sw=4 et
