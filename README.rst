Matrix PreMiD
=============

A Python script that sets your Matrix presence and status based on native OS-level media sessions (MPRIS). It monitors what you are listening to or watching via D-Bus and instantly pushes it to your Matrix account.

Requirements
------------

* Linux OS with D-Bus and MPRIS support
* ``playerctl`` installed (e.g. ``sudo apt install playerctl`` or ``sudo pacman -S playerctl``)
* Python 3.7+
* A Matrix account and homeserver

Global Installation (Systemd Service)
-------------------------------------

If you want to run this constantly in the background as a Linux service, independent of your cloned repository folder:

1. Clone the repository:

   .. code-block:: bash

      git clone https://github.com/user/matrix-premid
      cd matrix-premid

2. Install the script, systemd user service, and dependencies globally to ``/opt``:

   .. code-block:: bash

      make install

   This creates the directory ``/opt/matrix-premid``, sets up an isolated Python virtual environment exclusively for the service, symlinks the script to ``/usr/local/bin/matrix-premid``, and automatically initializes a systemd user service and config template for your user account.

3. Edit your configuration file at ``~/.config/matrix-premid/config.json`` to add your homeserver and username.

4. Store your Matrix access token in the system keyring:

   .. code-block:: bash

      python -m keyring set matrix-premid @username:domain.com

User Installation
-----------------

Alternatively, you can install the package to your user site-packages:

.. code-block:: bash

   pip install --user .

This will install the ``matrix-premid`` command to your ``~/.local/bin`` (make sure ``~/.local/bin`` is on your ``PATH``).

Basic Usage
-----------

1. **Install dependencies**: ``pip install .`` (or use installation methods above).
2. **Setup configuration**: Create your configuration file at ``~/.config/matrix-premid/config.json``. You can use ``matrix-premid install-service`` to generate a default template, or copy ``config.template.json`` from this repository to that location.
3. **Store credentials**: For security, store your Matrix access token in your system keyring rather than putting it in plain text inside the config file:

   .. code-block:: bash

      python -m keyring set matrix-premid @username:domain.com

   *(Alternatively, for non-interactive setups like CI, you can set the ``access_token`` directly in the ``accounts`` section of ``config.json``).*

4. **Run the script**: ``matrix-premid``

Command-line Options
--------------------

* ``--unset`` or ``--clear``: Manually set Matrix presence to ``offline``, clear the status message/account data, and exit.
* ``--debug``: Enable verbose debug logging.
* ``--help``: Show all available options.

Shell Completion
----------------

This script supports bash/zsh completion via ``argcomplete``. To enable it:

1. Install ``argcomplete`` (included in requirements).
2. Register the script:

   .. code-block:: bash

      eval "$(register-python-argcomplete matrix-premid)"

   (Add this to your ``.bashrc`` or ``.zshrc`` for persistence).

Development / Local Running
---------------------------

If you want to run the script locally from the folder (for testing or development) without installing it system-wide:

1. Clone the repository and configure your configuration:

   .. code-block:: bash

      git clone https://github.com/user/matrix-premid
      cd matrix-premid
      mkdir -p ~/.config/matrix-premid
      cp config.template.json ~/.config/matrix-premid/config.json

   Edit the ``~/.config/matrix-premid/config.json`` file to set your Matrix username and homeserver.

2. Store your Matrix access token in the system keyring:

   .. code-block:: bash

      python -m keyring set matrix-premid @your_username:homeserver.com

3. Install development dependencies:

   .. code-block:: bash

      make deps

4. Run the script directly:

   .. code-block:: bash

      make run

The script will listen to Linux MPRIS events natively. As long as the script is running, when you play media in a browser or application (like Spotify, VLC, Firefox), your standard Matrix presence and your custom Element status will be instantly updated. When media is stopped or paused, the status will return to Idle and clear the custom text.

Code Quality Tools
------------------

You can format the code using Black:

.. code-block:: bash

   make format

You can lint the code using Flake8:

.. code-block:: bash

   make lint

To clean up the virtual environment and cache files:

.. code-block:: bash

   make clean
