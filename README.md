# Heart disease prediction and deployed in GCP and Docker

## ML Model Development and Google Cloud Deployment Procedure

> **Note**: Before proceeding with deployment, ensure your Flask ML project works locally and that you have a `requirements.txt` file containing the necessary libraries.

### Step-by-Step Guide:

1. **Create Google Account**:
   - If you don't already have a Google Account, create one.

2. **Go to Google Cloud Console**:
   - Navigate to [Google Cloud Console](https://console.cloud.google.com/).

3. **Activate Free Credits**:
   - Enable free credits for Google Cloud if this is your first time using it.

4. **Go to IAM**:
   - Click on the three stacked horizontal lines on the left side, then select **IAM & Admin** from the options.

5. **Navigate to "Managed Resources"**:
   - In the IAM section, select **Managed Resources**.

6. **Create a New Project**:
   - Click **Create Project** and enter a project name in lowercase (e.g., `heartdockergcp`).

7. **Download Google Cloud SDK**:
   - Use [this link](https://dl.google.com/dl/cloudsdk/channels/rapid/GoogleCloudSDKinstaller.exe) to download the Google Cloud SDK installer.

8. **Install Google Cloud SDK**:
   - Install the downloaded `.exe` file.

9. **Download Docker Desktop**:
   - Download Docker for Windows using [this link](https://docs.docker.com/desktop/install/windows-install/).

10. **Install Docker Desktop**:
    - Follow the installation process for Docker on your system.

11. **Verify Docker Installation**:
    - Open **Command Prompt** and issue the command:
      ```bash
      docker --version
      ```
    - If Docker is installed correctly, this will display the Docker version.

12. **Create Dockerfile**:
    - Open the project folder in Windows, right-click, and select **New > Text Document**.
    - Name it `Dockerfile.txt`, then paste the following code into the file:

    ```dockerfile
    # Use an official Python runtime as the base image
    FROM python:3.11-slim

    # Set the working directory in the container
    WORKDIR /app

    # Copy the current directory contents into the container at /app
    COPY . /app

    # Install required Python packages from the requirements file
    RUN pip install --no-cache-dir -r requirements.txt

    # Expose the port that the Flask app will run on
    EXPOSE 8080

    # Set the default command to run the Flask app (point to main.py)
    CMD ["gunicorn", "--bind", ":8080", "--workers", "1", "--threads", "8", "--timeout", "0", "main:app"]
    ```

13. **Rename Dockerfile**:
    - Go to **Command Prompt** and navigate to the working directory.
    - Rename the Dockerfile by running:
    ```bash
    ren Dockerfile.txt Dockerfile
    ```

14. **Authenticate Google Cloud**:
    - Run the following command to authenticate your account:
    ```bash
    gcloud auth login
    ```
    - This will open a browser window where you will log in to your Google account.

15. **Set Google Cloud Project**:
    - Set your Google Cloud project with the command:
    ```bash
    gcloud config set project YOUR_PROJECT_ID
    ```
    - Replace `YOUR_PROJECT_ID` with the project ID you created earlier, e.g., `heartdockergcp`.

16. **Enable Cloud Build API**:
    - Enable the Cloud Build service with the command:
    ```bash
    gcloud services enable cloudbuild.googleapis.com
    ```

17. **Submit the Build to Google Cloud**:
    - Use the following command to build and tag your Docker image:
    ```bash
    gcloud builds submit --tag gcr.io/YOUR_PROJECT_ID/your_ml_app .
    ```
    - Replace `YOUR_PROJECT_ID` with your project name and `your_ml_app` with your image name. For example:
    ```bash
    gcloud builds submit --tag gcr.io/heartdockergcp/heartdockerimagegcp .
    ```

18. **List Container Images**:
    - To verify the container image has been uploaded, use the following command:
    ```bash
    gcloud container images list
    ```

19. **Deploy the Application**:
    - Deploy your containerized app to Google Cloud Run:
    ```bash
    gcloud run deploy --image gcr.io/YOUR_PROJECT_ID/your_ml_app --platform managed
    ```
    - For example:
    ```bash
    gcloud run deploy --image gcr.io/heartdockergcp/heartdockerimagegcp --platform managed
    ```

20. **View the App**:
    - Once the app is deployed, you will be provided with a global URL. To access it from the browser, use:
    ```bash
    gcloud app browse
    ```

