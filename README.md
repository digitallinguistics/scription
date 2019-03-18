# Scription

[![GitHub release](https://img.shields.io/github/release/digitallinguistics/scription.svg)][releases]
[![DOI](https://zenodo.org/badge/175884660.svg)][Zenodo]
[![GitHub](https://img.shields.io/github/license/digitallinguistics/scription.svg)][license]
[![GitHub stars](https://img.shields.io/github/stars/digitallinguistics/scription.svg?style=social)][GitHub]

This document specifies a simple text format for representing linguistic texts as interlinear glossed examples. This format, known as `scription` (a term coined by [Patrick J. Hall][Pat Hall] (University of California, Santa Barbara)), makes it easy to quickly enter data. It is easily read by humans, and easily converted to other formats used in documentary linguistics.

[View the example scription file.][example]

You may also be interested in the [`scription2dlx` JavaScript library][scription2dlx], which converts scription files to the Data Format for Digital Linguistics (DaFoDiL).

Cite this format using the following model:

> Hieber, Daniel W. 2019. digitallinguistics/scription. DOI:[10.5281/zenodo.2595548][Zenodo]

## File Extension / Media Type

Scription files should be treated as plain text files (`text/plain`) and given the `.txt` extension. Using other extensions such as `.scription` or `.text` is not recommended.

## Header

Each scription file may begin with a header containing metadata about the text, between two triple dashes (`---`). For example:

```
---
title: How the world began
---
```

The header content should consist of metadata about the text, in [YAML format][YAML]. The properties included in the header must use the field names recommended for linguistic texts specified by the [Data Format for Digital Linguistics][DaFoDiL], with the exception that the `utterances` property must NOT be included. Some examples of attributes that users might include are the `title`, `abbreviation`, and `dateRecorded` properties.

If present, the header may not be empty. At a minimum, a `title` property is required.

## Interlinear Gloss Schema

An optional *interlinear gloss schema* may be included after the header and one or more line breaks. This schema tells parsers what each line in your subsequent interlinear glossed utterances represents. If no schema is provided, utterances with 3 lines should be assumed to follow this schema:

```
\schema
\morph
\gl
\tln
```

The above schema tells parsers that each interlinear glossed example in the text will have 3 lines unless otherwise specified, and that those three lines will be the morpheme breakdown for the utterance (`\morph`), the glosses for the utterance (`\gl`), and the translation for the utterance (`\tln`), in that order.

If no schema is provided, and an utterance has only 2 lines, those two lines should be assumed to be the transcription (`\txn`) and the translation (`\tln`) lines respectively (**not** the morpheme breakdown and translation).

The complete list of supported backslash codes is listed in the [Lines](#lines) section. Each backslash code may only appear once in a schema (different orthographies or languages for the same code count as distinct backslash codes). Editors and parsers may support additional backslash codes, but other editors and parsers are not required to support them. Custom backslash codes must only contain ASCII characters; parsers which encounter invalid backslash codes should throw an error. When parsers encounter an undefined backslash code, however, they should not throw an error; parsers may either ignore the line, or attempt to process the data.

Each line in the schema must consist of a backslash `\`, followed immediately by the code indicating the type of line (e.g. `gl`, `txn`), and optionally a hyphen followed by the abbreviation of a writing system or language (e.g. `-spa`, `-modern`), depending on the code. Abbreviations may only contain ASCII characters and numbers. No other content is permitted on the line. Some examples of backslash codes are below:

  - `\gl` - The glosses line
  - `\txn-practical` - The transcription line, not broken down into morphemes, in the practical orthography for the language
  - `\tln-spa` - The translation line, in Spanish

## Utterances

Following the interlinear gloss schema and one or more line breaks is the collection of utterances in the text, each represented as an interlinear glossed utterance. The collection of utterances may be empty.

Each interlinear glossed utterance is a set of lines of text, and each utterance is separated from other utterances by one or more blank lines. The lines within an interlinear glossed utterance must not be separated by blank lines.

Parsers should assume that each line in an interlinear glossed utterance corresponds to the same number line in the interlinear gloss schema. For example, a scription file using the default schema (see above) should treat the first line in an interlinear gloss as the morpheme break, the second as the glosses, and the third as the translation.

If an utterance contains an extra line (that is, one more line than specified in the interlinear gloss schema), that line should be treated as a note line (`\n`). The behavior of parsers for any additional lines is undefined; parsers may choose to attempt to process that data or not.

By default, lines within an utterance do not need to be preceded by backslash codes (e.g. `\morph`, `\gl`, etc.). However, if an individual utterance follows a different schema than the one specified in the interlinear gloss schema, the user can indicate which line is which by including the backslash code at the beginning of the line, followed by one or more spaces or tabs, and then the data for that line. This is most useful when a specific utterance requires an extra line in the interlinear gloss, for whatever reason. If one line in an utterance includes a backslash code, all the other lines in that utterance must have one as well. Parsers should throw an error if they encounter an utterance where only some of the lines begin with backslash codes.

As an example, consider a scription file using the default interlinear gloss schema. Most utterances will look something like this:

```
kˀiht-ik
want-1SG
I want
```

If, however, one utterance contains a morphophonological change, the user may choose to add a fourth line to the interlinear gloss for that specific utterance, like so:

```
\txn ʔučaːši
\m   ʔuči-ʔiš-i
\gl  do-IPFV-3SG
\tln he did it
```

Note that the following format is also valid:

```
\txn ʔučaːši
\m ʔuči-ʔiš-i
\gl do-IPFV-3SG
\tln he did it
```

Each utterance may only contain one line of each type and orthography/language, with the exception of the note line (`\n`). Users may include multiple note lines, but each must be preceded by the `\n` backslash code.

## Lines

Lines within an interlinear gloss must be formatted in different ways, depending on the line type. This section provides guidelines on formatting each line of an interlinear glossed utterance. The sections are in their recommended order for an interlinear gloss.

### Speaker: `\sp`

This line consists of an abbreviation for the person who spoke the utterance, usually their initials. The speaker line may not be in multiple languages or writing systems.

### Transcript: `\trs`

A transcript of this utterance, including things like prosodic markup, overlap, pauses, and various other discourse features. The transcript may be in multiple orthographies or representational systems. For example, you might have a transcript in both Discourse Functional Transcription (DFT) and Conversation Analysis (CA) formats, which might be represented on two different lines as `\trs-dft` and `\trs-ca` respectively.

### Phonemic Transcription: `\txn`

A phonemic transcription of the utterance. Punctuation and capitalization should be avoided in this line. This line should not be broken into morphemes. (Morpho)phonological sound changes should be represented in this line. In other words, this line serves as a phonemic transcription of the utterance as the speaker actually pronounced it. Do not include phonemic slashes (`\ \`) in this line.

This line may be used with multiple orthographies. For example, a language which has a practical writing system may have both `\txn-practical` and `\txn-ipa`, to represent each utterance in both the practical orthography and in IPA.

### Phonetic Transcription: `\phon`

A phonetic transcription of the utterance. This transcription must be in IPA; it may not be used with multiple orthographies. Do not include phonetic brackets (`[ ]`) in this line.

### Morphemes: `\m`

This line shows the individual morphemes in an utterance, separated by hyphens, equal signs, or other symbols recognized as valid glossing symbols by the [Leipzig Glossing Rules][Leipzig]. Words may be separated by one or more white spaces or tabs (useful for aligning words vertically for readability). It is recommended that if this line is present, the glosses line be present also. This line must contain the same number of words as the glosses line (`\gl`), if present. Each word within the utterance must also contain the same number of morphemes as the corresponding word in the glosses line.

The morphemes line may be represented in more than one orthography. For example, in a language that has a practical writing system, a user might include both a `\m-practical` and `\m-ipa` line, for the practical orthography and IPA respectively.

Data should be entered in this line using regular hyphens (U+2010) rather than non-breaking hyphens (U+2011), for ease of entry. Tools may replace regular hyphens with non-breaking hyphens for display purposes, but must not alter the original data by replacing the original, regular hyphens. Non-breaking hyphens are not permitted on this line.

### Glosses: `\gl`

This line shows the glosses for each morpheme in the morphemes (`\m`) line, separated by hyphens, equal signs, or other symbols recognized as valid glossing symbols by the [Leipzig Glossing Rules][Leipzig]. Words may be separated by one or more white spaces or tabs (useful for aligning words vertically for readability). If this line is present, the morphemes line must also be present. This line must contain the same number of words as the morphemes line. Each word within the utterance must also contain the same number of glosses as the corresponding word in the morphemes line.

Grammatical glosses should be written in CAPS. Lexical glosses should avoid capitalization. Personal names should be glossed `NAME` or with their literal meaning. Affixes whose meaning is unknown or uncertain may be glossed `??`, although other glosses are acceptable (for example, `aff1`, `aff2`, etc.).

Data should be entered in this line using regular hyphens (U+2010) rather than non-breaking hyphens (U+2011), for ease of entry. Tools may replace regular hyphens with non-breaking hyphens for display purposes, but must not alter the original data by replacing the original, regular hyphens. Non-breaking hyphens are not permitted on this line.

### Literal Translation: `\lit`

The literal translation for this utterance. Do not include brackets (`[ ]`) or quotes (`‘ ’`) around the data for this line. This line may be represented in multiple languages. For example, an utterance with a translation in both Spanish and English might have the lines `\tln-spa` and `\tln-eng`.

### Free Translation: `\tln`

The free translation for this utterance. Do not include brackets (`[ ]`) or quotes (`‘ ’`) around the data for this line. This line may be represented in multiple languages. For example, an utterance with a translation in both Spanish and English might have the lines `\lit-spa` and `\lit-eng`.

### Note: `\n`

A note about this utterance. Each note line has the following structure:

`{source}-{language/orthography}: {note text}`

The `{source}` is the source of the note (usually the initials of a speaker, or a bibliographic reference), and is optional but strongly recommended. The source must contain only ASCII letters and numbers. The `{language/orthography}` is also optional. If absent, parsers should assume that the language is English (`eng`). If present, the source must be present as well. The language/orthography must contain only ASCII letters and numbers. If only the source is present, no hyphen is required. The colon may be followed by one or more tabs or spaces. The colon (`:`) may be omitted if both the source and language are absent. Some examples of notes are below:

```
DWH-eng: Is this utterance past tense or present tense?
MM:      I think this is plural.
KB-swa:  Sentensi hii ni kuhusu bwana yule.
What would this utterance mean if the verb were perfect?
```

[DaFoDiL]:       https://spec.digitallinguistics.io/schemas/Text.html
[example]:       https://github.com/digitallinguistics/scription/blob/master/example.txt
[GitHub]:        https://github.com/digitallinguistics/scription
[Leipzig]:       https://www.eva.mpg.de/lingua/resources/glossing-rules.php
[license]:       https://github.com/digitallinguistics/scription/blob/master/LICENSE.md
[Pat Hall]:      https://github.com/amundo
[releases]:      https://github.com/digitallinguistics/scription/releases
[scription2dlx]: https://developer.digitallinguistics.io/scription2dlx/
[YAML]:          https://yaml.org/start.html
[Zenodo]:        https://zenodo.org/badge/latestdoi/175884660
