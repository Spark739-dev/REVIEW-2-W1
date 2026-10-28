# REVIEW-2-W1
# Deep Learning and Agent-Assisted Smart Civic Infrastructure and Public Service Monitoring System
## NAME: VESHWANTH .
## REGNO: 212224230300
## 1. Project overview
This project is a local prototype for detecting visible civic infrastructure issues from street or public-facility images and turning reviewed detections into structured reports. Target categories may include potholes, road cracks, waterlogging, garbage accumulation, damaged streetlights, and selected public-facility defects.

## 2. Main functions
- Upload and validate JPG, JPEG, PNG, or WEBP images.
- Run YOLOv8 object detection when a trained model checkpoint is installed.
- Display predicted issue classes, confidence values, bounding boxes, and an annotated image.
- Require human confirmation and manual location entry before report submission.
- Suggest severity, priority, and responsible department using transparent rules.
- Save report details in SQLite, update status, and export the report register to CSV.

## 3. Architecture and code headings
- `app.py`: Streamlit UI and workflow orchestration.
- `civic_pipeline.py`: image validation and YOLO inference.
- `civic_rules.py`: rule-based severity/department/priority suggestion.
- `database.py`: SQLite schema, report persistence, querying, and status updates.
- `train_yolo.py`: YOLOv8 training entry point for a compatible labeled dataset.
- `models/`: location for model checkpoints; trained weights are not included.
- `uploads/` and `outputs/`: local files created while testing.

## 4. Environment setup (Windows / VS Code)
Use Python 3.10 or 3.11. In the VS Code terminal:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
```

If PowerShell blocks activation, use Command Prompt:
```bat
.venv\Scripts\activate.bat
```

## 5. Run the application
```powershell
streamlit run app.py
```
Open the local URL printed by Streamlit, usually `http://localhost:8501`.

## 6. Train and install YOLOv8 weights
1. Prepare a labeled detection dataset and create `dataset/data.yaml`.
2. Edit `DATA_YAML` in `train_yolo.py` to point to that YAML file.
3. Check that class IDs, class names, image paths, and bounding-box labels match.
4. Run:
```powershell
python train_yolo.py
```
5. The script copies the best checkpoint to `models/civic_yolo.pt` when available.
6. Restart the Streamlit app and upload a test image.

The project does not ship with trained weights or claim measured accuracy. Validate with held-out images and report precision, recall, mAP@0.5, mAP@0.5:0.95, per-class metrics, and inference latency.

## 7. Report workflow
A user uploads an image, reviews candidate detections, enters the location, and confirms the report details. The application uses rules to suggest a severity and department, then saves the report with a timestamp and `Pending` status. An administrator can inspect the register, update the status, and export CSV.

## 8. Rule-based routing
The routing function is intentionally transparent. Low-confidence detections are marked `Needs Review`; potholes/road damage and drainage issues receive high-priority suggestions; waste issues are routed to sanitation; streetlight issues to electrical; and public-facility defects to facilities. These are prototype rules and should be reviewed by a human before any real-world action.

## 9. Testing checklist
- Upload a valid image.
- Try an unsupported file type and confirm validation.
- Run detection with and without `models/civic_yolo.pt`.
- Check that low-confidence detections are marked for review.
- Save a report and verify it appears in Issue Register.
- Update status and confirm it persists after refresh.
- Export CSV and verify the exported columns and records.
- Evaluate the trained model on a held-out dataset before reporting accuracy.

## 10. Limitations and next steps
- No trained weights or dataset are included in this source package.
- CNN crop classification is described in the project plan but is not yet connected to this app's inference path.
- Severity is a transparent rule-based suggestion, not a validated visual severity model.
- Location is entered manually; GPS extraction is not implemented.
- No municipal dispatch integration is included.
- Before deployment, add authentication, file-size limits, audit logging, data-retention controls, and security review.

## 11. Repository hygiene
Keep datasets, generated reports, database files, and large checkpoints out of Git unless they are intentionally published and appropriately licensed. `.gitignore` excludes local outputs and model weights by default.

