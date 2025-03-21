# См. статью по ссылке https://aka.ms/customizecontainer, чтобы узнать как настроить контейнер отладки
# и как Visual Studio использует этот Dockerfile для создания образов для ускорения отладки.

# Этот этап используется при запуске из VS в быстром режиме (по умолчанию для конфигурации отладки)
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER $APP_UID
WORKDIR /app
EXPOSE 5090 
EXPOSE 5091 


# Этот этап используется для сборки проекта службы
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /CRS.Centrum
COPY ["CRS.Centrum.API/CRS.Centrum.API.csproj", "CRS.Centrum.API/"]
COPY ["CRS.Centrum.Infrastructure/CRS.Centrum.Infrastructure.csproj", "CRS.Centrum.Infrastructure/"]
COPY ["CRS.Centrum.Application/CRS.Centrum.Application.csproj", "CRS.Centrum.Application/"]
COPY ["CRS.Centrum.Core/CRS.Centrum.Core.csproj", "CRS.Centrum.Core/"]
RUN dotnet restore "./CRS.Centrum.API/CRS.Centrum.API.csproj"
COPY ../ .
WORKDIR "/CRS.Centrum/CRS.Centrum.API"
RUN dotnet build "./CRS.Centrum.API.csproj" -c $BUILD_CONFIGURATION -o /app/build

# Этот этап используется для публикации проекта службы, который будет скопирован на последний этап
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./CRS.Centrum.API.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# Этот этап используется в рабочей среде или при запуске из VS в обычном режиме (по умолчанию, когда конфигурация отладки не используется)
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "CRS.Centrum.API.dll"]