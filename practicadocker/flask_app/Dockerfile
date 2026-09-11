FROM python:3.9-slim

EXPOSE 5000

WORKDIR /app

COPY requirements.txt /app
RUN pip install -r requirements.txt

COPY app/app.py /app
COPY app/data.py /app
CMD python app.py
