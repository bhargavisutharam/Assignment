#using official python image (we are using python slim as this is just a basic simple application)
FROM python:3.9-slim

#set working directory
WORKDIR /app


COPY requirements.txt .
RUN pip install -r requirements.txt

#copy app code
COPY . .

#Expose prot
EXPOSE 5000

CMD ["python","app.py"]