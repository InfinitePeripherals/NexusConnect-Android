---
layout: default
#Change the title below to reflect your project. This title will be shown on the left panel as Menu item.
title: Documentation
nav_order: 1
has_children: false
---

# A Sample Documentation Title
{: .fs-9 .no_toc }

This is a sample description for the documentation. Please change this to reflect your project.
{: .fs-5 .fw-300 }

---
<!-- This section reserved for the Table of Contents. Don't remove it. -->
<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>
---

<!-- Add your documentation content below this line -->

## Create Your Repo
Create a public repo to host your public documentation

## Add Docs Folder
We should separate documentation and other things in the repo. So go ahead and create a `/docs` folder in the root of your repo to host the document files.

## Activate GitPages
We need to activate GitPages for this public repo, so GitHub can render these documentation files as webpages.

1. Go to your repo's *Settings*
2. Select *Pages* on the left panel
3. In the *Source* section:
    - Select the *Branch* that you want GitPages to pull documentation from
    - Select the folder that contains the documentation files. In this case, it should be `/docs` folder that we created earlier.
    - Click *Save*

## Add Contents
Now we start adding the documentation files and resources to the /docs folder. 

1. Download the `/docs` folder from GitPages-Document-Template repo.
2. Copy all the contents in there to your `/docs` folder. The `_sass` folder contains theme info, and the `images` folder contains Infinite Peripherals logo used for the documentation.
3. Update `_config.yml` file to reflect your documentation's info. Specifically, you should update the `title` and `aux_link`.
4. Update `index.md` file with your documentation contents. The theme will build webpages using markdown files that are in your repo.

## Commit & Push
Once you done adding contents, commit your works and push to the *Branch* that we specified earlier in repo's Settings > Pages.

## Troubleshooting
If you don't see your page show up at the *https://infiniteperipherals.github.io/your-project* link, check the commit status in GitHub.com to see if there is any issue with the push.