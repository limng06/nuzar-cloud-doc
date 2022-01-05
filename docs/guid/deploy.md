# 部署

## sh/bat

* java -jar artifact.jar

## docker

在启动项目中添加`Dockerfile`，请确认需要用的jdk版本，使用openjdk-jre作为基础环境

```dockerfile
FROM openjdk:8-jre-bullseye
#FROM openjdk:11.0.13-jre-bullseye jdk11
VOLUME /tmp
MAINTAINER NUZAR_CLOUD

ENV SAMPLE_BUILD_MODE=dockerfile
LABEL PROJECT_NAME=${project.artifactId} \
      PROJECT=${project.artifactId}

RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
RUN echo 'Asia/Shanghai' >/etc/timezone
# Arbitrary files can be added
ADD target/${project.artifactId}.jar /

# 端口号
EXPOSE 20880
# 指定JVM启动参数
ENV PROFILES_OPTS=""

ENTRYPOINT [ "sh", "-c","java $PROFILES_OPTS  -jar /${project.artifactId}.jar"]
```

在`pom.xml`中引入docker-plugin

```xml
<build>
    <finalName>${project.artifactId}</finalName>
    <plugins>
        <!-- docker打包插件 -->
        <plugin>
            <groupId>io.fabric8</groupId>
            <artifactId>docker-maven-plugin</artifactId>
            <version>0.37.0</version>
            <configuration>
                <pushRegistry>http://10.122.0.56:5000</pushRegistry>
                <images>
                    <image>
                        <name>10.122.0.56:5000/com.nuzar.cloud/${project.artifactId}:${saas.image.version}</name>
                        <alias>${project.artifactId}</alias>
                        <build>
                            <contextDir>${project.basedir}</contextDir>
                        </build>
                        <run>
                            <!--设置容器名，可采用通配符-->
                            <containerNamePattern>${project.artifactId}</containerNamePattern>
                            <!--设置端口映射-->
                            <ports>
                                <port>20880:20880</port>
                            </ports>
                            <!--设置容器和宿主机目录挂载-->
                            <volumes>
                                <bind>
                                    <volume>/etc/localtime:/etc/localtime</volume>
                                </bind>
                            </volumes>
                        </run>
                    </image>
                </images>
            </configuration>
        </plugin>
    </plugins>
</build>
```

* 生成镜像

mvn run docker:build -f pom.xml

* 推送到私服

mvn run docker:push -f pom.xml
