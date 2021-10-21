# ASM Resource-Announcement
This document provides additional documentation and supplemental data supporting the **ATCC Genome Portal** (AGP, https://genomes.atcc.org) manuscript currently under development. 
## Data Usage Agreement
All whole genome assemblies from the ATCC Genome Portal are avialable for Research use Only. Researchers who are interested in downloading individual genomes must 1/ create an account on ATCC's main website (https://www.atcc.org), and 2/ agree to ATCC's Data Use Agreement when downloading data from the AGP. A copy of the Data Use Agreement can be found here: https://www.atcc.org/policies/product-use-policies/data-use-agreement.
## API Access
Programatic access to the data found within the ATCC Genome Portal is available via a REST API. Documentation for the API is hosted on SwaggerHub here: https://app.swaggerhub.com/apis/bcamarda/genome-portal_api/v1. *__WARNING: the API is still under development and may change at any time, so please do not use this API for any production bioinformatics pipeline.__*
## Raw FASTQ Data
Raw data for all ATCC Genome Portal assemblies is subject to the same Data Use Agreement described above. Bulk download of Illumina and Nanopore data is available from our MS Azure Blog storage space. The base URL is https://atccbioinformatics.blob.core.windows.net/publicdata, to which you would add a specific suffix for a compressed tarball for both the ONT and Illumina from each assembly. The specific suffix list for all assemblies is maintained in a separate GitHub repo ([AGP-Raw-Data](https://github.com/ATCC-Bioinformatics/AGP-Raw-Data)) to be shared upon request.
## Technical Details on AGP Content and Methods
General details on the lab methods and bioinformatics pipelines used to create the whole genome assemblies on the ATCC Genome Portal can be found at the locations below. Details on the methods used for a specific strain are avilable on request, but in general will conform to the details below.
### Lab Methods
* **Cell Line Authentication:** Each strain is first verified and authenticated in accordance with ATCC’s quality management system, which complies with several internationally recognized quality assurance standards (ISO 9001, ISO 17025, ISO 17034). Some materials are also produced under ISO 13485 for the production of materials that can be used as controls in clinical diagnostics. The specific protocols for authentication vary by the type of organism in question. For example, bacterial strain authentication includes assessment of colony morphology, gram staining, culture purity, metabolic profiling, antibiotic susceptibility testing (AST), broad-spectrum biochemical reactivity testing, 16S rRNA gene sequencing, ribotyping, matrix-assisted laser desorption/ionization time-of-flight mass spectrometry (i.e. bioMérieux VITEK MS system), and whole-genome next-generation sequencing (NGS). Additional details on ATCC's Quality Management Standards can be found here: https://www.atcc.org/about-us/quality-commitment.
* **Culturing:** All lab culture conditions were performed according to each specific strain's recommended protocols that are included in each strain's product information sheet. For example, detailed on how Staphylococcus aureus subsp. aureus Rosenbach (BAA-1717) was cultured can be found on the product information page (https://www.atcc.org/products/baa-1717) under the "Handling Information" section. 
* **Extraction:** High-quality DNA and RNA extraction is the critical starting point to creating a complete reference-grade genome. ATCC uses several  protocols to obtain high-molecular-weight extractions from our microbial portfolio; the method chosen is dependent on the organism undergoing extraction. For strains with no available genomic DNA in ATCC’s repository, total high molecular weight genomic DNA (HMW gDNA) was extracted from thawed or resuspended frozen cultures with 10^7 -10^9 cells/mL using the QIAGEN Genomic-Tip 20/g or 100/g kit and analyzed for purity, concentration and fragment size.  HMW-gDNA samples meeting or exceeding the following criteria were subjected to sequencing; median fragment size larger than 20 kb, optical density A260/280 between 1.75 – 2.00, and a final elution concentration over 20ng/µL per extraction. Each sample of HMW gDNA was split in half for both short- and long-read sequencing workflows.
* **Library Prep & Sequencing:** For Ilumina sequencing, libraries from each extraction were prepared using Illumina's DNA Prep kit and indexed using Illumina's DNA/RNA UD indexes, and subsequently subjected to paired-end sequencing on either an Illumina MiSeq® or NextSeq 2000® instrument. For long-read sequencing on Oxford Nanopore, we typically use the ONT Ligation Sequencing Kit (Oxford Nanopore, UK, SQK-LSK109) with the same physical samples of HMW gDNA used for Illumina sequencing, multiplex using the ONT Native Barcoding Expansion kit (Oxford Nanopore, UK, EXP-NBD104 or EXP-NBD114), and sequence on a GridION (Oxford Nanopore, UK, R9.4.1).
* **Illumina Data QC:** Base-calling and adapter trimming was initially done using onboard Illumina instrument software and followed by an additional round of trimming and quality-score filtering using fastp and FastQC. Illumina reads accepted for further use passed the following quality control thresholds: median Q score, all bases > 30, median Q score, per base > 25, ambiguous content (% N bases) < 5%.
* **Oxford Nanopore QC:** The latest version of ONT's MinKNOW is used to base-call, using the high accuracy settings, demultiplex, and barcode trim. Futhermore, ONT sequencing reads were quality trimmed and filtered using Filtlong to meet the following minimum acceptance criteria: minimum mean Q score per read > 10, minimum read length > 5000 bp 23.
### Bioinformatics Methods
We use the [One Codex](https://www.onecodex.com) microbial genomics platform extensively for taxonomic profiling, de novo assembly, and genome annotation. A brief description of  is below.
![ocpipeline](https://user-images.githubusercontent.com/64888/138312171-ec43b80f-5520-48f2-be0d-a201fc1742bd.png)
* **Read-Based Contamination QC:**  First, read-level k-mer–based taxonomic classification is carried out and and estimation of strain abundances on all our processed Illumina read sets, which represent a highly-accurate snap shot of a given DNA extraction. A minimum of 1,000,000 Illumina reads per sequenced sample is required to undergo such analysis; Illumina read sets otherwise passing quality control criteria but possess less than 1,000,000 reads are sent for re-sequencing. When an Illumina read set is confirmed as an isolate by the One Codex platform, all read sets from that extraction continue to genome assembly. Please note that the results of this reads-based analysis are not currently presented on the portal but that all published genomes have passed our stringent thresholds for purity.
* **de novo Assembly:** The de novo assembly pipeline for the ATCC Genome Portal is also hosted on One Codex's backend. Briefly, genome size is first estimated from raw reads using MASH, and this estimate was used to down-sample the Illumina and ONT raw sequencing libraries to a maximum 100x and 40x coverage respectively. After down-sampling each sequencing library, a hybrid de novo assembly approach was taken using Unicyler. Sequencing and assembly artifacts of less than 1000 bp that also had significantly different coverage depth (e.g., “chaff” contigs) were removed from the final draft reference. These draft assemblies were subsequently checked using One Codex to confirm the species. Finally, each draft assembly was checked for completeness and potential contamination with CheckM v1.12+. Assemblies which were determined to have a CheckM “completeness” score above 95% and a contamination value below 5% were deemed final, complete assembles. Each final bacterial assembly was subsequently annotated using Prokka v1.14 for CDS, rRNA, tRNA, signal leader peptides, and non-coding RNA identification.
## Software & Services Used
* Busco - https://gitlab.com/ezlab/busco/
* CheckM - https://github.com/Ecogenomics/CheckM
* fastp - https://github.com/OpenGene/fastp
* FastQC - https://github.com/s-andrews/FastQC
* FiltLong - https://github.com/rrwick/Filtlong
* FLYE - https://github.com/fenderglass/Flye/
* MasUrca - https://github.com/alekseyzimin/masurca
* MASH - https://github.com/marbl/Mash
* MinKnow - https://github.com/nanoporetech/minknow_api
* One Codex - https://onecodex.com 
* Prokka - https://github.com/tseemann/prokka
* SPADes - https://github.com/ablab/spades
* Unicycler - https://github.com/rrwick/Unicycler
