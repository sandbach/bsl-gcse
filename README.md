# BSL Anki Deck

This is an [Anki](https://docs.ankiweb.net) deck of British Sign Language (BSL) signs, based on the [GCSE vocabulary](https://www.signbsl.com/gcse-vocabulary) section of [SignBSL.com](https://www.signbsl.com/).

If all you need is the deck itself, that can be found at [BSL GCSE.apkg](BSL%20GCSE.apkg).

## How it works

Requirements:

- A Python interpreter
- FFmpeg
- Anki desktop

The main Python script is a web scraper which follows each link on the GCSE Vocabulary page and downloads the information for one Anki note for each of the headings (`h1` or `h2`) on the page. In the interest of simplicity, the video URL that it chooses is always that of the first video that follows the heading. For example, on the page for the word [finish](https://www.signbsl.com/sign/finish), the script finds a note for each of the three senses, and chooses the first videos by SignStation, the University of Wolverhampton, and SignStation, respectively. It downloads and compresses the videos, placing them in the [appropriate directory](https://docs.ankiweb.net/importing/text-files.html#importing-media).

There is a great deal of variety within BSL, and this is reflected in the number of different signs shown for a single English word on SignBSL.com. For this reason, the video chosen for a particular sign by the script may not be one you have been taught. See below for how to add a different video to a note.

Anki supports importing notes en masse with CSV files. The script writes to a CSV file according to the requirements described in the [Anki documentation](https://docs.ankiweb.net/importing/text-files.html), where the fields are as follows:

1. Headword
2. Definition
3. Example
4. Video (a filename based on the video URL)
5. VideoURL
6. VideoTitle
7. URL
8. Tags

### Video compression

While the videos on SignBSL.com are already fairly small and well-compressed, the script uses FFmpeg to compress them further, in the interest of minimizing the size of the final deck. The incantation used is as follows:

``` shell
ffmpeg -i "input.mp4" -vcodec libx265 -crf 32 "output.mp4"
```

The larger the number following `-crf`, the greater the rate of compression.

### Word frequency

The notes in the deck ought to be in order of frequency, so that users see the most common signs first. I do not have data on the frequency of signs in BSL, so I am using English word frequency data as a proxy. I found an Excel spreadsheet of the 5000 most frequent lemmas in English at www.wordfrequency.info, converted the relevant sheet to tab-separated values with VisiData, and used Awk to obtain a [file of words](frequency.txt) and their frequency rankings.

## Possible modifications

- If the Anki `collection.media` file you want to use is not found under the default `User 1`, you will have to amend [script.py](script.py) to reflect the correct filepath.

- To add a card, use the function `get_definition` in the Python script to scrape the relevant information from a page on SignBSL.com, and print the resulting note or notes to a CSV file. Run `download_videos` on the CSV file to download and compress the first video for each definition. You can then import the CSV file into Anki, making sure that all the fields correspond as described above, and that 'Allow HTML in fields' is selected.

- To change the video associated with a particular card, find the URL of the video you want and modify the relevant part of the CSV file. Then, use `download_videos` as described above.

- In terms of file size, the deck is already fairly small. If you want to make it extremely tiny, at the cost of needing to download the videos when they appear for review, you could eschew the `download_videos` step and replace `{{Video}}` in the Anki card templates with the following:

``` html
<iframe src={{VideoURL}}></iframe>
```
