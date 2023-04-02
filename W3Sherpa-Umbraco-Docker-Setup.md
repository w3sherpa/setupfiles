# Standup Docker Locally
1. Get lastes from  
   `https://our.umbraco.com/download/releases`

2. Create a destination folder for new umbraco project  
3. Open command promt in that destination folder location  
4. Run following command to download the intended version umbraco template. Make sure to replase <Project-Name> in the commands below.   
    >`dotnet new -i Umbraco.Templates::11.2.0`  
    `dotnet new umbraco -n "<Project-Name>"`  
    `dotnet run --project "<Project-Name>"`  
5. Create a new empty solution file in vs 2022 and add the newly created project in the solution
6. Add docker file for containerization  
   >FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build  
    WORKDIR /src  
    COPY ["tintinsherpa.csproj", "./"]  
    RUN dotnet restore "tintinsherpa.csproj"  
    COPY . .  
    WORKDIR "/src/."  
    RUN dotnet publish "tintinsherpa.csproj" -c Release -o /app/publish  <br/><br/>
    FROM mcr.microsoft.com/dotnet/aspnet:7.0  
    WORKDIR /app  
    EXPOSE 80  
    ENV ASPNETCORE_URLS=http://+:80  
    COPY --from=build /app/publish .    <br/><br/>
    ENTRYPOINT ["dotnet", "tintinsherpa.dll"]  
7. Open the solution in vs 2022.  
8. Build the project and run it in iis express. Installation page will load on the browser and enter the Name, email and password and make sure to `SAVE IT`.
9. Make sure to change the database type to point it to the sql server instance locally ( ubuntu).
10. Once the installation is done, new umbraco site will be up.

# Run it in docker  
## Pre-requisites
1. Make sure the connection string to sql server has server value of '<sqlsever-host-ip> ,1433'  

1. Open command prompt at the location of docker file.  
2. Run to build the image  
`docker build -t umbraco11-image:latest .`  
3. Run the start the container  
`docker run --rm -t -p 8081:80 umbraco11-image:latest`


## To push to docker hub  
1. docker login
2. username:w3sherpa
3. password:T3serract

`docker build -t umbraco11-chamalpitho:latest .`
Run docker tag <imange-name> <username>/<image-name>:version  
Run docker push <username>/<image-name>:version
example   
`docker tag umbraco11-chamalpitho w3sherpa/umbraco11-chamalpitho:latest`  
`docker push w3sherpa/umbraco11-chamalpitho:latest`  


## Pulling and running the image   
1. docker login
2. username:w3sherpa
3. password:T3serract  

`docker image pull w3sherpa/sherpa-ticket:latest`

`docker run --rm -d -p 8090:80 w3sherpa/sherpa-ticket:latest`
