# AI4Chem@LUAS: A Framework for Chemical Experiment Automation
[![Python Version](https://img.shields.io/badge/python-3.9%2B-blue.svg)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
---
## 1. Introduction
`AI4Chem@LUAS` is a framework for automating experiments using Large Language Models (LLMs). It allows users to define experiments and prompts in JSON configuration files and run them against various language models via the command line.

The project is structured to separate concerns into distinct modules:
*   **`configs/`**: Holds all user-facing configurations for tasks and prompts.
*   **`src/`**: Contains the core logic for API interaction, experiment execution, and file I/O.
*   **`main.py`**: Parses command-line arguments and orchestrates the experiment run.

This modular design allows for easier extension and maintenance.

---

## 2. Project Structure
```python
AI4Chem@LUAS/
├── main.py                 # Main execution entry point
├── config.py               #   Stores all API keys
├── requirements.txt        #   Project dependencies
|
├── configs/                  # Contains all project JSON configurations
│   ├── prompts.json          #   All Prompt templates
│   └── tasks.json            #   All task definitions (paths, column names, etc.)
|
├── src/                      # Core source code directory
│   ├── __init__.py
│   ├── config_loader.py      #   Configuration loader
│   ├── experiment_runner.py  #   Experiment runner (core logic)
│   ├── llm_api.py            #   Encapsulates all LLM API calls
│   └── utils/                #   Common utility packages
│       ├── __init__.py
│       └── file_io.py        #     Encapsulates all file I/O operations
|
├── data/                     # Stores raw data CSV files
│   ├── task1_data.csv
│   ├── task2_data.csv
│   └── ...
|
├── results/                  # (Auto-generated) Experiment results output directory
├── logs/                     # (Auto-generated) Detailed log output directory
└── repair_results/           # (Auto-generated) Repair results directory
```

---

## 3. Getting Started

### 3.1. Prerequisites
*   Python 3.9 or higher
*   An API key from an LLM provider (e.g., OpenAI)

### 3.2. Installation

1.  **Clone the repository**
    
    ```bash
    git clone <repository-url>
    cd LLM_Chem
    ```
    
2.  **Create and activate a virtual environment (recommended) :**
    
    ```bash
    python3 -m venv venv
    source venv/bin/activate
    ```
    
3.  **Install the required dependencies:**

    ```bash
    pip install -r requirements.txt
    ```
    ```bash
    pip install -r requirements.txt
    ```

### 3.3. Configuration

1.  **API Keys**: Open the `config.py` file and replace the placeholder values with your actual API keys and base URLs. 
    
    ```python
    # config.py
    DASHSCOPE_API_KEY = "YOUR_DASHSCOPE_API_KEY"
    DASHSCOPE_BASE_URL = "YOUR_DASHSCOPE_BASE_URL"
    ```
    
2.  **Data**: Place your data files (in CSV format) in the `data/` directory. 

3.  **Tasks**: Define your tasks in `configs/tasks.json`. Each task should have a unique ID and specify the data file, column mapping, and any learning materials. 

4.  **Prompts**: Define your prompts in `configs/prompts.json`. Each prompt should have a unique ID and specify the prompt template and the task type it belongs to. 

5.  **Learning Materials**: The framework supports two types of learning materials that are used as system prompts for the LLM: 
    
    *   **General Learning Materials**: These are general context files for a whole task (e.g., `1HNMR_spectroscopy.md` for all of Task 6). Their paths are specified in `configs/tasks.json` under the `context_filepath` key. 
    *   **Specific Learning Materials**: These are materials for a specific experiment (e.g., for experiment `6-4` only). To enable this, you must set `"requires_learning_material": true` for the prompt in `configs/prompts.json`. The framework will then automatically look for a file named `task_{exp_id}_specific_material.md` in the `learning_materials/` directory (e.g., `task_6-4_specific_material.md`). 

---

## 4. How to Run Experiments

This project is designed to be run from the command line. You can specify the model and the experiments you want to run using command-line arguments. 

### 4.1. Command-Line Arguments

*   `--model`: (Required) The name of the model to use. 
*   `--experiments`: (Required) One or more experiment IDs to run.

### 4.2. Examples

**1. Running a single experiment**

```bash
python main.py --model qwen-max-latest --experiments 6-1
```

**2. Running multiple experiments**

```bash
python main.py --model deepseek-reasoner --experiments 6-1 6-2 6-3
```

**3. Getting help**

If you provide invalid arguments or need to see the available options, you can use the `--help` flag. 

```bash
python main.py --help
```

---

## 5. Output
The results of each experiment are saved in the `results/` directory, with a subdirectory for each experiment. The output includes: 

*   **CSV file**: A CSV file containing the input and the model's output for each data point.
*   **Text file**: A text file containing a summary of the experiment, including the system prompt and the full model output for each data point.
*   **JSONL file**: A JSONL file in the `logs/` directory containing detailed logs of all interactions with the LLM. 

---

## 6. How to Extend the Framework

### 6.1. Adding a New Tas
1.  Add a new entry to `configs/tasks.json` with a unique ID. 
2.  Specify the `data_filepath`, `column_map`, and any other relevant options. 
3.  Add your data file to the `data/` directory.

### 6.2. Adding a New Prompt
1.  Add a new entry to `configs/prompts.json` with a unique ID. 
2.  Specify the `description`, `type` (which should match a task type in `tasks.json`), and `template`. 

### 6.3. Adding a New LLM Provider
1.  Add a new function to `src/llm_api.py` to handle the API calls for the new provider. 
2.  Update the `get_llm_response` function to call the new function based on the `client_type` in the `MODEL_CONFIG`. 
3.  Add the new API key and base URL to `config.py`. 

---

## 7. Contributing
Contributions are welcome! Please feel free to submit a pull request or open an issue if you have any suggestions or find any bugs. 

---

## 8. License
This project is licensed under the MIT License. See the `LICENSE` file for details. 
