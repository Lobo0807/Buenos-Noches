name: Build

on:
  push:
    branches:
      - 'D'
    tags-ignore:
      - '**'
    paths-ignore:
      - 'showcase/**'
      - README.md

jobs:
  windows:
    runs-on: windows-2019
    steps:
    - uses: actions/checkout@v2
    - uses: actions/setup-node@v1
      with:
        node-version: 12
    - uses: actions/cache@v2
      id: yarn-cache
      with:
        path: node_modules
        key: ${{ runner.os }}-node-modules-${{ hashFiles('**/yarn.lock') }}
    - run: yarn --frozen-lockfile
      if: steps.yarn-cache.outputs.cache-hit != 'true'
    - run: yarn electron:publish
      env:
        GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    - name: Hash
      run: type .\dist_electron\latest.yml

