# os we need in container
FROM python:3.8

# this make any python output appear on terminal
# set to any non zero value
ENV PYTHONUNBUFFERED=1

# a working dir
WORKDIR /django

# copy the requirements file
COPY requirements.txt requirements.txt

# install the required packages
RUN pip3 install -r requirements.txt
