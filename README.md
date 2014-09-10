MEIMassaging
============

MEIMassaging is a set of Python files using [libmei](https://github.com/DDMAL/libmei)
which transform ("massage") the raw MEI files that have been created
by [sibmei](https://github.com/DuChemin/sibmei) to make them more suitable for
a particular use—for example, to make them more compatible with
[MEItoVexFlow](https://github.com/ironchicken/MEItoVexFlow).

More specifically: Sibelius files that include variant readings or
reconstructions have these annotations included as extra staves
in the score, and make use of color to mark these variants.
The purpose of the sibmei plugin is to translate those annotations
exactly into their corresponding MEI equivalents. This helps ensure
that MEI may be used for other projects as a generic plugin, and
also avoids writing code for non-straightforward conversion
in ManuScript, the language for Sibelius plugins.

MEIMassaging is expected to handle at least some of the following
transformations which may be necessary after export via sibmei.

Various ‘modules’ transform the information contained in the ‘flat’ MEI files produced by sibmei. As detailed below, these modules address musical information (like beams, brackets, clefs, final longa, incipits, lyrics, and time signatures) as well as meta data (editorial responsibility, relationship among variant or alternative sources, and copyright).  They can be configured to run in different combinations with the transform.py routine.

Modules in MEIMassaging:


Arranger.py
Various functions to change all occurrence of the <arranger> tag to <editor>, either globally, or as @role in the element <persName> 
Reinterprets arranger as editor in all occurrences

Beams.py
Removes all beams that contain a single note

Clef.py
Finds clefs in a document, adds regularized clefs if appropriate, and adds original clefs if provided, using <staffDef>, <staffGrp> and <choice>.

Copyright.py
Adds string from sibmei “copyright_text” to the <useRestrict> element in MEI header.

Cut_time.py
Changes all 2/2 time signatures to 4/2, as might be desired in mensural repertory for integer valor transcriptions.  Cut time “symbol” (if used in the original edition) will be left in fact.  Uses MEI meter.count, meter.unit, and meter.sym attributes. 

Emendations.py
Uses the list of alternate readings in separate staves to find the emendations, and reorganizes the MEI file so that these readings are grouped together with the lemma using <app> and <rdg> elements.

Ignored.py
?

Incipit.py
Calculates the number of incipit measures and removes them. Renumbers remaining measures if needed.

Invisible.py
Turns all invisible notes, rests, and mRests into <space> elements.  In DuChemin we use these for gaps in sources.

Longa.py
Changes duration of the final note in each part to a longa.

Remove_attributes.py
A cleanup routine:  removes meter from incipit, removes page dimensions from <scoreDef>, removes ppq [pulses per quarter] attributes from <scoreDef>, removes key.mode from <staff Def>, removes color from all notes and rests.

Remove_elements.py
Removes all <annot> elements with type= “bracket”  Removes all <meterSig> elements. Removes all empty <persName> elements.  Removes all syllables with no text inside.  Removes all <anchoredText> elements.  Removes all <pb> [page break] elements.  Removes all <sb> [system break] elements.

Responsibility.py
Adds @func and @resp attributes to editorial accidentals

Sources.py
Add the sourceDesc element, source elements, and editor elements to MEI header.

Supplied.py
Returns list of all staff objects marked as supplied (as part of encoding of variants and emendations).

Transform.py
Runs all other routines, in sequence

Variants.py
Uses list of alternate readings to find variants, then reorganizes the MEI to group them using <app> and <rdg> elements.






Usage
=====

To massage a single MEI file, run the `massage_mei.py` script in the `massage`
directory. This script must take the name of the input file as a parameter;
an output filename can be specified by adding `--out new_file_name.mei`
(where you can substitute the output file name for `new_file_name.mei`.)

To switch on or off any of the functions in the MEIMassaging
modules, simply edit the relevant parameters passed to `TransformData`—
or leave them out to use default settings.

To massage an entire directory, you can use the `massage_dir.py` script.
This also can take the `--out` parameter; instead of files, however, input
and output _directories_ are indicated. `--ignore`, `--filter` and `-R`
(for “recursive”) are additional, advanced optional parameters that are
available.

A simpler script, `massage_script.py`, runs massaging of a single file
multiple times on the shell. This is not as powerful as `massage_dir.py`,
but is potentially useful since a single segfault will not cause
massaging of the entire directory to fail.
