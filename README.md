# Production NLP 

## Training Named Entity Recognition (NER) model
```bash
    Spacy custom NER model
    # Auto-fill base template
    ner_path="src/ner"
    # The downloaded file from spaCy
    config_file_path_input="$(pwd)/$ner_path/base_config.cfg"
    # The output file we will use for training
    config_file_path_output="$(pwd)/$ner_path/config_final_gpu_blank.cfg"
    python -m spacy init fill-config "$config_file_path_input" \
    "$config_file_path_output"
```

```bash
    # Train spaCy model on NER annotations
    output_path="$(pwd)/models/ag_dataset/ner/ner-gpu-blank"
    train_path="$(pwd)/data/ag_dataset/ner/annotations/binary/train"
    dev_path="$(pwd)/data/ag_dataset/ner/annotations/binary/eval"
    python -m spacy train "$config_file_path_output" \
    --output "$output_path" --paths.train "$train_path" \
    --paths.dev "$dev_path" --training.max_epochs 30 --gpu-id 0 --verbose
```

## Training Text classification model
```bash
    # Generate config file
    # The output file we will use for training
    config_file_path_output="$(pwd)/src/textcat/base_config.cfg"
    python -m spacy init config "$config_file_path_output" --lang en \
    --pipeline textcat_multilabel --optimize efficiency --gpu --force

    annots_path="data/ag_dataset/textcat/annotations/binary/"
    output_path="$(pwd)/models/ag_dataset/textcat/full_labels"
    train_path="$(pwd)/$annots_path/train_full_labels"
    dev_path="$(pwd)/$annots_path/eval"
    python -m spacy train "$config_file_path_output" \
    --output "$output_path" --paths.train "$train_path" \
    --paths.dev "$dev_path" --gpu-id 0 --training.max_epochs 1 --verbose
```

## Building Machine Learning Pipeline with Databricks
We will show how to trigger the machine learning pipeline based on two mechanisms: (1) a schedule we set and (2) events.
One of the beautiful things about Databricks is that we can select a notebook to run
as part of a machine learning pipeline. Although it is best practice to create a script to
run your machine learning model in production (with CI/CD, etc.)