
name: Generate Profile Graphs

on:
  schedule:
    - cron: "0 0 * * *"
  workflow_dispatch:
  push:
    branches:
      - main

permissions:
  contents: write

jobs:
  generate:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Generate GitHub stats
        uses: stats-organization/github-readme-stats-action@v2
        with:
          card: stats
          options: "username=${{ github.repository_owner }}&show_icons=true&include_all_commits=true&count_private=true&theme=dracula"
          path: dist/stats.svg
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Generate language graph
        uses: stats-organization/github-readme-stats-action@v2
        with:
          card: top-langs
          options: "username=${{ github.repository_owner }}&layout=compact&langs_count=5&theme=dracula"
          path: dist/languages.svg
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Generate snake animation
        uses: Platane/snk/svg-only@v3
        with:
          github_user_name: ${{ github.repository_owner }}
          outputs: dist/snake.svg

      - name: Publish SVG files
        uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_branch: output
          publish_dir: ./dist
