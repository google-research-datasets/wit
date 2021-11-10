# Wikipedia-based Image Text (WIT) Dataset

WIT Dataset is now available for download and use.

WIT is also available as a [Tensorflow Dataset(TFDS)](https://www.tensorflow.org/datasets/catalog/wit).

We are providing WIT as a set of 10 tsv files (compressed). The total dataset
size is about ~25GB. If you want to start quick, pick any one file which will be
about ~2.5GB and will give you about ~10% of the data which has about ~3.5M+
image text example sets.

This is the entire training dataset. We are holding back the validation and
test sets for now. The validation and test datasets put together is about ~1% of
the data. We are planning to use this as the basis for a competition with the
WIT dataset. Please stay tuned and we will announce the details soon.

Here is a [1% data sample file](https://storage.googleapis.com/gresearch/wit/wit_v1.train.all-1percent_sample.tsv.gz) for a quick start.

## File Details

Each row of the WIT dataset consists of 17 columns and they are as follows in this order.

FIELD_NAME                              | FIELD_TYPE |
----------------------------------------|------------|
language                                | string     |
page_url                                | string     |
image_url                               | string     |
page_title                              | string     |
section_title                           | string     |
hierarchical_section_title              | string     |
caption_reference_description           | string     |
caption_attribution_description         | string     |
caption_alt_text_description            | string     |
mime_type                               | string     |
original_height                         | int        |
original_width                          | int        |
is_main_image                           | bool       |
attribution_passes_lang_id              | bool       |
page_changed_recently                   | bool       |
context_page_description                | string     |
context_section_description             | string     |

## Download Links

Here are the links to download the 10 files.

[wit_v1.train.all-00000-of-00010.tsv.gz](https://storage.googleapis.com/gresearch/wit/wit_v1.train.all-00000-of-00010.tsv.gz)

[wit_v1.train.all-00001-of-00010.tsv.gz](https://storage.googleapis.com/gresearch/wit/wit_v1.train.all-00001-of-00010.tsv.gz)

[wit_v1.train.all-00002-of-00010.tsv.gz](https://storage.googleapis.com/gresearch/wit/wit_v1.train.all-00002-of-00010.tsv.gz)

[wit_v1.train.all-00003-of-00010.tsv.gz](https://storage.googleapis.com/gresearch/wit/wit_v1.train.all-00003-of-00010.tsv.gz)

[wit_v1.train.all-00004-of-00010.tsv.gz](https://storage.googleapis.com/gresearch/wit/wit_v1.train.all-00004-of-00010.tsv.gz)

[wit_v1.train.all-00005-of-00010.tsv.gz](https://storage.googleapis.com/gresearch/wit/wit_v1.train.all-00005-of-00010.tsv.gz)

[wit_v1.train.all-00006-of-00010.tsv.gz](https://storage.googleapis.com/gresearch/wit/wit_v1.train.all-00006-of-00010.tsv.gz)

[wit_v1.train.all-00007-of-00010.tsv.gz](https://storage.googleapis.com/gresearch/wit/wit_v1.train.all-00007-of-00010.tsv.gz)

[wit_v1.train.all-00008-of-00010.tsv.gz](https://storage.googleapis.com/gresearch/wit/wit_v1.train.all-00008-of-00010.tsv.gz)

[wit_v1.train.all-00009-of-00010.tsv.gz](https://storage.googleapis.com/gresearch/wit/wit_v1.train.all-00009-of-00010.tsv.gz)


### Data related questions

For any questions, please contact wit-dataset@google.com.

We welcome feedback and will try our best to incorporate it. Feel free to file a github issue too.

Last but not least, if WIT dataset is useful to you, please do write to us about it. 
Be it a blog post, a research project or a paper you are working, we are delighted to learn about it.

