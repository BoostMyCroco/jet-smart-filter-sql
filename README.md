# JetSmartFilters SQL Query for Filtering

This repository contains a custom SQL query designed for advanced filtering with [JetSmartFilters](https://crocoblock.com/plugins/jetsmartfilters/). The query is optimized to handle dynamic date ranges, ratings, and additional conditions.

## Features

- Handles dynamic `start_date` and `end_date` with `BETWEEN`.
- Automatically adjusts the date range to include flexible conditions.
- Supports fallback values for missing filters.
- Compatible with `JetSmartFilters` macros for dynamic filtering.

## Query Overview

The query processes dynamic inputs from JetSmartFilters to filter WordPress posts by:

- `start_date` (with a one-day offset if needed).
- `end_date` (with inclusive logic).
- A rating filter for further customization.

### How to Use
Configure JetSmartFilters:

Ensure your meta_query keys (start_date, end_date, and rating) are properly configured.
Use the correct macros to generate dynamic inputs.
Insert the Query:

Add the query into your WordPress or database management system to test the results.
Adjust Fallbacks:

Modify fallback values (1131097600 for start_date, 2147483647 for end_date, and 0 for rating) if needed to match your use case.

### Youtube Video

https://youtube.com/@boostmycroco

### Query Structure

```sql
SELECT 
    posts.ID,
    posts.post_title,
    start_date_meta.meta_value AS start_date,
    end_date_meta.meta_value AS end_date,
    image_meta.meta_value AS image,
    rating_meta.meta_value AS rating
FROM 
    wp_posts AS posts
INNER JOIN 
    wp_postmeta AS start_date_meta 
    ON posts.ID = start_date_meta.post_id 
    AND start_date_meta.meta_key = 'start_date'
INNER JOIN 
    wp_postmeta AS end_date_meta 
    ON posts.ID = end_date_meta.post_id 
    AND end_date_meta.meta_key = 'end_date'
INNER JOIN 
    wp_postmeta AS image_meta 
    ON posts.ID = image_meta.post_id 
    AND image_meta.meta_key = '_thumbnail_id'
INNER JOIN 
    wp_postmeta AS rating_meta 
    ON posts.ID = rating_meta.post_id 
    AND rating_meta.meta_key = 'service_average_rating'
WHERE 
    start_date_meta.meta_value BETWEEN 
        (CAST('%jsf_filter_query|meta|start_date||plain%{"fallback":"1131097600","context":""}' AS UNSIGNED) - 86400)
        AND '%jsf_filter_query|meta|end_date||plain%{"fallback":"2147483647","context":""}' 
    AND rating_meta.meta_value >= '%jsf_filter_query|meta|rating||plain%{"fallback":"0","context":""}'
ORDER BY 
    start_date_meta.meta_value ASC;




