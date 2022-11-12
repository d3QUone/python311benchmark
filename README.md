# Django 4.2 + Python 3.11 benchmark

The main goal here is to measure how Python 3.11 internal speedups affect web frameworks performance. 
Here we compare Django 4.2 performance on **Python 3.10.7** vs **Python 3.11.0**.

All tests were done on Macbook M1. Steps to reproduce are at the end of the page.

TLDR: it's faster on Python 3.11.

## Positive highlights 

Which cases are way faster now:

| Name         | Difference |
|--------------| ---------|
| query_update | 4.2677x faster |
| query_delete_related | 3.8393x faster |
| query_count  | 3.6758x faster |
| url_reverse  | 2.0484x faster |
| query_dates | 1.6432x faster |
| query_exists | 1.6107x faster |
| query_delete | 1.5411x faster |
| query_latest | 1.5172x faster |
| url_resolve_nested | 1.5135x faster |
| template_compilation | 1.4355x faster |


## Negative highlights

Which cases are slower now:

| Name         | Difference |
|--------------|------------|
| raw_sql | 2.7545x slower |
| query_values | 1.6635x slower |
| query_aggregate | 1.5818x slower |
| query_iterator | 1.2973x slower |
| query_in_bulk | 1.2651x slower |
| query_complex_filter | 1.2029x slower |


## Conclusion

Almost all test points are faster based on avg data from 50 runs.

Raw results are available here in the [static folder](static/results.txt).


## How to reproduce

Here are steps to reproduce the results on your local machine. No need to clone this repo, just follow the commands.
Standard Django util is used: https://github.com/django/djangobench. 

Set up both 3.10 and 3.11 virtual envs. Installing benchmark util and Django in both envs.
```shell
cd ~/Developer
mkdir benchmarks
cd benchmarks

python3.10 -m venv .venv310
source .venv310/bin/activate

pip install -e git+https://github.com/django/djangobench.git#egg=djangobench
pip install django pytz

deactivate

python3.11 -m venv .venv311
source .venv311/bin/activate

pip install -e git+https://github.com/django/djangobench.git#egg=djangobench
pip install django pytz

git clone https://github.com/django/django.git
cd django
```

Run comparison 3.10 and 3.11 for the most recent Django version (4.2):
```shell
djangobench --vcs=none --control=. --experiment=. \
    --control-python=~/Developer/benchmarks/.venv310/bin/python \
    --experiment-python=~/Developer/benchmarks/.venv311/bin/python
```
