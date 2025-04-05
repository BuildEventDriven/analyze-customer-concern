# ML Pipeline Request: Kafka, Flink, Hugging Face Integration

## Objective
Create a simple ML pipeline using Kafka, Flink, and Hugging Face. The pipeline will consist of two parts:

1. **Training/Fine-Tuning Pipeline**: Classifies customer feedback and elects the top 3 reasons for customer dissatisfaction based on message repetition.
2. **Inference Pipeline**: Classifies new customer feedback in real-time and triggers alerts if it matches any of the top 3 elected reasons.

---

## Pipeline 1: Training/Fine-Tuning Pipeline (Flink Batch Processing)

### Purpose
Classify customer feedback messages and elect the top 3 reasons for client dissatisfaction based on repetition.

### Flow

#### Data Sources:
- User messages from various platforms: CRM, support portal, review portal, etc.
- Data includes: raw feedback, timestamps, user metadata, tags, unique IDs.

#### Data Ingestion:
- Kafka collects messages from multiple sources (CRM, support portal, review portal, etc.).

#### Classification:
- Flink processes the messages in batch mode (weekly).
- Each message is classified using **BERTopic** to identify the type/category of issue (e.g., payment issue, product defect, etc.).

#### Count & Election:
- After classifying the messages, count the occurrences of each issue type.
- Elect the top 3 reasons based on the maximum repetition (most frequent issues).

#### Output:
- The top 3 reasons for client dissatisfaction are identified based on the most frequently mentioned issue types.

### Result
- Top 3 reasons for customer dissatisfaction based on the most frequent issues.

---

## Pipeline 2: Inference Pipeline (Flink Stream Processing)

### Purpose
Classify incoming customer feedback in real-time and trigger an alert if it matches any of the top 3 elected reasons.

### Flow

#### Data Sources:
- User messages coming from various platforms: CRM, support portal, review portal, etc.
- Data includes: feedback, timestamps, user metadata, tags, unique IDs.

#### Data Ingestion:
- Kafka collects live messages from multiple sources (CRM, support portal, review portal, etc.).

#### Classification:
- Flink processes incoming messages in real-time.
- Each incoming message is classified using **BERTopic** to predict the type/category of the issue.

#### Comparison & Alerting:
- The predicted issue type is compared with the top 3 reasons elected in the training pipeline.
- If the predicted type matches any of the top 3 reasons, an alert is triggered.

#### Output:
- Alert is generated if the message falls under one of the top 3 reasons for dissatisfaction.

### Result
- **Classification**: Does the incoming message classify as one of the top 3 unhappy reasons?
- **Alert**: If it matches, an alert is triggered.

---

## Upstream Components & Considerations

- **Training Frequency**: The model is trained on last week's data (weekly).
- **Data Sources**: Messages come from multiple platforms (CRM, support portal, review portal, etc.).
- **Model Update**: The training pipeline updates the top 3 unhappy reasons each week.
- **Real-Time Feedback**: The inference pipeline processes incoming data in real-time.
