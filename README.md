TutorRAG

Plateforme d'apprentissage basée sur une architecture RAG. Les enseignants déposent leurs cours au format PDF ; les élèves posent des questions et génèrent des quiz à partir de ces documents.

Fonctionnalités

-Comptes et rôles : inscription et connexion pour les élèves et les enseignants, mots de passe hachés avec bcrypt, authentification HTTP Basic

-Enseignant : import de documents PDF associés à un niveau scolaire (1 à 3 (1ére année - 3éme année))

-Élève : questions-réponses sur les cours de son niveau, avec indication des sources

-Quiz : génération de QCM à partir d'un thème, correction automatique, historique des tentatives

-Interface : application Streamlit (accueil, inscription, connexion, tableaux de bord enseignant et élève)

Pipeline RAG

Ingestion : extraction du texte du PDF (PyPDFLoader), découpage en chunks de 500 caractères avec un chevauchement de 50
Stockage : texte des chunks dans MongoDB ; embeddings (gemini-embedding-001) et métadonnées (source, page, niveau, accès) dans Pinecone
Récupération : embedding de la requête, recherche des 5 chunks les plus proches, filtrés par niveau de l'élève et par droits d'accès
Génération : le texte des chunks retrouvés est transmis à un LLM Groq (llama-3.3-70b-versatile) avec pour consigne de répondre uniquement à partir du contexte


Structure du projet


tutorRag/
├── client/
│   ├── main.py          # Application Streamlit
│   └── assets/          # Visuels de l'interface
├── server/
│   ├── main.py          # Application FastAPI
│   ├── auth/            # Inscription, connexion, hachage
│   ├── docs/            # Import PDF, chunking, indexation
│   ├── chat/            # RAG, génération et correction de quiz
│   └── config/          # Connexion MongoDB
└── requirements.txt


Installation

Prérequis : Python ≥ 3.10, une base MongoDB, un index Pinecone (dimension 3072), des clés API Groq et Google.

bash
git clone https://github.com/Leila04-code/tutorRag.git
cd tutorRag
uv venv
source .venv/bin/activate   # Windows : .venv\Scripts\activate
uv pip install -r requirements.txt
Configuration

server/.env

env
MONGO_URI=
DB_NAME=
GOOGLE_API_KEY=
PINECONE_API_KEY=
PINECONE_INDEX_NAME=
GROQ_API_KEY=

client/.env

env
BACKEND_URL=http://localhost:8000
Lancement
bash
# Backend
cd server
uvicorn main:app --reload

# Interface (second terminal)
cd client
streamlit run main.py
