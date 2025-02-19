# Utilisation d'une image officielle de Python
FROM python:3.11

# Définition du répertoire de travail
WORKDIR /app

# Copie des fichiers nécessaires
COPY requirements.txt .

# Installation des dépendances
RUN pip install --no-cache-dir -r requirements.txt

# Copie du code source
COPY . .

# Exposition du port
EXPOSE 5000

# Commande de lancement
CMD ["python", "app.py"]
