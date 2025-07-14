.. image:: docs/img/GhostTracker_logo.png

.. image:: https://github.com/arvidn/libtorrent/actions/workflows/windows.yml/badge.svg
    :target: https://github.com/arvidn/libtorrent/actions/workflows/windows.yml

.. image:: https://github.com/arvidn/libtorrent/actions/workflows/macos.yml/badge.svg
    :target: https://github.com/arvidn/libtorrent/actions/workflows/macos.yml

.. image:: https://github.com/arvidn/libtorrent/actions/workflows/linux.yml/badge.svg
    :target: https://github.com/arvidn/libtorrent/actions/workflows/linux.yml

.. image:: https://github.com/arvidn/libtorrent/actions/workflows/python.yml/badge.svg
    :target: https://github.com/arvidn/libtorrent/actions/workflows/python.yml

.. image:: https://ci.appveyor.com/api/projects/status/w7teauvub5813mew/branch/RC_2_0?svg=true
    :target: https://ci.appveyor.com/project/arvidn/libtorrent/branch/RC_2_0

.. image:: https://api.cirrus-ci.com/github/arvidn/libtorrent.svg?branch=RC_2_0
    :target: https://cirrus-ci.com/github/arvidn/libtorrent

.. image:: https://oss-fuzz-build-logs.storage.googleapis.com/badges/libtorrent.svg
    :target: https://bugs.chromium.org/p/oss-fuzz/issues/list?sort=-opened&q=proj%3Alibtorrent&can=1

.. image:: https://codecov.io/github/arvidn/libtorrent/coverage.svg?branch=RC_2_0
    :target: https://codecov.io/github/arvidn/libtorrent?branch=RC_2_0&view=all#sort=missing&dir=desc

.. image:: https://www.openhub.net/p/rasterbar-libtorrent/widgets/project_thin_badge.gif
    :target: https://www.openhub.net/p/rasterbar-libtorrent

.. image:: https://bestpractices.coreinfrastructure.org/projects/3020/badge
    :target: https://bestpractices.coreinfrastructure.org/en/projects/3020

.. image:: https://img.shields.io/github/stars/arvidn/libtorrent.svg?style=social
    :target: https://github.com/arvidn/libtorrent

libtorrent is an open source C++ library implementing the BitTorrent protocol, along with most popular extensions, making it suitable for real world deployment. It is configurable to be able to fit both servers and embedded devices.

**GHOSTRACKERS CHANGES**

This build introduces the "Ghostrackers" modifications designed to enable stealth mode in BitTorrent communication. These changes ensure no upload or download statistics are reported to trackers and the total torrent size is always reported instead of the actual remaining download size.

**Ghostrackers functionality includes:**

- **No upload reporting:** Always reports uploaded data as `0` to the tracker.
- **No download reporting:** Always reports downloaded data as `0` to the tracker.
- **Total size for 'left':** Always reports the total torrent size for the 'left' field in tracker requests.
- **Event suppression:** Only sends "started" and "stopped" events - never "completed" or "paused" events.
- **Disable DHT and PEX:** DHT and Peer Exchange (PEX) are disabled by default for stealth operation.
- **Tracker interval compliance:** Adheres to the tracker-provided interval for requests.
- **Zero progress reporting:** Always appears as 0% complete regardless of actual download progress.

These modifications are suitable for privacy-oriented deployments and do not affect the library's ability to download torrents.

See `libtorrent.org`__ for more detailed build and usage instructions.

.. __: https://libtorrent.org

### Building Ghostrackers Build and qBittorrent

To build and install libtorrent with the Ghostrackers changes, follow these steps:

.. code-block:: bash

    # Build and Install libtorrent
    clear
    cd /path/to/libtorrent
    git checkout GhostTrackers
    git pull origin GhostTrackers
    rm -rf build install
    mkdir build install
    cd build
    cmake .. -G "MinGW Makefiles" -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/path/to/libtorrent/install
    mingw32-make -j$(nproc)
    mingw32-make install

    # Configure and Build qBittorrent
    cd /path/to/qBittorrent/build
    rm -rf *
    cmake .. -G "MinGW Makefiles" \
      -DCMAKE_BUILD_TYPE=Release \
      -DCMAKE_PREFIX_PATH=/path/to/libtorrent/install \
      -DLibtorrent_DIR=/path/to/libtorrent/install/lib/cmake/LibtorrentRasterbar
    mingw32-make -j$(nproc)

    # Additional Configuration for qBittorrent
    # Download the `dll.zip` file from the qBittorrent repository and extract it into the `build` directory of qBittorrent.
    # Ensure to include the `libtorrent-rasterbar.dll` file located in the libtorrent build directory.

    # Verify and Run
    # find . -iname "qbittorrent.exe"
    # ./src/app/qbittorrent.exe

### Links and Documentation

See `building.html`__ for more details on how to build and configure the Ghostrackers build. For python bindings, see `the python docs`__.

libtorrent `ABI report`_.

.. _`ABI report`: https://abi-laboratory.pro/index.php?view=timeline&l=libtorrent

libtorrent package versions in linux distributions, on repology_.

.. _repology: https://repology.org/project/libtorrent-rasterbar/versions

.. __: docs/building.rst
.. __: docs/python_binding.rst
