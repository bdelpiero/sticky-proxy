# sticky-proxy
Practice project. A custom solution to a common problem for specific constraints. It is not an optimal solution.

# Sticky Proxy - Specification

## Overview
The goal of this project is to build a "sticky proxy" service that acts as a gateway between client applications and backend services. The proxy ensures that requests with side effects are only processed once, even if a client sends the same request multiple times. Additionally, it provides clients with the status of ongoing processes through a polling mechanism.

The proxy can handle multiple backend services, each identified by a unique service identifier, making it work as a general-purpose gateway for handling idempotent process execution across different services.

## Requirements and Constraints
- **Idempotency**: The proxy must ensure that requests from the client with the same parameters are not processed more than once concurrently by the target service. This means that if a process is already running for a given request, any duplicate requests will not trigger additional instances of the process until the current one completes. Once completed, the process can be triggered again if the client requests it.
- **Process Status Tracking**: The proxy must keep track of the status of each client request (`pending`, `in-progress`, `completed`, `failed`) and make this information available to the client.
- **Service Integration**: The proxy must be able to forward requests to multiple instances of registered services and keep track of these requests in a context where the proxy has no way to directly identify which specific instance is handling a given request.
- **Communication Limitations**: The proxy cannot use pub/sub or WebSocket solutions to communicate with service instances. All communication must be done over HTTP.
- **Persistence**: The proxy must be able to persist the state of ongoing processes to disk, so that it can resume tracking them if the proxy service is restarted.
- **Caching**: The proxy should cache the results of completed processes for a configurable amount of time, so that it can quickly serve subsequent requests for the same process without needing to forward them to the target service.

## Proposed Solution
The proxy will have the following components:

- **Service Registration**: The proxy will maintain a registry of backend services, including their identifiers and connection details (base URLs, health check endpoints, etc.).
- **Request Handling**: The proxy will expose an HTTP endpoint that the client can use to submit requests. The proxy will generate a unique identifier for each request and keep track of its status.
- **Process Tracking**: The proxy will maintain an in-memory (and persisted to disk) data structure to keep track of the status of each ongoing process. This will include the process identifier, the current status, and any result data.
- **Service Integration**: When the proxy receives a new request, it will forward the request to an available instance of the target service, including the generated process identifier. Services will be responsible for notifying the proxy when a process has completed.
- **Client Polling**: The client will be able to poll the proxy to check the status of a specific process by providing the process identifier. The proxy will respond with the current status and any available result data.
- **Caching**: The proxy will cache the results of completed processes for a configurable amount of time. If a client requests the status of a process that has completed and the result is still cached, the proxy will simply return the cached data.

## Component Diagram

### [pending]

## API Specifications

### [pending]

## Additional Features 
- Ability to force trigger a new process even if one is already running.
- Ability to cancel an ongoing process (add an endpoint).
- Metrics.
- Admin Panel.
