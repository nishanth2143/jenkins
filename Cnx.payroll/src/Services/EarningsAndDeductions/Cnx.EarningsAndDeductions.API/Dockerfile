FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS base
WORKDIR /app
EXPOSE 80

FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /src

COPY src/Services/*/*/*.csproj /src/csproj-files/

ARG RUN=pwd
RUN ${RUN}

COPY . .
WORKDIR /src/src/Services/EarningsAndDeductions/Cnx.EarningsAndDeductions.API
RUN dotnet publish -c Release -o /app

FROM build AS publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "Cnx.EarningsAndDeductions.API.dll"]