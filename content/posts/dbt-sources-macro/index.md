---
title: "dbt: How to loop over sources in a dbt macro"
date: 2024-07-23
draft: false
summary: "Learn how to use Jinja within dbt macros to extract table names from your sources.yml file and automate SQL query generation."
tags: ["dbt", "data engineering"]
---

## Automate Table Name Extraction from `sources.yml` in dbt Macros

In a recent project, I needed to automate the creation of Snowflake views for table names listed in my `sources.yml` file using dbt. This task, while seemingly straightforward, 
was difficult to find documentation on in my usual two places (dbt documentation and the dbt slack channel - both amazing resources). Here's how I used Jinja within a dbt macro to extract these names and generate the necessary SQL queries.

The following code snippet demonstrates how to loop over entries in `sources.yml`, extracting each table name and executing a Snowflake query to create corresponding views. This approach can be adapted to fit different use cases as needed.



    
    {% macro create_replica_view() %}
 
     {% set sources = [] %}
     {% for node in graph.sources.values() %}
         {%- do sources.append(node.name) %}
     {% endfor %}
     
     {%- for source_name in sources %}
 
     {%- set query %}
         CREATE OR REPLACE VIEW PRESENTATION_DATABASE.SCHEMA.{{ source_name }} AS
         SELECT *
         FROM MODEL_DATABASE.SCHEMA.{{ source_name }}
     {% endset -%}
     {% do run_query(query) %}
 
     {% endfor %}
     
     {% endmacro %}

 
By using this macro, you can streamline the process of managing and updating views in your SQL database, ensuring consistency and efficiency across your data engineering workflows.