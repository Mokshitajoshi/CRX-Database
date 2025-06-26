# 🧠 Coders Crux – Database Schema Documentation

Welcome to **Coders Crux**, a full-stack application designed to streamline the management of coding events, quizzes, hackathons, user profiles, and more. This document provides a detailed overview of the **PostgreSQL database schema** that powers the backend of the application.

---

## 🗂️ Table of Contents

- [Overview](#overview)
- [Entity Relationship Summary](#entity-relationship-summary)
- [Core Tables](#core-tables)
  - [User](#user)
  - [Quiz Schema](#quiz-schema)
  - [Quiz Answer](#quiz-answer)
  - [Hackathon](#hackathon)
  - [Hackathon Team](#hackathon-team)
  - [Hackathon Verification](#hackathon-verification)
  - [Hackathon Readme](#hackathon-readme)
  - [Hackathon Details](#hackathon-details)
  - [Codeathon](#codeathon)
  - [User Codeathon](#user-codeathon)
  - [Codeathon Submission](#codeathon-submission)
- [Normalization Notes](#normalization-notes)
- [Best Practices](#best-practices)
- [License](#license)

---

## 📘 Overview

Coders Crux is built to support multiple coding-related features:
- User registration and role management
- Quiz creation and evaluation
- Hackathon and codeathon participation
- Submission verification workflows
- Custom readme and scoring mechanisms

The schema is modular and normalized to support scalability and maintainability.

---

## 🧩 Entity Relationship Summary

- A **User** can participate in **Quizzes**, **Hackathons**, and **Codeathons**.
- A **Hackathon** can have multiple **Teams** and **Submissions**.
- A **Quiz** contains multiple **Questions**, which are answered and stored in the **Quiz Answer** table.
- Submissions and profiles can be verified using the **Hackathon Verification** table.

---

## 📄 Core Tables

### 🔐 User

Stores user information and credentials.

| Column          | Type        | Description                    |
|-----------------|-------------|--------------------------------|
| id              | BIGINT      | Primary Key                    |
| email           | VARCHAR(255)| Unique email                   |
| password        | VARCHAR(255)| Encrypted password             |
| username        | VARCHAR(255)| Unique username                |
| role            | VARCHAR(255)| User role (admin, user, etc.)  |
| profile_pic     | TEXT        | Profile picture URL            |
| profile_links   | TEXT[]      | Social/media links             |
| languages       | TEXT[]      | Known programming languages    |
| devices         | TEXT[]      | Registered devices             |
| college_name    | VARCHAR(255)| Name of user's college         |
| user_type       | VARCHAR(255)| Developer, Student, etc.       |
| phone_number    | VARCHAR(20) | Optional contact number        |

---

### ❓ Quiz Schema

Represents a quiz with questions and metadata.

| Column         | Type         | Description                   |
|----------------|--------------|-------------------------------|
| id             | BIGINT       | Primary Key                   |
| quiz_name      | VARCHAR(255) | Name of the quiz              |
| questions      | JSONB        | Question set (JSON structure) |
| owner_id       | BIGINT       | FK to `User` (creator)        |

---

### 📝 Quiz Answer

Stores answers submitted by users for quiz questions.

| Column     | Type     | Description                         |
|------------|----------|-------------------------------------|
| id         | BIGINT   | Primary Key                         |
| quiz_id    | BIGINT   | FK to `Quiz Schema`                |
| user_id    | BIGINT   | FK to `User`                        |
| question_id| BIGINT   | Internal question ID                |
| answer     | TEXT     | Submitted answer                    |

---

### 🏁 Hackathon

Defines a hackathon event.

| Column     | Type         | Description                   |
|------------|--------------|-------------------------------|
| id         | BIGINT       | Primary Key                   |
| name       | VARCHAR(255) | Hackathon name                |
| description| TEXT         | Overview of the event         |
| start_time | TIMESTAMP    | When the event starts         |
| end_time   | TIMESTAMP    | When the event ends           |
| type       | VARCHAR(50)  | e.g. "team", "individual"     |

---

### 👥 Hackathon Team

Maps users to teams within a hackathon.

| Column       | Type    | Description                   |
|--------------|---------|-------------------------------|
| id           | BIGINT  | Primary Key                   |
| hackathon_id | BIGINT  | FK to `Hackathon`             |
| user_id      | BIGINT  | FK to `User`                  |
| team_name    | VARCHAR(255) | Optional custom name     |

---

### ✅ Hackathon Verification

Handles verification of user identity or submission authenticity.

| Column   | Type    | Description                     |
|----------|---------|---------------------------------|
| id       | BIGINT  | Primary Key                     |
| user_id  | BIGINT  | FK to `User`                    |
| verified | BOOLEAN | Verification status             |
| notes    | TEXT    | Optional admin notes            |

---

### 📃 Hackathon Readme

Stores ReadMe files or project overviews for hackathon submissions.

| Column       | Type   | Description             |
|--------------|--------|-------------------------|
| id           | BIGINT | Primary Key             |
| team_id      | BIGINT | FK to `Hackathon Team`  |
| content      | TEXT   | Markdown/HTML content   |

---

### 📊 Hackathon Details

Holds extended scoring and feedback.

| Column        | Type     | Description              |
|---------------|----------|--------------------------|
| id            | BIGINT   | Primary Key              |
| team_id       | BIGINT   | FK to `Hackathon Team`   |
| score         | INT      | Final evaluation score   |
| feedback      | TEXT     | Optional reviewer notes  |

---

### 🧪 Codeathon

Defines a coding challenge event.

| Column       | Type         | Description            |
|--------------|--------------|------------------------|
| id           | BIGINT       | Primary Key            |
| name         | VARCHAR(255) | Codeathon name         |
| description  | TEXT         | Event description      |
| start_time   | TIMESTAMP    | Start time             |
| end_time     | TIMESTAMP    | End time               |

---

### 👤 User Codeathon

Mapping table between users and the codeathons they participate in.

| Column        | Type   | Description                  |
|---------------|--------|------------------------------|
| id            | BIGINT | Primary Key                  |
| user_id       | BIGINT | FK to `User`                 |
| codeathon_id  | BIGINT | FK to `Codeathon`            |

---

### 📤 Codeathon Submission

Stores user submissions for coding events.

| Column        | Type   | Description               |
|---------------|--------|---------------------------|
| id            | BIGINT | Primary Key               |
| user_id       | BIGINT | FK to `User`              |
| codeathon_id  | BIGINT | FK to `Codeathon`         |
| submission    | TEXT   | Code or GitHub link       |
| language      | VARCHAR(50) | Programming language |
| score         | INT    | Final score               |

---

## 🔄 Normalization Notes

The schema avoids redundancy by:
- Separating user details, event metadata, and submission data.
- Using bridge tables (e.g., `User Codeathon`, `Hackathon Team`) for many-to-many relationships.
- Abstracting repeated structures like `languages`, `profileLinks`, and `devices` (recommended for further normalization).

---

## 💡 Best Practices Followed

- Usage of `BIGINT` for primary keys to ensure scalability.
- Separation of concerns across related tables.
- Use of foreign keys to maintain referential integrity.
- Use of `JSONB` for dynamic or unstructured data (e.g., quiz questions).



