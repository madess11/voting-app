# Utilisation de l'image officielle de .NET SDK
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build

# Définition du répertoire de travail
WORKDIR /app

# Copie et restauration des dépendances
COPY Worker.csproj ./

RUN dotnet restore

# Copie du code source
COPY . .

# Compilation de l'application
RUN dotnet publish -c Release -o /out


# Image pour exécuter l'application
FROM mcr.microsoft.com/dotnet/aspnet:7.0

WORKDIR /app
COPY --from=build /out .

CMD ["dotnet", "Worker.dll"]
