# End-to-End Implementation (Lab Midterm ML Pipeline)

**Repository:** `Arhamhir/lab-mid`  
**Goal:** Deploy an ML pipeline that automatically retrains a model when new data is uploaded to the GitHub repository, then exposes updated performance metrics (including accuracy) through a FastAPI endpoint (`/metrics`) served from a Docker container on port **8000**.

The required starter files and configs were taken from: `malik-qasim/lab-midter-sp26-configs`.

---

## Step 1 — Generate Dataset and Upload to GitHub (`dataset/train.csv`)

I used the provided `generate_dataset.py` script, which reads settings from `config.json`.

- Generated the dataset locally.
- Ensured the output file is named exactly:



- Committed and pushed `dataset/train.csv` to my repository.

**Why this matters:**  
The evaluation script later pushes additional rows/changes to this dataset to trigger retraining.

---

## Step 2 — Add Instructor as Collaborator

I added `qasimalik@gmail.com` as a collaborator to my GitHub repository so the instructor/evaluation process can access and push changes.

---

## Step 3 — Fetch the Data from GitHub (Pipeline Start)

The pipeline begins by fetching the latest dataset from the repository so it always trains on the newest version (including updates pushed during evaluation).

### Typical implementation approach:

- Use `git pull` (if the pipeline runs inside the repository), **or**
- Download `dataset/train.csv` directly from GitHub (raw file URL / GitHub API), then save it locally in the runtime environment.

**Key point:**  
This ensures retraining uses the updated dataset after the evaluator augments it.

---

## Step 4 — Train the Model on the Dataset (`train.py`)

After fetching the latest `dataset/train.csv`, the pipeline runs training using the provided `train.py`.

### Outputs typically include:

- A trained model artifact:
  - `model.pkl`
  - `model.joblib`
- Saved metrics (accuracy and other stats), either:
  - Stored in a JSON file (e.g., `metrics.json`), or
  - Computed dynamically by the API

**Important for evaluation:**  
When the evaluator pushes new data:
- Retraining must happen.
- Accuracy must change accordingly.
- The updated accuracy must be visible via the API within ~5 minutes.

---

## Step 5 — Dockerize the FastAPI App (`app.py`)

I containerized the FastAPI service to ensure consistent deployment.

### In Docker:

- The image includes:
  - Python environment
  - All required dependencies
- `app.py` is served via `uvicorn`


**Why Docker is required:**  
The grader expects a stable API endpoint and a reproducible environment.


---

## Step 6 — Build and Run Container; Expose `/metrics` on Port 8000

I built and ran the container, mapping container port `8000` to host port `8000`.

