FROM tiangolo/uvicorn-gunicorn-fastapi:python3.8 as base

COPY requirements.txt /tmp
# RUN pip install --upgrade pip
# RUN apt-get update -y && apt-get upgrade -y
RUN apt-get update -y

# required for opencv
RUN apt-get install ffmpeg libsm6 libxext6 -y
RUN pip install --trusted-host pypi.org --trusted-host files.pythonhosted.org -r /tmp/requirements.txt
WORKDIR /app
COPY ./app .
EXPOSE 5000

############# Debugger
FROM base as debug
RUN pip install --trusted-host pypi.org --trusted-host files.pythonhosted.org ptvsd

WORKDIR /app
CMD ["python", "-m", "ptvsd", "--host", "0.0.0.0", "--port", "5678", \
    "--wait", "--multiprocess", "-m", \
    "uvicorn", "main:app", "--host", "0.0.0.0", "--port", "5000"]

############# Production
FROM base as prod

WORKDIR /app
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "5000"]