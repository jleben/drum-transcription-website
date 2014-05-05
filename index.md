---
---

<link rel='stylesheet' href="css/style.css"/>

# Drum Transcription System

Authors: **Jakob Leben** and **Robert Van Rooyen**, University of Victoria,
Canada

## Overview

This software system provides central components for drum transcription from
audio recordings into a symbolic form (MIDI). In addition, it provides
assistance in further software development by evaluation of the transcription
quality and visualization of data at different stages of audio processing.

The system consists of three main software components with the following roles:

- **Audio Processor** - drum hit detection in raw audio
- **Symbolic Processor** - transcription to MIDI and quality evaluation
- **Sonic Visualiser plugin** - audio processing visualization

The diagram below shows the complete workflow (from top to bottom) of
drum transcription and quality evaluation. Blue components represent
software provided by our system while green components are external to the
system. Gray parts represent intermediate data formats:

<div style="text-align:center" markdown="1">
![Graph: System Overview](images/architecture.jpg)
</div>

## Drum Hit Detection

The audio processor takes as input an audio file containing a recording of a
musical performance on drums. It analyses the audio to extract features which
allow detection of individual drum hits and their properties.

Currently, three types of drum sounds can be distinguished:

- kick drums or tom-toms
- snare drums
- hi-hats or cymbals.

The output is a list of detected drum hits with the following information:

1. time of occurence
2. estimated drum type
3. estimated hit strength

The output is in form of a file in the CSV (Comma-Separated Value) format. For
example:

    0.00580499,0,0.627796
    0.272834,2,0.12992
    0.516644,2,0.141721
    0.667574,2,0.138631
    0.748844,1,0.630138
    1.00426,2,0.195278
    1.24807,2,0.143118
    1.5151,0,0.618974

## Visualization

The Sonic Visualiser plugin provides insight into the operation of the audio
processor and allows exploration of its parameters. It re-uses the audio
processor\'s components to visualize the data at various stages.
Processing parameters can be tweaked and the resulting data is immediately
re-plotted.

<div style="text-align:center" markdown="1">
![Sonic Visualiser Screenshot](images/sonic_vis.png)
</div>

## Transcription and Evaluation

The symbolic processor operates on information about individual drum hits
to transcribe it into MIDI and evaluate the transcription by comparison
to a reference file. This is done with the aid of a CSV file that defines
the mapping between the detected drum types and hit strengths on one side
and MIDI instrument numbers and note velocities on the other side.

### Transcription

The input to the transcription process is the output of the audio processor.
With the aid of the mapping file, a MIDI file is generated with notes
corresponding to the detected drum hits. The MIDI file can easily be loaded into
a digital audio workstation (DAW) for resynthesis.

### Evaluation

Statistical evaluation of transcription quality requires an additional CSV or
MIDI file to serve as reference to compare the audio processor\'s output with.
This can be the result of annotation of the drum recording in a DAW. However,
for highly controlled testing of the system, it is typical to create reference
MIDI scores first and then synthesize them into audio to serve as input to the
transcription system. This avoids the potential ambiguity of real recordings and
imprecision in the process of manual annotation.

The result of quality evaluation contains:

- accuracy, precision and recall of drum hit detection
- accuracy of drum type estimation
- accuracy of hit strength estimation
- confusion matrix of detected versus true events, by type

Example evaluation output:

    Onset: A = 100.0% | P = 100.0% | R = 100.0%
    Type = 66.7%
    Strength = 66.7%
    Confusion matrix:
    ref/det       0       1       2   other  missed
          0       8       0       0       0       0
          1       0       8       0       0       0
          2       8       8      16       0       0
      other       0       0       0       0       0
      ghost       0       0       0       0

## Examples

The audio examples in this section demonstrate the effectiveness of the
transcription system.

### 1. Synthetic

This example is based on a MIDI score as an origin. The original score is
synthesized into audio and then passed through the system. Finally, the
resulting MIDI transcription is resynthesized again:

- [Original](audio/kshtc1203.wav) - The audio synthesized from the original MIDI
score.
- [Original+Resynthesis](audio/kshtc1203_resynthesis.wav) - Resynthesized
    transcription on the left channel and synthesized original on the right
    channel.
- [MIDI transcription](midi/kshtc1203_resynthesis.mid)

### 2. Natural

This example is based on transcription of a real popular music recording.
The drum hits are well-matched in time, however the drum type recognition is not
perfect - it is made more difficult by the presence of other instruments
and voice:

- [Original+Resynthesis](audio/country00063_resynth.wav) - Original audio
    on the left channel and resynthesized drums on the right channel.
- [MIDI transcription](midi/country00063_resynthesis.mid)

## Acknowledgements

The [Marsyas framework](http://marsyas.info/) is used for composition and
coordination of audio processing components in the drum hit detector and the
Sonic Visualiser plugin.

This work is a result of the Music information Retrieval course (CSC 575)
at the University of Victoria, Canada, instructed by prof. George Tzanetakis.

