# thplay

thplay is a basic command-line player for the music of the [Touhou Project](http://en.touhouwiki.net/wiki/Touhou_Wiki) series of games.  It, like many others of its kind, is designed to read the music and looping data from the game archives and play it back or dump it to an audio file.  The only things that are different about this one are that it is designed to run on Linux and runs in the command-line, providing a curses-based interface for playback.  It currently supports the plain WAV files used in TH06, the archived WAV of TH07, TH07.5, TH08, TH09, TH09.5, TH10, TH11, TH12, TH12.5, TH12.8, TH13, TH14, TH14.3, and TH15, and the ancrypted Ogg of TH10.5 and TH12.3.  Newer games by Tasogare Frontier (TH13.5 and TH14.5) use a new encrypted format, which I hope to eventually support.  I have no particular plans to try to support games earlier than TH06.

## Setup

Running the script requires three files (in addition to the archives): `thplay`, `dirs.txt`, and `titles.db`.  They must all be located in the same directory.  `titles.db` is a SQLite 3 database containing the titles, comments, looping information, and other information required to play the music.  It was derived, in a roundabout way, from the [standard titles_thxx.txt](http://www.selena-net.com/~piabrpg/mata-ri) information files and should not need to be modified.  At one point I wrote a tool that would completely regenerate this database from the game files and information stored online, but it had to have so many quirks and exceptions hardcoded into it that I felt like I was basically rewriting the database in code.  Instead, I am now working on a program that will update the English titles and comments from the translations on the [Touhou Wiki](http://en.touhouwiki.net/wiki/Touhou_Wiki).

The file `dirs.txt` tells thplay where to find the game archives.  The first line is the full path to the base directory under which all games reside.  (If they are in completely different places, this could just be `/`.)  The following lines each contain the number of a game (without a dot, if there is one), followed by a colon and then the relative path of that game's directory within the base directory.  For example, if my `dirs.txt` looked like this:

    /Games/Touhou
    105:Tasofro/SWR

it would indicate that I have only TH10.5 installed, and its files are located in `/Games/Touhou/Tasofro/SWR`.  The program assumes that the files within each game directory have not been moved around or renamed from their original configuration.

The current script uses `pacat`, which of course assumes that your system is running PulseAudio.  If it isn't, there is a commented line in the script that uses SoX for output instead.  If you have SoX installed, this should work on ALSA, Pulse, and possibly other sound systems.  I am currently looking into using libmpv for playback, which in addition to supporting richer playback features like seeking and volume adjustment would also transparently support multiple backends.

## Usage

The executable, `thplay`, does not take any command-line arguments.  Running it should bring up a menu of games and songs that you can browse and play as you wish.  The supported operations are as follows:

  - left and right: change game
  - up and down: change song
  - enter: start playing
  - space: toggle pause
  - `x`: stop playing
  - `s`: select random song
  - `c`: switch language
  - `/`: search in song titles
  - `n`: next match
  - `p`: previous match
  - `d`: dump current song to file
  - `g`: go to song (`game_number:song_number`)
  - `q`: quit

## Issues

  - The next step is to add support for the `.pak` format of the newer Tasogare Frontier games.  I haven't yet had time to do research into the layout and encryption of those files.

  - Running the player in a subprocess in a thread is a bit tricky, so things like switching songs too quickly might mess it up.  If I switch to libmpv, this might be resolved.

