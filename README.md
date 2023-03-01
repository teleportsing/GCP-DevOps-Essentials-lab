# Troubleshooting Workloads on GKE for Site Reliability Engineers
Site Reliability Engineers (SRE) have a broad set of responsibilities, and managing incidents is a critical part of their role. You will learn how to take advantage of the integrated capabilities of Google Cloud's operations suite that includes logging, monitoring, and rich, out-of-the-box dashboards.

The troubleshooting process is an “iterative” approach where SREs form a hypothesis about the potential root cause of an incident, then filter, search, and navigate through large volumes of telemetry data collected from their systems to validate or invalidate their hypothesis. If a hypothesis is invalid, SREs will form another hypothesis and perform another iteration until they can isolate a root cause. One the Google website, see learn more about SREs at [Google Site Reliability Engineering](https://sre.google/).

In this lab, you will learn how to navigate that iterative journey efficiently and effectively using Google Cloud's operations tools!

# What you'll learn

 * In this lab, you will learn how to:

 * Navigate resource pages of Google Kubernetes Engine (GKE)

 * Leverage the GKE dashboard to quickly view operational data

 * Create logs-based metrics to capture specific issues

 * Create a Service Level Objective (SLO)

 * Define an Alert to notify SRE staff of incidents


# Scenario

Your organization has deployed a multi-tier microservices application. It is a web-based e-commerce application called "Hipster Shop", where users can browse for vintage items, add them to their cart and purchase them. Hipster Shop is composed of many microservices, written in different languages, that communicate via gRPC and REST APIs. The architecture of the deployment is optimized for learning purposes and includes modern technologies as part of the stack: Kubernetes, Istio, Cloud Operations, App Engine, gRPC, OpenTelemetry, and similar cloud-native technologies.

As a member of the Site Reliability Engineering (SRE) team, you are contacted when end users report issues viewing products and adding them to their cart. You will explore the various services deployed to determine the root cause of the issue and set up a Service Level Objective (SLO) to prevent similar incidents from occurring in the future. Learn more about this in the following blog article: SLOs, SLIs, SLAs, oh my—CRE life lessons.


# Task 1. Navigating Google Kubernetes Engine (GKE) resource pages

For the first part of this lab, you will view Google Kubernetes Engine (GKE) resource pages, then navigate to various metrics dashboards to investigate the issue reported by end users in more detail.
