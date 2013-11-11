#!/bin/bash
# Extract subtitles from each MKV file in the given directory
# Modified from http://www.computernerdfromhell.com/blog/automatically-extract-subtitles-from-mkv/
# I had the same problem as the author of the above blog post

# If no directory is given, work in local dir
if [ "$1" = "" ]; then
  DIR="."
else
  DIR="$1"
fi

# Get all the MKV files in this dir and its subdirs
find "$DIR" -type f -name '*.mkv' | while read filename
do
  # Get base name for subtitle
  subtitlename=${filename%.*}
  if [ -f "$subtitlename.srt" ]; then
    continue
  fi
  # Find out which tracks contain the subtitles
  mkvmerge -i "$filename" | grep 'subtitles' | while read subline
  do
    # Grep the number of the subtitle track
    tracknumber=`echo $subline | egrep -o "[0-9]{1,2}" | head -1`

    # Extract the track to a .tmp file
    `mkvextract tracks "$filename" $tracknumber:"$subtitlename.srt.tmp" > /dev/null 2>&1`
    `chmod g+rw "$subtitlename.srt.tmp"`

    # Do a super-primitive language guess: DUTCH
    #langtest=`egrep -ic ' ik | je | een ' "$subtitlename".srt.tmp`
    #trimregex="vertaling &\|vertaling:\|vertaald door\|bierdopje"

    # Do a super-primitive language guess: ENGLISH
    langtest=`egrep -ic ' you | to | the ' "$subtitlename".srt.tmp`
    trimregex=""

    # Do a super-primitive language guess: GERMAN
    #langtest=`egrep -ic ' ich | ist | sie ' "$subtitlename".srt.tmp`
    #trimregex=""

    # Do a super-primitive language guess: SPANISH
    #langtest=`egrep -ic ' el | es | por ' "$subtitlename".srt.tmp`
    #trimregex=""

    # Check if subtitle passes our language filter (10 or more matches)
    if [ $langtest -ge 10 ]; then
      `mv "$subtitlename.srt.tmp" "$subtitlename.srt"`
      `chmod g+rw "$subtitlename.srt"`
    fi
  done
done
