FROM registry.access.redhat.com/ubi8/dotnet-80-runtime:latest AS base
WORKDIR /app
EXPOSE 80

FROM registry.access.redhat.com/ubi8/dotnet-80:latest AS build
USER 0
WORKDIR /src
COPY ["test-repo/test-repo.csproj", "test-repo/"]
RUN dotnet restore "test-repo/test-repo.csproj"
COPY . .
WORKDIR "/src/test-repo"
RUN dotnet build "test-repo.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "test-repo.csproj" -c Release -o /app/publish /p:UseAppHost=false

FROM base AS final
USER 0
RUN microdnf update
RUN microdnf upgrade
WORKDIR /
COPY ["test-repo/licenses/license.txt", "licenses/"]
USER 1111
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "test-repo.dll"]