# AI Chatbot Docker Deployment Guide

This guide provides instructions for deploying the AI Chatbot application using the pre-built Docker image.

## Prerequisites

- Docker installed on your system
- Docker Compose (if using the compose file)
- Google Gemini API key

## Quick Start

### 1. Get Your Google Gemini API Key
1. Visit [Google AI Studio](https://ai.google.dev/)
2. Sign in with your Google account
3. Create a new API key
4. Copy the API key to your clipboard

### 2. Configure Environment
Create a `.env` file in the same directory as your `docker-compose.yml`:

```bash
GEMINI_API_KEY=your_actual_api_key_here
```

### 3. Deploy the Application
Pull and run the pre-built image:

```bash
docker-compose up -d
```

The application will be available at: http://localhost:8081

## Docker Image Details

- **Image**: `m0hamedabed/chatbot-combined:latest`
- **Includes**: Frontend, Backend, and Nginx in a single optimized container
- **Port**: Exposes port 80 (mapped to 8081 externally)
- **Components**: React frontend, Flask backend, and Nginx reverse proxy

## Environment Variables

Required:
- `GEMINI_API_KEY`: Your Google Gemini API key for AI functionality

Optional:
- `MONGO_INITDB_ROOT_USERNAME`: MongoDB username (default: admin)
- `MONGO_INITDB_ROOT_PASSWORD`: MongoDB password (default: password)

## Docker Compose Configuration

The provided `docker-compose.yml` file includes:
- The main application container running the combined image
- A MongoDB container for message persistence
- Port mappings for external access
- Environment variable configuration from `.env` file

## Manual Docker Run (Alternative Method)

If you prefer to run the container manually:

```bash
docker run -d \
  --name chatbot-app \
  --env-file .env \
  -p 8081:80 \
  --restart unless-stopped \
  m0hamedabed/chatbot-combined:latest
```

Note: When running manually, you'll need to set up MongoDB separately.

## Configuration Files Overview

### docker-compose.yml
Defines the multi-container application:
- **app**: The main container using your pre-built image
- **mongodb**: Database service for message persistence
- Port mappings and volume configurations
- Environment variable loading

### Dockerfile.combined
Multi-stage build that:
- Uses Nginx as base image
- Installs Python and Node.js
- Builds the React frontend
- Installs Python backend dependencies
- Configures Nginx to serve frontend and proxy API requests
- Uses startup script to run services

### nginx.conf
Nginx configuration that:
- Serves the React frontend files
- Proxies API requests (`/api/*`) to the Flask backend
- Handles static file serving and compression
- Sets up proper headers and security configurations

### start.sh
Startup script that:
- Starts the Python Flask backend server in background
- Starts the Nginx server in foreground
- Ensures both services run simultaneously in the container

## Troubleshooting

### Common Issues

1. **"Sorry, I couldn't get a response" Error**
   - Check that your GEMINI_API_KEY is set correctly in `.env`
   - Verify your API key has proper permissions
   - Check application logs: `docker logs chatbot_combined`

2. **Connection Issues**
   - Ensure all containers are running: `docker-compose ps`
   - Check MongoDB connection: Verify the MongoDB container is healthy
   - Check port availability: Ensure port 8081 is not in use

3. **Port Conflicts**
   - The application uses port 8081 by default
   - Change the port mapping in docker-compose.yml if needed: `host_port:container_port`

### Useful Commands

```bash
# Check container status
docker-compose ps

# View application logs
docker logs chatbot_combined

# View MongoDB logs
docker logs chatbot_mongodb

# Restart the application
docker-compose restart

# Stop the application
docker-compose down

# Pull latest image version
docker-compose pull
docker-compose up -d
```

## Security Considerations

- Store your `GEMINI_API_KEY` in the `.env` file, never commit it to version control
- Consider using a reverse proxy in production environments
- Regularly update the Docker image to get the latest features and security patches

## API Endpoints

Once deployed, the application exposes the following API endpoints:

### Available Endpoints

|Method|Endpoint|Description|
|---|---|---|
|`GET`|`/api/messages`|Retrieve last 5 messages|
|`POST`|`/api/messages`|Save a message|
|`POST`|`/api/chat`|Chat with AI and save conversation|
|`GET`|`/api/history`|Get full conversation history|
|`DELETE`|`/api/clear`|Clear all messages (for testing)|
|`GET`|`/health`|Health check endpoint|
|`GET`|`/`|Application root (serves frontend)|

### Testing Endpoints

After deployment, you can test the endpoints directly in your browser or using curl:

**Health Check:**

- Browser: `http://localhost:8081/health`
    
- Curl: `curl http://localhost:8081/health`
    

**Get Recent Messages:**

- Browser: `http://localhost:8081/api/messages`
    
- Curl: `curl http://localhost:8081/api/messages`
    

**Get Full History:**

- Browser: `http://localhost:8081/api/history`
    
- Curl: `curl http://localhost:8081/api/history`
    

**POST endpoints require curl or API client tools:**


### 1. **Health Check (GET request):**

```bash
Invoke-RestMethod -Uri http://localhost:8081/health
```

### 2. **POST a message to the chatbot:**

```bash
Invoke-RestMethod -Uri http://localhost:8081/api/chat -Method Post -ContentType "application/json" -Body '{"message": "Hello, how are you?"}'
```

### 3. **Clear all messages (DELETE request):**

```bash
Invoke-RestMethod -Uri http://localhost:8081/api/clear -Method Delete
```


## Updating

To update to the latest version:

```bash
docker-compose pull
docker-compose up -d
```

## Support

If you encounter issues with the deployment:

1. Check the troubleshooting section above
    
2. Ensure all prerequisite steps were followed correctly
    
3. Verify your Google Gemini API key is valid and has proper permissions
    
4. Check Docker logs for specific error messages
    