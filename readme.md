# Reddit Data Engineering Pipeline

A complete data pipeline that automatically extracts Reddit posts, processes them, and stores them in AWS for analysis. This project demonstrates modern data engineering practices using Apache Airflow, Docker, and AWS services.

## What This Project Does

This pipeline automatically:

- **Fetches data** from Reddit's r/dataengineering subreddit daily
- **Processes and cleans** the posts data
- **Uploads** the data to AWS S3 for storage
- **Transforms** the data using AWS Glue and Athena
- **Loads** everything into Amazon Redshift for analysis

Perfect for building your own Reddit analytics dashboard or demonstrating data engineering best practices!

## Table of Contents

- [What This Project Does](#-what-this-project-does)
- [How It Works](#️-how-it-works)
- [Prerequisites](#️-prerequisites)
- [Quick Start](#️-quick-start)
- [Configuration](#️-configuration)
- [Project Structure](#-project-structure)
- [Monitoring](#-monitoring)
- [Troubleshooting](#-troubleshooting)
- [Contributing](#-contributing)
- [License](#-license)

## 🏗️ How It Works

The data flows through these steps:

1. **Reddit API** → Extracts posts from specified subreddits
2. **Apache Airflow** → Orchestrates and schedules the entire pipeline
3. **Data Processing** → Cleans and transforms the raw Reddit data
4. **Amazon S3** → Stores the processed data files
5. **AWS Glue** → Catalogs and prepares data for querying
6. **Amazon Athena** → Enables SQL queries on the data
7. **Amazon Redshift** → Final data warehouse for analytics

### Key Components

- **Apache Airflow**: Manages and schedules data pipeline tasks
- **PostgreSQL**: Stores Airflow metadata and temporary data
- **Redis**: Handles task queuing for distributed processing
- **Docker**: Containerizes the entire application for easy deployment
- **AWS Services**: Provides scalable cloud storage and processing

## 🛠️ Prerequisites

Before starting, make sure you have:

### Required Accounts & Credentials

- **AWS Account** with permissions for S3, Glue, Athena, and Redshift
- **Reddit Account** to create API credentials
- **Docker** installed on your machine

### Software Requirements

- Python 3.9 or higher
- Docker and Docker Compose
- Git

### Hardware Requirements

- At least 4GB RAM (8GB recommended)
- 2GB free disk space

## 🏃‍♂️ Quick Start

### 1. Clone the Repository

```bash
git clone <repository-url>
cd RedditDataEngineering
```

### 2. Set Up Reddit API Access

1. Go to [Reddit Apps](https://www.reddit.com/prefs/apps)
2. Click "Create App" or "Create Another App"
3. Choose "script" as the app type
4. Note down your `client_id` and `client_secret`

### 3. Set Up AWS Credentials

1. Log into your AWS Console
2. Go to IAM → Users → Security Credentials
3. Create new access keys
4. Note down your `access_key_id` and `secret_access_key`

### 4. Configure the Pipeline

```bash
# Copy the example configuration
cp config/config.conf.example config/config.conf

# Edit the configuration with your credentials
nano config/config.conf
```

### 5. Start the Pipeline

```bash
# Build and start all services
docker-compose up -d

# Check if everything is running
docker-compose ps
```

### 6. Access the Dashboard

Open your browser and go to: <http://localhost:8080>

- **Username**: `airflow`
- **Password**: `airflow`

## Configuration

Edit the `config/config.conf` file with your credentials:

```ini
Edit the `config/config.conf` file with your credentials:

```ini
[database]
database_host = localhost
database_name = airflow_reddit
database_port = 5432
database_username = postgres
database_password = postgres

[api_keys]
reddit_secret_key = YOUR_REDDIT_SECRET_KEY
reddit_client_id = YOUR_REDDIT_CLIENT_ID

[aws]
aws_access_key_id = YOUR_AWS_ACCESS_KEY
aws_secret_access_key = YOUR_AWS_SECRET_KEY
aws_region = us-east-1
aws_bucket_name = your-s3-bucket-name

[etl_settings]
batch_size = 100
error_handling = abort
log_level = info
```
```

### Customizing Data Collection

You can modify the DAG to collect from different subreddits:

1. Open `dags/reddit_dag.py`
2. Change the `subreddit` parameter in the extraction task
3. Adjust `time_filter` (hour, day, week, month, year, all)
4. Modify `limit` to control how many posts to fetch

## Project Structure

```
RedditDataEngineering/
├── assets/              # Images and documentation assets
├── config/              # Configuration files
├── dags/                # Airflow DAG definitions
│   └── reddit_dag.py       # Main pipeline orchestration
├── 📁 data/                # Data storage directory
│   └── 📁 output/          # Processed data files
├── 📁 etls/                # ETL logic and functions
│   ├── reddit_etl.py       # Reddit data extraction and transformation
│   └── aws_etl.py          # AWS integration functions
├── 📁 pipelines/           # Pipeline orchestration
│   ├── reddit_pipeline.py  # Reddit data pipeline
│   └── aws_s3_pipeline.py  # S3 upload pipeline
├── 📁 utils/               # Utility functions and constants
├── docker-compose.yml      # Docker services configuration
├── Dockerfile             # Custom Airflow image
├── requirements.txt       # Python dependencies
└── README.md             # This file
```

## Monitoring

### Airflow Web UI

- **View pipeline status**: <http://localhost:8080>
- **Monitor task execution**: Navigate to DAGs → etl_reddit_pipeline
- **Check logs**: Click on individual tasks to view detailed logs
- **View data lineage**: Use the Graph view to see task dependencies

### Checking Data Output

```bash
# View collected data
ls -la data/output/

# Check the latest data file
head data/output/reddit_$(date +%Y%m%d).csv
```

### Common Commands

```bash
# View running containers
docker-compose ps

# Check container logs
docker-compose logs airflow-webserver
docker-compose logs airflow-scheduler

# Restart the pipeline
docker-compose restart

# Stop everything
docker-compose down
```

## 🔧 Troubleshooting

### Pipeline Not Starting

```bash
# Check if all containers are running
docker-compose ps

# Check logs for errors
docker-compose logs
```

### Reddit API Issues

- Verify your Reddit credentials in `config/config.conf`
- Check if your Reddit app is set to "script" type
- Ensure you're not hitting rate limits (try reducing the `limit` parameter)

### AWS Connection Problems

- Verify AWS credentials and permissions
- Check if your S3 bucket exists and is accessible
- Ensure your AWS region is correct

### Database Connection Issues

```bash
# Reset the database
docker-compose down -v
docker-compose up -d
```

### Permission Errors

```bash
# Fix file permissions
chmod -R 755 dags/ logs/ plugins/
```

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/new-feature`)
3. **Commit** your changes (`git commit -m 'Add new feature'`)
4. **Push** to the branch (`git push origin feature/new-feature`)
5. **Open** a Pull Request

### Ideas for Contributions

- Add support for more subreddits
- Implement data quality checks
- Add more transformation functions
- Create data visualization dashboards
- Improve error handling and retry logic

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- **Apache Airflow** community for the excellent orchestration framework
- **Reddit** for providing the API
- **AWS** for robust cloud services
- All contributors who help improve this project