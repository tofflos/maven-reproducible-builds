## Reproducible builds with Maven
From what I can tell there has been ongoing work to add support for reproducible builds in Maven for quite some time. Somehwere in Q4 2019 the support was considered good enough that documentation was added. See https://maven.apache.org/guides/mini/guide-reproducible-builds.html.


Write the following code:
```Java
public static void main(String[] args) {
    System.out.println("Hello reproducible world!");
}
```

Package the application as an executable. Note that support for reproducible builds is implemented at the plugin level. You are free to use whatever version of Maven you have installed but need to ensure that the plugins used by the project are recent enough to support reproducible builds. For the maven-jar-plugin this support was added in 3.2.0.
```XML
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-jar-plugin</artifactId>
    <version>3.2.0</version>
    <configuration>
        <archive>
            <manifest>
                <mainClass>com.example.Application</mainClass>
            </manifest>
        </archive>
    </configuration>
</plugin>
```

Add the following property:
```XML
<properties>
    <project.build.outputTimestamp>2019-10-02T08:04:00Z</project.build.outputTimestamp>
</properties>
```

Build the executable:
```Powershell
$ mvn package

[INFO] Scanning for projects...
[INFO]
[INFO] -----------------------< com.example:helloworld >-----------------------
[INFO] Building helloworld 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
...
[INFO] --- maven-jar-plugin:3.2.0:jar (default-jar) @ helloworld ---
[INFO] Building jar: C:\Users\Erik\Documents\NetBeansProjects\maven-reproducible-builds\helloworld\target\helloworld-1.0-SNAPSHOT.jar
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  3.117 s
[INFO] Finished at: 2020-02-28T06:20:10+01:00
[INFO] ------------------------------------------------------------------------
```

Calculate a checksum for the executable:
```Powershell
$ Get-FileHash .\target\helloworld-1.0-SNAPSHOT.jar | select -ExpandProperty Hash

51941B620F0D08DB5682AD968182545FC05F09038690C85FAB20790136AC6D22
```

Re-build the executable:
```Powershell
$ mvn clean package

[INFO] Scanning for projects...
[INFO]
[INFO] -----------------------< com.example:helloworld >-----------------------
[INFO] Building helloworld 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
...
[INFO] --- maven-jar-plugin:3.2.0:jar (default-jar) @ helloworld ---
[INFO] Building jar: C:\Users\Erik\Documents\NetBeansProjects\maven-reproducible-builds\helloworld\target\helloworld-1.0-SNAPSHOT.jar
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  3.171 s
[INFO] Finished at: 2020-02-28T06:23:50+01:00
[INFO] ------------------------------------------------------------------------
```

Re-calculate a checksum for the executable:
```Powershell
$ Get-FileHash .\target\helloworld-1.0-SNAPSHOT.jar | select -ExpandProperty Hash

51941B620F0D08DB5682AD968182545FC05F09038690C85FAB20790136AC6D22
```