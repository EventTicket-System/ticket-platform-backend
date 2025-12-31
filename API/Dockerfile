# ===========================================
# STAGE 1: Build
# Sử dụng SDK image để build ứng dụng
# ===========================================
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

# Copy các file .csproj và restore dependencies
# Làm riêng để tận dụng Docker layer caching
COPY ["API/API.csproj", "API/"]
COPY ["TicketPlatform.Application/TicketPlatform.Application.csproj", "TicketPlatform.Application/"]
COPY ["TicketPlatform.Domain/TicketPlatform.Domain.csproj", "TicketPlatform.Domain/"]
COPY ["TicketPlatform.Infrastructure/TicketPlatform.Infrastructure.csproj", "TicketPlatform.Infrastructure/"]
RUN dotnet restore "API/API.csproj"

# Copy toàn bộ source code và build
COPY . .
WORKDIR "/src/API"
RUN dotnet build "API.csproj" -c Release -o /app/build

# ===========================================
# STAGE 2: Publish
# Tạo bản publish optimized cho production
# ===========================================
FROM build AS publish
RUN dotnet publish "API.csproj" -c Release -o /app/publish /p:UseAppHost=false

# ===========================================
# STAGE 3: Runtime
# Image nhỏ gọn chỉ chứa runtime
# ===========================================
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS runtime
WORKDIR /app

# Copy từ stage publish
COPY --from=publish /app/publish .

# Expose port 8080 (default cho ASP.NET Core 8)
EXPOSE 8080
ENV ASPNETCORE_URLS=http://+:8080

# Entry point
ENTRYPOINT ["dotnet", "API.dll"]
