---
lang: en
layout: doc
permalink: /doc/how-to-update/
redirect_from:
- /doc/updating-qubes-os/
ref: 200
title: How to update
---

*This page is about updating your system while staying on the same [supported
version of Qubes OS](/doc/supported-releases/#qubes-os). If you're instead
looking to upgrade from your current version of Qubes OS to a newer version,
see the [Upgrade Guides](/doc/upgrade/).*

## Security updates

Security updates are an extremely important part of keeping your Qubes
installation secure. When there is an important security issue, we will issue a
[Qubes Security Bulletin (QSB)](/security/qsb/) via the [Qubes Security
Pack (`qubes-secpack`)](/security/pack/). It is very important to read each new
QSB and follow any user instructions it contains. Most of the time, simply
[updating your system normally](#routine-updates) will be sufficient to obtain
security updates. However, in some cases, special action may be required on
your part, which will be explained in the QSB.

## Routine updates

It is important to keep your Qubes OS system up-to-date to ensure you have the
latest [security updates](#security-updates), as well as the latest
non-security enhancements and bug fixes.

Fully updating your Qubes OS system means updating:

- [dom0](/doc/glossary/#dom0)
- [templates](/doc/glossary/#template)
- [standalones](/doc/glossary/#standalone) (if you have any)

You can accomplish this using the **Qubes Update** tool. (**Warning:** This
tool is currently affected by bug
[#6585](https://github.com/QubesOS/qubes-issues/issues/6585). See below for a
mitigation.)

[![Qubes Update](/attachment/doc/r4.0-software-update.png)](/attachment/doc/r4.0-software-update.png)

By default, the Qubes Update tool will appear as an icon in the Notification
Area when updates are available.

[![Qube Updates Available](/attachment/doc/r4.0-qube-updates-available.png)](/attachment/doc/r4.0-qube-updates-available.png)

However, you can also start the tool manually by selecting it in the
Applications Menu under "System Tools." Even if no updates have been detected,
you can use this tool to check for updates manually at any time by selecting
"Enable updates for qubes without known available updates," then selecting all
desired items from the list and clicking "Next."

## Command-line interface

<div class="alert alert-danger" role="alert">
  <i class="fa fa-exclamation-triangle"></i>
  <b>Warning:</b> Updating <em>exclusively</em> with direct commands such as
  <code>qubes-dom0-update</code>, <code>dnf update</code>, and <code>apt
  update</code> is <b>not</b> recommended, since these bypass built-in Qubes OS
  update security measures. Instead, we strongly recommend <em>first</em> using
  the <b>Qubes Update</b> tool or its command-line equivalents, as described
  below, <em>then</em> using the direct commands for confirmation (see <a
  href="https://github.com/QubesOS/qubes-issues/issues/6585">#6585</a> and <a
  href="https://github.com/QubesOS/qubes-posts/pull/79">PR #79</a>). (By
  contrast, <a href="/doc/how-to-install-software/">installing</a> packages
  using direct package manager commands is fine.)
</div>

As a temporary mitigation until
[#6585](https://github.com/QubesOS/qubes-issues/issues/6585) is fixed, the
following update sequence is recommended (see
[PR #79](https://github.com/QubesOS/qubes-posts/pull/79)
for explanation and discussion):

1. Update dom0 with Salt.
2. Update dom0 by direct command.
3. Update templates and standalones with Salt.
4. Update templates and standalones by direct commands.

Example using only the command line (all commands with `sudo` or as root):

1. In dom0: `qubesctl --show-output state.sls update.qubes-dom0`
2. In dom0: `qubes-dom0-update --clean -y`
3. In dom0: `qubesctl --show-output --skip-dom0 --templates state.sls
   update.qubes-vm`
4. In dom0: `qubesctl --show-output --skip-dom0 --standalones state.sls
   update.qubes-vm`
5. In every Fedora template and standalone: `dnf -y --refresh upgrade`
6. In every Debian template and standalone: `apt-get clean && apt-get -y update
   && apt-get -y dist-upgrade && apt-get clean`

### Qubes 4.0

Advanced users may wish to perform updates via the command-line interface. The
recommended way to do this is by using the command-line equivalents of the
**Qubes Update** tool.

There are two Salt formulae and two corresponding `qubesctl` commands:
 - [`update.qubes-dom0`](/doc/salt/#updatequbes-dom0)
 - [`update.qubes-vm`](/doc/salt/#updatequbes-vm)

Advanced users may also be interested in learning [how to enable the
testing repos](/doc/testing/).

### Qubes 4.1

The recommendation, commands, and Salt formulae from 4.0 remain the same.

Command-line updates in dom0 behave differently in 4.1. In your update qube, a
terminal window opens that displays the progress of operations and output as it
is logged. At the end of the process, logs are sent back to dom0. You answer any
yes/no prompts in your dom0 terminal window.

## Upgrading to avoid EOL

The above covers updating *within* a given operating system (OS) release.
Eventually, however, most OS releases will reach **end-of-life (EOL)**, after
which point they will no longer be supported. This applies to Qubes OS itself
as well as OSes used in [templates](/doc/templates/) (and
[standalones](/doc/standalones-and-hvms/), if you have any).

**It's very important that you use only supported releases so that you continue
to receive security updates.** This means that you *must* periodically upgrade
Qubes OS and your templates before they reach EOL. You can always see which
versions of Qubes OS and select templates are supported on the [Supported
Versions](/doc/supported-releases/) page.

In the case of Qubes OS itself, we will make an
[announcement](/news/categories/#releases) when a supported Qubes OS release is
approaching EOL and another when it has actually reached EOL, and we will
provide [instructions for upgrading to the next stable supported Qubes OS
release](/doc/upgrade/).

Periodic upgrades are also important for templates. For example, you might be
using a [Fedora template](/doc/templates/fedora/). The [Fedora
Project](https://getfedora.org/) is independent of the Qubes OS Project. They
set their own
[schedule](https://fedoraproject.org/wiki/Fedora_Release_Life_Cycle#Maintenance_Schedule)
for when each Fedora release reaches EOL. You can always find out when an OS
reaches EOL from the upstream project that maintains it. We also pass along any
EOL notices we receive for official template OSes as a convenience to Qubes
users (see [Supported Versions:
Templates](/doc/supported-releases/#templates)).

The one exception to all this is the specific release used for dom0 (not to be
confused with Qubes OS as a whole), which [doesn't have to be
upgraded](/doc/supported-releases/#note-on-dom0-and-eol).
