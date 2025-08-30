# Base image
FROM mcr.microsoft.com/windows/servercore:ltsc2019

# Use PowerShell for all RUN commands
SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

# Environment variables
ENV PYTHONIOENCODING=UTF-8
ENV PYTHON_VERSION=3.12.0
ENV PYTHON_SHA256=5e4e6e7f8f9a0b1c2d3e4f5a6b7c8d9e0f1a2b3c4d5e6f7a8b9c0d1e2f3a4b5c

# Download and install Python
RUN $url = "https://www.python.org/ftp/python/$env:PYTHON_VERSION/python-$env:PYTHON_VERSION-amd64.exe"; \
    Write-Host ("Downloading {0} ..." -f $url); \
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12; \
    Invoke-WebRequest -Uri $url -OutFile "python.exe"; \
    Write-Host ("Verifying sha256 ({0}) ..." -f $env:PYTHON_SHA256); \
    if ((Get-FileHash python.exe -Algorithm sha256).Hash -ne $env:PYTHON_SHA256) { \
        Write-Host "FAILED!"; exit 1; \
    }; \
    Write-Host "Installing ..."; \
    $exitCode = (Start-Process python.exe -Wait -NoNewWindow -PassThru -ArgumentList @( \
        "/quiet", \
        "InstallAllUsers=1", \
        "TargetDir=C:\Python", \
        "PrependPath=1", \
        "Shortcuts=0", \
        "Include_doc=0", \
        "Include_pip=1", \
        "Include_test=0" \
    )).ExitCode; \
    if ($exitCode -ne 0) { exit $exitCode }; \
    $env:PATH = [Environment]::GetEnvironmentVariable("PATH", [EnvironmentVariableTarget]::Machine); \
    Write-Host "Verifying install ..."; python --version; \
    Remove-Item python.exe -Force; \
    $env:PYTHONDONTWRITEBYTECODE="1"; \
    Write-Host "Complete."

# Default command
CMD ["python"]
