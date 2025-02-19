# Utilisation de l'image officielle de Node.js
FROM node:18

# Définition du répertoire de travail
WORKDIR /app


# Copie et installation des dépendances
COPY package.json ./
RUN npm install --omit=dev

# Copie du code source
COPY . .

# Exposition du port
EXPOSE 3000

# Commande de lancement
CMD ["node", "server.js"]
