---
layout: post
title:  "My submission for NASA's SpaceApps challenge (2023)"
tags: [Project,AI,Challenge,Competition]
gh-repo: Uralstech/hAI-Spec-NASA-SpaceApps-Challenge
gh-badge: [star, watch, fork, follow]
readtime: true
---

About my submission for NASA's SpaceApps challenge.
<!--more-->

### The Team

#### hAI! Spec
Three strangers brought together by a passion for innovation and science. United by the dream of humanity spreading out to the stars! To go where no human has gone before!

#### Team members

- Udayshankar R (me) ([@Uralstech](https://github.com/uralstech))
- Devika S Kumar
- Pranamika Pandey ([@Pihu1998](https://github.com/Pihu1998))

### The Submission

#### HIGH-LEVEL SUMMARY

Our solution, named hAI! Spec, is a Large Language Model (LLM) designed to optimize the management of technical standards within the aerospace industry. Trained on changes between historical and current versions of various standards, hAI! Spec uses a unique Section-By-Section training approach. This allows the model to provide accurate recommendations for improving document consistency, relevance, and accuracy, even down to the smallest grammatical detail in extensive documents like NASA standards.

#### PROJECT DEMO

<https://youtu.be/cdnzlXnl41g>

#### FINAL PROJECT

<https://github.com/Uralstech/hAI-Spec-NASA-SpaceApps-Challenge>

#### PROJECT DETAILS

##### OBJECTIVE

The primary goal of hAI! Spec is to revolutionize the way technical requirements are managed in the aerospace industry by using AI-powered analysis to recommend changes for improvement. It aims to enhance safety, reliability, and efficiency.

##### TECHNOLOGY
- Python

> *Role:*<br/>
> Scripting and Data Processing
>
> *Description:*<br/>
> Used for writing utility scripts for various tasks such as generating prompts for GPT, downloading standards, and splitting sections.
> 
> *GitHub repository:*<br/>
> <https://github.com/Uralstech/hAI-Spec-NASA-SpaceApps-Challenge>

- Python Library: PDFMiner

> *Role:*<br/>
> PDF Processing
> 
> *Description:*<br/>
> Employed for parsing and converting PDF documents of aerospace standards into text format.

- Python Library: HuggingFace Transformers

> *Role:*<br/>
> Model Inferencing
> 
> *Description:*<br/>
> Utilized for inferencing and merging the Language Learning Model (LLM) during the development phase.

- HuggingFace AutoTrain Advanced

> *Role:*<br/>
> Model Training
> 
> *Description:*<br/>
> Command-line tool used for training the LLM (hAI! Spec) based on LLaMA-2.

- hAI! Spec HuggingFace model

***<https://huggingface.co/uralstech/hAI-Spec-Merged>***

```
train_runtime: 2192.9027, train_samples_per_second: 0.602,
train_steps_per_second: 0.018, train_loss: 0.5044329412281513,
epoch: 26.67
Google Cloud Platform (GCP)
Role: Training and deploying the LLM.
```

> *Description:*<br/>
> Used for training and deploying our hAI! Spec model on Google Compute Engine (part of GCP) Virtual Machines using Nvidia L4 GPUs.
>
> *Training cost:*<br/>
> **$0.435**

- (Website) Flask:

> *Role:*<br/>
> Creating the web application using Python
>
> *Description:*<br/>
> Flask is a lightweight and versatile web framework for Python. It handles the HTTP requests, file uploads, and serves as the backend for your web application.

- (Website) Werkzeug:

> *Role:*<br/>
>  WSGI web application library
> 
> *Description:*<br/>
>  Werkzeug is a WSGI (Web Server Gateway Interface) utility library for Python. It is often used with Flask for handling HTTP requests and provides secure file upload functionalities.

- (Website) HTML/CSS:

> *Role:*<br/>
> Styling the web application
>
> *Description:*<br/>
> Used for designing the user interface of your web application. HTML provides the structure, while CSS handles the styling of the elements.

- (Website) JavaScript:

> *Role:*<br/>
> Client-side interactivity and asynchronous communication with the server
> 
> *Description:*<br/>
> JavaScript for client-side interactivity and asynchronous communication with the server, especially if you want to update the prediction section without refreshing the entire page.
> 
> The web application allows user to upload the technical specifications document through a file upload UI, which is then parsed for the entity extraction, which is fed to the AI model trained using large language model (Llama 2) to provide a suggestion towards a better version of the technical specification.

#### TRAINING METHODOLOGY

##### Data Collection:

We collected historical and existing versions of various aerospace standards, including NASA-Standard-5018.

##### Data Preprocessing:

All documents were converted to text format to enable easy parsing and analysis.

##### Section-By-Section Training:

The LLM was trained section by section on each standard, contrasting each section against its corresponding section in earlier versions.

##### Contextual Understanding:

Annotations were provided to help the model understand the reason behind each change between versions, aiding in the decision-making process.

Since the training data was very less, the inference need improvements. We plan increase the dataset size to get better inference.

#### CORE FEATURES

##### Automated Review:

Upload the technical document, and hAI! Spec will scan the entire text, identifying any inconsistencies, errors, or omissions.

##### Intelligent Recommendations:

Based on its training, hAI! Spec will provide recommendations on the language of a standard in relation to changes it has seen on historical standards.

##### User-Friendly Interface:

Easy-to-use interface for uploading documents and viewing recommendations.

##### Security:

As hAI! Spec is a freely available model, it can be used on any servers.

##### And more, like:

- Streamlined process for technical document management
- Improved document accuracy and consistency
- Time-efficient, reducing the labor required for manual review

#### OUTPUT EXAMPLE:

For a document like NASA-STD-5018, an output may look like:

```
### Output:
Suggesting Changes: [Yes / No]

Current Language: [Problematic language in standard]
Suggested Language: [Suggested improved language]
Reason For Change: [Reason for suggestion]
```

By addressing the industry's critical pain points, hAI! Spec aims to have a lasting impact on the safety and efficiency of aerospace missions.

#### Demo Website:

<http://specguru.co/>

#### USE OF ARTIFICIAL INTELLIGENCE

##### ChatGPT/GPT-4 by OpenAI:

Utilized to script the identification and extraction of similar sections from different versions of aerospace standards. Also used for generating prompts that contributed to the training and validation dataset for hAI! Spec.

##### LLaMA-2 (Meta's Large Language Model):

Served as the base model upon which hAI! Spec was trained. Crucial for understanding and generating content specific to the aerospace standards that hAI! Spec aims to optimize. 

By leveraging the capabilities of ChatGPT/GPT-4 for data generation and LLaMA-2 for foundational training, hAI! Spec aims to provide a comprehensive and accurate solution for the optimization of aerospace standards and technical documents.

#### SPACE AGENCY DATA

[**NASA 5000 Standards**](https://standards.nasa.gov/structures-mechanical-systems)

##### REFERENCES

[**hAI! Spec GitHub repository**](https://github.com/Uralstech/hAI-Spec-NASA-SpaceApps-Challenge)

[**hAI! Spec HuggingFace model**](https://huggingface.co/uralstech/hAI-Spec-Merged)

### The Conclusion

Sadly, we didn't win the competition. Yeah. But we got certificates!

![My certificate!](/assets/img/2024-02-28-NASA-SpaceApps-Challenge/Certificate.png)