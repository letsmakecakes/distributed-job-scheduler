# Distributed Job Scheduler

A Distributed Job Scheduler written in Go, designed for task queuing, scheduling, and distributed execution across multiple worker nodes. This system supports scheduling recurring jobs, handling retries, and monitoring tasks. It is built to be highly available and scalable, using Redis or RabbitMQ as a queue, PostgreSQL for job persistence, and Prometheus for monitoring.

## Features

- **Job Scheduling**: Schedule one-time or recurring tasks.
- **Task Distribution**: Distribute tasks to multiple worker nodes in a distributed environment.
- **Retry Handling**: Automatic retries for failed tasks with a configurable retry policy.
- **Monitoring and Metrics**: Track task counts, retry rates, and errors with Prometheus metrics.
- **REST API**: Manage jobs through an API for creating, updating, and retrieving jobs.
- **Modular Design**: Separated components for scheduler, worker, and API services.

## Project Structure

```plaintext
distributed-job-scheduler/
├── cmd/
│   ├── scheduler/                   # Main entry point for the Scheduler service
│   ├── worker/                      # Main entry point for the Worker service
│   └── api/                         # Main entry point for the REST API service
├── config/                          # Configuration files
├── internal/
│   ├── job/                         # Job-related logic
│   ├── scheduler/                   # Scheduling logic
│   ├── worker/                      # Task worker logic
│   ├── api/                         # REST API logic
│   ├── queue/                       # Queue management
│   ├── storage/                     # Storage and cache management
│   └── monitoring/                  # Monitoring and logging
├── pkg/                             # Utility packages
├── scripts/                         # Automation and setup scripts
├── Dockerfile                       # Dockerfile for containerization
├── docker-compose.yml               # Docker Compose for multi-service setup
└── README.md                        # Project documentation
```

## Getting Started

### Prerequisites

- **Go** (version 1.18+)
- **Docker** and **Docker Compose**
- **PostgreSQL** for job persistence
- **Redis** or **RabbitMQ** for the task queue
- **Prometheus** for monitoring metrics

### Configuration

1. Copy the example configuration file:

   ```bash
   cp config/config.yaml.example config/config.yaml
   ```

2. Edit `config.yaml` to specify settings for your environment:
   - **Database** (PostgreSQL)
   - **Queue** (Redis or RabbitMQ)
   - **Logging and Metrics**

### Build and Run

#### Using Docker Compose

To quickly start all services (scheduler, worker, API, and dependencies) in a development environment, use Docker Compose:

```bash
docker-compose up --build
```

#### Running Locally

Alternatively, you can start individual services locally:

1. **Database Migration**:
   ```bash
   ./scripts/migrate.sh
   ```

2. **Scheduler**:
   ```bash
   go run cmd/scheduler/main.go
   ```

3. **Worker**:
   ```bash
   go run cmd/worker/main.go
   ```

4. **API Service**:
   ```bash
   go run cmd/api/main.go
   ```

### API Documentation

The API provides endpoints to manage jobs. Below are some key endpoints:

- **Create Job**: `POST /jobs` - Schedule a new job with a specific payload and schedule.
- **Get Job**: `GET /jobs/{id}` - Retrieve details of a job by its ID.
- **Update Job**: `PATCH /jobs/{id}` - Update job properties such as schedule and payload.
- **List Jobs**: `GET /jobs` - List all jobs with optional filters.

Refer to the [API documentation](docs/api.md) for more details.

## Usage

### Creating a New Job

Use the API to create a job with a schedule and a task payload. For example:

```bash
curl -X POST http://localhost:8080/jobs -H "Content-Type: application/json" -d '{
    "type": "email_notification",
    "schedule": "*/10 * * * *",   // Runs every 10 minutes
    "payload": {
        "recipient": "user@example.com",
        "message": "Your scheduled email notification!"
    }
}'
```

### Monitoring and Metrics

Prometheus metrics are exposed at the `/metrics` endpoint. Key metrics include:

- `jobs_started`: Number of jobs started.
- `jobs_completed`: Number of jobs completed successfully.
- `jobs_failed`: Number of jobs that failed.
- `job_duration_seconds`: Histogram of job execution time.

You can visualize these metrics with a Prometheus + Grafana setup for detailed monitoring.

## Architecture Overview

### Scheduler Service

- Handles task scheduling based on cron expressions.
- Periodically fetches tasks due for execution and queues them for workers.

### Worker Service

- Listens to the task queue and processes jobs.
- Executes the job payload, updates job status, and manages retries on failure.

### API Service

- Provides endpoints for managing jobs (CRUD operations).
- Allows users to create, view, update, and delete scheduled jobs.

### Queue Management

- **Redis or RabbitMQ** is used for task distribution, supporting a distributed architecture by decoupling task production from consumption.

### Database and Persistence

- **PostgreSQL** is used to persist job details and manage job states.
- **Redis** is used for caching job metadata for faster access.

## Development

### Code Structure

- **internal/job**: Contains job definitions, status enums, repository, and service logic.
- **internal/scheduler**: Scheduler logic to fetch due jobs and enqueue them.
- **internal/worker**: Worker processing logic, including retry handling.
- **internal/api**: API endpoints and middleware for managing jobs.
- **internal/queue**: Queue implementations (Redis and RabbitMQ).
- **internal/storage**: Database and caching interfaces and implementations.
- **internal/monitoring**: Setup for Prometheus metrics and logging.

### Adding a New Feature

1. Create a branch:

   ```bash
   git checkout -b feature/your-feature
   ```

2. Implement the feature following the modular structure in `internal/`.

3. Add tests in the respective module (e.g., `job/service_test.go`).

4. Submit a pull request for review.

## Testing

Unit tests can be found in each component's directory (e.g., `internal/job/service_test.go`). Run all tests with:

```bash
go test ./...
```

## Contributing

We welcome contributions to improve the system. Please follow these guidelines:

1. Fork the repository.
2. Clone your fork and create a new branch.
3. Commit changes with descriptive messages.
4. Push your branch and submit a pull request.

## License

Distributed under the MIT License. See `LICENSE` for more information.

---

## Resources

- [Go Documentation](https://golang.org/doc/)
- [Prometheus](https://prometheus.io/)
- [RabbitMQ](https://www.rabbitmq.com/)
- [Redis](https://redis.io/)

---

Thank you for using the Distributed Job Scheduler! If you encounter any issues, please feel free to open an issue or contribute to make this project better.
