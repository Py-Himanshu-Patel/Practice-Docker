# choose the os image with python
FROM python:3.8-alpine

# set working directory
WORKDIR /src

# copy requirement file from current location to inside WORKDIR
COPY requirements.txt requirements.txt

# use pip3 to install required pacages
RUN pip3 install -r requirements.txt

# copy src folder into WORKDIR
COPY ./src .

# command we need to run the server and make it available 
# outside the container, the 0.0.0.0:8000 make app externally visible
CMD ["python3", "manage.py", "runserver", "0.0.0.0:8000"]
