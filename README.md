# MLOps Docs

## (1) Install

```shell
pip install sphinx
pip install sphinx_rtd_them
```

## (2) 내용 추가 
```shell
source
├── TEST # 추가
│   ├── TEST_1.md # 추가
│   └── TEST_2.md # 추가
├── _static
├── _templates
├── conf.py
├── favicon.ico
└── index.rst
```

```rust
// source/index.rst

.. toctree::
   :maxdepth: 2
   :caption: TEST: // 목차 이름

   A/a_1.md // 내용
   A/a_2.md // 내용
```
```
source/index.rst

.. toctree::
   :maxdepth: 2
   :caption: TEST: 

   TEST/TEST_1.md 
   TEST/TEST_2.md 


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
```


## (3) 문서 빌드
```shell
cd docs
make html
```