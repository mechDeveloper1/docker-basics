#openJDK windows nanoserver container
FROM microsoft/nanoserver

# $ProgressPreference: https://github.com/PowerShell/PowerShell/issues/2138#issuecomment-251261324
SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

ENV JAVA_HOME C:\\ojdkbuild
RUN $newPath = ('{0}\bin;{1}' -f $env:JAVA_HOME, $env:PATH); \
        Write-Host ('Updating PATH: {0}' -f $newPath); \
# Nano Server does not have "[Environment]::SetEnvironmentVariable()"
        setx /M PATH $newPath;

# https://github.com/ojdkbuild/ojdkbuild/releases
ENV JAVA_VERSION 8u121
ENV JAVA_OJDKBUILD_VERSION 1.8.0.121-2
ENV JAVA_OJDKBUILD_ZIP java-1.8.0-openjdk-1.8.0.121-2.b13.ojdkbuild.windows.x86_64.zip
ENV JAVA_OJDKBUILD_SHA256 99a842b88b42c049ba4ffc3b13a9b98fbdad818703750e319ae59aa717e95ad3


#Commented below line as we have manually downloaded the zip file from url below
#RUN $url = ('https://github.com/ojdkbuild/ojdkbuild/releases/download/{0}/{1}' -f $env:JAVA_OJDKBUILD_VERSION, $env:JAVA_OJDKBUILD_ZIP); \
#	Write-Host ('Downloading {0} ...' -f $url); \
#	Invoke-WebRequest -Uri $url -OutFile 'ojdkbuild.zip'; \

#Copies manually downloaded zip from local machine to windows container
COPY ojdkbuild.zip .

RUN Write-Host ('Verifying sha256 ({0}) ...' -f $env:JAVA_OJDKBUILD_SHA256); \
        if ((Get-FileHash ojdkbuild.zip -Algorithm sha256).Hash -ne $env:JAVA_OJDKBUILD_SHA256) { \
                Write-Host 'FAILED!'; \
                exit 1; \
        }; \
        \
        Write-Host 'Expanding ...'; \
        Expand-Archive ojdkbuild.zip -DestinationPath C:\; \
        \
        Write-Host 'Renaming ...'; \
        Move-Item \
                -Path ('C:\{0}' -f ($env:JAVA_OJDKBUILD_ZIP -Replace '.zip$', '')) \
                -Destination $env:JAVA_HOME \
        ; \
        \
        Write-Host 'Verifying install ...'; \
        Write-Host '  java -version'; java -version; \
        Write-Host '  javac -version'; javac -version; \
        \
        Write-Host 'Removing ...'; \
        Remove-Item ojdkbuild.zip -Force; \
        \
        Write-Host 'Complete.';

# docker command to build image from this Dockerfile
# docker build -t labs/openjdk-8u121:nanoserver .
