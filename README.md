# Label Studio Converter Fork

## Fork Purpose
This repository is a fork of the now-archived [Label Studio Converter](https://github.com/HumanSignal/label-studio-converter), which was merged into the [Label Studio SDK](https://github.com/HumanSignal/label-studio-sdk/tree/master/src/label_studio_sdk/converter).

## Why This Fork?
- **Dependency Management**: The `label-parser` project relies on this package. To ensure compatibility and prevent disruptions due to the repository's archival, we forked it to retain availability and control.
- **Extensibility**: This fork allows us to introduce fixes or enhancements, such as the patch for supporting null spans.
- **Version Control**: We maintain this fork to track specific versions (`0.0.43` in this case, specifically commit 9efe8eea10c413eb37784e3758c5df11da9d1da1) used in `label-parser`.

## Changes in This Fork
- **Allow Null Spans**: Applied a patch to handle cases where spans are null or empty.

### Patch:
```diff
@@ -239,7 +239,7 @@ class Converter(object):
             for item in item_iterator(input_data):
                 tokens, tags = create_tokens_and_tags(
                     text=item['input'][data_key],
-                    spans=next(iter(item['output'].values()))
+                    spans=next(iter(item['output'].values())) if len(item['output'].values()) else []
                 )
                 for token, tag in zip(tokens, tags):
                     fout.write('{token} -X- _ {tag}\n'.format(token=token, tag=tag))
```
- **Bump requests**: minimum version to 2.31.0 for compatibility with pyartparse requests version

<br><br><br>

# Label Studio Converter

[Website](https://labelstud.io/) • [Docs](https://labelstud.io/guide) • [Twitter](https://twitter.com/heartexlabs) • [Join Slack Community <img src="https://go.heartex.net/docs/images/slack-mini.png" width="18px"/>](https://docs.google.com/forms/d/e/1FAIpQLSdLHZx5EeT1J350JPwnY2xLanfmvplJi6VZk65C2R4XSsRBHg/viewform?usp=sf_link)

## Table of Contents

- [Introduction](#introduction)
- [Examples](#examples)
    - [JSON](#json)
    - [CSV](#csv)
    - [CoNLL 2003](#conll-2003)
    - [COCO](#coco)
    - [Pascal VOC XML](#pascal-voc-xml)
- [Contributing](#contributing)
- [License](#license)

## Introduction

Label Studio Format Converter helps you to encode labels into the format of your favorite machine learning library.

## Examples

#### JSON
Running from the command line:
```bash
python backend/converter/cli.py --input examples/sentiment_analysis/completions/ --config examples/sentiment_analysis/config.xml --output tmp/output.json
```

Running from python:
```python
from converter import Converter

c = Converter('examples/sentiment_analysis/config.xml')
c.convert_to_json('examples/sentiment_analysis/completions/', 'tmp/output.json')
```

Getting output file: `tmp/output.json`
```json
[
  {
    "reviewText": "Good case, Excellent value.",
    "sentiment": "Positive"
  },
  {
    "reviewText": "What a waste of money and time!",
    "sentiment": "Negative"
  },
  {
    "reviewText": "The goose neck needs a little coaxing",
    "sentiment": "Neutral"
  }
]
```

Use cases: any tasks


#### CSV
Running from the command line:
```bash
python backend/converter/cli.py --input examples/sentiment_analysis/completions/ --config examples/sentiment_analysis/config.xml --output tmp/output.tsv --format CSV --csv-separator $'\t'
```

Running from python:
```python
from converter import Converter

c = Converter('examples/sentiment_analysis/config.xml')
c.convert_to_csv('examples/sentiment_analysis/completions/', 'tmp/output.tsv', sep='\t', header=True)
```

Getting output file `tmp/output.tsv`:
```tsv
reviewText	sentiment
Good case, Excellent value.	Positive
What a waste of money and time!	Negative
The goose neck needs a little coaxing	Neutral
```

Use cases: any tasks

#### CoNLL 2003

Running from the command line:
```bash
python backend/converter/cli.py --input examples/named_entity/completions/ --config examples/named_entity/config.xml --output tmp/output.conll --format CONLL2003
```

Running from python:
```python
from converter import Converter

c = Converter('examples/named_entity/config.xml')
c.convert_to_conll2003('examples/named_entity/completions/', 'tmp/output.conll')
```

Getting output file `tmp/output.conll`
```text
-DOCSTART- -X- O
Showers -X- _ O
continued -X- _ O
throughout -X- _ O
the -X- _ O
week -X- _ O
in -X- _ O
the -X- _ O
Bahia -X- _ B-Location
cocoa -X- _ O
zone, -X- _ O
...
```

Use cases: text tagging


#### COCO
Running from the command line:
```bash
python backend/converter/cli.py --input examples/image_bbox/completions/ --config examples/image_bbox/config.xml --output tmp/output.json --format COCO --image-dir tmp/images
```

Running from python:
```python
from converter import Converter

c = Converter('examples/image_bbox/config.xml')
c.convert_to_coco('examples/image_bbox/completions/', 'tmp/output.conll', output_image_dir='tmp/images')
```

Output images could be found in `tmp/images`

Getting output file `tmp/output.json`
```json
{
  "images": [
    {
      "width": 800,
      "height": 501,
      "id": 0,
      "file_name": "tmp/images/62a623a0d3cef27a51d3689865e7b08a"
    }
  ],
  "categories": [
    {
      "id": 0,
      "name": "Planet"
    },
    {
      "id": 1,
      "name": "Moonwalker"
    }
  ],
  "annotations": [
    {
      "id": 0,
      "image_id": 0,
      "category_id": 0,
      "segmentation": [],
      "bbox": [
        299,
        6,
        377,
        260
      ],
      "ignore": 0,
      "iscrowd": 0,
      "area": 98020
    },
    {
      "id": 1,
      "image_id": 0,
      "category_id": 1,
      "segmentation": [],
      "bbox": [
        288,
        300,
        132,
        90
      ],
      "ignore": 0,
      "iscrowd": 0,
      "area": 11880
    }
  ],
  "info": {
    "year": 2019,
    "version": "1.0",
    "contributor": "Label Studio"
  }
}
```

Use cases: image object detection

#### Pascal VOC XML
Running from the command line:
```bash
python backend/converter/cli.py --input examples/image_bbox/completions/ --config examples/image_bbox/config.xml --output tmp/voc-annotations --format VOC --image-dir tmp/images
```

Running from python:
```python
from converter import Converter

c = Converter('examples/image_bbox/config.xml')
c.convert_to_voc('examples/image_bbox/completions/', 'tmp/output.conll', output_image_dir='tmp/images')
```

Output images can be found in `tmp/images`

Corresponding annotations could be found in `tmp/voc-annotations/*.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<annotation>
<folder>tmp/images</folder>
<filename>62a623a0d3cef27a51d3689865e7b08a</filename>
<source>
<database>MyDatabase</database>
<annotation>COCO2017</annotation>
<image>flickr</image>
<flickrid>NULL</flickrid>
</source>
<owner>
<flickrid>NULL</flickrid>
<name>Label Studio</name>
</owner>
<size>
<width>800</width>
<height>501</height>
<depth>3</depth>
</size>
<segmented>0</segmented>
<object>
<name>Planet</name>
<pose>Unspecified</pose>
<truncated>0</truncated>
<difficult>0</difficult>
<bndbox>
<xmin>299</xmin>
<ymin>6</ymin>
<xmax>676</xmax>
<ymax>266</ymax>
</bndbox>
</object>
<object>
<name>Moonwalker</name>
<pose>Unspecified</pose>
<truncated>0</truncated>
<difficult>0</difficult>
<bndbox>
<xmin>288</xmin>
<ymin>300</ymin>
<xmax>420</xmax>
<ymax>390</ymax>
</bndbox>
</object>
</annotation>
```

Use cases: image object detection

## Contributing

We would love to get your help for creating converters to other models. Please feel free to create pull requests.

- [Contributing Guideline](/CONTRIBUTING.md)
- [Code Of Conduct](/CODE_OF_CONDUCT.md)

## License

This software is licensed under the [Apache 2.0 LICENSE](/LICENSE) © [Heartex](https://www.heartex.ai/). 2020

<img src="https://github.com/heartexlabs/label-studio/blob/master/images/opossum_looking.png?raw=true" title="Hey everyone!" height="140" width="140" />
