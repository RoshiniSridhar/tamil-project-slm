Tamil Handwritten Character Recognition with Confidence-Aware SLM Validation

Overview

This project implements a handwritten Tamil character recognition pipeline using classical computer vision and machine learning. The current implementation focuses on 30 Tamil character classes and combines skeleton-based feature extraction with a K-Nearest Neighbors (KNN) classifier.

A Small Language Model (SLM) is incorporated as a validation and decision-support layer for uncertain predictions. The SLM does not independently recognize the image; instead, it receives the vision model's Top-K candidates and confidence-related metadata to analyze ambiguous outputs.

The project is designed as a foundation for future expansion to all 247 Tamil characters and manuscript-level transcription.
   
Key Components

1. Dataset

The current dataset contains isolated handwritten Tamil characters grouped into 30 classes.

The implemented character mapping includes:

Tamil vowels

Tamil consonant base forms

The dataset is split into training and testing sets using a fixed random seed to ensure reproducibility.

2. Image Preprocessing

Each handwritten character image undergoes preprocessing before feature extraction.

The main operations include:

Grayscale image loading

Median filtering for noise reduction

Otsu thresholding

Foreground bounding-box detection

Character cropping

Aspect-ratio-preserving resizing

Centering on a fixed-size canvas

Morphological closing to bridge small gaps

The objective is to normalize differences in image position, scale, and minor stroke discontinuities.

3. Skeletonization

The preprocessed binary character image is converted into a one-pixel-wide skeleton.

Skeletonization provides a structural representation of the handwritten character and reduces the influence of stroke thickness.

The extracted skeleton is then converted into coordinate points.

4. Skeleton Path Ordering

Skeleton pixels do not naturally provide an ordered representation.

A graph-based method is used to:

Build neighborhood relationships between skeleton points

Identify connected components

Select the main component

Identify endpoints

Traverse the structure to produce an ordered path

This ordered path provides a representation of the character's geometric structure.

5. Point Reduction

The ordered skeleton path may contain a large number of points.

Polygon approximation is used to reduce redundant points while preserving the overall shape of the character.

This reduces computational complexity before curve fitting.

6. B-Spline Feature Extraction

The reduced path is fitted using a B-spline representation.

The resulting curve is sampled at a fixed number of points and then normalized for:

Translation

Scale

The sampled coordinates are flattened to create a fixed-length numerical feature vector for classification.

7. KNN Vision Classifier

The extracted feature vectors are standardized using StandardScaler.

A K-Nearest Neighbors classifier is then trained on the feature vectors.

The classifier provides:

Top-1 prediction

Top-K candidate predictions

Class probabilities

Neighbor distances

These outputs form the primary vision-recognition layer.

8. Performance Evaluation

The project evaluates recognition performance using:

Top-1 accuracy

Top-3 accuracy

Top-5 accuracy

Classification reports

Confusion matrices

The current implementation achieved approximately:

Metric

Result

Top-1 Accuracy

~61%

Top-3 Accuracy

~81%

Top-5 Accuracy

~83%

These results demonstrate that several errors made by the Top-1 classifier still contain the correct character within the candidate list.

9. Confidence Analysis

Predictions are divided into confidence groups:

HIGH

MEDIUM

LOW

The confidence analysis considers information such as:

Top-1 probability

Top-2 probability

Confidence gap

Nearest-neighbor distance

Mean neighbor distance

This allows the system to distinguish between relatively reliable predictions and ambiguous predictions.

The routing concept is:

HIGH confidence
Accept vision prediction

MEDIUM / LOW confidence
Send candidate information for validation

10. SLM-Based Validation Agent

The Small Language Model (SLM) is used as a validation and decision-support agent.

For ambiguous cases, the agent can receive:

Predicted class

Top-K candidate classes

Candidate probabilities

Confidence gap

Minimum visual distance

Mean class distance

Character metadata

The SLM then provides a structured decision based on the evidence supplied by the vision system.

Important Limitation

For the current dataset, each sample is an isolated character. Therefore, the SLM does not have sufficient linguistic context to independently determine the correct character.

The SLM should therefore be treated as:

An uncertainty-aware validation and decision-support layer, rather than an independent visual recognition model.

The current project does not claim that the SLM improves accuracy unless such improvement is measured experimentally against ground-truth labels.

Future Work

1. Expansion from 30 to 247 Tamil Characters

The current classifier can be extended to cover the full set of Tamil characters.

Future work would require:

A larger labelled dataset

Additional handwritten samples per class

Re-training and hyperparameter tuning

Improved handling of visually similar characters

Per-class performance analysis

2. Tamil Manuscript Recognition

The next major extension is recognition of historical or handwritten Tamil manuscripts.

The input may consist of complete pages, lines, or word images rather than isolated characters.

The proposed pipeline is:

Binarized Tamil Manuscript
Page / Region Processing
Line Segmentation
Word Segmentation
Character / Recognition Units
Vision Recognition
Top-K Candidates per Position

3. Candidate Word Sequence Generation

When multiple characters form a word, the vision model can produce Top-K candidates for each position.

For example:

Position 1 -> Top-K candidates
Position 2 -> Top-K candidates
Position 3 -> Top-K candidates
...

Candidate character sequences can then be generated from these alternatives.

This provides the linguistic context that is currently unavailable in isolated-character recognition.

4. Tamil Corpus and Retrieval Layer

A Tamil text corpus or lexicon can be used to retrieve linguistic evidence for candidate sequences.

The retrieval layer may provide:

Exact word matches

Approximate word matches

Word frequency

Linguistic plausibility

Similar known sequences

The pipeline would become:

Vision Top-K Candidates
       
Candidate Sequence Generation
        
Tamil Corpus / Lexicon Retrieval
       
Relevant Linguistic Evidence

5. Context-Aware SLM Validation

In the future manuscript-level system, the SLM would have a more meaningful role.

The SLM could receive:

Top candidate transcriptions

Vision confidence scores

Distance-based evidence

Retrieved corpus matches

Word frequency information

Previous and next words or sentence context

The architecture would be:

Vision Model
    
Candidate Character Sequences
   
Corpus / Lexicon Retrieval
  
Visual + Linguistic Evidence
   
SLM Validation Agent
   
Final Validated Transcription

The roles are clearly separated:

Vision model: What does the handwriting visually resemble?

Retrieval layer: What Tamil words or sequences are linguistically plausible?

SLM agent: Given the visual and linguistic evidence, which candidate is most plausible?

Proposed Future End-to-End Architecture

       Tamil Handwritten Manuscript
                  |
                  v
          Image Preprocessing
                  |
                  v
       Line / Word Segmentation
                  |
                  v
        Character Recognition Model
          (Expanded to 247 classes)
                  |
                  v
       Top-K Predictions per Unit
                  |
                  v
      Candidate Sequence Generation
                  |
                  v
        Tamil Corpus Retrieval
                  |
                  v
      Linguistic Evidence Extraction
                  |
                  v
          SLM Validation Agent
                  |
                  v
      Final Tamil Transcription

Conclusion

The current work establishes the character-level foundation of a hybrid handwritten Tamil recognition system.

The implemented system combines:

Image preprocessing

Skeleton-based structural analysis

B-spline feature extraction

KNN classification

Top-K candidate generation

Confidence-aware routing

SLM-based validation

The next phase of the project can expand the system from isolated 30-character recognition to all 247 Tamil characters and eventually to complete handwritten manuscripts.

At the manuscript level, candidate sequences can be formed from the vision model's Top-K predictions. A Tamil corpus can retrieve relevant linguistic evidence, and an SLM can act as a validation agent to resolve ambiguous recognition outputs using both visual and contextual information.

This provides a scalable roadmap from isolated character recognition to context-aware Tamil manuscript transcription.
