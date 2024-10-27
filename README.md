## Example Base URL
```
http://localhost:5000
```

## Endpoints Overview

1. [Health Check](#health-check)
2. [Train Model](#train-model)
3. [Merge Files](#merge-files)
4. [Deduplicate Files](#deduplicate-files)
5. [Process CoNLL](#process-conll)

---

## Health Check

Check if the API is running.

```
GET /health
```

### Response

```json
{
    "status": "healthy"
}
```

---

## Train Model

Train a new NER model using provided data.

```
POST /train
```

### Request Body

```json
    "data_path": "./data/small-0001",  // Optional: Will use latest if not provided
    "base_model_path": "./models/finetuned_xlm_roberta", // Optional: Default model if not provided
    "custom_model_name": "medicine", // Optional: Custom name for the trained model
    "label_list": [                        // Optional: Custom label list
        "O",
        "B-PER", "I-PER",
        "B-ORG", "I-ORG",
        "B-LOC", "I-LOC",
        "B-MISC", "I-MISC"
    ],
    "training_args": {                     // Optional: Override default training arguments
        "learning_rate": 2e-5,
        "num_train_epochs": 3,
        "per_device_train_batch_size": 16
    }
}
```

### Postman Example

1. Method: POST
2. URL: `http://localhost:5000/train`
3. Headers:
   - Content-Type: application/json
4. Body (raw JSON):
```json
{
    "message": "Training completed successfully",
    "model_path": "./models/medicine-0002",
    "train_result": {
        "train_runtime": 1234.5678,
        "train_samples_per_second": 123.45
    },
    "eval_result": {
        "eval_accuracy": 0.95,
        "eval_f1": 0.94,
        "eval_loss": 0.123
    },
    "data_path": "./data/small-0001",
    "base_model_path": ".models/finetuned_xlm_roberta",
    "warnings": []
}
```

---

## Merge Files

Merge multiple CoNLL files into a single file.

```
POST /merge
```

### Request

- Content-Type: multipart/form-data
- Body: Multiple files with key 'files[]'

### Postman Example

1. Method: POST
2. URL: `http://localhost:5000/merge`
3. Body:
   - Type: form-data
   - Key: files[] (select File type)
   - Add multiple files with the same key name

### Response

```json
{
    "file_name": "merged_0006.conll",
    "file_path": "./data\\merged\\merged_0006.conll",
    "merged_files": [
        "1.conll",
        "2.conll"
    ],
    "message": "Files merged successfully",
    "number_of_files_merged": 2,
    "status": "Merged successfully",
    "success": true
}
```

---

## Deduplicate Files

Remove duplicate sentences from a CoNLL file.

```
POST /deduplicate
```

### Request

- Content-Type: multipart/form-data
- Body: Single file with key 'file'

### Postman Example

1. Method: POST
2. URL: `http://localhost:5000/deduplicate`
3. Body:
   - Type: form-data
   - Key: file (select File type)
   - Add a single .conll file

### Response

```json
{
    "cleaned_file": {
        "filename": "cleaned_0005.conll",
        "path": "./data\\removed_duplicates\\cleaned_0005.conll"
    },
    "message": "File deduplicated successfully",
    "removed_sentences_file": {
        "filename": "removed_sentences_0005.conll",
        "path": "./data\\removed_duplicates\\removed_sentences_0005.conll"
    },
    "statistics": {
        "duplicates_removed": 1816,
        "original_sentences": 8323,
        "unique_sentences": 6507
    },
    "status": "Deduplicated successfully",
    "success": true
}
```

---

## Process CoNLL

Process a CoNLL file by splitting it into train/val/test sets and converting to JSON format.

```
POST /process_conll
```

### Request

- Content-Type: multipart/form-data
- Body Parameters:
  - file: CoNLL file (required)
  - folder_name: Custom name for output folder (optional)
  - ratios: Comma-separated train, val, test ratios (optional, default: 0.7,0.15,0.15)
  - custom_map: JSON string of label mapping (optional)

### Postman Example

1. Method: POST
2. URL: `http://localhost:5000/process_conll`
3. Body:
   - Type: form-data
   - Keys:
     - file: (select File type) - Add .conll file
     - folder_name: (Text) my-dataset
     - ratios: (Text) 0.7,0.15,0.15
     - custom_map: (Text) {"0": "O", "1": "B-PER", "2": "I-PER", "3": "B-ORG", "4": "I-ORG", "5": "B-LOC", "6": "I-LOC", "7": "B-MISC", "8": "I-MISC"}
       
### Response

```json
{
    "message": "Processing completed successfully",
    "output_directory": "./data\\small-0001",
    "processing_info": {
        "steps": [
            {
                "action": "split",
                "files_created": [
                    [
                        "train.conll",
                        5827
                    ],
                    [
                        "val.conll",
                        1249
                    ],
                    [
                        "test.conll",
                        1250
                    ]
                ],
                "ratios": {
                    "test": 0.15,
                    "train": 0.7,
                    "val": 0.15
                }
            },
            {
                "action": "convert_to_json",
                "results": {
                    "test.conll": {
                        "new_entities": [],
                        "sentences_processed": 1249,
                        "tag_counts": {
                            "B-LOC": 756,
                            "B-MISC": 292,
                            "B-ORG": 1095,
                            "B-PER": 644,
                            "I-LOC": 277,
                            "I-MISC": 356,
                            "I-ORG": 839,
                            "I-PER": 580,
                            "O": 34665
                        },
                        "unique_tags": 9
                    },
                    "train.conll": {
                        "new_entities": [],
                        "sentences_processed": 5826,
                        "tag_counts": {
                            "B-LOC": 3427,
                            "B-MISC": 1569,
                            "B-ORG": 5205,
                            "B-PER": 3058,
                            "I-LOC": 1290,
                            "I-MISC": 2342,
                            "I-ORG": 3448,
                            "I-PER": 2778,
                            "O": 162017
                        },
                        "unique_tags": 9
                    },
                    "val.conll": {
                        "new_entities": [],
                        "sentences_processed": 1248,
                        "tag_counts": {
                            "B-LOC": 730,
                            "B-MISC": 312,
                            "B-ORG": 1090,
                            "B-PER": 619,
                            "I-LOC": 324,
                            "I-MISC": 514,
                            "I-ORG": 705,
                            "I-PER": 545,
                            "O": 35238
                        },
                        "unique_tags": 9
                    }
                }
            }
        ]
    },
    "summary_csv_path": "./data\\training_data_summary.csv"
}
```

## Error Responses

Error responses include detailed messages:
```json
{
    "error": "Error type",
    "message": "Detailed error message",
    "files_status": {
        "train.json": true,
        "val.json": false,
        // ...
    }
}
```

## Error Handling

The API returns appropriate HTTP status codes:
- 200: Success
- 400: Bad Request (invalid parameters)
- 404: Not Found (model/data not found)
- 500: Internal Server Error

## Data Directories

The application creates and uses the following directory structure:
- `./data`: Base directory for all data
- `./data/merged`: Directory for merged files
- `./data/removed_duplicates`: Directory for deduplicated files
- `./models`: Directory for trained models
- `./results`: Directory for training results
- `./logs`: Directory for training logs
