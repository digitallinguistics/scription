# Scription

[![GitHub release](https://img.shields.io/github/release/digitallinguistics/scription.svg)][releases]
[![DOI](https://zenodo.org/badge/175884660.svg)][Zenodo]
[![GitHub](https://img.shields.io/github/license/digitallinguistics/scription.svg)][license]
[![GitHub stars](https://img.shields.io/github/stars/digitallinguistics/scription.svg?style=social)][GitHub]

This document specifies a simple text format for representing linguistic texts as interlinear glossed examples. This format, known as _scription_ (a term coined by [Patrick J. Hall][Pat Hall] (University of California, Santa Barbara)), makes it easy to quickly enter data. It is easily read by humans, and easily converted to other formats used in documentary linguistics.

At its simplest, a scription file is just a basic interlinear gloss. Below is a valid scription file containing a single utterance in Chitimacha:

```
waxdungu qasi
waxt-qungu qasi
day-one    man
one day a man
```

However, the scription format supports much more complicated interlinear glosses, as well as the ability to specify metadata about the text. Click the example link below to view a slightly more complex scription file.

[View the example scription file.][example]

The complete specification for formatting valid scription files is given below.

_**Note:** You may also be interested in the [`scription2dlx` JavaScript library][scription2dlx], which converts scription files to the Data Format for Digital Linguistics (DaFoDiL)._

Cite this format using the following model:

> Hieber, Daniel W. 2019. digitallinguistics/scription. DOI:[10.5281/zenodo.2595548][Zenodo]

## Contents

* [File Extension / Media Type](#file-extension--media-type)
* [Header](#header)
* [Interlinear Gloss Schema](#interlinear-gloss-schema)
* [Utterances](#utterances)
* [Lines](#lines)
  - [Utterance Metadata (`#`)](#utterance-metadata-)
  - [Speaker (`\sp`)](#speaker-sp)
  - [Transcript (`\trs`)](#transcript-trs)
  - [Phonemic Transcription (Utterance) (`\txn`)](#phonemic-transcription-txn)
  - [Phonetic Transcription (Utterance) (`\phon`)](#phonetic-transcription-phon)
  - [Phonemic Transcription (Word) (`\w`)](#word-transcription-w)
  - [Morphemic Analysis (`\m`)](#morphemic-analysis-m)
  - [Glosses (`\gl`)](#glosses-gl)
  - [Literal Word Translation (`\wlt`)](#literal-word-translation-wlt)
  - [Literal Translation (`\lit`)](#literal-translation-lit)
  - [Free Translation (`\tln`)](#free-translation-tln)
  - [Note (`\n`)](#note-n)
  - [Source (`\s`)](#source-s)
* [Emphasis](#emphasis)

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

Each text has an *interlinear gloss schema* that tells readers or parsers what each line in an utterance represents. The interlinear gloss schema is always inferred from the first utterance in the text. Subsequent utterances are then assumed to follow the same schema unless otherwise specified.

Users can specify an interlinear gloss schema using backslash codes at the beginning of each line in an utterance, followed by one or more spaces or tabs, and then the data for that line. Consider the following example text:

```
\txn   ninakupenda
\m     ni-na-ku-pend-a
\gl    1SG.SUBJ-PRES-2SG.OBJ-love-IND
\tln   I love you

ninaenda
ni-na-end-a
1SG-PRES-go-IND
I am going
```

This text has 2 utterances, separated by a blank line. The lines in the first utterance are preceded by backslash codes indicating the function of each line. This schema tells readers and parsers that the lines in this utterance are a phonemic transcription of the utterance (`\txn`), followed by a morphemic analysis (`\m`) and glosses (`\gl`), and finally a free translation (`\tln`). The second utterance is then assumed to follow the same schema, so it does not need backslash codes.

By default, an utterance with only 2 lines is assumed to follow this schema:

```
\txn
\tln
```

An utterance with 3 lines is assumed to follow this schema:

```
\m
\gl
\tln
```

An utterance with 4 lines is assumed to follow this schema:

```
\txn
\m
\gl
\tln
```

The complete list of supported backslash codes is listed in the [Lines](#lines) section. If a backslash code appears more than once in a schema, each instance must have a language or orthography specified. (For example, an utterance with both `\tln-en` and `\tln-es` would be valid, but an utterance with `\tln` and `\tln-es` would not be valid.) Editors and parsers may support additional backslash codes, but other editors and parsers are not required to support them. Parsers which encounter invalid backslash codes should throw an error. When parsers encounter an undefined backslash code, however, they should not throw an error; parsers may either ignore the line, or attempt to process the data.

Each backslash code must consist of a backslash `\`, followed immediately by the code indicating the type of line (ex: `gl`, `txn`), and optionally a hyphen followed by an abbreviation or [ISO language tag][language-tag], depending on the line. Backslash codes may only contain basic alphanumeric characters (A-Z, a-z; no diacritics) and numbers (0-9). Some examples of backslash codes are below:

  - `\gl` - The glosses line
  - `\txn-practical` - The phonemic transcription line, in the practical orthography for the language
  - `\tln-es` - The translation line, in Spanish

If one line in an utterance includes a backslash code, all the other lines in that utterance must have one as well. Parsers should throw an error if they encounter an utterance where only some of the lines begin with backslash codes.

If an individual utterance in a text follows a different schema than the one specified in the first utterance, the user must indicate the function of each line by including the backslash code at the beginning of the line. This is most useful when a specific utterance requires an extra line in the interlinear gloss, for whatever reason.

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

This will only affect the interlinear gloss schema for this specific utterance. All other utterances in the text will be assumed to continue following the same schema as the interlinear gloss schema in the first utterance.

If the first utterance in a text happens to follow a different interlinear gloss schema than the rest of the utterances in the text, users can simply provide a schema with no data, like so:

```
\txn
\m
\gl
\tln
```

In this case, parsers should use this utterance only for the purpose of inferring the interlinear gloss schema; they should not treat it as data.

## Utterances

Following the header and one or more line breaks is the collection of utterances in the text, each represented as an interlinear glossed utterance. The collection of utterances may be empty.

Each interlinear glossed utterance is a set of lines of text, and each utterance is separated from other utterances by one or more blank lines. The lines within an interlinear glossed utterance must not be separated by blank lines. To indicate that there is no data for a line, include that line's backslash code, and leave the rest of the line blank, like so:

```
\txn hujambo
\gl
\tln hello
```

Each utterance may only contain one line of each type and orthography/language, with the exception of the note line (`\n`). Users may include multiple note lines, but each must be preceded by the `\n` backslash code.

The first utterance in a text is always used to infer the interlinear gloss schema for the text. Parsers should assume that each line in an interlinear glossed utterance corresponds to the same number line in the interlinear gloss schema. For example, a scription file using the default schema (see above) should treat the first line in an interlinear gloss as the morphemic analysis, the second as the glosses, and the third as the translation.

If an utterance contains an extra line (that is, one more line than specified in the interlinear gloss schema), that line should be treated as a note line (`\n`). The behavior of parsers for any additional lines is undefined; parsers may choose to attempt to process that data or not.

## Lines

This section provides guidelines on formatting each line of an interlinear glossed utterance. Lines will have different formatting requirements depending on their type.

Words on a line may be grouped together using square brackets (`[ ]`). Multiple words that are grouped using square brackets must be treated as one word for the purpose of aligning items in an interlinear gloss. In the following example, the first and last name of a person are grouped together into a single unit, and given the gloss `NAME`.

```
\trs Qix kapx [John Smith].
\txn qix kapx [John Smith]
\gl  1SG name NAME
\tln My name is John Smith.
```

### Utterance Metadata: `#`

Each utterance may be preceded by a metadata line, beginning with a hash (`#`). This can be used to indicate the name of the language of that utterance, the language family, or other notes or metadata about the utterance. This is most useful when the scription text contains a collection of unrelated examples in different languages. An example of an utterance with a metadata line is shown below.

```
# Chitimacha (isolate; Louisiana)
waxdungu qasi
one day a man
```

The format of the data contained within this line is unspecified. The behavior of parsers with respect to this line is undefined, except that parsers should not throw an error if this line is encountered. It is recommended that parsers ignore this line by default.

### Speaker: `\sp`

This line consists of an abbreviation for the person who spoke the utterance, usually their initials. The speaker line may not be in multiple languages or writing systems. It may contain only the letters `a-z`, `A-Z`, and numbers `0-9`. No spaces are allowed. If you need to provide more details about a speaker, you can do so in the metadata header at the beginning of the text.

### Transcript: `\trs`

A transcript of this utterance, including things like prosodic markup, overlap, pauses, and various other discourse features. The transcript may be in multiple orthographies or representational systems. For example, you might have a transcript in both Discourse Functional Transcription (DFT) and Conversation Analysis (CA) formats, which might be represented on two different lines as `\trs-dft` and `\trs-ca` respectively.

### Phonemic Transcription: `\txn`

A phonemic transcription of the utterance. Punctuation and capitalization should be avoided in this line. This line should not be broken into morphemes, and should not contain extra white space to align words (use the `\w` line for that instead). (Morpho)phonological sound changes should be represented in this line. In other words, this line serves as a phonemic transcription of the utterance as the speaker actually pronounced it. Do not include phonemic slashes (`/ /`) in this line.

This line may be used with multiple orthographies. For example, a language which has a practical writing system may have both `\txn-practical` and `\txn-ipa`, to represent each utterance in both the practical orthography and in IPA. It is recommended but not required that orthography abbreviations be valid [ISO language tags][language-tag] (for example, `\txn-x-practical`). However, sometimes this is impractical or unreadable.

### Phonetic Transcription: `\phon`

A phonetic transcription of the utterance. This transcription must be in IPA; it may not be used with multiple orthographies. Do not include phonetic brackets (`[ ]`) in this line.

### Word Transcription: `\w`

A phonemic transcription of each word in the utterance. This line may be in multiple orthographies. Words in this line are often separated by additional white space, to vertically align words. Otherwise, this line typically contains the same data as the utterance's phonemic transcription line (`\txn`). This line should not contain morpheme breakdowns (use the `\m` line instead). Do not include phonemic slashes (`/ /`) in this line.

### Morphemic Analysis: `\m`

This line shows the individual morphemes in an utterance, separated by hyphens, equal signs, or other symbols recognized as valid glossing symbols by the [Leipzig Glossing Rules][Leipzig]. Words may be separated by one or more white spaces or tabs (useful for aligning words vertically for readability). If this line is present, the glosses line (`gl`) must also be present. This line must contain the same number of words as the glosses line and the literal word translation line (if present). Each word within the utterance must also contain the same number of morphemes as the corresponding word in the glosses line.

The morphemes line may be represented in more than one orthography. For example, in a language that has a practical writing system, a user might include both a `\m-practical` and `\m-ipa` line, for the practical orthography and IPA respectively. It is recommended but not required that orthography abbreviations be valid [ISO language tags][language-tag] (for example, `\m-x-practical`). However, sometimes this is impractical or unreadable.

Data should be entered in this line using regular hyphens (U+2010) rather than non-breaking hyphens (U+2011), for ease of entry. Tools may replace regular hyphens with non-breaking hyphens for display purposes, but must not alter the original data by replacing the original, regular hyphens. If non-breaking hyphens are included in the data for this line, they must be treated as word characters rather than as morpheme separators or punctuation.

### Glosses: `\gl`

This line shows the glosses for each morpheme in the morphemic analysis (`\m`) line, separated by hyphens, equal signs, or other symbols recognized as valid glossing symbols by the [Leipzig Glossing Rules][Leipzig]. Words may be separated by one or more white spaces or tabs (useful for aligning words vertically for readability). If this line is present, the morphemic analysis line must also be present. This line must contain the same number of words as the morphemic analysis line and the literal word translation line (if present). Each word within the utterance must also contain the same number of glosses as the corresponding word in the morphemic analysis line.

Grammatical glosses should be written in CAPS. Lexical glosses should avoid capitalization. Personal names should be glossed `NAME` or with their literal meaning. Affixes whose meaning is unknown or uncertain may be glossed `??`, although other glosses are acceptable (for example, `aff1`, `aff2`, etc.).

Data should be entered in this line using regular hyphens (U+2010) rather than non-breaking hyphens (U+2011), for ease of entry. Tools may replace regular hyphens with non-breaking hyphens for display purposes, but must not alter the original data by replacing the original, regular hyphens. Non-breaking hyphens are not permitted on this line.

The glosses line may be represented in multiple languages. For example, an utterance with glosses in both English and Spanish might have the lines `\gl-en` and `\gl-es`. Language abbreviations must be valid [ISO language tags][language-tag].

If the same gloss appears twice within a word, it should be treated as a discontinuous morpheme (ex: a circumfix or transfix). The following examples in illustrate this use:

```
# Lakota
na-wíčha-wa-xʔu̧
hear-3PL.UND-1SG.ACT-hear
I hear them
```

```
# Darfur Arabic
t-u-r-u-g
way-PL-way-PL-way
ways
```

To avoid this behavior, you can change the gloss of one of the morphemes (ex: `PL^1` and `PL^2`).

Infixes are also supported, using the angle brackets convention specified in the [Leipzig Glossing Rules][Leipzig]:

```
# Tagalog
b<um>ili
<FOC>buy
buy
```

### Literal Word Translation: `\wlt`

A word-by-word literal translation of the utterance. Literal translations of each word must not contain white space; words within each translation may be separated by periods, hyphens, underscores, or other characters. This line must have the same number of words as the morphemic analysis and glosses lines. An example utterance with literal word translations is shown below.

```
\m   naakxte-m-puy-na
\gl  write-PLACT-PAST.IPFV-3PL
\wlt they.usually.write.with.it
\tln a pen/pencil
```

### Literal Translation: `\lit`

The literal translation for this utterance. Do not include brackets (`[ ]`) or quotes (`‘ ’`) around the data for this line, unless using quotes for reported speech. This line may be represented in multiple languages. For example, an utterance with a translation in both Spanish and English might have the lines `\tln-spa` and `\tln-eng`. Language abbreviations must be valid [ISO language tags][language-tag].

### Free Translation: `\tln`

The free translation for this utterance. Do not include brackets (`[ ]`) or quotes (`‘ ’`) around the data for this line, unless using quotes for reported speech. This line may be represented in multiple languages. For example, an utterance with a translation in both Spanish and English might have the lines `\lit-spa` and `\lit-eng`. Language abbreviations must be valid [ISO language tags][language-tag].

### Note: `\n`

A note about this utterance. Note lines may be in multiple languages (ex: `\n-en` and `\n-es`). If the language is absent, parsers should assume that the language is English (`en`). The language tag, if present, must be a valid [ISO language tag][language-tag].

The source of the note may also be indicated at the beginning of the note text, followed by a colon (`:`). This should be the initials of the person who was the source of the note, and may contain only basic Latin characters (A-Z, a-z).

Some examples of note lines are below.

```
What would this utterance mean if the verb were perfect?
DWH: Is this utterance past tense or present tense?
\n MM: I think this is plural.
\n-swa Sentensi hii ni kuhusu bwana yule.
```

### Source (`\s`)

The source line is used to indicate the bibliographic source of the utterance. This is most useful when the scription file consists of a collection of utterances from different texts or publications, as often happens when preparing a set of examples for typological publications. This line would typically be included immediately after an interlinear glossed example in a publication. It may only be in a single language.

## Emphasis

Emphasis may be added on any lines containing linguistic data by adding asterisks (`*`) around the emphasized item or portion of the data:

```
*wax*dungu qasi
*waxt*-qungu qasi
*day*-one man
one *day* a man
```

Information about emphasis is most useful when preparing specific pieces of data for publication. Because the location of emphasis needs to vary from example to example and publication to publication, authors should **not** mark up their original data with emphasis. Instead they should create a new scription file containing the examples to be used in the publication, and indicate emphasis there.

For the following lines (utterance-level data), asterisks may occur anywhere in the data:

- transcript (`\trs`)
- phonemic transcription (`\txn`)
- phonetic transcription (`\phon`)
- literal translation (`\lit`)
- free translation (`\tln`)

For the following lines (word-level data), pairs of asterisks may only appear at word and morpheme boundaries. Asterisks placed elsewhere should be stripped from the data and ignored by parsers.

- word transcription (`\w`)
- morphemic analysis (`\w`)
- glosses (`\gl`)
- literal word translation (`wlt`)

If an odd number of asterisks is found, they should be stripped from the data and ignored.

Asterisks are for presentational purposes only, and parsers must **not** save asterisks as part of the linguistic data for the utterance. However, parsers may choose to utilize information about emphasis in other ways, or save that information in separate fields.

[DaFoDiL]:       https://format.digitallinguistics.io/schemas/Text.html
[example]:       https://github.com/digitallinguistics/scription/blob/master/example.txt
[GitHub]:        https://github.com/digitallinguistics/scription
[Leipzig]:       https://www.eva.mpg.de/lingua/resources/glossing-rules.php
[language-tag]:  https://www.w3.org/International/articles/language-tags/
[license]:       https://github.com/digitallinguistics/scription/blob/master/LICENSE.md
[Pat Hall]:      https://github.com/amundo
[releases]:      https://github.com/digitallinguistics/scription/releases
[scription2dlx]: https://developer.digitallinguistics.io/scription2dlx/
[YAML]:          https://yaml.org/start.html
[Zenodo]:        https://zenodo.org/badge/latestdoi/175884660
