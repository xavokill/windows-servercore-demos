# escape=`

FROM mcr.microsoft.com/dotnet/framework/runtime:4.8-windowsservercore-2004

ENV DOTNET_RUNNING_IN_CONTAINER=true `
    COMPLUS_NGenProtectedProcess_FeatureEnabled=0 `
    ROSLYN_COMPILER_LOCATION=C:\RoslynCompilers\tools


SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

# -----------------------------
# Installng IIS
# -----------------------------

RUN Add-WindowsFeature Web-Server; `
    Add-WindowsFeature NET-Framework-45-ASPNET; `
    Add-WindowsFeature Web-Asp-Net45; `
    Invoke-WebRequest -Uri https://dotnetbinaries.blob.core.windows.net/servicemonitor/2.0.1.10/ServiceMonitor.exe -OutFile C:\ServiceMonitor.exe; `
    &$Env:windir\Microsoft.NET\Framework64\v4.0.30319\ngen update; `
    &$Env:windir\Microsoft.NET\Framework\v4.0.30319\ngen update

RUN Invoke-WebRequest https://api.nuget.org/packages/microsoft.net.compilers.2.9.0.nupkg -OutFile C:\microsoft.net.compilers.2.9.0.zip; `
    Expand-Archive -Path C:\microsoft.net.compilers.2.9.0.zip -DestinationPath C:\RoslynCompilers; `
    Remove-Item C:\microsoft.net.compilers.2.9.0.zip -Force; `
    &$Env:windir\Microsoft.NET\Framework64\v4.0.30319\ngen install C:\RoslynCompilers\tools\csc.exe /ExeConfig:C:\RoslynCompilers\tools\csc.exe | `
    &$Env:windir\Microsoft.NET\Framework64\v4.0.30319\ngen install C:\RoslynCompilers\tools\vbc.exe /ExeConfig:C:\RoslynCompilers\tools\vbc.exe | `
    &$Env:windir\Microsoft.NET\Framework64\v4.0.30319\ngen install C:\RoslynCompilers\tools\VBCSCompiler.exe /ExeConfig:C:\RoslynCompilers\tools\VBCSCompiler.exe

EXPOSE 80

# -----------------------------
# Installng MSSQL
# -----------------------------
ENV sql_express_download_url "https://go.microsoft.com/fwlink/?linkid=829176"

ENV sa_password="Nissan2021" `
    attach_dbs="[]" `
    ACCEPT_EULA="Y" `
    sa_password_path="C:\ProgramData\Docker\secrets\sa-password"

COPY start.ps1 /
WORKDIR /

RUN Invoke-WebRequest -Uri "https://go.microsoft.com/fwlink/?linkid=2143496" -OutFile "sqlpackage.zip"; `
    Expand-Archive -LiteralPath "sqlpackage.zip" -DestinationPath "Sqlpackage"  -Force;

SHELL ["CMD", "/S", "/C"]
RUN setx /M PATH "%PATH%;C:\Sqlpackage"

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

RUN Invoke-WebRequest -Uri $env:sql_express_download_url -OutFile sqlexpress.exe ; `
    Start-Process -Wait -FilePath .\sqlexpress.exe -ArgumentList /qs, /x:setup ; `
    .\setup\setup.exe /q /ACTION=Install /INSTANCENAME=SQLEXPRESS /FEATURES=SQLEngine /UPDATEENABLED=0 /SQLSVCACCOUNT='NT AUTHORITY\System' /SQLSYSADMINACCOUNTS='BUILTIN\ADMINISTRATORS' /TCPENABLED=1 /NPENABLED=0 /IACCEPTSQLSERVERLICENSETERMS ; `
    Remove-Item -Recurse -Force sqlexpress.exe, setup

RUN stop-service MSSQL`$SQLEXPRESS ; `
        set-itemproperty -path 'HKLM:\software\microsoft\microsoft sql server\mssql14.SQLEXPRESS\mssqlserver\supersocketnetlib\tcp\ipall' -name tcpdynamicports -value '' ; `
        set-itemproperty -path 'HKLM:\software\microsoft\microsoft sql server\mssql14.SQLEXPRESS\mssqlserver\supersocketnetlib\tcp\ipall' -name tcpport -value 1433 ; `
        set-itemproperty -path 'HKLM:\software\microsoft\microsoft sql server\mssql14.SQLEXPRESS\mssqlserver\' -name LoginMode -value 2 ;

CMD .\start -sa_password $env:sa_password -ACCEPT_EULA $env:ACCEPT_EULA -attach_dbs \"$env:attach_dbs\" -Verbose;