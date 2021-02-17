# Featured Tags

* `4.8-windowsservercore-2004`
  * `docker pull xavierhdz/dev_4_demos:4.8-windowsservercore-2004`

# About This Image

Use this image for your develop, testing or demostrate your applications.

In order to keep things as simple as possible and to help all .Net Framework applications to be migrated into a containerize environment, this image was build. The image allows 
to run in a single windows container both an IIS server and a mssql server.

Intended Use: Development and Testing only. Not supported in production environments.

# How to Use the Image

docker run -d -p 80:80 -p 1433:1433 -e sa_password=<SA_PASSWORD> -e ACCEPT_EULA=Y xavierhdz/dev_4_demos:4.8-windowsservercore-2004

You can connect to the SQL Server instance from inside the same container by using the sqlcmd utility, or from outside the container by downloading SQL Server Management Studio (SSMS). Follow this blog for detailed steps on how to connect to the SQL Server instance from inside or outside the container.

After the application starts, navigate to http://localhost:80 in your web browser. You need to navigate to the application via IP address instead of localhost for earlier Windows versions, which is demonstrated in View the ASP.NET app in a running container on Windows.

# Requirements

* This image requires Docker Engine 1.8+ in any of their supported platforms.
* At least 2GB of RAM (3.25 GB prior to 2017-CU2). Make sure to assign enough memory to the Docker VM if you're running on Docker for Mac or Windows.

# Feedback

* [File an issue](https://github.com/xavokill/docker-dev_4_demos/issues)
* [Contact](mailto:xavier.hernandez.app@outlook.com)

# License

[MIT](https://github.com/git/git-scm.com/blob/master/MIT-LICENSE.txt)
