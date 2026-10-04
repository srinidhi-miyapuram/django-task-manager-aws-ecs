FROM python:3

WORKDIR /usr/src/app

COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt


COPY . .

COPY docker-script.sh /docker-script.sh
RUN chmod +x /docker-script.sh
ENTRYPOINT ["/docker-script.sh"]



EXPOSE 8000

# CMD [ "python", "./manage.py", "runserver", "0.0.0.0:8000" ]
# CMD ["gunicorn", "--bind", "0.0.0.0:8000", "config.wsgi:application"]
