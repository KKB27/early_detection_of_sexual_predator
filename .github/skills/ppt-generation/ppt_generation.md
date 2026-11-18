# ROLE
Act as a senior academic presentation designer, research presentation specialist, data scientist, and PowerPoint developer.

Your task is to thoroughly analyze my existing project and create a high-quality, professional, academically appropriate PowerPoint presentation explaining the complete project lifecycle.

IMPORTANT: Do not start creating slides immediately. First understand my project completely by exploring the available project files.

# PHASE 1: UNDERSTAND THE PROJECT

Explore the entire project directory, including:

- Source code and Python scripts.
- Jupyter notebooks.
- Dataset files and their schemas.
- Data preprocessing and cleaning implementations.
- Model architectures and training configurations.
- Training and validation logs.
- Evaluation metrics and classification reports.
- Confusion matrices.
- Generated visualizations and graphs.
- README files and project documentation.

Understand:
1. What problem the project addresses.
2. Why this problem is important.
3. What approach has been implemented.
4. What dataset is being used.
5. How the dataset is structured.
6. What preprocessing techniques are applied.
7. What models and experiments are implemented.
8. How the models are trained.
9. How the models are evaluated.
10. What the experimental results demonstrate.
11. What limitations and future improvements exist.

Do not assume anything that cannot be verified from the project files.

If multiple experiments or models exist, understand their differences and present them in a logical comparison.

# PHASE 2: PRESENTATION STRUCTURE

Create a coherent presentation with the following suggested slide sequence.

Adapt the number of slides based on the actual project complexity. Aim for approximately 18–22 slides.

## Section 1: Introduction
1. Title Slide
   - Project title.
   - Team members / author details (use existing information if available).
   - Department and institution.
   - Academic guide (if available).

2. Problem Background
   - Introduce the domain.
   - Explain the real-world context.
   - Explain why the problem matters.

3. Problem Statement and Motivation
   - Clearly define the problem.
   - Identify existing challenges.
   - Explain the motivation behind the proposed solution.

4. Project Objectives
   - Present the major objectives as concise bullet points.

## Section 2: Dataset Exploration
5. Dataset Overview
   - Dataset source.
   - Dataset size.
   - Number of samples.
   - Features and columns.
   - Target classes and their meanings, if established.

6. Exploratory Data Analysis (EDA)
   - Class distribution.
   - Data statistics.
   - Text/data length distribution, if applicable.
   - Important patterns and observations.
   - Relevant graphs.

7. Dataset Challenges
   - Class imbalance.
   - Missing or noisy data.
   - Duplicate records.
   - Data quality issues.
   - Other challenges identified during exploration.

## Section 3: Data Preprocessing
8. Preprocessing Pipeline
   - Explain every preprocessing stage.
   - Explain why each operation is necessary.
   - Show the transformation from raw data to model-ready data.

9. Data Preparation
   - Label encoding.
   - Train/validation/test splitting.
   - Tokenization or feature extraction.
   - Sequence length handling.
   - Other project-specific transformations.

10. Preprocessing Workflow Diagram
    - Create a clean, professional flowchart.
    - Show the complete data transformation pipeline.
    - Use arrows to communicate the sequence clearly.

## Section 4: Methodology and Model Architecture
11. Proposed System Architecture
    - Present the complete system architecture.
    - Show how input data flows through the system.
    - Include the major processing and prediction stages.

12. Model Architecture
    - Explain the actual model architecture.
    - Include important components and their functionality.
    - Use an architecture diagram where appropriate.

13. Training Configuration
    - Model parameters.
    - Hyperparameters.
    - Optimizer and loss function.
    - Batch size, epochs and learning rate.
    - Other relevant implementation details.

14. Experimental Setup
    - Explain the experiments conducted.
    - Describe differences between approaches.
    - Explain the reason for each experiment.

## Section 5: Evaluation and Results
15. Evaluation Methodology
    - Accuracy.
    - Precision.
    - Recall.
    - F1-score.
    - Macro and weighted metrics where relevant.
    - Explain why these metrics are appropriate for the problem.

16. Experimental Results
    - Present actual evaluation results.
    - Use well-designed comparison tables.
    - Compare different experiments or models.
    - Highlight important findings.

17. Confusion Matrix and Error Analysis
    - Include actual confusion matrices.
    - Explain true positives, false positives, true negatives and false negatives where applicable.
    - Identify important classification errors.

18. Result Visualizations
    - Training and validation loss.
    - Training and validation performance.
    - Class-wise performance.
    - Other meaningful project-specific graphs.

## Section 6: Conclusion
19. Key Findings and Discussion
    - What did the project achieve?
    - What did the experiments demonstrate?
    - What are the most important observations?

20. Limitations and Future Scope
    - Discuss genuine limitations.
    - Suggest realistic future improvements.

21. Conclusion
    - Summarize the project.
    - Reinforce its contribution and significance.

22. References
    - Dataset references.
    - Research papers.
    - Libraries and frameworks.
    - Other relevant sources.

# PHASE 3: DESIGN REQUIREMENTS

THIS IS EXTREMELY IMPORTANT.

I want a LIGHT THEME ONLY.

Do not use dark backgrounds, black presentation themes, or dark-mode aesthetics.

Design direction:

- Background: white or very light off-white.
- Primary heading color: dark navy blue.
- Secondary accent: muted blue.
- Supporting colors: subtle grey and restrained complementary colors.
- Use a clean academic and corporate visual style.
- Use consistent typography throughout the presentation.
- Use professional fonts such as Aptos, Arial, or Calibri.
- Maintain consistent margins, alignment and spacing.
- Use clear visual hierarchy.

## Typography and Content

Every slide must have:

- A clear, meaningful heading.
- A concise subtitle where useful.
- Properly structured bullet points.
- Consistent font sizes.
- Readable labels and captions.

Avoid:
- Long paragraphs.
- Excessive text.
- Overcrowded slides.
- Unnecessary decorative elements.
- Random icons.
- Excessive gradients.
- Unnecessary animations.
- Generic AI-generated presentation layouts.

Use bold text selectively to emphasize important technical terms and findings.

## Visualizations and Diagrams

Prefer actual project visualizations over decorative images.

Include:
- Dataset distribution charts.
- Data exploration graphs.
- Preprocessing flowcharts.
- System architecture diagrams.
- Model architecture diagrams.
- Training curves.
- Confusion matrices.
- Evaluation comparison charts.
- Other meaningful technical visualizations.

All charts must be readable, correctly labelled and accurately represent the underlying data.

Do not create fake graphs or illustrative metrics.

For architecture diagrams, use clean boxes, connectors and directional arrows.

# PHASE 4: STORYTELLING AND SLIDE FLOW

The presentation must tell a coherent technical story.

Follow this narrative:

Problem → Motivation → Dataset → Exploration → Challenges → Preprocessing → Proposed Methodology → Architecture → Training → Evaluation → Results → Analysis → Conclusion.

Each slide should introduce information that logically follows from the previous slide.

Avoid repeating the same information across multiple slides.

Use section divider slides only when they genuinely improve navigation.

The presentation should feel like it was carefully designed by a human researcher preparing for an academic project evaluation.

# PHASE 5: TECHNICAL ACCURACY

This is a strict requirement.

- Never fabricate dataset statistics.
- Never invent model results.
- Never invent accuracy, precision, recall or F1-scores.
- Never claim that an implementation exists unless it is present in the project.
- Do not confuse training results with testing results.
- Clearly distinguish between experiments.
- Use actual generated graphs and evaluation outputs.
- If some information is unavailable, omit it or mark it for my review.

Preserve the actual terminology and methodology used in the project.

# PHASE 6: POWERPOINT IMPLEMENTATION

Generate a fully editable .pptx file.

Use Python-pptx or another suitable PowerPoint generation approach.

Requirements:
- Consistent slide dimensions (16:9 widescreen).
- Editable text, charts, tables and shapes wherever possible.
- Properly embedded images.
- Consistent headers and footers.
- Slide numbers.
- Appropriate whitespace.
- No overlapping objects.
- No clipped text.
- No unreadable charts.
- No inconsistent formatting.

Do not simply convert screenshots of slides into a PowerPoint.

# PHASE 7: QUALITY ASSURANCE

After generating the presentation:

1. Render every slide into an image.
2. Inspect all slides individually.
3. Check text overflow and clipping.
4. Check alignment and spacing.
5. Check image quality and aspect ratios.
6. Check chart labels and legends.
7. Check consistency of colors and fonts.
8. Check the narrative flow between slides.
9. Fix any visually weak or overcrowded slides.
10. Regenerate and inspect the presentation if necessary.

Do not stop after generating the first draft.

# FINAL DELIVERABLES

Provide:

1. Final editable PowerPoint: project_presentation.pptx
2. A PDF version for convenient preview.
3. A folder containing generated charts and diagrams, if applicable.
4. A short summary of the presentation structure.
5. A list of any assumptions or missing project information that needs my confirmation.

FINAL EXPECTATION:

The final presentation should look like a carefully crafted university-level research/project presentation, suitable for presentation before professors and an academic evaluation panel.

It must be visually polished, technically accurate, logically structured, consistent, and natural-looking.

Prioritize quality over speed. Do not sacrifice technical accuracy or design quality just to finish quickly.