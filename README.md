# Corrosion detection dashboard

Web dashboard for an IoT system that detects early corrosion on steel plates with a machine learning model. Field cameras and Raspberry Pi nodes photograph steel plates under a fixed capture protocol; a YOLOv8 segmentation model classifies and segments the corrosion; this React app lets operators see every plant, measurement point and alert.

Backend and infrastructure: [corrosion-detection-infra](https://github.com/andresjsaco-sudo/corrosion-detection-infra)

## System overview

```
Capture                       AWS (serverless, defined in CDK)                     This repo
Phone or Raspberry Pi  --->   API Gateway + Cognito JWT authorizer           --->  React dashboard
plate photos                  Lambda inference: YOLOv8-seg on ONNX Runtime         plant map, alerts,
(ASTM B117 salt spray)        S3 (images, Glacier lifecycle) + DynamoDB            image overlays, reports
```

- **Model.** YOLOv8 segmentation trained with transfer learning on images from ASTM B117 salt spray tests of galvanized and pre-painted steel, exported to ONNX for inference inside AWS Lambda.
- **Capture protocol.** Each plate is photographed from 8 angles under 4 lighting conditions per session, which, with augmentation, gives a training set of tens of thousands of images.
- **Cloud.** API Gateway with a Cognito authorizer, six Lambda functions (inference, measurements, points, alerts, reports, users), S3 for raw and processed images and DynamoDB for measurements, points and users.

## Features

- Login with AWS Cognito (Amplify) and role-based user management
- Map of monitored plants with per-plant status
- Measurement point detail with the history of corrosion levels
- Image gallery with segmentation masks and bounding boxes drawn over the original photo
- Upload flow that sends a new photo to the inference endpoint and shows the result
- Alerts panel and per-plant reports

## Run locally

```bash
npm install
cp .env.example .env.local   # fill in the outputs of the infrastructure deploy
npm run dev                  # http://localhost:5173
```

Environment variables:

```
VITE_API_URL=https://<api-id>.execute-api.<region>.amazonaws.com/prod
VITE_COGNITO_USER_POOL_ID=<region>_<pool-id>
VITE_COGNITO_CLIENT_ID=<client-id>
VITE_AWS_REGION=us-east-1
```

## Stack

React 18, Vite 5, React Router, AWS Amplify (Cognito), Recharts, Leaflet and React-Leaflet, lucide-react.

Built as a final degree project in Electronic and Mechanical Engineering at Universidad del Norte, Barranquilla, Colombia.
