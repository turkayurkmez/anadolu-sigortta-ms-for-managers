# See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

# This stage is used when running from VS in fast mode (Default for Debug configuration)
FROM mcr.microsoft.com/dotnet/aspnet:9.0 AS base
USER $APP_UID
WORKDIR /app
EXPOSE 8080
EXPOSE 8081


# This stage is used to build the service project
FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["Services/Catalog/ECommerce.Catalog.API/ECommerce.Catalog.API.csproj", "Services/Catalog/ECommerce.Catalog.API/"]
COPY ["Services/Catalog/ECommerce.Catalog.Infrastructure/ECommerce.Catalog.Infrastructure.csproj", "Services/Catalog/ECommerce.Catalog.Infrastructure/"]
COPY ["Services/Catalog/ECommerce.Catalog.Application/ECommerce.Catalog.Application.csproj", "Services/Catalog/ECommerce.Catalog.Application/"]
COPY ["Services/Catalog/ECommerce.Catalog.Domain/ECommerce.Catalog.Domain.csproj", "Services/Catalog/ECommerce.Catalog.Domain/"]
RUN dotnet restore "./Services/Catalog/ECommerce.Catalog.API/ECommerce.Catalog.API.csproj"
COPY . .
WORKDIR "/src/Services/Catalog/ECommerce.Catalog.API"
RUN dotnet build "./ECommerce.Catalog.API.csproj" -c $BUILD_CONFIGURATION -o /app/build

# This stage is used to publish the service project to be copied to the final stage
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./ECommerce.Catalog.API.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# This stage is used in production or when running from VS in regular mode (Default when not using the Debug configuration)
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "ECommerce.Catalog.API.dll"]