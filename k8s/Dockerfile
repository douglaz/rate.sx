FROM python:3.9-slim

WORKDIR /app

# Install dependencies
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the code
COPY . .

# Create log directory for app logs
RUN mkdir -p /app/log

EXPOSE 8004

CMD ["python", "bin/srv.py"]
