# Assembly of SARS-CoV-2 from PreProcessed Reads

## The point

Use a combination of Illumina and Oxford Nanopore reads to produce SARS-CoV-2 genome assembly

## The outline

We used Illumina and Oxford Nanopore reads that were pre-processed to remove human-derived sequences. We use two assembly tools: [`SPAdes`](http://cab.spbu.ru/software/spades/) and [`Unicycler`](https://github.com/rrwick/Unicycler). While `SPAdes` is a tool fully dedicated to assembly `Unicycler` is a "wrapper" that combines multiple existing tools. It uses `SPAdes` as an engine for short read assembly while utilizing [`mimiasm`](https://github.com/lh3/miniasm) and [`Racon`](https://github.com/isovic/racon) for assembly of long noisy reads. 