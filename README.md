## Docker Task 2

### Instructions

#### Objective
In this assignment, you will:
1. Write a Dockerfile to create a custom API container and optimize the build.
2. Pull a MinIO container from Docker Hub.
3. Make both containers communicate over Docker networks.
4. Store data on the host using Docker volumes.
5. Introduce and explain a new Docker Compose keyword in your setup.

#### Steps

1. **Initialize a New Repository**  
   Create a new repository on GitHub and clone it to your local machine.

2. **Create the Directory Structure**  
   Set up the following directory structure in your repository:
   ```
   your-repo/
   ├── api/
   │   ├── app.py
   │   └── requirements.txt
   ├── docker-compose.yml
   ├── Dockerfile
   └── README.md
   ```

3. **Write the Python Application**  
   Inside the `api/` directory, create a simple Python Flask application that interacts with MinIO.

   **app.py**
   ```python
   from flask import Flask, request, jsonify
   from minio import Minio
   from minio.error import S3Error
   import os

   app = Flask(__name__)

   minio_client = Minio(
       "minio:9000",
       access_key=os.getenv('MINIO_ACCESS_KEY'),
       secret_key=os.getenv('MINIO_SECRET_KEY'),
       secure=False
   )

   bucket_name = "mybucket"

   @app.route('/store', methods=['POST'])
   def store():
       data = request.json
       object_name = data.get("name")
       content = data.get("content")

       try:
           minio_client.put_object(bucket_name, object_name, content, len(content))
           return jsonify({"message": "Data stored successfully"}), 200
       except S3Error as e:
           return jsonify({"error": str(e)}), 500

   if __name__ == "__main__":
       if not minio_client.bucket_exists(bucket_name):
           minio_client.make_bucket(bucket_name)
       app.run(host='0.0.0.0', port=5000)
   ```

   **requirements.txt**
   ```
   Flask
   minio
   ```
4. **Write the Dockerfile**  
   Create a Dockerfile to build the Flask application. Optimize the Docker build by using multi-stage builds or caching mechanisms.

5. **Create Docker Compose File**  
   Create a `docker-compose.yml` file to define and run multi-container Docker applications. Use one container from the Dockerfile and another from Docker Hub (MinIO).

6. **Explain the New Keyword**  
   In your `README.md` file, explain the new keyword used in the `docker-compose.yml` (e.g., `healthcheck`).
   
   ## How to Run
   1. Clone the repository
   2. Build and run the containers using Docker Compose:
   3. Access the Flask application at `http://localhost:5000`.

8. **Submission**  
   Push your changes to your GitHub repository and submit the repository link for review.
