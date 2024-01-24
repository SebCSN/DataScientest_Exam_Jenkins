FROM python:3.8-slim

WORKDIR /app

COPY ./requirements.txt /app/requirements.txt

RUN apt-get update \
    && apt-get install gcc -y \
    && apt-get clean

RUN pip install -r /app/requirements.txt \
    && rm -rf /root/.cache/pip

COPY . /app/

# Définit la commande par défaut à exécuter lorsque le conteneur est lancé. Ici, on lance l'application avec Uvicorn en écoutant sur l'adresse 0.0.0.0 et le port 1234.
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "1234"] 
