..  Copyright (c) 2014-present PlatformIO <contact@platformio.org>
    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at
       http://www.apache.org/licenses/LICENSE-2.0
    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.

.. _scripting_envs:

Construction Environments
-------------------------

The PlatformIO Build System uses the following construction environments
to process a project:

*  ``Import("env")``, ``DefaultEnvironment()`` - the global
   construction environment.

   Is used for the :ref:`platforms` and :ref:`frameworks` build scripts,
   upload tools, :ref:`ldf`, and for other internal operations.

   .. note::
     If you use :ref:`library_json` and :ref:`library_json_buid_extra_script`,
     the ``Import("env")`` environment refers to the library's isolated
     environment from which a script was called (not to the global environemnt).
     Please use ``env = DefaultEnvironment()`` instead to access the global
     environemnt.

*  ``Import("projenv")`` - the isolated construction environment
   used for processing the project source code in :ref:`projectconf_pio_src_dir`.

   Please note that any :ref:`projectconf_build_src_flags` specified in
   :ref:`projectconf` will be passed to the ``projenv`` and not to the ``env``.

.. warning::
  1. ``projenv`` is available only for the POST-type scripts (see :ref:`scripting_launch_types`)
  2. Flags passed to the ``env`` using PRE-type script will affect the ``projenv`` too.

``my_pre_extra_script.py``:

.. code-block:: python

    Import("env")

    # access to global construction environment
    print(env)

    # Dump construction environment (for debug purpose)
    print(env.Dump())

    # append extra flags to global build environment
    # which later will be used to build:
    # - project source code
    # - frameworks
    # - dependent libraries
    env.Append(CPPDEFINES=[
      "MACRO_1_NAME",
      ("MACRO_2_NAME", "MACRO_2_VALUE")
    ])


``my_post_extra_script.py``:

.. code-block:: python

    Import("env", "projenv")

    # access to global construction environment
    print(env)

    # access to project construction environment
    print(projenv)

    # Dump construction environments (for debug purpose)
    print(env.Dump())
    print(projenv.Dump())

    # append extra flags to global build environment
    # which later will be used to build:
    # - frameworks
    # - dependent libraries
    env.Append(CPPDEFINES=[
      "MACRO_1_NAME",
      ("MACRO_2_NAME", "MACRO_2_VALUE")
    ])

    # append extra flags to only project build environment
    projenv.Append(CPPDEFINES=[
      "PROJECT_EXTRA_MACRO_1_NAME",
      ("ROJECT_EXTRA_MACRO_2_NAME", "ROJECT_EXTRA_MACRO_2_VALUE")
    ])
