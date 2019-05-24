# miscalibrate
## intro
this is a zsh script to keep a calibre (*spits*) library in sync with audiobooks stored in beets, without giving up file control to calibre (*spits*).

currently the script accepts four main options: `-c` to **c**reate audiobooks in calibre (*spi*—you get the idea) that aren't there already, `-u` to **u**pdate every audiobook in calibre with metadata from beets (potentially a long and unnecessary operation), `-d` to **d**elete any broken audiobooks from calibre and erase broken records from beets, and `-s` to **s**ync, equivalent to `-cud` and the default behaviour if no options are specified.

the update operation can be run with the additional optional flags `-h` and `-y` to delete the book files from the calibre folders and recreate them as **h**ardlinks or s**y**mlinks, respectively. this is useful if something happens to break the established links, or merely to switch from the hardlinks which are mandatory for initial creation to symlinks. be aware that symlinks are relative to paths, and so if you use them and then the locations of your beets tracks changes you'll need to do `miscalibrate -uy` again.

calibre is a ~~finicky, bloated, unmanageable pain in the proverbial~~ ~~flagrant violation of unix philosophy~~ piece of software which makes certain opinionated choices about library storage that can be inconvenient for certain use cases. it insists on storing all files in its own folders, and when adding books it always copies the files rather than moving them. it fails if told to add a symlink instead of a real file. however, if it's told to add a format to a book that already exists in the correct location, no copying is done. and if that file is a hardlink of a file existing elsewhere, calibre doesn't notice. (this doesn't work for symlinks, though if a symlink replaces the original book later, calibre won't notice.) this script exploits this behaviour, and therefore requires your audiobooks and your calibre library to be stored on the same drive/subvolume/whatever you can make hardlinks across.

## config
three configuration options must be set for the script to work. if there's a file called `options` in the same folder as the script, they will be sourced from there. `$AUDIOBOOKQUERY` is the beets query phrase used to limit operations to audiobooks so every song doesn't get added to calibre. this whole setup also assumes that your audiobooks are one file per book and tagged in a specific way. `$CALIBRELIBRARY` is the path to your calibre library, no trailing slash. `CDB` should be aliased to the calibredb command to use. it's better to have calibre-server running separately and connect to it than to use calibredb directly, cos otherwise it's loading up the database for every operation, apparently. example config:

```zsh
AUDIOBOOKQUERY="genre::^Audiobook$"
CALIBRELIBRARY="/mnt/Cecilia/LibraryOfStJohnTheBeheaded"
alias CDB="calibredb --library-path=http://localhost:8084"
```
