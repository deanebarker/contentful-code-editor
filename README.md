# Contentful Code Editor

A UI extension for Contentful that provides a content-configured Code Mirror instance.  The editor instance is generic by default, but can be configured from a Contentful entry, so the editor settings are stored as content which lets you re-use the UI extension on multiple field with different settings.

>For example: on one field, the editor can be used with HTML syntax highlighting and line-numbering. In another instance (on the same type or on another), it can be used with YAML highlighting, no line-numbering, and completely different styling. These two fields use _the same UI extension_, the settings simply vary from field to field and type to type.

To use the extension:

1. Upload the extension and enable it on a "Text" field (example of `extension.json` provided below).  Unconfigured, it will simply render a CodeMirror instance using the "htmlmixed" mode.
2. Create an entry of the "Extension Settings" content type (explained further below).
3. Enter "typeName/fieldName" as one value in the "Applies To" field.  For example, if you have a content type called "HTML File" with a field called "body", you might enter "htmlFile/body" as one value in the "Applies To" field.
4. When the UI extension loads, it will search for an "Extension Settings" entry with a value in the "Applies To" field which matches the "typeName/fieldName" of the field for which it's loading. If it finds a matching entry, it will configure itself based on the settings contained within that entry.

Multiple fields on multiple types can use the same Extension Settings, allowing you to centrally-control the settings for a particular type of editor scenario, regardless of where or how many times it's used in your content model.

## The Extension Settings Content Type

Settings to configure the editor are stored in an entry, using a type called "Extension Settings."  The type should have an id of `extensionSettings`, and contain the following fields:

(Field _IDs_ are provided -- these need to be identical, since the extension will search for these IDs. You can _name_ the fields whatever you like.)

* **description (short text):** Just a description of what the settings should apply to. This field is really just for displaying the name in list of content when browsing.
* **appliesTo (short text, list):** A list of the "typeName/fieldName" combinations for which these settings should apply. A value in this list is matched to locate settings for a particular type/field combination.
* **settingData (JSON):** A JSON object of settings for the Code Mirror constructor. This JSON object is passed into the constructor, unaltered. (Some examples provided below.)
* **scriptUrls (long text)**: A set of URLs (one per line) that should be loaded as Javascript includes (SCRIPT tags)
* **stylesheetUrls (long text):** A set of URLs (one per line) that should be loaded as stylesheets (LINK tags)
* **additionalCss (longtext):** Any additional CSS that should be loaded in the HEAD tag of the UI extension document in a single STYLE tag. Use this to style the Code Mirror instance, using the `.CodeMirror` class.

## Settings Data Examples

The JSON object stored in the `settingsData` field will be passed unaltered to the CodeMirror constructor, like this (second parameter):

    var editor = CodeMirror.fromTextArea(document.getElementById("editor"), settingsData);

The simplest example, to change syntax highlighting:

    {
      "mode": { "name": "xml" }
    }

To change synxtax highlighting and provide line-numbering wrapping:

    {
      "mode": { "name": "htmlmixed" },
      "lineNumbering": true,
      "lineWrapping": true
    }

More examples of CodeMirror configuration can be found here: https://codemirror.net/doc/manual.html#config

## Extension.json example

To load the extension in your space, this JSON can be used:

    var extension = {
      id: 'code-editor',
      name: 'Code Editor',
      fieldTypes: ["Text"],
      srcdoc: [contents of the HTML file, or...],
      src: [URL to the HTML file]
      sidebar: false
    };