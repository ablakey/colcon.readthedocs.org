Quick start
===========

This section gives a high-level overview of how to use the ``colcon`` command.

TL;DR
-----

The following is an example workflow and sequence of commands using default settings:

.. code-block:: bash

    $ mkdir -p /tmp/workspace/src     # Make a workspace directory with a src subdirectory
    $ cd /tmp/workspace               # Change directory to the workspace root
    $ <...>                           # Populate the `src` directory with packages
    $ colcon list -g                  # List all packages in the workspace and their dependencies
    $ colcon build                    # Build all packages in the workspace
    $ catkin test                     # Test all packages in the workspace
    $ catkin test-result --all        # Enumerate all test results
    $ . install/prefix.bash           # Setup the environment to use the built packages
    $ <...>                           # Use the built packages

The most commonly used arguments for the ``build`` and ``test`` verbs are to only process a specific package or a specific package including all the recursive dependencies it needs.

.. code-block:: bash

    $ colcon build --package-whitelist <name-of-pkg>
    $ colcon build --package-up-to <name-of-pkg>

.. note::

    The log files of the latest invocation can be found in the log directory which is by default in ``~/.colcon/log/latest``.

.. note::

    If you want to see the output of each package after it finished you can pass the argument ``--event-handler console_cohesion+``.

Build ROS 2 packages
--------------------

The process of building `ROS 2 <http://www.ros2.org/>`_ packages is described in the `ROS 2 building from source <https://github.com/ros2/ros2/wiki/Installation#building-from-source>`_ instructions.
Using ``colcon`` instead of the recommended tool ``ament_tools`` only changes a couple of the steps.

Instead of invoking ``ament build`` you can invoke ``colcon``.

.. code-block:: bash

    $ colcon build

In order to use the built packages you need to source the ``install/prefix.<ext>`` rather than the ``local_setup.<ext>`` script mentioned in the instructions.

Build ROS 1 packages
--------------------

The process of building `ROS 1 <http://www.ros.org/>`_ packages is described in the `distro specific <http://wiki.ros.org/kinetic/Installation/Source>`_ building from source instructions.
Using ``colcon`` instead of the recommended tool ``catkin_make_isolated`` only changes a couple of the steps.

Instead of invoking ``catkin_make_isolated --install`` you can invoke ``colcon``.

.. code-block:: bash

    $ colcon build

.. note::

    ``colcon`` does by design not support the concept of a "devel space" as it exists in ROS 1.
    Instead it requires each package to be installed so each package must declare an install step in order to work with ``colcon``.

In order to use the built packages you need to source the ``install/prefix.<ext>`` rather than the ``setup.<ext>`` script mentioned in the instructions.

Build Gazebo and the ignition packages
--------------------------------------

In more recent versions `Gazebo <http://www.gazebosim.org/>`_ has been refactored to split out a lot of the functionality into `ignition <https://bitbucket.org/ignitionrobotics/>`_ libraries.
While that makes the project more modular it also increases the effort necessary to build all these packages from source.
``colcon`` can make this process easy again.

In order to build a specific Gazebo version you need the right versions of the ignition libraries.
The following steps use a ``.repos`` to specify the various repositories with specific branches.

.. code-block:: bash

    $ mkdir -p /tmp/gazebo/src && cd /tmp/gazebo
    $ wget https://gist.githubusercontent.com/dirk-thomas/99ece26beb36553e57f02ac903109978/raw/c6e952f9c2b40955d51841eee45d741a3a4962f3/gazebo.repos
    $ vcs import src

.. note::

    The Gist containing the repository list should be replaced with an "official" URL coming from the Gazebo project.

Before building the workspace with ``colcon`` the steps also fetch some additional metadata for Gazebo from a public repository.

.. code-block:: bash

    $ colcon metadata add default https://raw.githubusercontent.com/colcon/colcon-metadata-repository/master/index.yaml
    $ colcon metadata update
    $ colcon build

To run Gazebo which requires environment variables for e.g. the model paths the same commands as for other packages can be used.
Using the additional metadata the source script will also automatically source the Gazebo specific file ``share/gazebo/setup.sh`` which defines these environment variables.

.. code-block:: bash

    $ . install/prefix.bash
    $ gazebo