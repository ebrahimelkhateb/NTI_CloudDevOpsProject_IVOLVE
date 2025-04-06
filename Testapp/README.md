# 📦 Step 1: App Setup & Local Testing 

This step describes how to set up, test, and run the Spring Boot application locally .

---

## ✅ Prerequisites

Make sure the following are installed:

- Git
- Java 17
- Gradle (wrapper is included)

---

## 🧾 Clone the Repository

```bash
git clone https://github.com/IbrahimAdell/FinalProjectCode.git
cd FinalProjectCode


![Image](https://github.com/user-attachments/assets/08ac5da5-7851-4cd6-a2a4-f4c1454673d8)

```

---

## ☕ Install Java (if not already installed)

```bash
sudo dnf install java-17-openjdk-devel -y
java -version
```

---

## 🛠 Build the Project

Use the Gradle wrapper to build the project and generate the JAR file:

```bash
./gradlew build
```

---

## 🧪 Run Unit Tests

To verify that everything works as expected:

```bash
./gradlew test
```


---

## 🚀 Run the App Locally

```bash
./gradlew bootRun
```



---

## 🌐 Access the App

Once the app is running, open your browser and go to:

```
http://localhost:8080
```

You should see your application’s homepage or API response.
