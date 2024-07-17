# Screenshot Script

This script captures a screenshot of the active window and saves the URL of the active tab in a browser running with the DevTools protocol enabled. The URL and timestamp are saved in BibTeX format.

## Prerequisites

- **xdotool:** Used to get the active window ID and title.
- **jq:** Used to parse JSON.
- **gnome-screenshot:** Used to take screenshots.
- **curl:** Used to fetch the URL from the DevTools protocol.

## Setup

1. Install the necessary tools:

    ```bash
    sudo apt-get install xdotool jq gnome-screenshot curl
    ```

2. Start your browser (e.g., Google Chrome or Chromium) with the following flag to enable the DevTools protocol on port 9222:

    ```bash
    google-chrome --remote-debugging-port=9222
    ```

    or

    ```bash
    chromium-browser --remote-debugging-port=9222
    ```

## Usage

1. Run the script:

    ```bash
    ./screenshot_script.sh
    ```

    This will save a screenshot of the active window and create a `.txt` file with the URL and timestamp in BibTeX format in the `~/Pictures/Screenshots` directory.

## Script Details

```bash
#!/bin/bash

# Define screenshot directory

TAB_INFO=$(curl http://localhost:9222/json | jq '.[0]')
URL=$(echo $TAB_INFO | jq -r '.url')

SCREENSHOT_PATH="$HOME/Pictures/Screenshots"

# Check if screenshot directory exists, if not, create it
if [ ! -d "$SCREENSHOT_PATH" ]; then
    mkdir -p "$SCREENSHOT_PATH"
    if [ $? -ne 0 ]; then
        echo "Failed to create directory: $SCREENSHOT_PATH"
        exit 1
    fi
fi

# Get the active window ID
WINDOW_ID=$(xdotool getactivewindow)

# Get the active window title
WINDOW_TITLE=$(xdotool getwindowname $WINDOW_ID | sed "s/[\/:*?\"<>|]/-/g")

# Format the timestamp
TIMESTAMP=$(date +%Y%m%d-%H%M%S)

# Filename with replaced problematic characters
FILE_NAME="$TIMESTAMP - $WINDOW_TITLE.png"

# Take a screenshot of a selected area and save it
gnome-screenshot -a -f "$SCREENSHOT_PATH/$FILE_NAME"

if [ $? -eq 0 ]; then
    echo "Screenshot saved as $SCREENSHOT_PATH/$FILE_NAME"
    BIBTEX_ENTRY="@misc{screen_capture_$TIMESTAMP,
  author = {User},
  title = {Screenshot of $WINDOW_TITLE},
  year = {$(date +%Y)},
  month = {$(date +%m)},
  day = {$(date +%d)},
  url = {$URL},
  note = {Accessed: $TIMESTAMP}
}"
    echo "$BIBTEX_ENTRY" > "$SCREENSHOT_PATH/$FILE_NAME.txt"
else
    echo "Failed to save screenshot."
fi
