===============
zsft.recipe.cmd
===============

.. contents::

This recipe allows you to run arbitrary shell and python scripts from buildout.
It's inspired by similar recipes but has few added features.

Repository: https://github.com/zartsoft/zsft.recipe.cmd

To clone:

    `git clone https://github.com/zartsoft/zsft.recipe.cmd`

Issue tracker: https://github.com/zartsoft/zsft.recipe.cmd/issues

Supported Python versions: 2.7, 3.3+

Supported zc.buildout versions: 1.x, 2.x.


Usage
=====

``install``
    Commands to execute on install phase.

``update``
    Commands to execute on update phase.

``shell``
    Shell to run script with. If not set uses default system shell.
    Special value `internal` means executing as python code from buildout.

``install-shell``
    Can override shell for install phase.

``update-shell``
    Can override shell for update phase.

``shell-options``
    Additional switch to shell, like `-File` for PowerShell, `-f` for Awk, etc.

``install-shell-options``
    Can override shell options for install phase.

``update-shell-options``
    Can override shell options for update phase.

``env``
    List of KEY=VALUE pairs to set environment variables.


Examples
========

.. code:: ini
    
    [cmmi]
    recipe = zsft.recipe.cmd
    install =
        ./configure --prefix=${buildout:parts-directory}/opt
        make
        make install
    env =
        CFLAGS = -g -Wall -O2
        LDFLAGS = -lm
        LD_RUN_PATH = $ORIGIN/../lib
        
    [pythonscript]
    recipe = zsft.recipe.cmd
    shell = internal
    install =
        os.chdir('${buildout:parts-directory}')
        if not os.path.exists('opt'):
            os.makedirs('opt')
        os.chdir('opt')
        check_call(['./config ; make'], shell=True)
    
    [msbuild;windows]
    recipe = zsft.recipe.cmd
    configuration = Release
    platform = Win32
    install =
        msbuild.exe /t:Build /p:Configuration=${:configuration} /p:Platform=${:platform}

    [service-restart;windows]
    recipe = zsft.recipe.cmd
    shell = powershell.exe
    shell-options = -File
    service = foo
    update =
        $service = "${:service}"
        Write-Host -ForegroundColor Yellow "Restarting service '$service'"
        Restart-Service -Verbose $service


Difference from other recipes
=============================

Unlike other similar recipes this one allows you to specify custom shell on
Windows and environment variables.

``iw.recipe.cmd``
    Does not allow you to have different scripts for install and update.
    Specifying shell is POSIX only.

``collective.recipe.cmd``
    Same limitations as in `iw.recipe.cmd`. Has `uninstall_cmds` and python mode.

``plone.recipe.command``
    Has `stop-on-error` option and allows different scripts for install/update.
    Does not seem to allow multiline commands or custom shells.
