# Introduction

In today's scientific and engineering research, software plays a critical role to collect, process, analyze, and visualize data or simulate complex physical systems {cite}`Storer2017`.
Whenever existing software does not meet specific research needs, the existing software must be adapted, or completely new software must be developed.
The researchers (referred to as "Domain Experts" or "DE" in this paper) will either have to tread the unfamiliar water of software engineering, or collaborate with developers who likely possess different knowledge-sets and expectations {cite}`HannayEtAl2009,KillcoyneEtAl2009`.

The main motivation behind our work is to move research software away from being a disposable tool for a single paper/project, to being a sustainable product that can be maintained, reused and adapted for the future.
We argue that software should meet the same rigorous standards, such as peer review and reproducibility, as the research itself.

Our work presents a framework tailored toward small project teams who have not seen much attention. We wish to help developers effectively collaborate with domain experts to produce high-quality, sustainable research software.
Our framework comes in the form of a [GitHub repository template](https://omltcat.github.io/research-software-template/), around which we will also discuss our [Proposed Practices](./practices.md).
The template uses structured Markdown files for documentation, and automatically deploys a website to GitHub Pages using GitHub Actions whenever it is updated.
Our approach enables quick issue creation that links to specific sections of the documentation (Shown in Figure below) and quick editing, lowering the participation barrier for domain experts who may not be familiar with GitHub.

```{figure} figures/report_issue.png
:name: Quick Issue Creation Button
:align: center

Example of a quick issue creation button present on each section/subsection of the documents in our template, with generated issue linking back to this exact location.
```


## Pain Points
Our literature review has identified several prominent pain points in research software development.
Some of these are also corroborated by our own experience as discussed in the [Background](./background.md) section.
- Lack of software engineering knowledge by the domain experts (researchers), especially areas like testing and project management {cite}`HannayEtAl2009,PintoEtAl2018`.
- "Feature creep" as the research evolves and hard-to-define requirements {cite}`PintoEtAl2018,SegalEtAl2008,KillcoyneEtAl2009`.
- Poor documentation and little interest in making the software sustainable, due to lack of recognition and outside user feedback {cite}`PintoEtAl2018`.
- Knowledge gap and communication breakdown between the developer and the domain expert, making it difficult for the developer to grasp the requirements {cite}`SegalEtAl2008`.
