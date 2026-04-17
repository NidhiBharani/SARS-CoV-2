# SARS-CoV-2 Project Documentation

This directory provides a maintainers-oriented documentation layer for the `galaxyproject/SARS-CoV-2` repository.

It complements the existing analysis pages in the repository with:

- architecture-level documentation
- operational documentation for automated updates
- cross-domain workflow mapping across genomics, cheminformatics, proteomics, direct RNA-seq, and ARTIC analyses

## Documents in this folder

1. [Architecture](./ARCHITECTURE.md)
2. [Workflow and Operations](./WORKFLOW_OPERATIONS.md)
3. [Maintenance Guide](./MAINTENANCE_GUIDE.md)

## What this repository is

At a high level, this repository is both:

- a VuePress-based website (`yarn develop`, `yarn build`) for publishing analysis documentation
- a data-and-workflow companion repository containing Galaxy workflow definitions, run artifacts, notebooks, and update automation scripts

## Scope boundaries

- Most analysis content is already documented in domain `README.md` files under top-level folders.
- This documentation focuses on how the repository is structured and operated as a system.
