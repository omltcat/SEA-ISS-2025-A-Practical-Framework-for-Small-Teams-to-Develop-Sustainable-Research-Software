# Proposed Practices

The full representation of our proposed practices is publicly available as a [GitHub template](https://omltcat.github.io/research-software-template/).
In this section, we provide an overview and discuss the most notable proposals and the rationale behind them.

## Overview
For the overall life cycle, we choose a V-model approach and focus on first building the Minimum Viable Product (MVP).
The main reason we prefer this over agile or waterfall is that it allows us to create a high and a low involvement level zones for the domain expert, and tie the information gathering process into several important documents early on, as illustrated in the Figure below.

```{figure} figures/v-model.png
:name: V-model
:align: center

Our V-model workflow, with numbering indicating suggested chronological order of activities. Bold frame items are the main focus of this paper. Green (Information Gathering) is newly introduced in this thesis. Blue items are where we primarily cut-down development overhead.
```

## Information Gathering
In our workflow, the developer is responsible for writing requirements and design documents after gathering necessary information and understanding the problem domain. However, too much information can overwhelm the developer, as domain experts may be enthusiastic about sharing details. The developer's aim is to understand just enough to translate the problem into software. To achieve this, we propose a single technical meeting where the domain expert briefly present their research, before the developer ask a set of specific questions to efficiently extract essential information needed to build an MVP. We arrange these questions into six categories:

- Q1.* - **Problem Domain** helps the developer to understand the big picture of research field;
- Q2.* - **Software Goals** focuses on what the software should achieve;
- Q3.* - **Theoretical Model** gathers the resources needed to implement the theory;
- Q4.* - **Scale of the Problem** sets performance expectations for solving a typical problem;
- Q5.* - **Data and Inputs/Outputs** is about how users can realistically interact with the software;
- Q6.* - **Testing** discuses possible ways to verify and validate the software to build confidence in its correctness.

For example, a question regarding the computation scale in a typical use case is:
> Q4.2: In a typical use case, what is the approximate scale of the problem. Depending on the problem/model, this can be in terms of:
> - Number of data points
> - Matrix/vector/grid size
> - Simulation length and time step
> - Data rate expected to flow through the software (for a real-time application)
> - Number of iterations/steps expected before the result can be considered acceptable


A full list of questions and the related meeting agenda can be found in the [GitHub template](https://omltcat.github.io/research-software-template/template/first-meeting/) (excerpt Figure below).
The domain expert will review the meeting notes written by the developer based on the answers to these questions, before the developer proceeds to refine such information into various documents as shown in the traceability matrix below.

```{figure} figures/trace.png
:name: Information Traceability Matrix
:align: center

Traceability matrix between meeting questions and sections in documentation
```

## Testing and Continuous Integration
For research software, proper testing routines not only gives confidence in the software but also credibility to the research that it powers.
Since a (pseudo)oracle is often nonexistent for the exact research problem/model, the design may need to be structured around what is available for testing, such as simple models.
By designing a standardized interface, we can test the majority of the software with simpler models, minimizing the untestable portion, which can be verified with more manual methods such as code walkthroughs {cite}`Smith2016`.

Since the domain expert likely does not possess the same level of knowledge to make and conduct tests, Continuous Integration (CI) with GitHub Actions and language specific testing tools should be introduced early on in the project.
This prepares the eventual departure of the developer from the project, after which the domain expert (and potential future users) will take over the software.
The actual test cases run with GitHub Actions may be a subset of all the cases, since for some projects, a full-scale system test may take too long or use more resources than what is feasible with GitHub Actions.
Developers can use our GitHub template as an example of how to set up GitHub Actions for continuous integration.

## Simplified Design Documents
Originally, Parnas introduced two pieces of design documents: Module Guide (MG) {cite}`Parnas1972` and Module Interface Specification (MIS) {cite}`Parnas1972MIS`.
Because the domain expert's involvement is likely low in the design phase as shown in our V-model above, we propose the reduction of the MIS into a code skeleton during the design phase, as it will not be reviewed by the domain expert.
This leverages the function headers, type definitions and comments formatting of modern IDEs and programming language.
It also reduces the risk of code diverging from the design documents that are not always kept up to date.
Another added benefit of this approach is that nowadays if the developer uses any coding assistant, such as GitHub Copilot, it will receive better context from all the in-code documentation.
Below shows a docstring example from SynthEddy:

```python
def sum_vel_chunk(
    centers: np.ndarray,
    sigma: np.ndarray,
    alpha: np.ndarray,
    x_coords: np.ndarray,
    y_coords: np.ndarray,
    z_coords: np.ndarray,
):
    """
    Calculate the velocity field due to each eddy within a chunk.

    Uses the instance model (IM1) in SRS as the core of the calculation.
    Choice of shape function is set in the shape_function module.

    Parameters
    ----------
    centers : np.ndarray
        Array of eddy centers.
    sigma : np.ndarray
        Array of eddy length scales.
    alpha : np.ndarray
        Array of eddy intensities.
    x_coords : np.ndarray
        Array of x coordinates spanning the chunk.
    y_coords : np.ndarray
        Array of y coordinates spanning the chunk.
    z_coords : np.ndarray
        Array of z coordinates spanning the chunk.

    Returns
    -------
    np.ndarray
        Array of velocity fluctuations due to each eddy within the chunk.
    """
```

## Performance-aware Modularization
Organizing software based on object-oriented programming principles, such as encapsulation and information hiding, offers clear benefits in readability and extensibility.
However, this approach can sometimes lead to performance issues, as seen in the SynthEddy project where the computation scale (grid points and eddy objects) in practice caused significant slowdowns and memory consumption. 

To address these performance concerns, it may be necessary to break away from strict encapsulation to better leverage optimized libraries, if available for the problem under consideration, such as NumPy for vector operations.
This trade-off allows for more efficient handling of large computation scale.
Some future-proofing attributes can be retained through detailed documentation of the optimizations employed, ensuring that subsequent developers can understand and build upon these improvements. The below example shows how SynthEddy helps future end-user developer to make their own optimized shape function:

```python
def gaussian(dk, sigma):
    """Gaussian shape function"""
    return np.where(
        dk < cutoff,
        C * np.exp(-HALF_PI * dk**2),
        0
    )
    # The function is structured in this way so that it can be broadcasted by NumPy over many eddies efficiently.
    # Mathematically, this function is equivalent to:

    #         ┌ C * e^(-π/2 * dk^2), if dk < cutoff
    # q(dk) = ┤
    #         └ 0, elsewhere

    # where C = 3.6276, HALF_PI = π/2
    # These are defined as constants at the top of this file. You can use different values or define new ones.
    # Use pre-calculated constants when possible because this function is called many times.
    # It is faster not to recalculate these values every time.

    # Your inputs must include dk and sigma (sigma), even if some shape functions may not use sigma.

    # You can choose what shape function and cutoff value to use in query arguments.
```

## User Guide and "Advertising"
As identified in the [Pain Points](./introduction.md#pain-points) section, the domain expert may not be motivated to spend time and effort toward making the software more sustainable as they do not perceive the academic recognition for it.
To address this, we believe two approaches that are already seen on some other projects should be applied to research software whenever possible:

First is the amalgamation between software and paper. An example can be found on a computer vision research project, v2e {cite}`HuEtAl2021`. Its GitHub repository README contains the follow section:

> **Citation**
>
> If you use v2e, we appreciate a citation to the paper below. See the v2e home page for further background papers.
>
> Y. Hu, S-C. Liu, and T. Delbruck. v2e: From Video Frames to Realistic DVS Events. In 2021 IEEE/CVF Conference on Computer Vision and Pattern Recognition Workshops (CVPRW). URL: https://arxiv.org/abs/2006.07722, 2021
>
> To reproduce the experiments of the paper, please find [this repository](https://github.com/SensorsINI/v2e_exps_public).

This way, to reproduce or build upon the research is not only possible, but also encouraged, and the software is more likely to be used by other researchers in the field.

Second is the practice, common in the Self-Hosting community, of including a "Quick Start" guide in addition to the detailed User Guide, such as in the case of [Jellyfin](https://jellyfin.org/docs/general/quick-start/), [Nginx Proxy Manager](https://nginxproxymanager.com/guide/#quick-setup) and [Vaultwarden](https://github.com/dani-garcia/vaultwarden?tab=readme-ov-file#docker-compose).
If potential users can quickly spin up a typical use case of the software with docker images, pre-packaged runtime environments, etc., they may be more inclined to use it for their own research, provide feedback and even contribute back to the project.
