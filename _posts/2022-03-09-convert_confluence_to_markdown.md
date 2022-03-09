# Converting Confluence to Markdown

## Exporting from Confluence
1. Select **Space Tools** in bottom-left-hand corner
2. Select **Content Tools** from the context menu
3. Select the **Export** tab
4. Select the **HTML** format and click Next
5. Select the **Custom Export** radio button
    1. Unselect **Include comments** checkbox
    2. Select **Deselect All** shortcut
    3. Select the articles you do want to export
    4. Select **Export** button at the bottom of the page
    5. ` Exporting Space - In Progress` waiting page
    6. `Export complete. Download here` - select here
    7. Extract the above `zip` into your preferred location


## Converting with `confluence-to-markdown`
1. Install `pandoc`
2. Clone this repo: `https://github.com/meridius/confluence-to-markdown`
3. `cd` into the folder of the above repo
4. `yarn install` project dependencies
5. `yarn run start <pathResource> <pathResult>`, where:
    - `pathResource` = File or directory to convert with extracted Confluence export
    - `pathResult` = Destination directory of generated output. Default is CWD


## Post-Migration Cleanup and Fixing
1. Move articles into folders
2. Add `.md` to files in links
3. Remove no-space-break and other weird characters
4. Fix markdown image links to attachments folder
5. Remove `<div>` tags
6. Search for references to Lucidchart diagrams (using the string `vnd.lucid.confluence-onprem`), 
   and download those separately as Visio files, 
   deleting the associated JSON files (which are excessively large) from attachments

## Wish list

If output were hosted on Github, it would be nice to rename
index.md in every folder to README.md

