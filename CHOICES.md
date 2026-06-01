# Engineering Choices and Tradeoffs

## Introduction

This document explains the major technical decisions made while developing the Store Intelligence System and the reasoning behind those decisions.

## Detection Model Choice

YOLOv8 was selected as the primary detection model because it provides a strong balance between accuracy, speed, and ease of integration.

Advantages include:

- Real-time performance
- Pre-trained person detection
- Simple Python API
- Strong community support

Alternative models such as Faster R-CNN or SSD were considered but would increase implementation complexity and runtime requirements.

## Tracking Strategy

The project uses YOLOv8 built-in tracking IDs to maintain visitor identity across consecutive frames. This enables visitor-level event generation and reduces duplicate counting within a single camera stream.

This approach reduces duplicate counting and enables visitor-level event generation.

A more advanced production solution would use appearance embeddings and re-identification models, but this would significantly increase complexity for the challenge timeline.

## Event-Driven Architecture

Event storage also enables future integration with POS systems and multi-camera analytics without major architectural changes.

Instead of directly calculating metrics from video streams, the system generates structured events.

Benefits include:

- Separation of concerns
- Easier debugging
- Historical analysis support
- Reusable analytics logic

Metrics can be recalculated at any time from stored events.

## Database Selection

SQLite was chosen because:

- No external infrastructure required
- Easy setup
- Portable database file
- Sufficient for challenge-scale workloads

For large deployments, PostgreSQL would be preferred.

## API Framework

FastAPI was selected because:

- Automatic Swagger documentation
- High performance
- Easy request validation
- Modern Python ecosystem

The built-in API documentation simplified testing and verification.

## Dashboard Technology

Streamlit was selected for dashboard development because it allows rapid creation of interactive analytics interfaces with minimal code.

This accelerated development while still providing a useful visualization layer.

## Containerization

Docker was used to package the application and ensure consistent execution across environments.

Benefits include:

- Reproducible builds
- Simplified deployment
- Environment consistency
- Easier evaluation by reviewers

The challenge explicitly requires containerized execution, making Docker a natural choice.

## Edge Case Handling

Several edge cases were considered:

### Group Entry

Tracking IDs help distinguish multiple visitors entering simultaneously.

### Re-entry

Previously seen visitors can generate REENTRY events instead of new ENTRY events.

### Empty Store Periods

Analytics endpoints continue functioning even when no visitors are present.

### False Positives

Confidence thresholds and filtering reduce incorrect detections.

### Zone Transitions

Zone tracking generates movement events while avoiding repeated events when visitors remain within the same zone.

## Future Enhancements

The current implementation focuses on delivering a reliable end-to-end store intelligence workflow while maintaining simplicity and ease of evaluation.

Potential enhancements include:

* Cross-camera visitor re-identification for large multi-camera deployments.
* POS transaction correlation using visitor session windows and billing-zone activity.
* Staff identification using appearance and movement patterns.
* Advanced queue analytics for checkout optimization.
* Zone-level heatmaps derived from dwell-time distributions.
* Real-time event streaming using Kafka or message queues.
* PostgreSQL migration for large-scale production workloads.
* Cloud-native deployment with container orchestration.

The architecture has been intentionally designed in a modular manner so these capabilities can be integrated with minimal changes to the analytics layer and API interfaces.


## Conclusion

The overall design emphasizes simplicity, maintainability, and successful delivery of the challenge requirements. Tradeoffs favored a working end-to-end pipeline over highly complex solutions, ensuring reliability and ease of evaluation.