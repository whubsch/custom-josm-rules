# Custom JOSM Validation Rules for OpenStreetMap

**Disclaimer:** These custom JOSM validation rules are provided for OpenStreetMap contributors to use freely, but they should be used with caution and at your own discretion. These rules may help you identify potential issues in your OpenStreetMap data, but they may also produce false positives or overlook certain issues. Always use your best judgment and refer to the OpenStreetMap data editing guidelines when making changes to the map.

## Table of Contents

- [Introduction](#introduction)
- [Installation](#installation)
- [Usage](#usage)
- [Contributing](#contributing)

## Introduction

JOSM (Java OpenStreetMap Editor) is a popular tool for editing and contributing to the OpenStreetMap (OSM) project. JOSM provides a validation feature that helps identify and fix issues in OSM data. These custom validation rules aim to extend JOSM's validation capabilities by adding rules specific to certain data quality concerns or regional issues.

Please note that these custom rules are not officially endorsed by the OpenStreetMap community or the JOSM development team. They are maintained and shared by individual contributors who believe they can improve the quality of OSM data.

## Installation

To use these custom JOSM validation rules, follow these steps:

1. **Download the Rules File(s):** Visit the repository or source where these rules are hosted and download the custom validation rules file (typically in MapCSS format).

2. **Open JOSM:** Launch JOSM on your computer.

3. **Access Validation Rules Preferences:**

   - In JOSM, open `Preferences`.
   - In the Preferences window, select the `Data validator` on the left sidebar.

4. **Add Custom Rules:**

   - Click the `Tag checker` tab, and click the <kbd>+</kbd> button on the right.
   - Locate and select the downloaded custom rules file(s).
   - Click `OK` to add the rules to your JOSM installation.

5. **Activate Custom Rules:**

   - In the `Tag checker` tab, ensure that the custom rules you added are active.
   - If you choose to start tweaking the rules, JOSM should pick up your local edits immediately. If it stops doing so because of a crash, restart JOSM or remove the file and start over from step 4.

## Usage

Once you've installed and activated the custom validation rules, you can use them while editing OpenStreetMap data in JOSM. Here are some general usage guidelines:

- **Review Validation Results:** After loading or editing OSM data, click the `Validation` button in the toolbar, or go to `Windows` > `Validation Results` if you don't see the validation pane.

- **Address Issues:** Review the issues listed in the validation results and use your best judgment to address them. Some issues may allow you to click the `Fix` button, some require manually correcting data, and others may be false positives.

- **Use Caution:** Remember that these custom rules may produce false positives or not cover all possible issues. Always use your local knowledge and common sense when making edits. Do not just blindly mash `Fix`.

- **Contribute Feedback:** If you encounter issues with the custom rules or have suggestions for improvements, consider reaching out to the rule's maintainer or contributing to the rules' development. See below.

## Contributing

Contributions to these custom validation rules are welcome, but it's important to coordinate with the rule's maintainer to ensure consistency and avoid conflicts. A patchy set of documentation for JOSM's use of MapCSS for validation is available on the [JOSM Wiki](https://josm.openstreetmap.de/wiki/Help/Validator/MapCSSTagChecker). If you'd like to contribute or report issues, feel free to fork this repository to your own account and submit a pull request. Using the [MapCSS Syntax Highlighter](https://marketplace.visualstudio.com/items?itemName=whammo.mapcss-syntax) VS Code extension can help visualize the complicated JOSM validation syntax. Also consider contributing to the JOSM Wiki if you find it's missing anything you come across.

## License

Available [here](LICENSE.md).
