# m4b.py

This is a simple python script to convert and split m4b audio books into mp3 files.


## Requirements

* [Python](http://www.python.org/download/) (tested with v2.7)
* ffmpeg
* [mp4v2](http://code.google.com/p/mp4v2/downloads/detail?name=mp4v2-1.9.1.tar.bz2&can=2&q=) (v1.9.1)


## Installation

### Debian 8 Jessie

1. Install packages: `sudo apt-get install python2.7 ffmpeg libavcodec-extra-57 mp4v2-utils`
2. ln -s /usr/lib/x86_64-linux-gnu/libavcodec.so.57 /usr/lib/x86_64-linux-gnu/libavcodec.so.52

### Command Line Help

    usage: m4b.py [-h] [-o DIR] [--custom-name "STR"] [--ffmpeg BIN]
                  [--encoder BIN] [--encode-opts "STR"] [--ext EXT] [--pipe-wav]
                  [--skip-encoding] [--no-mp4v2] [--debug]
                  filename [filename ...]

    Split m4b audio book by chapters.

    positional arguments:
      filename              m4b file(s) to be converted

    optional arguments:
      -h, --help            show this help message and exit
      -o DIR, --output-dir DIR
                            directory to store encoded files
      --custom-name "STR"   customize chapter filenames (see README)
      --ffmpeg BIN          path to ffmpeg binary
      --encoder BIN         path to encoder binary (default: ffmpeg)
      --encode-opts "STR"   custom encoding string (see README)
      --ext EXT             extension of encoded files
      --pipe-wav            pipe wav to encoder
      --skip-encoding       do not encode audio (keep as .mp4)
      --no-mp4v2            use ffmpeg to retrieve chapters (not recommended)
      --debug               output debug messages and save to log file

#### Chapter filenames

You can customize the chapter filenames with `--custom-name "STR"` where `STR` is a valid python [format string](http://docs.python.org/library/stdtypes.html#string-formatting-operations).

Default ("My Title.mp3"):

    --custom-name "%(title)s"

Chapter number ("3 - My Title.mp3"):

    --custom-name "%(num)d - %(title)s"

Chapter number with leading zero ("03 - My Title.mp3"):

    --custom-name "%(num)02d - %(title)s"

#### Encoding

By default the audio will be encoded with the lame mp3 codec using [ffmpeg](http://www.ffmpeg.org/ffmpeg-doc.html). The bit rate and sampling freq will be the same as the source file.
If you wish to use other settings you can specify your own encoding options with `--encode-opts "STR"`. `STR` will be passed to the encoder (`--encoder` or skip to use ffmpeg). Variables available:

    %(outfile)s - output filename (required)
    %(infile)s - .m4b file
    %(bit_rate)d - bit rate of .m4b file
    %(sample_rate)d - sampling rate of .m4b file


### Examples

Convert multiple audio books (you can also drag multiple m4b files onto `m4b.py`):

    python m4b.py myfile.m4b otherfile.m4b

Include chapter number in the generated filenames: (example: "Chapter 10 - Some Title.mp3")

    python m4b.py --custom-name "Chapter %(num)d - %(title)s" myfile.m4b

If you rather want .mp4 files you can skip encoding to speed up the conversion process:

    python m4b.py --skip-encoding myfile.m4b

Force sampling freq to 22050 Hz and bit rate to 128 kbit/s:

    python m4b.py --encode-opts "-y -i %(infile)s -acodec libmp3lame -ar 22050 -ab 128k %(outfile)s" myfile.m4b

Encode with lame.exe:

    python m4b.py --encoder lame.exe --pipe-wav --encode-opts "-V 3 -h - %(outfile)s" myfile.m4b
