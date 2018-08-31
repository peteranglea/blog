---
layout: post
title: How to create a sitemap.xml file for a Jekyll website
description: A simple step-by-step guide to creating an XML sitemap file suitable for Google in your Jekyll website
tags: 
- SEO
- Jekyll
---

The best way to make sure your content gets crawled by Google (and other search engines, for that matter), is to create an XML sitemap file.

A good sitemap file should:
- Follow the official Sitemap protocol (see [sitemaps.org](https://www.sitemaps.org/))
- Contain a list of all your site's pages and/or posts
- Indicate the relative priority of each URL
- Indicate the date of last modification for each URL
- Exclude any extraneous URLs

## Step 1: Create your sitemap.xml file in the website's root

Here's a starter file that will loop through each page and post in your Jekyll site and output the URL, last modified date, and priority for each. Just copy/paste this and save it as `sitemap.xml` in the root of your website.

```xml
{%raw%}---
layout: null
sitemap:
  exclude: 'yes'
---
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  {% for post in site.posts %}
    {% unless post.published == false %}
    <url>
      <loc>{{ site.url }}{{ post.url }}</loc>
      {% if post.sitemap.lastmod %}
        <lastmod>{{ post.sitemap.lastmod | date: "%Y-%m-%d" }}</lastmod>
      {% elsif post.date %}
        <lastmod>{{ post.date | date_to_xmlschema }}</lastmod>
      {% else %}
        <lastmod>{{ site.time | date_to_xmlschema }}</lastmod>
      {% endif %}
      {% if post.sitemap.changefreq %}
        <changefreq>{{ post.sitemap.changefreq }}</changefreq>
      {% else %}
        <changefreq>daily</changefreq>
      {% endif %}
      {% if post.sitemap.priority %}
        <priority>{{ post.sitemap.priority }}</priority>
      {% else %}
        <priority>0.5</priority>
      {%- endif -%}
    </url>
    {% endunless %}
  {% endfor %}
  {% for page in site.pages %}
    {% unless page.sitemap.exclude == "yes" or page.url contains "assets" %}
    <url>
      <loc>{{ site.url }}{{ page.url | remove: "index.html" }}</loc>
      {% if page.sitemap.lastmod %}
        <lastmod>{{ page.sitemap.lastmod | date: "%Y-%m-%d" }}</lastmod>
      {% elsif page.date %}
        <lastmod>{{ page.date | date_to_xmlschema }}</lastmod>
      {% else %}
        <lastmod>{{ site.time | date_to_xmlschema }}</lastmod>
      {% endif %}
      {% if page.sitemap.changefreq %}
        <changefreq>{{ page.sitemap.changefreq }}</changefreq>
      {% else %}
        <changefreq>daily</changefreq>
      {% endif %}
      {% if page.sitemap.priority %}
        <priority>{{ page.sitemap.priority }}</priority>
      {% else %}
        <priority>0.5</priority>
      {% endif %}
    </url>
    {% endunless %}
  {% endfor %}
</urlset>{%endraw%}
```

Notice how this file has front matter (as indicated by the dashes). This is so that Jekyll will parse this file and not just copy/paste it as-is into the `_site/` folder.

## Step 2: Manually exclude and prioritize individual posts with front matter

In each page or post on your site, you can manually set the priority (between 0–1) or exclude it from the sitemap entirely with some front matter.

**To manually set the priority, add:**
```yaml
sitemap:
  priority: 1 # default will be 0.5 otherwise
```

**To exclude the page from the sitemap, add:**
```yaml
sitemap:
  exclude: 'yes'
```

**Note:** you have to put `'yes'` in quotes. Otherwise, Jekyll will parse it as `true` and our sitemap.xml file is looking for a value of a literal string "yes".

## That's it

Now, everytime you rebuild your Jekyll site, all new pages and posts will automatically get added without any extra work from you, and the timestamp for every post will get updated, as well, which will help to ensure that Google crawls them more frequently.

All that remains is to submit your site to Google.

Watch for that in a future post.
