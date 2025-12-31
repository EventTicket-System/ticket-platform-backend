# ===========================================
# STAGE 1: Build
# ===========================================
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY ["TicketPlatform.Worker/TicketPlatform.Worker.csproj", "TicketPlatform.Worker/"]
COPY ["TicketPlatform.Application/TicketPlatform.Application.csproj", "TicketPlatform.Application/"]
COPY ["TicketPlatform.Domain/TicketPlatform.Domain.csproj", "TicketPlatform.Domain/"]
COPY ["TicketPlatform.Infrastructure/TicketPlatform.Infrastructure.csproj", "TicketPlatform.Infrastructure/"]
RUN dotnet restore "TicketPlatform.Worker/TicketPlatform.Worker.csproj"
COPY . .
WORKDIR "/src/TicketPlatform.Worker"
RUN dotnet build "TicketPlatform.Worker.csproj" -c Release -o /app/build
# ===========================================
# STAGE 2: Publish
# ===========================================
FROM build AS publish
RUN dotnet publish "TicketPlatform.Worker.csproj" -c Release -o /app/publish /p:UseAppHost=false
# ===========================================
# STAGE 3: Runtime
# ===========================================
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS runtime
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "TicketPlatform.Worker.dll"]