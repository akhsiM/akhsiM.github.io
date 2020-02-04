# Changelog

**v3.0 04/02/2020**
- Introduce exclusion for pages that cannot be built by Jekyll

**v2.1 03/09/2019**
- Update post template to allow image header
- Add cheatshet index page to show PDF files

**v2.0 29/08/2019**
- Update index.md
- New Post folder "Random"

**v1.0 - 22/08/2019**
- Page Launch

# To-dos
- Image in posts

# Exclusions

**Type 1: Pages that cannot be built by Jekyll** 

- Case 1: Learning/_posts/2020-02-04-Django.html

These are legitimate pages whose content body contains what looks like unknown Liquid tags to Jekyll.

The following workaround is applied:
- Exclude file in **_config.yml**
- Copy file to relevant folder in **_site**
- Remove datestamp
- Add direct URL on index page if needed


