#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:6.0-alpine AS base
WORKDIR /app

FROM mcr.microsoft.com/dotnet/sdk:6.0-alpine AS build
WORKDIR /src
COPY ["src/WisdomPetMedicine.RescueQuery.API/WisdomPetMedicine.RescueQuery.API.csproj", "src/WisdomPetMedicine.RescueQuery.API/"]
RUN dotnet restore "src/WisdomPetMedicine.RescueQuery.API/WisdomPetMedicine.RescueQuery.API.csproj"

COPY . .
WORKDIR "/src/src/WisdomPetMedicine.RescueQuery.API"
RUN dotnet build "WisdomPetMedicine.RescueQuery.API.csproj" -c Release -o /app/build
RUN dotnet test -c Release

FROM build AS publish
RUN dotnet publish "WisdomPetMedicine.RescueQuery.API.csproj" -c Release -o /app/publish

FROM base AS final
ENV ASPNETCORE_ENVIRONMENT Production

# Install cultures (same approach as Alpine SDK image)
RUN apk add --no-cache icu-libs

# Disable the invariant mode (set in base image)
ENV DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=false
ENV ASPNETCORE_URLS http://+:80
EXPOSE 80

WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "WisdomPetMedicine.RescueQuery.API.dll"]