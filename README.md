# Kerb's Auto-Rice Bootstrapping Scripts (KARBS)

## Installation:

On an Arch-based distribution as root, run the following:

```
curl -LO larbs.xyz/larbs.sh
sh larbs.sh
```

That's it.

## What is KARBS?

KARBS is a script that autoinstalls and autoconfigures a fully-functioning*
and minimal terminal-and-vim-based** Arch Linux environment.

KARBS can be run on a fresh install of Arch or Artix Linux, and provides you
with a fully configured diving-board for work or more customization.

KARBS is a fork of [Luke Smith's LARBS](https://larbs.xyz/) where I attmept to create my own auto-intaller. The purpose of KARBS is to have a similar setup to LARBS, but to use Wayland focused apps compared to X.

I initially intended to use [dwl](https://codeberg.org/dwl/dwl.git) for the window manager, but ended up using [sway](https://swaywm.org/) becuase I had issues with certain patches (like the ipc patch) or I'm just too dumb to figure it out.

*This script is still a work in progress

**Mostly vim-based

## Customization

By default, KARBS uses the programs [here in progs.csv](static/progs.csv) and installs
[my dotfiles repo (voidrice) here](https://github.com/lukesmithxyz/voidrice),
but you can easily change this by either modifying the default variables at the
beginning of the script or giving the script one of these options:

- `-r`: custom dotfiles repository (URL)
- `-p`: custom programs list/dependencies (local file or URL)
- `-a`: a custom AUR helper (must be able to install with `-S` unless you
  change the relevant line in the script

### The `progs.csv` list

KARBS will parse the given programs list and install all given programs. Note
that the programs file must be a three column `.csv`.

The first column is a "tag" that determines how the program is installed, ""
(blank) for the main repository, `A` for via the AUR or `G` if the program is a
git repository that is meant to be `make && sudo make install`ed.

The second column is the name of the program in the repository, or the link to
the git repository, and the third column is a description (should be a verb
phrase) that describes the program. During installation, KARBS will print out
this information in a grammatical sentence. It also doubles as documentation
for people who read the CSV and want to install my dotfiles manually.

Depending on your own build, you may want to tactically order the programs in
your programs file. KARBS will install from the top to the bottom.

If you include commas in your program descriptions, be sure to include double
quotes around the whole description to ensure correct parsing.

### The script itself

The script is extensively divided into functions for easier readability and
trouble-shooting. Most everything should be self-explanatory.

The main work is done by the `installationloop` function, which iterates
through the programs file and determines based on the tag of each program,
which commands to run to install it. You can easily add new methods of
installations and tags as well.

Note that programs from the AUR can only be built by a non-root user. What
KARBS does to bypass this by default is to temporarily allow the newly created
user to use `sudo` without a password (so the user won't be prompted for a
password multiple times in installation). This is done ad-hocly, but
effectively with the `newperms` function. At the end of installation,
`newperms` removes those settings, giving the user the ability to run only
several basic sudo commands without a password (`shutdown`, `reboot`,
`pacman -Syu`).
