# Visualization API Endpoint Additions

The GDC Visualization Suite uses the same API as the files and their metadata and takes advantage of three new endpoints:

* __ssms:__ The simple somatic mutation (`ssms`) endpoint allows users to access information about each somatic point mutation.   
* __ssm_occurrences:__ A SSM entity as applied to a single instance (case).
* __genes:__ The `genes` endpoint allows users to access in-depth information about each gene.  

The methods for retrieving information from these endpoint are very similar to those used for the `cases` and `files` endpoints. These methods are explored in depth in the [API Search and Retrieval](https://docs.gdc.cancer.gov/API/Users_Guide/Search_and_Retrieval/) documentation.

While not an endpoint, the `observation` entity is featured in the visualization portion of the API. The `observation` entity provides information from the MAF file, such as read depth and normal genotype, that supports the assessment that the associated `ssm` was observed.  

[![Viz Model](images/Viz_Model.png)](images/Viz_Model.png "Click to see the full image.")


## Endpoint Examples

__Example 1:__ A user would like to access information about the gene `ZMPSTE24`, which has an Ensembl gene ID of `ENSG00000084073`.  This would be accomplished by appending the Ensembl gene ID (`gene_id`) to the `genes` endpoint.

```Shell
curl "https://gdc-api-staging.datacommons.io/genes/ENSG00000084073?pretty=true"
```

```Response
{
  "data": {
    "description": "This gene encodes a member of the peptidase M48A family. The encoded protein is a zinc metalloproteinase involved in the two step post-translational proteolytic cleavage of carboxy terminal residues of farnesylated prelamin A to form mature lamin A. Mutations in this gene have been associated with mandibuloacral dysplasia and restrictive dermopathy. [provided by RefSeq, Jul 2008]",
    "cytoband": [
      "1p34.2"
    ],
    "gene_start": 40258107,
    "symbol": "ZMPSTE24",
    "gene_id": "ENSG00000084073",
    "gene_chromosome": "1",
    "synonyms": [
      "FACE-1",
      "HGPS",
      "PRO1",
      "STE24",
      "Ste24p"
    ],
    "is_cancer_gene_census": null,
    "biotype": "protein_coding",
    "gene_end": 40294184,
    "canonical_transcript_id": "ENST00000372759",
    "gene_strand": 1,
    "name": "zinc metallopeptidase STE24"
  },
  "warnings": {}
}
```

__Example 2:__ A user wants a list of coordinates for all genes on chromosome 7. The query can be filtered for only results from chromosome 7 using a JSON-formatted query that is URL-encoded.

```
curl "https://gdc-api-staging.datacommons.io/genes?pretty=true&fields=gene_id,symbol,gene_start,gene_end&format=tsv&size=2000&filters=%7B%0D%0A%22op%22%3A%22in%22%2C%0D%0A%22content%22%3A%7B%0D%0A%22field%22%3A%22gene_chromosome%22%2C%0D%0A%22value%22%3A%5B%0D%0A%227%22%0D%0A%5D%0D%0A%7D%0D%0A%7D"
```

## Analysis Endpoints

In addition the three endpoints mentioned previously, several `analysis` endpoints were designed to quickly retrieve specific datasets used for visualization display.  

* __analysis/survival__
* __analysis/top_cases_counts_by_genes__
* __analysis/top_mutated_genes_by_project__
* __analysis/top_mutated_cases_by_gene__
* __analysis/top_mutated_cases_by_ssm__
* __analysis/mutated_cases_count_by_project__

__Example 1:__  This gives the number of cases with a mutation in each gene listed in the `gene_ids` parameter.  Note that this endpoint cannot be used with the `format` or `fields` parameters.
```
curl "https://gdc-api-staging.datacommons.io/analysis/top_cases_counts_by_genes?gene_ids=ENSG00000155657&pretty=true"
```

__Example 2:__ The following demonstrates a use of the `analysis/top_mutated_genes_by_project` endpoint.  This will output the top mutated genes for "TCGA-DLBC" and only count the mutations that have a `HIGH` or `MODERATE` impact on gene function.

```json
{  
   "op":"AND",
   "content":[  
      {  
         "op":"in",
         "content":{  
            "field":"case.project.project_id",
            "value":[  
               "TCGA-DLBC"
            ]
         }
      },
      {  
         "op":"in",
         "content":{  
            "field":"case.ssm.consequence.transcript.annotation.impact",
            "value":[  
               "HIGH",
               "MODERATE"
            ]
         }
      }
   ]
}
```
```Shell
curl "https://gdc-api-staging.datacommons.io/analysis/top_mutated_genes_by_project?fields=gene_id,symbol&filters=%7B%22op%22%3A%22AND%22%2C%22content%22%3A%5B%7B%22op%22%3A%22in%22%2C%22content%22%3A%7B%22field%22%3A%22case.project.project_id%22%2C%22value%22%3A%5B%22TCGA-DLBC%22%5D%7D%7D%2C%7B%22op%22%3A%22in%22%2C%22content%22%3A%7B%22field%22%3A%22case.ssm.consequence.transcript.annotation.impact%22%2C%22value%22%3A%5B%22HIGH%22%2C%22MODERATE%22%5D%7D%7D%5D%7D"
```


__Example 3:__ The `analysis/top_mutated_cases_by_gene` endpoint will generate information about the cases that are most affected by mutations in a given number of genes.  Below, the file count for each category is given for the cases most affected by mutations in these 50 genes.

```
curl "https://gdc-api-staging.datacommons.io/analysis/top_mutated_cases_by_gene?fields=diagnoses.days_to_death,diagnoses.age_at_diagnosis,diagnoses.vital_status,diagnoses.primary_diagnosis,demographic.gender,demographic.race,demographic.ethnicity,case_id,summary.data_categories.file_count,summary.data_categories.data_category&filters=%7B%22op%22%3A%22and%22%2C%22content%22%3A%5B%7B%22op%22%3A%22%3D%22%2C%22content%22%3A%7B%22field%22%3A%22cases.project.project_id%22%2C%22value%22%3A%22TCGA-DLBC%22%7D%7D%2C%7B%22op%22%3A%22in%22%2C%22content%22%3A%7B%22field%22%3A%22genes.gene_id%22%2C%22value%22%3A%5B%22ENSG00000166710%22%2C%22ENSG00000005339%22%2C%22ENSG00000083857%22%2C%22ENSG00000168769%22%2C%22ENSG00000100906%22%2C%22ENSG00000184677%22%2C%22ENSG00000101680%22%2C%22ENSG00000101266%22%2C%22ENSG00000028277%22%2C%22ENSG00000140968%22%2C%22ENSG00000181827%22%2C%22ENSG00000116815%22%2C%22ENSG00000275221%22%2C%22ENSG00000139083%22%2C%22ENSG00000112851%22%2C%22ENSG00000112697%22%2C%22ENSG00000164134%22%2C%22ENSG00000009413%22%2C%22ENSG00000071626%22%2C%22ENSG00000135407%22%2C%22ENSG00000101825%22%2C%22ENSG00000104814%22%2C%22ENSG00000166415%22%2C%22ENSG00000142867%22%2C%22ENSG00000254585%22%2C%22ENSG00000139718%22%2C%22ENSG00000077721%22%2C%22ENSG00000130294%22%2C%22ENSG00000117245%22%2C%22ENSG00000117318%22%2C%22ENSG00000270550%22%2C%22ENSG00000163637%22%2C%22ENSG00000166575%22%2C%22ENSG00000065526%22%2C%22ENSG00000156453%22%2C%22ENSG00000128191%22%2C%22ENSG00000055609%22%2C%22ENSG00000204469%22%2C%22ENSG00000187605%22%2C%22ENSG00000185875%22%2C%22ENSG00000110888%22%2C%22ENSG00000007341%22%2C%22ENSG00000173198%22%2C%22ENSG00000115568%22%2C%22ENSG00000163714%22%2C%22ENSG00000125772%22%2C%22ENSG00000080815%22%2C%22ENSG00000189079%22%2C%22ENSG00000120837%22%2C%22ENSG00000143951%22%5D%7D%7D%2C%7B%22op%22%3A%22in%22%2C%22content%22%3A%7B%22field%22%3A%22ssms.consequence.transcript.annotation.impact%22%2C%22value%22%3A%5B%22HIGH%22%5D%7D%7D%5D%7D&pretty=true&size=50"
```



```
curl "https://gdc-api-staging.datacommons.io/analysis/mutated_cases_count_by_project?size=0&pretty=true"
```
### Survival Analysis

[Survival plots](link) are generated for different subsets of data, based on variants or projects, in the GDC Data Portal. The `analysis/survival` endpoint can be used to programmatically retrieve the raw data used to generate these plots and apply different filters to the data. Note that the `fields` and `format` parameters cannot be modified.

 __Example:__ A user wants to download data to generate a survival for cases from the project TCGA-DLBC.

```Shell
curl "https://gdc-api-staging.datacommons.io/analysis/survival?filters=%5B%7B%22op%22%3A%22%3D%22%2C%22content%22%3A%7B%22field%22%3A%22cases.project.project_id%22%2C%22value%22%3A%22TCGA-DLBC%22%7D%7D%5D&pretty=true"
```
```json
{
  "overallStats": {},
  "results": [
    {
      "donors": [
        {
          "survivalEstimate": 1,
          "id": "dc87a809-95de-4eb7-a1c2-2650475f2d7e",
          "censored": true,
          "time": 1
        },
        {
          "survivalEstimate": 1,
          "id": "4dd86ebd-ef16-4b2b-9ea0-5d1d7afef257",
          "censored": true,
          "time": 17
        },
        {
          "survivalEstimate": 1,
          "id": "0bf573ac-cd1e-42d8-90cf-b30d7b08679c",
          "censored": false,
          "time": 58
        },
        {
          "survivalEstimate": 0.9777777777777777,
          "id": "f978cb0f-d319-4c01-b4c5-23ae1403a106",
          "censored": true,
          "time": 126
        },
        {
          "survivalEstimate": 0.9777777777777777,
          "id": "a43e5f0e-a21f-48d8-97e0-084d413680b7",
          "censored": true,
          "time": 132
        },
        {
          "survivalEstimate": 0.9777777777777777,
          "id": "1843c82e-7a35-474f-9f79-c0a9af9aa09c",
          "censored": true,
          "time": 132
        },
        {
          "survivalEstimate": 0.9777777777777777,
          "id": "0030a28c-81aa-44b0-8be0-b35e1dcbf98c",
          "censored": false,
          "time": 248
        },
        {
          "survivalEstimate": 0.9539295392953929,
          "id": "f553f1a9-ecf2-4783-a609-6adca7c4c597",
          "censored": true,
          "time": 298
        },
        {
          "survivalEstimate": 0.9539295392953929,
          "id": "f784bc3a-751b-4025-aab2-0af2f6f24266",
          "censored": false,
          "time": 313
        },
        {
          "survivalEstimate": 0.929469807518588,
          "id": "29e3d122-15a1-4235-a356-b1a9f94ceb39",
          "censored": true,
          "time": 385
        },
        {
          "survivalEstimate": 0.929469807518588,
          "id": "0e251c03-bf86-4ed8-b45d-3cbc97160502",
          "censored": false,
          "time": 391
        },
        {
          "survivalEstimate": 0.9043490019099776,
          "id": "e6365b38-bc44-400c-b4aa-18ce8ff5bfce",
          "censored": true,
          "time": 427
        },
        {
          "survivalEstimate": 0.9043490019099776,
          "id": "b56bdbdb-43af-4a03-a072-54dd22d7550c",
          "censored": true,
          "time": 553
        },
        {
          "survivalEstimate": 0.9043490019099776,
          "id": "31bbad4e-3789-42ec-9faa-1cb86970f723",
          "censored": false,
          "time": 595
        },
        {
          "survivalEstimate": 0.8777505018538018,
          "id": "0e9fcccc-0630-408d-a121-2c6413824cb7",
          "censored": true,
          "time": 679
        },
        {
          "survivalEstimate": 0.8777505018538018,
          "id": "a5b188f0-a6d3-4d4a-b04f-36d47ec05338",
          "censored": false,
          "time": 708
        },
        {
          "survivalEstimate": 0.8503207986708705,
          "id": "ed746cb9-0f2f-48ce-923a-3a9f9f00b331",
          "censored": true,
          "time": 719
        },
        {
          "survivalEstimate": 0.8503207986708705,
          "id": "c85f340e-584b-4f3b-b6a5-540491fc8ad2",
          "censored": true,
          "time": 730
        },
        {
          "survivalEstimate": 0.8503207986708705,
          "id": "69f23725-adca-48ac-9b33-80a7aae24cfe",
          "censored": true,
          "time": 749
        },
        {
          "survivalEstimate": 0.8503207986708705,
          "id": "67325322-483f-443f-9ffa-2a20d108a2fb",
          "censored": true,
          "time": 751
        },
        {
          "survivalEstimate": 0.8503207986708705,
          "id": "eda9496e-be80-4a13-bf06-89f0cc9e937f",
          "censored": true,
          "time": 765
        },
        {
          "survivalEstimate": 0.8503207986708705,
          "id": "25ff86af-beb4-480c-b706-f3fe0306f7cf",
          "censored": true,
          "time": 788
        },
        {
          "survivalEstimate": 0.8503207986708705,
          "id": "1d0db5d7-39ca-466d-96b3-0d278c5ea768",
          "censored": true,
          "time": 791
        },
        {
          "survivalEstimate": 0.8503207986708705,
          "id": "c8cde9ea-89e9-4ee8-8a46-417a48f6d3ab",
          "censored": true,
          "time": 832
        },
        {
          "survivalEstimate": 0.8503207986708705,
          "id": "f0a326d2-1f3e-4a5d-bca8-32aaccc52338",
          "censored": true,
          "time": 946
        },
        {
          "survivalEstimate": 0.8503207986708705,
          "id": "a8e2df1e-4042-42af-9231-3a00e83489f0",
          "censored": true,
          "time": 965
        },
        {
          "survivalEstimate": 0.8503207986708705,
          "id": "e56e4d9c-052e-4ec6-a81b-dbd53e9c8ffe",
          "censored": true,
          "time": 972
        },
        {
          "survivalEstimate": 0.8503207986708705,
          "id": "45b0cf9f-a879-417f-8f39-7770552252c0",
          "censored": true,
          "time": 982
        },
        {
          "survivalEstimate": 0.8503207986708705,
          "id": "1f971af1-6772-4fe6-8d35-bbe527a037fe",
          "censored": true,
          "time": 1081
        },
        {
          "survivalEstimate": 0.8503207986708705,
          "id": "33365d22-cb83-4d8e-a2d1-06b675f75f6e",
          "censored": true,
          "time": 1163
        },
        {
          "survivalEstimate": 0.8503207986708705,
          "id": "6a21c948-cd85-4150-8c01-83017d7dc1ed",
          "censored": false,
          "time": 1252
        },
        {
          "survivalEstimate": 0.8003019281608192,
          "id": "f855dad1-6ffc-493e-ba6c-970874bc9210",
          "censored": true,
          "time": 1299
        },
        {
          "survivalEstimate": 0.8003019281608192,
          "id": "c1c06604-5ae2-4a53-b9c0-eb210d38e3f0",
          "censored": true,
          "time": 1334
        },
        {
          "survivalEstimate": 0.8003019281608192,
          "id": "58e66976-4507-4552-ac53-83a49a142dde",
          "censored": true,
          "time": 1373
        },
        {
          "survivalEstimate": 0.8003019281608192,
          "id": "ea54dbad-1b23-41cc-9378-d4002a8fca51",
          "censored": true,
          "time": 1581
        },
        {
          "survivalEstimate": 0.8003019281608192,
          "id": "d7df78b5-24f1-4ff4-bd9b-f0e6bec8289a",
          "censored": true,
          "time": 1581
        },
        {
          "survivalEstimate": 0.8003019281608192,
          "id": "29aff186-c321-4ff9-b81b-105e27e620ff",
          "censored": true,
          "time": 1617
        },
        {
          "survivalEstimate": 0.8003019281608192,
          "id": "5eff68ff-f6c3-40c9-9fc8-00e684a7b712",
          "censored": true,
          "time": 1739
        },
        {
          "survivalEstimate": 0.8003019281608192,
          "id": "f8cf647b-1447-4ac3-8c43-bef07765cabf",
          "censored": true,
          "time": 2131
        },
        {
          "survivalEstimate": 0.8003019281608192,
          "id": "c3d662ee-48d0-454a-bb0c-77d3338d3747",
          "censored": true,
          "time": 2983
        },
        {
          "survivalEstimate": 0.8003019281608192,
          "id": "6e9437f0-a4ed-475c-ab0e-bf1431c70a90",
          "censored": true,
          "time": 3333
        },
        {
          "survivalEstimate": 0.8003019281608192,
          "id": "fdecb74f-ac4e-46b1-b23a-5f7fde96ef9f",
          "censored": true,
          "time": 3394
        },
        {
          "survivalEstimate": 0.8003019281608192,
          "id": "a468e725-ad4b-411d-ac5c-2eacc68ec580",
          "censored": false,
          "time": 3553
        },
        {
          "survivalEstimate": 0.6402415425286554,
          "id": "1ea575f1-f731-408b-a629-f5f4abab569e",
          "censored": true,
          "time": 3897
        },
        {
          "survivalEstimate": 0.6402415425286554,
          "id": "7a589441-11ef-4158-87e7-3951d86bc2aa",
          "censored": true,
          "time": 4578
        },
        {
          "survivalEstimate": 0.6402415425286554,
          "id": "3622cf29-600f-4410-84d4-a9afeb41c475",
          "censored": true,
          "time": 5980
        },
        {
          "survivalEstimate": 0.6402415425286554,
          "id": "3f5a897d-1eaa-4d4c-8324-27ac07c90927",
          "censored": false,
          "time": 6425
        }
      ],
      "meta": {
        "id": 140429063094496
      }
    }
  ]
}
```
