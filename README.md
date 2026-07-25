<div align="center">

<img src="assets/logo.png" alt="Basma 360 Logo" width="200"/>

# Basma 360° 
**AI-Powered Volunteering Platform**

<img src="assets/Frame 427318175.png" alt="Basma 360 Banner" width="100%"/>

[![Flutter](https://img.shields.io/badge/Flutter-%2302569B.svg?style=for-the-badge&logo=Flutter&logoColor=white)](#)
[![Dart](https://img.shields.io/badge/dart-%230175C2.svg?style=for-the-badge&logo=dart&logoColor=white)](#)
[![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54)](#)
[![Supabase](https://img.shields.io/badge/Supabase-3ECF8E?style=for-the-badge&logo=supabase&logoColor=white)](#)
[![PostgreSQL](https://img.shields.io/badge/postgresql-4169e1?style=for-the-badge&logo=postgresql&logoColor=white)](#)
[![Firebase](https://img.shields.io/badge/firebase-%23039BE5.svg?style=for-the-badge&logo=firebase)](#)

*Connecting the right volunteers with the right opportunities.*<br>
**#1 First in Egypt**

</div>

---

## 📖 Project Overview

**Basma 360°** is a smart platform that matches volunteers with verified organizations based on skills, interests, and location[cite: 1]. 

Millions of people want to volunteer, but finding the right organizations isn't easy[cite: 1]. Our objective is to connect volunteers with trusted organizations, match them with suitable opportunities using AI, and support diverse volunteering fields across different locations[cite: 1]. 

> **Note:** This repository serves as a project showcase and portfolio piece for our graduation project. The source code is currently closed-source.

---

## 🛑 Problem Statement & 💡Solution

<details>
<summary><b>1. Trust and Verification</b></summary>
<br>

*   **Problem:** Volunteers may hesitate to join organizations because it is difficult to verify their reliability[cite: 1].
*   **Solution:** Organizations must submit official documents for verification before publishing opportunities to increase trust and safety[cite: 1].
</details>

<details>
<summary><b>2. Skill Awareness</b></summary>
<br>

*   **Problem:** Some volunteers are unsure about their skills, making it difficult to choose suitable opportunities[cite: 1].
*   **Solution:** A Skill Assessment Test helps volunteers identify their strengths and supports more accurate opportunity recommendations[cite: 1].
</details>

<details>
<summary><b>3. Communication Gaps</b></summary>
<br>

*   **Problem:** There is no direct communication between volunteers and organizations, forcing them to rely on external communication channels[cite: 1].
*   **Solution:** A Real-time Chat feature enables direct and instant communication between both parties[cite: 1].
</details>

<details>
<summary><b>4. Inefficient Matching</b></summary>
<br>

*   **Problem:** Volunteers struggle to find opportunities that match their skills, interests, and location[cite: 1]. Organizations spend valuable time searching for qualified volunteers, especially during urgent needs[cite: 1].
*   **Solution:** An AI-powered Matching System uses semantic similarity, skills, field, and location to recommend the most suitable opportunities and qualified volunteers[cite: 1].
</details>

---

## ✨ Key Features

*   ✅ **Role-Based Dashboards:** Distinct interfaces for both volunteers and organizations[cite: 1].
*   ✅ **Real-time Chat:** Seamless, instant messaging[cite: 1].
*   ✅ **Smart Opportunity Recommendations:** Tailored feeds based on user profiles[cite: 1].
*   ✅ **AI-Powered Skill Matching:** Intelligent pairing algorithms[cite: 1].
*   ✅ **Organization Verification:** Document-backed trust systems[cite: 1].

---

## 🧠 AI Matching System & Workflow

The core of Basma 360° is its intelligent matching engine, executed in three distinct phases[cite: 1]:

### Phase 1: User Journey[cite: 1]
| 🧑‍🤝‍🧑 Volunteer Journey | 🏢 Organization Journey |
| :--- | :--- |
| 1. Create Profile | 1. Create Profile |
| 2. Set Location | 2. Upload Verification Documents |
| 3. Complete Skills Assessment | 3. Create Opportunity with Requirements & Location |
| 4. Add Field & Bio | |

### Phase 2: AI Semantic Analysis[cite: 1]
1.  **Text Processing:** A Sentence Transformer Model converts volunteer profiles and opportunity text into semantic embeddings[cite: 1].
2.  **Storage:** These embeddings are stored securely in a Supabase Database[cite: 1].
3.  **Scoring:** A final match score is calculated based on semantic similarity, skills match, location match, gender match, and field match[cite: 1].

### Phase 3: AI Matching Results[cite: 1]
The algorithm yields dual benefits: identifying the best opportunities for volunteers and surfacing the most qualified volunteers for organizations to review and approve[cite: 1].

---

## 🛠 Technology Stack

<div align="center">

| Category | Technology | Description |
| :--- | :--- | :--- |
| **Frontend** | Flutter | Cross-platform mobile development[cite: 1] |
| **Architecture**| Feature-Based MVVM | Clean, scalable structure[cite: 1] |
| **State Management**| Bloc (Cubit) | Predictable state handling[cite: 1] |
| **Authentication** | Firebase | Secure user login and identity[cite: 1] |
| **Backend** | Supabase | Scalable backend infrastructure[cite: 1] |
| **Database** | PostgreSQL | Relational data management[cite: 1] |
| **AI Engine** | Python | Core logic for matching algorithms[cite: 1] |
| **Semantic AI** | Sentence Transformers | Generating semantic embeddings[cite: 1] |

</div>

---

## 📐 System Architecture & Database Design

*   **Architecture:** The application utilizes a Feature-Based MVVM pattern to separate business logic from the UI layer, ensuring maintainability[cite: 1]. BLoC/Cubit is implemented for robust state management[cite: 1].
*   **Database:** Hosted on PostgreSQL via Supabase, utilizing relational tables for Users, Organizations, Opportunities, and Applications[cite: 1]. The database securely handles vectorized embeddings generated by the Python AI engine to perform rapid similarity searches[cite: 1].

---

## 📱 Screenshots

<div align="center">
  
| Welcome Screen | Skills Assessment | Organization Dashboard | AI Matching Results |
| :---: | :---: | :---: | :---: |
| <img src="[PLACEHOLDER_SCREENSHOT_1_URL]" width="200" alt="Welcome Screen"/> | <img src="[PLACEHOLDER_SCREENSHOT_2_URL]" width="200" alt="Skills Assessment"/> | <img src="[PLACEHOLDER_SCREENSHOT_3_URL]" width="200" alt="Dashboard"/> | <img src="[PLACEHOLDER_SCREENSHOT_4_URL]" width="200" alt="Matches"/> |

</div>

---

## 🎥 Demo Video

[![Basma 360 Demo Video]([PLACEHOLDER_VIDEO_THUMBNAIL_URL])]([PLACEHOLDER_VIDEO_LINK])

> *Click the image above to watch the full project presentation and demo.*

---

## 🚀 Future Work

We are continuously planning enhancements to Basma 360°[cite: 1]:
*   🔔 **Push Notifications:** Notify users about new opportunities and applications[cite: 1].
*   🌐 **Community Hub:** Share posts, events, and discussions[cite: 1].
*   🏆 **Gamification:** Earn badges and achievement points[cite: 1].
*   🛡️ **Admin Dashboard:** Review verification requests, manage reports[cite: 1].
*   📜 **Volunteer Certificates:** Generate digital certificates automatically[cite: 1].
*   🤖 **AI Screening:** Rank applicants using custom questions[cite: 1].

---

## 📊 Project Status

🟡 **Under Active Development** - This graduation project has been successfully completed, and we are currently refining features for potential future deployment.

---

## 👥 Team

**Alaa Elsaidy (Alaa Ayman)** 
*   *Software Engineer & Mobile Application Developer*
*   Lead Developer & Architect for Basma 360°

---

## 🌐 Connect with Me

🔗 [All My Links](https://linktr.ee/alaa.elsaidy)

Questions and feedback are always welcome![cite: 1]

---

## 📄 License

This project is proprietary and confidential. All rights reserved. 
*(This repository is for showcase purposes only. Code reproduction or distribution is prohibited without explicit permission).*
