# VerifAI project

<img src="https://github.com/nikolamilosevic86/verif.ai/assets/5192295/e95b4877-0847-4fa2-99e5-a6a7fc0003f8"  width="500"/>

No more searches, just verifiably accurate answers.

## Project Description
VerifAI project aims to address problem of hallucinations in generative large language models and generative search engines. Initially, we started with biomedical domain, however, now we have expanded VerifAI to support indexing any documents in txt,md, docx, pptx, or pdf formats. 

VerifAI is an AI system designed to answer users' questions by retrieving the most relevant documents, generate answer with references to the relevant documents and verify that the generated answer does not contain any hallucinations. In the core of the engine is generative search engine, powered by open technologies. However, generative models may hallucinate, and therefore VerifAI is developed a second model that would check the sources of generative model and flag any misinformation or misinterpretations of source documents. Therefore, make the answer created by generative search engine completly verifiable. 

The best part is, that we are making it open source, so anyone can use it!

Check the [article about VerifAI project published on TowardsDataScience](https://towardsdatascience.com/verifai-project-open-source-biomedical-question-answering-with-verified-answers-5417cd9003e0)


# Installation and start-up

## VerifAI Core
1. Clone the repository or download latest release
2. Create virtual python environment by running: 
```shell
python -m venv verifai
source verifai/bin/activate
```
3. Run requirements.txt by running `pip install -r backend/requirements.txt`
4. Run install_datastore.py file. To run this file, it is necessary to install Docker (and run the daemon). This file is designed to install necessary components, such as OpenSearch, Qdrant and PostgreSQL, as well as to create database in PostgreSQL.
```shell
python install_datastore.py
```
5. Configure system, by replacing and modifying `.env.local.example` in backend folder and rename it into just `.env`:
The configuration should look in the following manner:
```text
SECRET_KEY=6183db7b3c4f67439ad61d1b798224a035fe35c4113bf870
ALGORITHM=HS256

DBNAME=verifai_database
USER_DB=myuser
PASSWORD_DB=mypassword
HOST_DB=localhost

OPENSEARCH_IP=localhost
OPENSEARCH_USER=admin
OPENSEARCH_PASSWORD=admin
OPENSEARCH_PORT=9200
OPENSEARCH_USE_SSL=False
QDRANT_IP=localhost
QDRANT_PORT=6333
QDRANT_API=8da7725d78141e19a9bf3d878f4cb333fedb56eed9727904b46ce4b32e1ce085
QDRANT_USE_SSL=False

OPENAI_PATH=<path-to-openai/azure/vllm/nvidia_nim/ollama-interface>
OPENAI_KEY=<key-in-interface>
DEPLOYMENT_MODEL=GPT4o
MAX_CONTEXT_LENGTH=128000

EMBEDDING_MODEL="sentence-transformers/msmarco-bert-base-dot-v5"

INDEX_NAME_LEXICAL = 'myindex-lexical'
INDEX_NAME_SEMANTIC = "myindex-semantic"

USE_VERIFICATION=True
```
6. Index your files, by running index_files.py and pointing it to the directory with files you would like to index. It will recuresevly index all files in the directory.
```shell
python index_files.py <path-to-directory-with-files>
```
As an example, we have created a folder with some example files in the folder `test_data`. You can index them by running:
```shell
python index_files.py test_data
```
7. Run the backend of VerifAI by running `main.py` in the backend folder.
```shell
python main.py
```
8. Install React by following [this guide](https://www.freecodecamp.org/news/how-to-install-react-a-step-by-step-guide/)
9. Install React requirements for the front-end in `client-gui/verifai-ui` folder and run front end:
```shell
cd ..
cd client-gui/verifai-ui
npm install
npm start
```
10. Go to `http://localhost:3000` to see the VerifAI in action.
## VerifAI BioMed

This is biomedical version of VerifAI. It is designed to answer questions from the biomedical domain.

One requirement to run locally is to have installed Postgres SQL. You can install it for example on mac by running `brew install postgresql`.

1. Clone the repository
2. Run requirements.txt by running `pip install -r backend/requirements.txt`
3. Download Medline. You can do it by executing `download_medline_data.sh` for core files for the current year and `download_medline_data_update.sh` for Medline current update files.
4. Install Qdrant following the guide [here](https://qdrant.tech/documentation/quick-start/)
5. Run the script: `python medline2json.py` to transform MEDLINE XML files into JSON
6. Run `python json2selected.py` to selects the fields that should be inported into the index
7. Run `python abstarct_parser.py` to concatinate abstract titles and abstracts and splits texts to 512 parts that can be indexed using a transformer model
8. Run `python embeddings_creation.py` to create embeddings.
9. Run `python scripts/indexing_qdrant.py` to create qdrant index. Make sure to point to the right folder created in the previous step and to the qdrant instance. 
10. Install OpenSearch following the guide [here](https://opensearch.org/docs/latest/install-and-configure/install-opensearch/index/)
11. Create OpenSearch index by running `python scripts/indexing_lexical_pmid.py`. Make sure to configure access to the OpenSearch and point the path variable to the folder created by json2selected script.
12. Set up system variables that are needed for the project:
```
export DBNAME=db_name
export USER_DB=db_username
export PASSWORD_DB=db_password
export HOST_DB=db_host_name

export VERIFAI_IP=ip_address_of_machines_with_qdrant_and_opensearch
export VERIFAI_USER=user_name_open_search
export VERIFAI_PASSWORD=password_open_search
export VERIFAI_PORT=port_open_search
export QDRANT_PORT=port_qdrant
```
13. Run backend by running `python backend/main.py`
14. Install React by following [this guide](https://www.freecodecamp.org/news/how-to-install-react-a-step-by-step-guide/)
15. Run `npm run-script build`
16. Run frontend by running `npm start` in client-gui/verifai-ui

## Developed models and datasets

1. [Fine tuned QLoRA addapted for Mistral 7B-instruct v01](https://huggingface.co/BojanaBas/Mistral-7B-Instruct-v0.1-pqa-10)
2. [Fine tuned QLoRA addapted for Mistral 7B-instruct v02](https://huggingface.co/BojanaBas/Mistral-7B-Instruct-v0.2-pqa-10)
3. [PQAref dataset](https://huggingface.co/datasets/BojanaBas/PQAref)
4. [Verification model based on DeBERTa, fine-tuned on SciFact dataset](https://huggingface.co/MilosKosRad/TextualEntailment_DeBERTa_preprocessedSciFACT)

## Using our APP

You can use our app [here](https://app.verifai-project.com). You need to create a free account by clicking on Join now.

<img width="1512" alt="image" src="https://github.com/user-attachments/assets/47d3b9bb-2c54-4448-b608-cf96589bd63d">


## Collaborators and contributions

Currently, two institutions are the main drivers of this project, namely Bayer A.G and Institute for Artificial Intelligence Research and Development of Serbia. Current contrbiutors are by institutions
* Bayer A.G.
  * Nikola Milosevic
  * Lorenzo Cassano
* Institute for Artificial Intelligence Research and Development of Serbia:
  * Adela Ljajic
  * Milos Kosprdic
  * Bojana Basaragin
  * Darija Medvecki
  * Angela Pupovac
  * Nataša Radmilović
  * Petar Stevanović

We welcome contribution to this project by anyone interested in participating. This is an open source project under AGPL license. In order to prevent any legal issues, before sending the first pull request, we ask potential contributors to sign [Individual Contributor Agreement](https://github.com/nikolamilosevic86/verif.ai/blob/main/Legal/Individual%20Contributor%20Agreement%20Verifai_fillable.pdf) and send to us via email (verif.ai.project@gmail.com).

## Citations

* [Adela Ljajić, Miloš Košprdić, Bojana Bašaragin, Darija Medvecki, Lorenzo Cassano, Nikola Milošević, “Scientific QA System with Verifiable Answers”, The 6th International Open Search Symposium 2024](https://arxiv.org/pdf/2407.11485)
* [Košprdić, M., Ljajić, A., Bašaragin, B., Medvecki, D., & Milošević, N. "Verif. ai: Towards an Open-Source Scientific Generative Question-Answering System with Referenced and Verifiable Answers." The Sixteenth International Conference on Evolving Internet INTERNET 2024 (2024).](https://arxiv.org/pdf/2402.18589.pdf)
* [Bojana Bašaragin, Adela Ljajić, Darija Medvecki, Lorenzo Cassano, Miloš Košprdić, Nikola Milošević "How do you know that? Teaching Generative Language Models to Reference Answers to Biomedical Questions", Accepted at BioNLP 2024, Colocated with ACL 2024](https://arxiv.org/pdf/2407.05015)

## Funding 

This project was in September 2023 funded by NGI Search project of the European Union. Views and opinions expressed are however those of the author(s) only and do not necessarily reflect those of the European Union or European Commission. Neither the European Union nor the granting authority can be held responsible for them. Funded within the framework of the NGI Search project under grant agreement No 101069364
