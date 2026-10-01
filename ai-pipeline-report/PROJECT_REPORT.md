# Project Report

**Phoenix Long**

*Abridged mediocracy*

This literature will go over my pipeline method, model choice and the
challenges/lessons learned, then wrapping up with some code produced by said
pipeline. To whom it may concern, good luck reading my work, as when creating
such an abomination of code, one can only drink Java Monster Energy[^1] with a
wavering will.

## Pipeline

My pipeline consisted of seven stages, each designed to handle a specific
transformation of the MITRE ATT&CK data into executable, varied, and documented
offensive security code samples. The architecture of the code was designed with
parallelization in mind, as sequential processing would have been financially
devastating given the costs involved as you will hear about later.

**Stage one** consisted of fetching real MITRE ATT&CK data from both their
website and their git repository. The benefit of such action was that if
techniques are added or removed you could still generate relevant code since
it's not hard coded. Then I used three datasets to give the AI model: the
standard bash-commands-dataset, an offensive-powershell set, and lastly a
shell-cmd-instruct dataset. This I felt was necessary to give it more uniqueness
than just relying on one dataset.

**Stage two** created baseline code for every technique from MITRE. This was done
with a parallel GPU process, running H100s. There were some hyperparameters like
"code must be executable," "first character must be code not text," etc. This
implementation allowed the full list of techniques to be split into equal
batches. As each GPU worker loaded the Qwen2.5 model individually and worked
independently, this reduced the time it took to run down drastically from 2 hours
to minutes.

**Stage three** was a parallel variation generation. This was discussed as a
potential method in class, and so I took this idea and implemented it. This used
10 H100 GPUs to generate three different iterations of the previously generated
code at minimum, thus giving at least 750 lines of code to evaluate. However in
some cases it produced more than the minimum. It had variation strategies ranging
from obfuscation, native binary, WMI based, fileless, scheduled tasks,
registry-based, remote execution, polyglot (nightmare), API-based, and
alternative language based. Each strategy had its own temperature setting, to
encourage appropriate levels of creativity and minimum length requirements to
ensure substantive variations.

**Stage four** selected 500 codes and saved them before adding explanatory
comments to them, as I wanted access to what it decided was best and to compare
before and after results between the two created datasets. The selection algorithm
prioritized diversity across several factors including technique coverage,
variation distribution, code complexity, and hash uniqueness. It used a hash to
eliminate exact duplicates which I thought was a trivial but interesting approach
to implement.

**Stage five** was creating the explanatory comments so that the dataset would be
understandable to humans reading it, and crucially, correctable to humans reading
it. The comments needed to explain not just what the code does, but why it
achieves the associated MITRE technique. The prompt engineering here was
particularly important. The model had a tendency to generate multi-paragraph
explanations or conversational text rather than concise inline comments. To combat
this, I constrained it to generate exactly 2 sentences, both starting with the `#`
comment character, with temperature set to 0.7 to encourage more consistent
descriptions.

**Stage six** was exporting said datasets in multiple formats for different cases.
Three primary files are generated: `Master_with_descriptions.txt`,
`code_with_descriptions`, and `dataset.json`. The Master dataset was the completed
set, whereas the code had minimal format. Lastly the JSON file had more structured
description fields for things like machine learning. I had planned to train on it
myself but ran out of time with procrastination.

**Stage seven** was to generate statistics for the code so I could see how it
compared within my own testing values. The statistics calculated the total code
count, unique technique coverage, average codes per technique, language
distribution, variation strategy distribution, and average code length. These
metrics were crucial for me in understanding what my pipeline was producing.

A fuller breakdown of each stage and function — its execution results, the
inspiration, and where external APIs assisted development — lives in the project
notebook in the private code repository.

## Model

The model implemented into this pipeline was **Qwen2.5-Coder-32B-Instruct**. The
decision to use such a model was derived from several factors beyond just the
initial recommendation. First, Qwen2.5-Coder is specifically fine-tuned for code
generation tasks, unlike general-purpose models. The 32B parameter size provided
sufficient capacity to understand complex security contexts and generate
syntactically correct code across multiple languages. Smaller models (7B, 14B)
consistently produced syntactically invalid code or failed to understand the
nuances of the techniques. The "Instruct" variant was critical because it's
trained to follow detailed system prompts and constraints. Non-instruct models
would frequently ignore requirements like "no conversational preamble" or "ensure
code is executable." The instruct tuning meant I could rely on prompt engineering
to control output format rather than requiring post-generation parsing heuristics.

## Challenges Encountered

This section may be longer, as I encountered a lot I wish to learn from for future
development. Each challenge taught me something about large-scale AI powered code
generation that wasn't obvious from small-scale experiments we had done.

The first challenge I encountered was creating a functioning pipeline to produce
simple code. For this, I started by just creating a pipeline that would generate
basic commands that I could use for daily tasks. This was just so I could get the
groundwork and understand the Modal framework's GPU allocation and volume systems.

The second problem I encountered was testing my code at scale. Specifically, as I
wanted to create iterations of the generated code, I attempted to do this on a
single GPU without parallelization. This meant I was leaving the generation of over
200 techniques to one GPU, and I started by having it create 20 iterations for
each. This test case took over 8 hours until the main program crashed due to a
timeout function I implemented. This costly mistake ate close to $175 of my
accredited tokens. The failure occurred at iteration 87 of 200, meaning I lost all
progress and had to start over. As such I decided to break it down into
parallelization, and to reduce the number of iterations down to 3 sadly. If given
the opportunity (money) to do it again, I would love to see the results with 20
variations per origin code generated.

Another problem I encountered was actually the code it produced. No matter what I
did, I always got syntactic errors, but I didn't want to restrict my model more as
to remove the generative aspect of it. This was challenging and I didn't end up
fixing it, so roughly a quarter of the dataset has improper syntax.

## Code

The pipeline produced samples across a range of MITRE ATT&CK techniques and
variation strategies (all map to publicly documented techniques):

| Technique | What the generated variation does |
| --- | --- |
| **T1036 — Masquerading** | Renames a known system binary so code could run under the guise of a trusted process name. |
| **T1042 — Change Default File Association** | Modifies the registry so a file type opens with a different handler (shown via PowerShell cmdlets and separately via WMIC). |
| **T1088 — Bypass User Account Control** | Launches a system binary with elevated arguments to illustrate a UAC-bypass pattern. |
| **T1090 — Proxy** | Creates a local port-forwarding rule that redirects traffic from one port to another host. |

These examples demonstrate the variety in implementation approaches — from direct
binary manipulation, to registry modification, to privilege-escalation patterns,
to network pivoting. The variation strategies ensured that the same technique
(like T1042) appears in multiple forms using different underlying mechanisms.

## Take Away

AI is scary. The alarming rate at which we can use something to construct new
attempts to attack a system is alarming. Brute-forcing a complex attack might
become the future of exposing things like network weaknesses or other
unconfigured/unprotected networks and hosts. Doing this project was only time
consuming, which means that anyone dedicated enough could complete said project.
This instills fear in how things will be conducted in the future, to me. Overall,
a reality check to the gut.

---

[^1]: Java Monster Energy: a beverage produced by Monster, which tastes like
coffee. Very good.
