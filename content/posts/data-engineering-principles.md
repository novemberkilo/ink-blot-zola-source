+++
title = "Data Engineering Principles"
date = 2020-05-25
tags = ["data engineering"]
+++

Here are a set of principles that guide the design and development of systems responsible
for transforming data. They come from experience with this general domain but they originated
from my experience at Ambiata. I was taught these principles by Russell Aronson, Mark Hibberd and
Nick Hibberd, and I am very grateful to them.

Perhaps the fundamental principle is this first one.

#### Expect data to be poor quality

Expect data to be poorly formed, to have errors, and work defensively to detect and correct this.
Failure to adopt this principle is indicated by sporadic failures in data pipelines with diagnoses
that point to unexpected data. These are usually accompanied by complaints, fingers pointed at 
the source of the data, and judgement passed. The code base ends up with churny fixes that 
patch the system to cope with these point problems - a whack-a-mole approach if you will.

Here are some examples of poor data quality in a data feed that should be expected
and accepted:
  - Date-formats that change over time
  - Schemas that change - columns disappear or appear
  - File formats change (dos, unix)
  - The size/amount varies suddenly (possibly indicating errors upstream to the system)

The remainder of the principles are mostly in response to this fundamental principle.

#### Measure the characteristics of the data

If we expect the data to be poor quality, it behooves us to gather information about data so that
we know what is being ingested and whether it should be processed. Here are some characteristics
that are relatively straightforward to collect and store as metadata:
  - Size
  - Header
  - Number of columns
  - Number of rows
  - Means, medians, quartiles of numeric data
  - Uniqueness of entries in col x
  - Type inference for column x (what primitive type does the column show up as - String, Int, Double or Bool)

A corollary is that we need steps in the pipeline to act on these measurements. This is usually
a stage of the pipeline that has operational responsibility - it typically occurs soon after
ingestion/extraction and compares incoming data with measurements from similar events. It raises
alerts and/or stops the pipeline from processing data that is deemed suspect.

#### Normalise and enrich the data

Have a transformation step to normalize the data to formats and schemas the pipeline can expect. The
sorts of normalising transformations that fit this step include:
  - Standardising file and character encodings (use command line tools like `file` and `iconv`)
  - Transforming different date and time formats
  - Standardising separators, padding etc. (this also allows for detection of mangled quoting in CSVs for example)

#### Maintain provenance information

Information pertaining to the provenance of data in the system enables the ability to trace
or backtrack through transformations. It helps with diagnosing issues but also with definitively being able
to answer questions about when and from where, data entered any stage of the pipeline. The next
principle follows on quite naturally from this.

#### Keep the data immutable

At every step in the pipeline, source data should always be available exactly as it was first encountered.
This will address:
  - Concerns around processing corrupt or otherwise mangled data
  - Easier and faster debugging or troubleshooting
  - The ability to re-run any component or stage of the pipeline in isolation

This will mean storing multiple copies of data but the copies should have different contexts (typically
corresponding to the stages of the pipeline).

#### Monitor information loss

As data traverses the pipeline it can lose information. This should not be accidental.
Consciously allow this to happen, or not.

---

Hopefully these principles are thought provoking and help provide some structure. They are not a complete
set by any means - please be in touch if you see errors or have suggestions for improvements. I recommend
Mark Hibberd's [Lake, Swamp or Puddle: Data Quality at Scale](http://mth.io/talks/data-quality/)
for more on the topic.
