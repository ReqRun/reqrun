# Security

ReqRun is designed to minimize risk with a small product boundary, strict request validation, and controlled handling of request data.

## Report a security issue

For security questions or responsible disclosure, contact:

- security@reqrun.com

Please do not open public GitHub issues for sensitive vulnerabilities.

## Security basics

- API keys are handled as sensitive credentials
- requests are validated before execution
- access is scoped per project
- raw request payloads are not logged in standard application logs

## Product boundary

ReqRun is a reliability layer for OpenAI-compatible request execution.

It is not:

- a general workflow platform
- a multi-provider routing marketplace
- a general-purpose data storage system

## Hosted service note

This public repository is a documentation and examples surface.

The hosted ReqRun backend, worker, dashboard, billing logic, and deployment stack are operated separately and are not fully represented in this repository.
