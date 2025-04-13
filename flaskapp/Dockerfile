FROM python:latest
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
ENV FLASK_RUN_PORT=80
ENV CUSTOM_HEADER='My Default Containerized Webapp'
ENV BG_COLOR=white
ENV FONT_COLOR=black
# RUN apt-get update && apt-get install -y gcc libc-dev linux-headers curl
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
EXPOSE 80
COPY . .
CMD ["flask", "run"]