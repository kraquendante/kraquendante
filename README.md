# See https://github.com/actions/starter-workflows/blob/1067f16ad8a1eac328834e4b0ae24f7d206f810d/ci/pylint.yml for original reference file
name: Run Linting/Formatting on Pull Requests

on:
  - push
  - pull_request
  # See https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#onpull_requestpull_request_targetbranchesbranches-ignore for syntax docs
  # if you want to filter out branches, delete the `- pull_request` and uncomment these lines :
  # pull_request:  
  #  branches:
  #    - master
  #  branches-ignore:
  #    - development

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v3
      - name: Set up Python 3.10
        uses: actions/setup-python@v3
        with:
          python-version: 3.10.6
      - uses: actions/cache@v2
        with:
          path: ~/.cache/pip
          key: ${{ runner.os }}-pip-${{ hashFiles('**/requirements.txt') }}
          restore-keys: |
            ${{ runner.os }}-pip-
      - name: Install PyLint
        run: |
         python -m pip install --upgrade pip
          pip install pylint
      # This lets PyLint check to see if it can resolve imports
      - name: Install dependencies
        run : |
          export COMMANDLINE_ARGS="--skip-torch-cuda-test --exit"
          python launch.py
      - name: Analysing the code with pylint
        run: |
          pylint $(git ls-files '*.py')
