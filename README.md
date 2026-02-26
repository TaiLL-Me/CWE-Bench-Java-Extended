# CWE-Bench-Java-Extended

A comprehensive Java vulnerability benchmark dataset containing **130 vulnerabilities** across **12 CWE categories** from **103 real-world projects**, enabling realistic evaluation of Java vulnerability detection approaches.

| CWE-ID  | CVE Count |
| ------- | --------- |
| CWE-20  | 1         |
| CWE-22  | 45        |
| CWE-73  | 1         |
| CWE-74  | 5         |
| CWE-78  | 12        |
| CWE-79  | 10        |
| CWE-89  | 4         |
| CWE-94  | 11        |
| CWE-284 | 1         |
| CWE-502 | 14        |
| CWE-611 | 15        |
| CWE-918 | 11        |
| Total   | 130       |


## Project Identifier

In this dataset, each project is uniquely identified with a **Project Slug**, encompassing its repository name, CVE ID, and a tag corresponding to the buggy version of the project. We show one example below:

```
perwendel__spark_CVE-2018-9159_2.7.1
^^^^^^^^^  ^^^^^ ^^^^^^^^^^^^^ ^^^^^
|          |     |             |--> Version Tag
|          |     |--> CVE ID
|          |--> Repository name
|--> Github Username
```

All the patches, advisory information, build information, and fix information are associated with project slugs. Since there are 130 projects in the CWE-Bench-Java-Extended dataset, we have 130 unique project slugs. Note that a single repository may be found to have different CVEs in different versions.

## Packaged Data

```
- data/
  - project_info.csv
  - fix_info.csv
- dataset/
  - <project_slug> (total 130)
```

The core set of information in this dataset lies in two files, `data/project_info.csv` and `data/fix_info.csv`. `dataset` contains the dependencies required for each CVE project.

### Project Info

| id   | project_slug                         | cve_id        | cwe_id  | cwe_name       | github_username | github_repository_name | github_tag | github_url                         | advisory_id         | buggy_commit_id                          | fix_commit_ids                           |
| ---- | ------------------------------------ | ------------- | ------- | -------------- | --------------- | ---------------------- | ---------- | ---------------------------------- | ------------------- | ---------------------------------------- | ---------------------------------------- |
| 1    | perwendel__spark_CVE-2018-9159_2.7.1 | CVE-2018-9159 | CWE-022 | Path Traversal | perwendel       | spark                  | 2.7.1      | https://github.com/perwendel/spark | GHSA-76qr-mmh8-cp8f | 5316c0d0f057daaf556c3907c20df975f7bf8a8a | 030e9d00125cbd1ad759668f85488aba1019c668 |

Each row in `data/project_info.csv` looks like the example above. We now get into each field and explain what they are.

- `id`: an integer from 1 to 130
- `project_slug`: (explained in the previous section)
- `cve_id`: a common vulnerability identifier `CVE-XXXX-XXXXX`
- `cwe_id`: a common weakness enumeration (CWE) identifier. In our dataset, there is only `CWE-022`, `CWE-078`, `CWE-079`, `CWE-094`
- `cwe_name`: the name of the CWE
- `github_username`: the user/organization that owns the repository on Github
- `github_repository_name`: the repository name on Github
- `github_tag`: the tag associated with the version where the vulnerability is found; usually a version tag
- `github_url`: the URL to the github repository
- `advisory_id`: the advisory ID in Github Security Advisory database
- `buggy_commit_id`: the commit hash (like `5316c0d0f057daaf556c3907c20df975f7bf8a8a`) where the vulnerability can be reproduced. For some vulnerabilities, this field may contain issue URLs or other references instead of commit hashes
- `fix_commit_ids`: the set of commit hashes (sequentially ordered and separated with semicolon `;`) corresponding to the fix of the vulnerability. Some projects may not have fix commit hashes available

### Fix Info

Each row in `data/fix_info.csv` looks like the following.

| project_slug                         | cve           | github_username | github_repository_name | commit                                                       | file                                                         | class                 | class_start | class_end | method                  | method_start | method_end | signature                                        |
| ------------------------------------ | ------------- | --------------- | ---------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------- | ----------- | --------- | ----------------------- | ------------ | ---------- | ----------------------------------------- |
| perwendel__spark_CVE-2018-9159_2.7.1 | CVE-2018-9159 | perwendel       | spark                  | `030e9d00125cbd1ad759668f85488aba1019c668;a221a864db28eb736d36041df2fa6eb8839fc5cd;ce9e11517eca69e58ed4378d1e47a02bd06863cc` | `src/main/java/spark/resource/ClassPathResource.java` | `ClassPathResource` | 41         | 252      | `ClassPathResource` | 75         | 86       | `ClassPathResource(String,ClassLoader)` |

- `project_slug`: the unique identifier of each project
- `cve_id`: the CVE id
- `github_username`: the user/organization that owns the repository on Github
- `github_repository_name`: the repository name on Github
- `commit`: the commit hash containing this fix
- `file`: the `.java` file that is fixed
- `class`: the name of the class that is fixed
- `class_start`, `class_end`: the start and end line number of the class. Line numbers differ between `.java` source files and `.class` bytecode files. Since our analysis does not rely on specific line numbers, some entries may have this field empty
- `method`: the name of the method that is fixed
- `method_start`, `method_end`: the start and end line number of the method. Same as above, some entries may have this field empty
- `signature`: the signature of the method. Note that we might have multiple overloaded methods with the same name but with different signatures

## Experiments

```
- experiments/
  - dataset(IRIS)/       # IRIS analysis results (available on Zenodo)
  - dataset(Tai-e)/      # Tai-e analysis results (available on Zenodo)
  - dataset(TaiLL-Me)/   # TaiLL-Me analysis results (available on Zenodo)
  - results/             # Evaluation results
```

The `experiments` folder contains analysis outputs from different taint analysis tools:

- `dataset(IRIS)`: Results from IRIS static analysis tool
- `dataset(Tai-e)`: Results from Tai-e static analysis framework
- `dataset(TaiLL-Me)`: Results from TaiLL-Me (our approach), including LLM-based chain rankings

Each subfolder contains 130 project directories matching the `dataset` structure, with analysis records such as detected taint flows and vulnerability chains.

**Note**: Due to their large size, the `dataset(IRIS)`, `dataset(Tai-e)`, and `dataset(TaiLL-Me)` directories are not included in this GitHub repository. The complete experimental data is available in the full dataset on Zenodo.

### Results Files

The `experiments/results/` directory contains evaluation results and cost analysis:

#### Evaluation Results (CSV)

Comparative evaluation results for different approaches on the CWE-Bench-Java-Extended dataset:

- **TaiLL-Me+claude.csv**: TaiLL-Me with claude-sonnet-4.5
- **TaiLL-Me+gemini-2.5-flash-nothinking.csv**: TaiLL-Me with Gemini 2.5 Flash (no thinking mode)
- **TaiLL-Me+gpt-5-mini.csv**: TaiLL-Me with GPT-5 Mini
- **TaiLL-Me+qwen3-coder-plus.csv**: TaiLL-Me with Qwen3-Coder-Plus
- **TaiLL-Me+nothing.csv**: TaiLL-Me baseline (no LLM support)
- **IRIS+qwen3-coder-plus.csv**: IRIS approach with Qwen3-Coder-Plus
- **Tai-e+qwen3-coder-plus.csv**: Tai-e approach with Qwen3-Coder-Plus

Each CSV file contains per-project evaluation metrics including:
- Project identification (project_slug, CVE ID, CWE ID)
- Analysis results (detected flows, true positives, false positives)
- Performance metrics (precision, recall, F1-score)

#### Cost Analysis (XLSX)

- **TaiLL-Me_analyze_costs.xlsx**: Comprehensive cost analysis for TaiLL-Me approach across all 130 projects

This spreadsheet includes:
- **Project Information**: Project slug, LOC size (lines of code)
- **LLM Analysis Flows**: Number of taint flows analyzed by each LLM (Claude, Gemini, GPT-5, Qwen3)
- **LLM API Calls**: Number of API calls made to each LLM service
- **Token Costs**: Estimated token consumption for each LLM (input + output tokens)
- **Total Costs**: Aggregated analysis costs per project and overall totals

The cost analysis provides insights into the computational resources and API costs required for large-scale vulnerability detection using LLM-assisted taint analysis.
