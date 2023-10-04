# escape=`
FROM mcr.microsoft.com/windows/servercore:ltsc2022

RUN powershell -Command `
    Add-WindowsFeature Web-Server; `
    Install-WindowsFeature -Name Web-Server -IncludeManagementTools; `
	Install-WindowsFeature -Name "NET-Framework-Core"; `
	Install-WindowsFeature -Name "Web-ASP"; `
	Install-WindowsFeature -N "Web-ISAPI-Ext"; `
    Invoke-WebRequest -UseBasicParsing -Uri "https://dotnetbinaries.blob.core.windows.net/servicemonitor/2.0.1.10/ServiceMonitor.exe" -OutFile "C:\ServiceMonitor.exe"


RUN powershell -NoProfile -Command Remove-Item -Recurse C:\inetpub\wwwroot\*

WORKDIR /inetpub/wwwroot

COPY index.asp .


EXPOSE 80

ENTRYPOINT ["C:\\ServiceMonitor.exe", "w3svc"]