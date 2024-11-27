## Jenkins Configuration

### Allow Jenkins to Access Docker
1. **Install Docker inside the Jenkins container:**
   ```bash
   docker exec -u root jenkins apt-get update && apt-get install -y docker.io
   ```

2. **Add Jenkins user to the Docker group:**
   ```bash
   docker exec -u root jenkins usermod -aG docker jenkins
   ```

3. **Restart the Jenkins container:**
   ```bash
   docker restart jenkins
   ```

### Install Required Plugins
- Pipeline
- HTML Publisher
- Git
- Docker Pipeline

