# README for `treame` Function

**Warning**: The `treame` function will overwrite existing `README.md` files in your current directory and its subdirectories. Ensure that you have backups or that overwriting is acceptable before running `treame`.

## Overview

The `treame` function is designed to generate a tree of your working directory in a `README.md` file, copy it to all subdirectories within your working directory structure, and customize each copy with a "YOU ARE HERE" indicator. This makes it easier to navigate and understand the directory hierarchy at a glance.

## Prerequisites

Before using `treame`, ensure the following:

1. **Zsh Shell**: You need to be using the Zsh shell.
2. **`tree` Command**: Make sure the `tree` command is installed and available in your PATH. This command is used to generate directory tree.

## Installation

To use `treame`, follow these steps:

1. **Copy Function**: Copy the `treame` function and `update_readme_you_are_here` helper function into your `.zshrc` file. You can do this [manually](https://github.com/19msb/treame/blob/main/treame.txt) or by referencing the `treame.txt` file located in this repository.

   ```sh
   echo '
   # Function to generate README.md, copy to all subdirectories, and customize each copy with "YOU ARE HERE"
   treame() {
     # Create the README.md file, excluding README.md from the tree output
     (
       echo "'''"
       command tree -I "README.md" | sed "1s/\\./root/"
       echo "'''"
     ) > README.md
     echo "Generated README.md in the current directory."
   
     # Find all subdirectories (excluding hidden directories) and copy README.md to each
     find . -type d -not -path "*/\.*" | while read -r dir; do
       if [[ "$dir" != "." ]]; then
         if [ ! -f "$dir/README.md" ]; then
           cp README.md "$dir/"
           echo "Copied README.md to $dir"
         else
           echo "README.md already exists in $dir, skipping."
         fi
       fi
     done
     echo "README.md has been copied to all subdirectories."
   
     # Update README.md files with "YOU ARE HERE"
     update_readme_you_are_here
   
     # Display the contents of the root README.md
     cat README.md
   }
   
   # Helper function to update README.md files with "YOU ARE HERE"
   update_readme_you_are_here() {
     local dir="${1:-.}"
   
     # Handle the root directory
     if [ "$dir" = "." ]; then
       sed -i "" "s/root/root <- - - -( YOU ARE HERE )/" "./README.md"
       echo "Updated root README.md"
     fi
   
     # Loop through each item in the directory
     for item in "$dir"/*; do
       if [ -d "$item" ]; then
         if [ -f "$item/README.md" ]; then
           local dirname=$(basename "$item")
           if grep -q "$dirname" "$item/README.md"; then
             sed -i "" "s/$dirname/$dirname <- - - -( YOU ARE HERE )/" "$item/README.md"
             echo "Updated $item/README.md"
           else
             echo "Folder name not found in $item/README.md"
           fi
         else
           echo "README.md not found in $item"
         fi
   
         # Recursive call for subdirectories
         update_readme_you_are_here "$item"
       fi
     done
   }
   ' >> ~/.zshrc
   ```

   Ensure that the `treame.txt` file contains the function definition for `treame` and `update_readme_you_are_here`.

2. **Save and Apply Changes**: Save the `.zshrc` file. Then, restart your terminal session or use the command `source ~/.zshrc` to apply the changes and make the `treame` function available for use.

## Usage

Once installed, follow these steps to use `treame`:

1. **Navigate**: Navigate to the directory where you want to generate and customize `README.md` files using `treame`.

2. **Run Command**: Simply type `treame` in your terminal and press Enter. This will execute the function, generate an initial `README.md` in the current directory, copy it to all subdirectories (overwriting existing `README.md` files), customize each `README.md` file with "YOU ARE HERE" indicators, and display the contents of the root `README.md`.

3. **Review**: Review the generated `README.md` files in each directory to see the updated directory structure with "YOU ARE HERE" indicators.
