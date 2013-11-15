Blissymbols
===========

A database of all official Blissymbols, and their definitions. Plus some additional utilities.

Contents:
- `blissdata.js`: this is the most important file, the database itself
- `blisseditor.html`: this is a very simplistic helper form for updating the database
- `check_consistency.py`: a Python script that checks `blissdata.js` for obvious inconsistencies
- `blisseditor.js`, `lib/*.js`: utility files for `blisseditor.html`
- `svg/*.svg`: more than 5000 SVG files
- `blissviewer.js`: a generic Javascript module for attaching Blissymbols to text
- `blissviewer-demo.html`: a simple webpage that shows how `blissviewer.js` can be used

The Javascript files that are supposed to be used as library files (currently `blissdata.js` and `blissviewer.js`) all follow the [Javascript Module Pattern] [JMP] to not pollute the global namespace. To be more exact, we are using "loose augmentation" and "global import" so that the files can be loaded in any order (se the linked article for more information).

[JMP]: http://www.adequatelygood.com/JavaScript-Module-Pattern-In-Depth.html

The namespace we are using is `BLISS`. This means that all our Javascript library files have the following structure:

    var BLISS = (function (BLISS, ...possible-global-modules) {
        // exported variables and methods
        BLISS.exported_variable = ...;
        BLISS.exported_method = exported_method;

	    // private variables and methods
        var private_variable = ...;
        function private_method(...) {...}
        function exported_method(...) {...}

	    return BLISS;
    }(BLISS || {}, ...possible-global-modules));

Below are some more detailed information about [blissdata.js](#blissdatajs), [blissviewer.js](#blissviewerjs) and [blisseditor.html](#blisseditorhtml).


blissdata.js
------------

This is a Javascript file that encodes all metadata about all official Blissymbols. Plus some additional to-be-official-in-the-near-future symbols that need to be added.

The database is added as a Javascript object `BLISS.data`, with the following structure:

    BLISS.data = {
        ...,
		"pineapple": {
			"type": "char",
			"height": "high",
			"width": null,
			"center": null,
			"kern": [],
			"modified": null,
			"verified": null
		},
        ...
		"Tarzan": {
			"type": "word",
			"definition": [
				"make-believe_man",
				"tropical_rain_forest,jungle"
			],
			"modified": null,
			"verified": null
		},
        ...
    }

Note: References of the form R3.4 refer to section 3.4 of "The fundamental rules of Blissymbolics", available from http://www.blissymbolics.org/images/bliss-rules.pdf

The `type` can be one of the following:

- `word` is a complex word (R3.3, R6.0, R6.1.2).

  Only `word`s can have (and must have) a `definition`, which is a list of smaller Bliss words. On the other hand, `word`s must not have a `height`, `width`, `center` or `kern`, because these attribues should be calculated from its definiens.

- `char` is a standard Bliss character (R3.2, R5.0, R6.1.1).

  `char`acters can have an optional `height`, `width` or `center`, which will be used for positioning `ind`icators above them (R4.3.3). These need only be set for nonstandard characters, such as extra-high (R4.3.4) or when the preferred position is not in the center (R7.1).

  `char`acters can have an associated list (or dictionary) `kern`, which consists of blisscharacters that should be kerned when they follow the current `char` (R3.7, R4.3.6).

- `mod` is a Bliss modifier character (R6.5).

  `mod`ifiers can also have an associated list (or dictionary) `kern`.

- `ind` is a Bliss indicator character (R3.6, R7.0)

  `ind`icators can have an optional `width` or `center`, which will be used for positioning.

- `punct` is a Bliss punctuation character (R4.2.2). They have no extra attributes.

If the `modified` field is not set, then the definition of the symbol is automatically generated by some to-be-forgotten scripts which were not at all correct. The idea is that any person can manually correct a character and then set the `modified` field to their username and then commit their changes to the repository. Then another person can verify that the symbol has a correct definition and sign by setting the `verified` field.

To help with this editing, there is [blisseditor.html](#blisseditorhtml), a small webpage that is supposed to be loaded locally on your own computer.


blissviewer.js
--------------

This Javascript file exports the function `BLISS.show_text`:

    BLISS.show_text = function(parent, blisswords, textwords) {...}

The arguments are as follows:

- `parent` should be a reference to a HTML container (such as a `<div>` or a ´<p>´).
- `blisswords` is a list of Blisswords, where each word is either a Blissymbol id (which is a string), or a list of ids.
- `textwords` is an optional list of text words, where each word is a string.

The function appends the sequence of `blisswords` to the `parent` container. The `textwords` are added below each Blissword, if present. See `blissviewer-demo.html` for an example of how it can be used.

The function can be configured by setting the following variables:

- `BLISS.config.imgtype`: The file type of the Blissymbol images. Possible values: "png" (default) and "svg".
- `BLISS.config.imgpath`: The relative URL to the directory where the Blissymbol images lie. Default value: "img".
- `BLISS.config.imgheight`: The height of the Blissymbol matrix square (R4.3) in pixels. Default value: 100.


blisseditor.html
----------------

When you use the blisseditor, all changes are done locally inside the browser. 
The original database file ***is not*** updated automatically, even if you "save".

To get the changes on your own computer, you have to press "Download database" and 
then replace your database file with the downloaded file. 
Be sure to check that the file is consistent before replacing.

Finally, to get your changes into the official list, you have to fork this repository 
and issue a pull request whenever you have made your changes.

