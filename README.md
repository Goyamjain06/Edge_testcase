## How it's built on AWS

| Component | Technology |
|---|---|
| AI agent architecture | Strands Agents SDK, designed for integration with Amazon Nova through Amazon Bedrock |
| Hindi responses | Rule-based Hindi response templates as a fallback when Bedrock model access is unavailable |
| Spoken replies | Amazon Polly, neural Hindi voice (Kajal) |
| API | AWS Lambda (Function URL), deployed with AWS SAM |
| Rider trip log | Amazon DynamoDB |
| Web app | AWS Amplify Hosting |
| Air-quality data | OpenAQ archive on AWS Open Data (`s3://openaq-data-archive`) and OpenAQ API for live readings |

### AI integration and fallback behavior

RideClean is designed around a tool-based agent architecture, where responses rely on air-quality data and exposure calculations rather than invented figures.

During the hackathon deployment, Amazon Bedrock model access encountered an error. Consequently, the application used a rule-based response engine with Hindi templates to provide responses using the available data and calculation tools.

This fallback allowed the core pollution-exposure features to remain usable without a successful model response. The Strands Agents and Amazon Nova integration represents the intended AI architecture; model-generated responses should not be assumed to be active in the deployed demo.

The application also includes frontend fallback behavior for situations where the backend is unavailable. This should be distinguished from the Bedrock fallback: the former concerns backend connectivity, while the latter concerns AI model access.

### Architecture

```text
Rider voice input (Hindi)
        |
        v
AWS Lambda API
        |
        v
Agent / response engine
        |
        +-- Intended integration:
        |      Strands Agents + Amazon Bedrock / Nova
        |
        +-- Available fallback:
        |      Rule-based Hindi response templates
        |
        +-- Exposure calculation and data tools
        |      +-- OpenAQ live PM2.5 readings
        |      +-- Historical seasonal pollution patterns
        |      +-- Ride exposure estimates
        |      +-- Cleaner break locations
        |      +-- Best-time recommendations
        |      +-- Daily trip dose
        |
        +-- Amazon DynamoDB (trip history)
        |
        v
Amazon Polly (Hindi audio)
        |
        v
Rider hears the response
```

The diagram describes the system architecture and fallback paths; it does not imply that every integration was operational in the hackathon deployment.

## Limitations and assumptions

- **Model availability:** Bedrock model access was unavailable during the hackathon deployment, so the deployed application relied on rule-based responses.
- **Exposure estimates:** Calculations use fixed-monitor readings and stated assumptions rather than personal pollution sensors.
- **On-road exposure:** A 1.3× factor is used as an estimate based on the cited Delhi on-road exposure study.
- **Cigarette equivalence:** The comparison is an approximate communication aid, not a direct measurement of an individual's inhaled dose.
- **Travel assumptions:** Speed (20 km/h) and road distance (1.3× straight-line distance) are modeling assumptions.

## AI tools used

Claude (Anthropic) assisted with research, implementation and documentation, as permitted by the hackathon rules. The project distinguishes the intended model-based agent architecture from the rule-based fallback used when Bedrock model access was unavailable.
