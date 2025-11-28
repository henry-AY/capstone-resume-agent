# Multi-Agent Resume Tailoring System

## Automates the entire resume tailoring process for job applications, from research, rewritting to polishing.

<p style="text-align:center;">[Link to Agents Intensive - Capstone Project].</p>

## Overview
A multi-agent LLM pipeline that ingests a raw resume and a job query, performs job research, rewrites the resume to match the job, and outputs a polished, job-tailored resume + explanation. This project is built using Google's Agent Development Kit (ADK) and follows a modular structure, easy for future expansions. 

> Insert photo of structure here

## Problem Statement

Personalizing a resume for every job application is a laborious and time-consuming grind. To do it well, you have to invest significant time into researching, drafting, editing, and polishing so it reads smoothly. Doing that once is fine, however, it is not scalable doing it tens-hunderds of times for internships or full-time jobs and can easily become exhausting. 

A lot of people end up reusing the same resume simply because personalizing for every job description takes too long. Thus, good candidates can get filtered out by the ATS system or simply overlooked because their resume doesn't clearly match the role. It's a workflow that's highly repetitive, which means it can efficiently be automated. This allows the candidates to focus their time on the actual application process and interview preperation, while the agent system handles the personalization in the background.

## Solution Statement

Instead of manually reading job descrpitions and rewriting sections of your resume, agents can handle that entire pipeline. Agents can automatically research job postings and sythesize key insights relevant to the position, summarizing, and rebuilding a resume draft that highlights the key details found in the job description. The final step of the entire pipeline is the polishing agent, that refines the structure, clarity, and consistency to produce a clean Markdown resume. By offloading research, rewriting, and editing to a sequential agent system, the process for job applications becomes significantly faster and scalable. 

## Architecture

Core to the architecture of this program, is the seqential multi-agent system: an ecosystem of specalized agents, each capable of contributing their own piece of the puzzle in the process of tailoring a resume. This modular approach allows for a sophisticated and easily expanded-upon workflow, that can easily integrate new agents and/or tools. 

The entire process is wrapped in a pipeline function `agent_pipeline`, which is responsible for verifying input(s), context engineering, and running the agents. Additionally, the pipeline takes in two key parameters: `resume_text`, the raw text of the user-input resume, and `job_query`, the position passed in by the User (i.e. Tesla SWE Intern 2026).

The system is split into **4 key agents**, each an expert in their specific role:

#### Resume Parser: `resume_intake_agent`

This agent extracts structured information from the user’s raw resume. It identifies sections, skills, technical stacks, and experience details and reformats them into a consistent, machine-readable summary. The goal is not to rewrite content but to normalize it so downstream agents can work with clean, predictable input.

#### Job Research Agent: `job_research_agent`

This agent investigates the target job by analyzing descriptions, qualifications, and responsibilities. Instead of returning a generic summary, this agent synthesizes traits that actually matter for tailoring a resume (e.g. required tech stacks, preferred experience, responsibilities, etc...). The output is structured enough for the rewriting stage to map the user’s background to the expectations of the position.

#### Rewrite Agent: `resume_rewrite_agent`

Given the parsed resume and the synthesized job research in a prompt, this agent generates a tailored resume draft that highlights the user’s most relevant strengths for the role. It restructures bullet points, improves clarity, and aligns skills and achievements with what employers look for. This agents output is highly controlled, outputting strictly a JSON with two values for the `resume` and `rationale` (justifications of the changes), to enable reliable and efficient parsing downstream.

#### Polishing Agent: `polish_agent`

The final step is refinement. The polishing agent cleans up the draft, improves readability, enforces consistent formatting and tone, and resolves any awkward phrasing that can appear in earlier generations. This stage is crucial to the quality of the final output, as the agent has strict instructions to expliclity produce a polished resume, and in the end, it outputs a fully polished Markdown resume.

## Essential Tools & Helper functions

#### Google Search Tool

The `job_research_agent` is equipped with the builtin `google_search` tool from the ADK, which allows it to query live against job postings and related resources. This provides a significant advantage to the tool, as it is able to pull from real descriptions, responsibilities, and skill requirements directly, without relying on static text. This lets the agent produce more accurate and relevant insight for tailoring the resume later in the pipeline.

#### Markdown Rendering

The notebook includes a lightweight `display_markdown()` helper function that formats and renders agent output(s) cleanly inside the notebook environment.

#### JSON Extracting & Validation

A custom `extract_resume_and_explanation()` helper function is used to parse the JSON returned by `resume_rewrite_agent`. Combined with `validate_output()`, this prevents bad output from silently breaking the pipeline.

#### Silent Debug Runner

The `silent_run_debug()` helper function captures streamed agent output without printing intermediate tokens or events to the notebook. This keeps the UI clean while still allowing you to retrieve the final model response for each agent stage. This is a purposeful decision, in order to hide the `run_debug()` output for privacy reasons. However, feel free to call `run_debug()` instead of `silent_run_debug()` for debugging or other reasons. 

## Conclusion

The Multi-Agent resume tailoring system aims to simplify a task almost everyone has to deal with at least once during their life. Customizing a resume for job applications, and it is done by utilizing ADK. It shows how a structured, repetitive task can be automated with powerful frameworks such as the ADK, tackling complex, real-world problems. The system can consistently produce targeted results with speed and efficiency, which is the main focus of this project; improving the workflow of tailoring resumes for positions.

## Value Statement

I tested the pipeline against real job postings to measure how well it performed in the 'real world', and it most definitely has improved the effiency of the resume tailoring process. I've noticed the real benefit isn't in rewriting the entire resume from scratch, instead, it's in quickly rewriting bullet points to closer match the job descrpition. What used to to take the time to tailor one resume, I can now get through 3-4x using the agent workflow, and that kind of efficiency gain is extremely valuable.

If I had more time, I'd focus on adding memory to the agents. Currently, the agents only know about the current resume and job description, however, giving them recall of previous resumes and/or context would let them build a significantly deeper profile of the user and produce a significantly more consistent and personalized rewrite.

## Running this Repository

### Requirements

- Python 3.10+    
- `.env` file containing: `GOOGLE_API_KEY=your_key_here`

### 1. Fork + Clone the Repo

```bash
git clone <repo_url>
cd capstone-resume-agent
pip install -r requirements.txt
```

### 2. Usage

Open notebooks/ResumeAgentPipeline.ipynb and run all cells.
Input your raw resume text and job query when prompted.
The final polished resume (and rationale) will be rendered in Markdown format.

## License

> Insert license here