Spacy custom NER model
# Auto-fill base template
ner_path="src/ner"
# The downloaded file from spaCy
config_file_path_input="$(pwd)/$ner_path/base_config.cfg"
# The output file we will use for training
config_file_path_output="$(pwd)/$ner_path/config_final_gpu_blank.cfg"
python -m spacy init fill-config "$config_file_path_input" \
"$config_file_path_output"

# Train spaCy model on NER annotations
output_path="$(pwd)/models/ag_dataset/ner/ner-gpu-blank"
train_path="$(pwd)/data/ag_dataset/ner/annotations/binary/train"
dev_path="$(pwd)/data/ag_dataset/ner/annotations/binary/eval"
python -m spacy train "$config_file_path_output" \
--output "$output_path" --paths.train "$train_path" \
--paths.dev "$dev_path" --training.max_epochs 30 --gpu-id 0 --verbose