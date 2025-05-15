# Start analysis from command line on VGP instance.

## Find/Create API key

The API key can be found/created at [https://vgp.usegalaxy.org/user/api_key](https://vgp.usegalaxy.org/user/api_key)

## Setup environment

Create environment
```bash
python3.9 -m venv venv && source venv/bin/activate
```

Install planemo
```bash
pip3 install planemo
```

## Configure profile
To make it easier to run planemo we can setup a profile handling the connection with `http://vgp.usegalaxy.org`.
```bash
planemo profile_create --engine external_galaxy --galaxy_url https://vgp.usegalaxy.org --galaxy_user_key XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX vgp
```

The profile can be found at `~/.planemo/profiles/`

## Workflow

The align_chain_net.ga workflow can be found at [https://github.com/galaxyproject/alignment-pipeline/blob/main/workflows/align_chain_net/align_chain_net.ga](https://github.com/galaxyproject/alignment-pipeline/blob/main/workflows/align_chain_net/align_chain_net.ga). (Verified as accessible on [20250515])

This workflow can be imported to galaxy at https://vgp.usegalaxy.org/workflows/import or downloaded using curl. If you import the workflow it can later be found at  

https://vgp.usegalaxy.org/workflows/list

and if you press run/edit/share you will be able to find the workflow id in the url

- https://vgp.usegalaxy.org/workflows/run?id=**WORKFLOW_ID**
- https://vgp.usegalaxy.org/workflows/edit?id=**WORKFLOW_ID**
- https://vgp.usegalaxy.org/workflows/sharing?id=**WORKFLOW_ID**

WORKFLOW_ID example `1dbfdd2e4b984bae`

## Create workflow job input configuration

Planemo can generate the job input file using the workflow file
```bash
planemo workflow_job_init align_chain_net/align_chain_net.ga -o job.yaml
```

The documentation can be found at [Generate the job file](https://planemo.readthedocs.io/en/latest/running.html#generating-the-job-file)

For this workflow the actual job input file is simple
```yaml
QUERY_Sequence:
  class: File
  path: PATH_TO/fasta_file_1.fna
TARGET_Sequence:
  class: File
  path: PATH_TO/fasta_file_2.fna
```

**Example local file**
```yaml
QUERY_Sequence:
  class: File
  path: GCF_000008865.2_ASM886v2_genomic.fna
TARGET_Sequence:
  class: File
  path: GCF_000005845.2_ASM584v2_genomic.fna
```

**Example files stored public accessible**
```yaml
QUERY_Sequence:
  class: File
  location: https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/008/865/GCF_000008865.2_ASM886v2/GCF_000008865.2_ASM886v2_genomic.fna.gz
TARGET_Sequence:
  class: File
  location: https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/005/845/GCF_000005845.2_ASM584v2/GCF_000005845.2_ASM584v2_genomic.fna.gz
```

## Run planemo
After the job input file and profile has been created it is easy to run the analysis. There are many
options of running the analysis and here's a few example:

**NOTE:** It is recommended to use an imported workflow by submitting with a WORKFLOW_ID. Submitting a workflow file each time will upload and import a new copy, leading to redundant entries in the workflow list. Using an imported workflow id also allows you to list all invocations created by that workflow—whereas workflows uploaded on-the-fly will each have only a single associated invocation.

Submit the analysis and don't wait until the job has completed/failed, will only wait for any files to be upload to the galaxy instance.
```bash
# Using the actual workflow file
planemo run align_chain_net.ga job.yaml --profile vgp --no_wait --output_json task.json --history_name MyAnalysis1
# Using imported worfklow
planemo run WORKFLOW_ID job.yaml --profile vgp --no_wait --output_json task.json --history_name MyAnalysis1
```

Wait for the job to complete and download the output
```bash
# Using the actual workflow file
planemo run align_chain_net.ga job.yaml --profile vgp --no_wait --output_json task.json --download_outputs --output_directory result_data --history_name MyAnalysis1
# Using imported worfklow
planemo run WORKFLOW_ID job.yaml --profile vgp --no_wait --output_json task.json --download_outputs --output_directory result_data --history_name MyAnalysis1
```

## List analysis/invocations running,finished,failed.

The analysis/invocation can be found on [https://vgp.usegalaxy.net](https://vgp.usegalaxy.net) or listed using the command line

```bash
planemo list_invocations WORKFLOW_ID --profile vgp
```


