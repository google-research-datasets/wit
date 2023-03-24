# The Wikipedia Webpage 2M (WikiWeb2M) Dataset

We present the WikiWeb2M dataset consisting of over 2 million English
Wikipedia articles. Our released dataset includes all of the text content on
each page, links to the images present, and structure metadata such as which
section each text and image element comes from.

This dataset is a contribution from the paper
[A Suite of Generative Tasks for Multi-Level Multimodal Webpage Understanding]().
The dataset is stored as gzipped TFRecord files which can be downloaded via these links.

Train:

Val: [https://storage.googleapis.com/gresearch/wit/wikiweb2m/wikiweb2m-val.tfrecord.gz](wikiweb2m-val.tfrecord.gz)

Test: [https://storage.googleapis.com/gresearch/wit/wikiweb2m/wikiweb2m-test.tfrecord.gz](wikiweb2m-test.tfrecord.gz)



## WikiWeb2M Statistics

WikiWeb2M is the first multimodal open source dataset to include all page
content in a unified format. Here we provide aggregate information about the
WikiWeb2M dataset as well as the number of samples available with each of the
fine-tuning tasks we design from it.

| Number of | Train | Validation | Test |
| ---- | ---- | ---- | ---- |
| Pages | 1,803,225 | 100,475 | 100,833 |
| Sections | 10,519,294 | 585,651 | 588,552 |
| Unique Images | 3,867,277 | 284,975 | 286,390 |
| Total Images | 5,340,708 | 299,057 | 300,666 |

Our data processing and filtering choices for each fine-tuning task are
described in the paper.

| Downstream Task Samples | Train | Validation | Test |
| ---- | ---- | ---- | ---- |
| Page Description Generation | 1,435,263 | 80,103 | 80,339 |
| Section Summarization | 3,082,031 | 172,984 | 173,591 |
| Contextual Image Captioning | 2,222,814 | 124,703 | 124,188 |


## Data and Task Examples

Here we illustrate how a single webpage can be processed into the three tasks we
study: page description generation, section summarization, and contextual image
captioning. The paper includes multiple Wikipedia article examples.


## Usage

### TFRecord Features

Here we provide the names of the fields included in the dataset, their
tensorflow Sequence Example type, their data type, and a brief description.

![Illustration of Succulents Wikipedia Article being used for page description generation, section summarization, and contextual image captioning](datamotivation.png){width="10"}


| Feature | Sequence Example Type | DType | Description |
| ---- | ---- | ---- | ---- |
| `split` | Context | string | Dataset split this page contributes to (e.g., train, val, or test) |
| `page_url` | Context | string | Wikipeda page URL |
| `page_title` | Context | string | Wikipedia page title, title of the article |
| `raw_page_description` | Context | string | Wikipedia page description, which is typically the same or very similar to the content of the first (root) section of the article |
| `clean_page_description` | Context | string | `raw_page_description` but with newline and tab characters removed; this provides the exact target text for our page description generation task |
| `page_contains_images` | Context | int64 | Whether the Wikipedia page has images after our cleaning and processing steps |
| `page_content_sections_without_table_list` | Context | int64 | Number of content sections with text or images that do not contain a list or table. This field can be used to reproduce data filtering for page description generation |
| `is_page_description_sample` | Context | int64 | Whether a page is used as a sample for the page description fine-tuning task |
| `section_title` | Sequence | string | Titles of each section on the Wikipedia page, in order |
| `section_index` | Sequence | int64 | Index of each section on the Wikipedia page, in order |
| `section_depth` | Sequence | int64 | Depth of each section on the Wikipedia page, in order |
| `section_heading_level` | Sequence | int64 | Heading level of each section on the Wikipedia page, in order |
| `section_subsection_index` | Sequence | int64 | Subsection indices, grouped by section in order |
| `section_parent_index` | Sequence | int64 | The parent section index of each section, in order |
| `section_text` | Sequence | string | The body text of each section, in order |
| `is_section_summarization_sample` | Sequence | int64 | Whether a section is used as a sample for the section summarization fine-tuning task |
| `section_raw_1st_sentence` | Sequence | string | The processed out first sentence of each section, in order |
| `section_clean_1st_sentence` | Sequence | string | The same as `section_raw_1st_sentence` but with newline and tab characters removed. This provides the exact target text for our section summarization task |
| `section_rest_sentence` | Sequence | string | The processed out sentences following the first sentence of each section, in order |
| `section_contains_table_or_list` | Sequence | int64 | Whether section content contains a table or list; this field is needed to be able to reproduce sample filtering for section summarization |
| `section_contains_images` | Sequence | int64 | Whether each section has images after our cleaning and processing steps, in order |
| `is_image_caption_sample` | Sequence | int64 | Whether an image is used as a sample for the image captioning fine-tuning task |
| `section_image_url` | Sequence | string | Image URLs, grouped by section in order |
| `section_image_mime_type` | Sequence | string | Image mime type, grouped by section in order |
| `section_image_width` | Sequence | int64 | Image width, grouped by section in order |
| `section_image_height` | Sequence | int64 | Image height, grouped by section in order |
| `section_image_in_wit` | Sequence | int64 | Whether an image was originally contained in the WIT dataset, grouped by section in order |
| `section_image_raw_attr_desc` | Sequence | string | Image attribution description, grouped by section in order |
| `section_image_clean_attr_desc` | Sequence | string | The English only processed portions of the attribution description |
| `section_image_raw_ref_desc` | Sequence | string | Image reference description, grouped by section in order |
| `section_image_clean_ref_desc` | Sequence | string | The same as `section_image_raw_ref_desc` but with newline and tab characters removed; this provides the exact target text for our image captioning task |
| `section_image_alt_text` | Sequence | string | Image alt-text, grouped by section in order |
| `section_image_captions` | Sequence | string | Comma separated concatenated text from alt-text, attribution, and reference descriptions; this is how captions are formatted as input text when used |


### Loading the Data

Here we provide a small code snippet for how to load the TFRecord files. First,
load any necessary packages.

```python
import numpy as np
import glob
import tensorflow.compat.v1 as tf
from collections import defaultdict
```

Next, define a data parser class.
~~~markdown
```python
class DataParser():
  def __init__(self,
               filepath: str = 'wikiweb2m-*',
               path: str):
    self.filepath = filepath
    self.path = path
    self.data = defaultdict(list)

  def parse_data(self):
    context_feature_description = {
        'split': tf.io.FixedLenFeature([], dtype=tf.string),
        'page_title': tf.io.FixedLenFeature([], dtype=tf.string),
        'page_url': tf.io.FixedLenFeature([], dtype=tf.string),
        'clean_page_description': tf.io.FixedLenFeature([], dtype=tf.string),
        'raw_page_description': tf.io.FixedLenFeature([], dtype=tf.string),
        'is_page_description_sample': tf.io.FixedLenFeature([], dtype=tf.int64),
        'page_contains_images': tf.io.FixedLenFeature([], dtype=tf.int64),
        'page_content_sections_without_table_list': tf.io.FixedLenFeature([] , dtype=tf.int64)
    }

    sequence_feature_description = {
        'is_section_summarization_sample': tf.io.VarLenFeature(dtype=tf.int64),
        'section_title': tf.io.VarLenFeature(dtype=tf.string),
        'section_index': tf.io.VarLenFeature(dtype=tf.int64),
        'section_depth': tf.io.VarLenFeature(dtype=tf.int64),
        'section_heading_level': tf.io.VarLenFeature(dtype=tf.int64),
        'section_subsection_index': tf.io.VarLenFeature(dtype=tf.int64),
        'section_parent_index': tf.io.VarLenFeature(dtype=tf.int64),
        'section_text': tf.io.VarLenFeature(dtype=tf.string),
        'section_clean_1st_sentence': tf.io.VarLenFeature(dtype=tf.string),
        'section_raw_1st_sentence': tf.io.VarLenFeature(dtype=tf.string),
        'section_rest_sentence': tf.io.VarLenFeature(dtype=tf.string),
        'is_image_caption_sample': tf.io.VarLenFeature(dtype=tf.int64),
        'section_image_url': tf.io.VarLenFeature(dtype=tf.string),
        'section_image_mime_type': tf.io.VarLenFeature(dtype=tf.string),
        'section_image_width': tf.io.VarLenFeature(dtype=tf.int64),
        'section_image_height': tf.io.VarLenFeature(dtype=tf.int64),
        'section_image_in_wit': tf.io.VarLenFeature(dtype=tf.int64),
        'section_contains_table_or_list': tf.io.VarLenFeature(dtype=tf.int64),
        'section_image_captions': tf.io.VarLenFeature(dtype=tf.string),
        'section_image_alt_text': tf.io.VarLenFeature(dtype=tf.string),
        'section_image_raw_attr_desc': tf.io.VarLenFeature(dtype=tf.string),
        'section_image_clean_attr_desc': tf.io.VarLenFeature(dtype=tf.string),
        'section_image_raw_ref_desc': tf.io.VarLenFeature(dtype=tf.string),
        'section_image_clean_ref_desc': tf.io.VarLenFeature(dtype=tf.string),
        'section_contains_images': tf.io.VarLenFeature(dtype=tf.int64)
    }

    def _parse_function(example_proto):
      return tf.io.parse_single_sequence_example(example_proto,
                                                 context_feature_description,
                                                 sequence_feature_description)

    suffix = '.tfrecord*'

    data_path = glob.Glob(self.path + self.filepath + suffix)
    raw_dataset = tf.data.TFRecordDataset(data_path, compression_type='GZIP')
    parsed_dataset = raw_dataset.map(_parse_function)

    for d in parsed_dataset:
      split = d[0]['split'].numpy().decode()
      self.data[split].append(d)
```
~~~

Then you can run the following to parse the dataset.
```python
parser = DataParser()
parser.parse_data()
print((len(parser.data['train']), len(parser.data['val']), len(parser.data['test'])))
```
### Models
Our full attention, transient global, and prefix global experiments were run
using the [LongT5](https://github.com/google-research/longt5) code base. In
coming months the Prefix Global attention mechanism may be open sourced.


## How to Cite

If you extend or use this work, please cite the [paper]() where it was
introduced:

```
@misc{burns2023wiki,
      title={A Suite of Generative Tasks for Multi-Level Multimodal Webpage Understanding},
      author={Andrea Burns and Krishna Srinivasan and Joshua Ainslie and Geoff Brown and Bryan A. Plummer and Kate Saenko and Jianmo Ni and Mandy Guo},
      year={2023},
      eprint={},
      archivePrefix={arXiv},
      primaryClass={cs.CL}
}
```
