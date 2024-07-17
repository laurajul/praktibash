
#Scripts

- scr - saves a screenshot alongside a txt file saving the url and timestamp in bibtex format
- kill8188 - kill port 8188





## Screenshot Script scr

This script captures a screenshot of the active window and saves the URL of the active tab in a browser running with the DevTools protocol enabled. The URL and timestamp are saved in BibTeX format.

### Prerequisites

- **xdotool:** Used to get the active window ID and title.
- **jq:** Used to parse JSON.
- **gnome-screenshot:** Used to take screenshots.
- **curl:** Used to fetch the URL from the DevTools protocol.

### Setup

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

### Usage

1. Run the script:

    ```bash
    ./screenshot_script.sh
    ```

    This will save a screenshot of the active window and create a `.txt` file with the URL and timestamp in BibTeX format in the `~/Pictures/Screenshots` directory.


