# waywiimote-mouse-driver (modification of xwiimote-mouse-driver https://github.com/MrApplejuice/xwiimote-mouse-driver)

This project implements a userspace mouse driver for Linux (root required).
The general design is to interface with the kernel wiimote driver using
the library [xwiimote](https://github.com/xwiimote/xwiimote). It then does
matches wiimote motion to screen coordinates and forwards the resulting events
to [libevdev](https://www.freedesktop.org/wiki/Software/libevdev/) to
create and control a virtual mouse.

Special thanks to [Suricrasia Online](https://suricrasia.online/) for providing 
this excellent blog post on how to get started with libevdev virtual mice:

- https://suricrasia.online/blog/turning-a-keyboard-into/

# Documentation

The documentation can be found at https://mrapplejuice.github.io/xwiimote-mouse-driver/#

# How To Compile:
    1. git clone https://github.com/gamrXerus/waywiimote-mouse-driver.git
    2. Navigate into the driver's directory:
       Bash
       cd waywiimote-mouse-driver
    3. Prepare the build process:
       Bash
       cmake .
    4. Fix the Driver's Build Bugs. This specific driver has two known bugs in its build process that we must work around.
    5. First, build its networking dependency separately to avoid a race condition:
       Bash
       make sockpp-install
    6. Next, fix the lib vs lib64 path bug. The driver looks for a file in a lib folder, but it was created in a lib64 folder. Create a link to fix this broken path:
       Bash
       mkdir -p sockpp-install/lib
       ln -s ../lib64/libsockpp.a sockpp-install/lib/libsockpp.a
    7. Finally, build the main program:
       Bash
       make

# License

[Full license text](LICENSE.md)

waywiimote-mouse-driver is a user-space mouse driver that allows using a wiimote
as a mouse on a desktop computer.

Copyright (C) 2024  Paul Konstantin Gerke

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <https://www.gnu.org/licenses/>.

# Build requirements

- libevdev (version 1.12 or higher) development files 

    - See https://www.freedesktop.org/wiki/Software/libevdev/
    - License: MIT

- libxwiimote development files

    - See https://github.com/xwiimote/xwiimote
    - License: Custom XWiimote License  

- cmake
- pkgconfig

# More handy documents about libevdev and the wiimote interfaces

- debug logs: https://who-t.blogspot.com/2016/09/understanding-evdev.html
- kernel events: https://www.kernel.org/doc/html/v4.17/input/event-codes.html
- multitouch: https://www.kernel.org/doc/html/latest/input/multi-touch-protocol.html
- python demo project: https://github.com/alex-s-v/10moons-driver
- Someone wrote an audio driver in C#: https://github.com/trigger-segfault/WiimoteLib.Net/tree/master
