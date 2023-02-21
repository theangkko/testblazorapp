# testblazorapp

```

name: 01 Deploy Blazor WASM to GitHub Page
env:
  PUBLISH_DIR: release/wwwroot
  
on:
  # Runs on pushes targeting the default branch
  push:
    branches: [ "main" ]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:


jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        
      - name: Publish application
        run: dotnet publish -c Release -o release
        
       # base href url value should be changed so that resources like CSS and scripts can load properly. 
      - name: Rewrite base href
        if: success()
        uses: SteveSandersonMS/ghaction-rewrite-base-href@v1
        with:
          html_path: ${{ env.PUBLISH_DIR }}/index.html
          base_href: /testblazorapp/
        
      # add .nojekyll file to tell GitHub pages to not treat this as a Jekyll project. (Allow files and folders starting with an underscore)
      - name: Add .nojekyll file
        run: touch ${{ env.PUBLISH_DIR }}/.nojekyll

      - name: Commit to GitHub pages Repo
        if: success()
        uses: crazy-max/ghaction-github-pages@v1.5.1
        with:
          target_branch: gh-pages
          build_dir: ${{ env.PUBLISH_DIR }}
        env:
          GITHUB_TOKEN: ${{ secrets.PUBLISH_TOKEN }}  

```
