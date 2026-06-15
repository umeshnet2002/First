# First


<dependencies>
		<dependency>
			<groupId>org.apache.logging.log4j</groupId>
			<artifactId>log4j-core</artifactId>
			<version>2.24.3</version>
		</dependency>

		<!-- JUnit -->
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.13.2</version>
			<scope>test</scope>
		</dependency>

		<!-- Selenium -->
		<dependency>
			<groupId>org.seleniumhq.selenium</groupId>
			<artifactId>selenium-java</artifactId>
			<version>4.44.0</version>
		</dependency>

		<!-- Cucumber -->
		<dependency>
			<groupId>io.cucumber</groupId>
			<artifactId>cucumber-java</artifactId>
			<version>7.22.1</version>
		</dependency>

		<dependency>
			<groupId>io.cucumber</groupId>
			<artifactId>cucumber-junit</artifactId>
			<version>7.14.0</version>
		</dependency>

		<dependency>
			<groupId>io.cucumber</groupId>
			<artifactId>cucumber-testng</artifactId>
			<version>7.22.1</version>
		</dependency>

		<!-- TestNG -->
		<dependency>
			<groupId>org.testng</groupId>
			<artifactId>testng</artifactId>
			<version>7.12.0</version>
			<scope>test</scope>
		</dependency>

		<!-- WebDriverManager -->
		<dependency>
			<groupId>io.github.bonigarcia</groupId>
			<artifactId>webdrivermanager</artifactId>
			<version>5.8.0</version>
		</dependency>

		<!-- Apache POI -->
		<dependency>
			<groupId>org.apache.poi</groupId>
			<artifactId>poi-ooxml</artifactId>
			<version>5.2.5</version>
		</dependency>

		<!-- Extent Reports -->
		<dependency>
			<groupId>com.aventstack</groupId>
			<artifactId>extentreports</artifactId>
			<version>5.1.1</version>
		</dependency>

		<!-- Logging -->
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-api</artifactId>
			<version>2.0.13</version>
		</dependency>

		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-simple</artifactId>
			<version>2.0.13</version>
		</dependency>

		<!-- Cucumber Reporting -->
		<dependency>
			<groupId>net.masterthought</groupId>
			<artifactId>cucumber-reporting</artifactId>
			<version>5.11.0</version>
		</dependency>

	</dependencies>

	<build>
		<plugins>

			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.13.0</version>
				<configuration>
					<source>17</source>
					<target>17</target>
				</configuration>
			</plugin>

			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-surefire-plugin</artifactId>
				<version>3.2.5</version>
				<configuration>
					<suiteXmlFiles>
						<suiteXmlFile>testng.xml</suiteXmlFile>
					</suiteXmlFiles>
				</configuration>
			</plugin>

		</plugins>
	</build>



  pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'JDK'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/umeshnet2002/First'
            }
        }

        stage('Build') {
            steps {
                dir('My_Project') {
                    bat 'mvn clean'
                }
            }
        }

        stage('Compile') {
            steps {
                dir('My_Project') {
                    bat 'mvn compile'
                }
            }
        }

        stage('Test') {
            steps {
                dir('My_Project') {
                    bat 'mvn test'
                }
            }

            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package') {
            steps {
                dir('My_Project') {
                    bat 'mvn package -DskipTests'
                }
            }
        }

        stage('Docker Build') {
            steps {
                dir('My_Project') {
                    bat 'docker build -t cucumber-framework .'
                }
            }
        }

        stage('Docker Run') {
            steps {
                dir('My_Project') {
                    bat 'docker run cucumber-framework'
                }
            }
        }
    }

    post {
        success {
            echo 'Build Successful'
        }

        failure {
            echo 'Build Failed'
        }
    }
}



FROM maven:3.9.15-eclipse-temurin-21

RUN apt-get update && apt-get install -y \
    wget \
    curl \
    unzip \
    gnupg \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL https://packages.microsoft.com/keys/microsoft.asc | \
    gpg --dearmor -o /usr/share/keyrings/microsoft.gpg

RUN echo "deb [arch=amd64 signed-by=/usr/share/keyrings/microsoft.gpg] https://packages.microsoft.com/repos/edge stable main" \
    > /etc/apt/sources.list.d/microsoft-edge.list

RUN apt-get update && \
    apt-get install -y microsoft-edge-stable && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY . .

RUN mvn clean install -DskipTests

CMD ["mvn","test","-DsuiteXmlFile=testng.xml"]




		EdgeOptions options = new EdgeOptions();

		options.addArguments("--headless=new");
		options.addArguments("--no-sandbox");
		options.addArguments("--disable-dev-shm-usage");
		options.addArguments("--disable-gpu");
		options.addArguments("--window-size=1920,1080");

	    driver = new EdgeDriver(options);
