# ASSIGNMENT: Sampling and Reproducibility in Python

Read the blog post [Contact tracing can give a biased sample of COVID-19 cases](https://andrewwhitby.com/2020/11/24/contact-tracing-biased/) by Andrew Whitby to understand the context and motivation behind the simulation model we will be examining.

Examine the code in `whitby_covid_tracing.py`. Identify all stages at which sampling is occurring in the model. Describe in words the sampling procedure, referencing the functions used, sample size, sampling frame, any underlying distributions involved, and how these relate to the procedure outlined in the blog post.

Run the Python script file called whitby_covid_tracing.py as is and compare the results to the graphs in the original blog post. Does this code appear to reproduce the graphs from the original blog post?

Modify the number of repetitions in the simulation to 1000 (from the original 50000). Run the script multiple times and observe the outputted graphs. Comment on the reproducibility of the results.

Alter the code so that it is reproducible. Describe the changes you made to the code and how they affected the reproducibility of the script file. The output does not need to match Whitby’s original blogpost/graphs, it just needs to produce the same output when run multiple times

# Author:Keyuan Huang

```
Please write your explanation here...

```
Sampling and Reproducibility in Python
Keyuan Huang - Nov.11

Sampling in whitby_covid_tracing.py

The sampling process in whitby_covid_tracing.py involves several distinct stages, each contributing to the overall simulation:

Infection Assignment: In the simulate_event() function, individuals are randomly infected using the np.random.choice() function. The infection is assigned based on a predefined attack rate (ATTACK_RATE). Specifically, a subset of the population, consisting of 200 participants from weddings and 800 from brunches, is selected for potential infection. This stage of sampling involves selecting a proportion of individuals (determined by ATTACK_RATE) to be marked as infected, which introduces a random element into the infection assignment.

infected_indices = np.random.choice(ppl.index, size=int(len(ppl) * ATTACK_RATE), replace=False)
ppl.loc[infected_indices, 'infected'] = True

Primary Tracing: After the initial infection, the script performs primary contact tracing to identify which infected individuals are successfully traced. This is done using np.random.rand() < TRACE_SUCCESS, where each infected individual has a probability (TRACE_SUCCESS) of being traced. This step models the effectiveness of contact tracing measures in real-world scenarios, where not all infected individuals are successfully traced.

ppl.loc[ppl['infected'], 'traced'] = np.random.rand(sum(ppl['infected'])) < TRACE_SUCCESS

Secondary Tracing: Secondary tracing is carried out based on the number of traced individuals at each event. If the number of traced infections at a specific event exceeds the SECONDARY_TRACE_THRESHOLD, all individuals attending that event are traced as a precautionary measure. This stage is crucial for capturing the broader impact of contact tracing, as it extends tracing beyond direct contacts to potentially exposed groups.

event_trace_counts = ppl[ppl['traced'] == True]['event'].value_counts()
events_traced = event_trace_counts[event_trace_counts >= SECONDARY_TRACE_THRESHOLD].index
ppl.loc[ppl['event'].isin(events_traced) & ppl['infected'], 'traced'] = True

After running the script with 50,000 repetitions, the results produced distributions that closely resemble the graphs in Andrew Whitby's original blog post. The histograms showed similar trends for the proportion of infections and traces attributed to weddings, suggesting that the simulation accurately represents the dynamics described in the blog.

Effect of Changing the Repetitions

When reducing the number of repetitions in the simulation to 1,000 and running the script multiple times, the variability in the outputted graphs increased noticeably. This is because a smaller number of repetitions leads to a higher sensitivity to randomness, causing fluctuations in the distributions of infections and traces. The lower sample size resulted in less smooth histograms, which highlighted the challenge of obtaining stable results with fewer iterations.

Changes for Reproducibility

To make the code reproducible, I made a key modification by adding a fixed random seed using np.random.seed(42) at the beginning of the script. Setting a random seed ensures that the sequence of random numbers generated is the same each time the script is run, which results in consistent and repeatable outputs. This change allows us to validate our findings, as identical results are produced every time the simulation is executed, regardless of the number of repetitions.

The modified script is as follows:

import numpy as np

# Set random seed for reproducibility
np.random.seed(42)

# Your existing code...

# Infection assignment, primary and secondary tracing code follows here.

infected_indices = np.random.choice(ppl.index, size=int(len(ppl) * ATTACK_RATE), replace=False)
ppl.loc[infected_indices, 'infected'] = True

ppl.loc[ppl['infected'], 'traced'] = np.random.rand(sum(ppl['infected'])) < TRACE_SUCCESS

event_trace_counts = ppl[ppl['traced'] == True]['event'].value_counts()
events_traced = event_trace_counts[event_trace_counts >= SECONDARY_TRACE_THRESHOLD].index
ppl.loc[ppl['event'].isin(events_traced) & ppl['infected'], 'traced'] = True

By using np.random.seed(42), we ensure that each run of the script produces the same set of infections and traced individuals, which is essential for comparing results and validating conclusions.
By setting a fixed random seed, the script became reproducible, meaning that the results are consistent across different runs. This change is crucial in scientific modeling to validate results and ensure that findings are reliable. Although the output may not perfectly match Whitby's original graphs, the key is that it produces the same output each time, demonstrating reproducibility.

## Criteria

|Criteria|Complete|Incomplete|
|--------|----|----|
|Altercation of the code|The code changes made, made it reproducible.|The code is still not reproducible.|
|Description of changes|The author explained the reasonings for the changes made well.|The author did not explain the reasonings for the changes made well.|

## Submission Information

🚨 **Please review our [Assignment Submission Guide](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md)** 🚨 for detailed instructions on how to format, branch, and submit your work. Following these guidelines is crucial for your submissions to be evaluated correctly.

### Submission Parameters:
* Submission Due Date: `HH:MM AM/PM - DD/MM/YYYY`
* The branch name for your repo should be: `sampling-and-reproducibility`
* What to submit for this assignment:
    * This markdown file (sampling_and_reproducibility.md) should be populated.
    * The `whitby_covid_tracing.py` should be changed.
* What the pull request link should look like for this assignment: `https://github.com/<your_github_username>/sampling/pull/<pr_id>`
    * Open a private window in your browser. Copy and paste the link to your pull request into the address bar. Make sure you can see your pull request properly. This helps the technical facilitator and learning support staff review your submission easily.

Checklist:
- [ ] Create a branch called `sampling-and-reproducibility`.
- [ ] Ensure that the repository is public.
- [ ] Review [the PR description guidelines](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md#guidelines-for-pull-request-descriptions) and adhere to them.
- [ ] Verify that the link is accessible in a private browser window.

If you encounter any difficulties or have questions, please don't hesitate to reach out to our team via our Slack at `#cohort-3-help`. Our Technical Facilitators and Learning Support staff are here to help you navigate any challenges.
