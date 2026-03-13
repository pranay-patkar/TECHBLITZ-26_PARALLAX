# Development Prompt: Clinic OS

This document contains the master prompt used to generate the architecture, database schema, and integration logic for the Clinic OS project.

---

### Master Prompt Text

Act as an expert Full-Stack Developer and Database Architect. I am building a "Clinic OS: Database & WhatsApp Integration Blueprint". [cite_start]Your task is to generate the complete code, database setup queries, and architectural instructions based on the highly specific requirements provided below. [cite: 1]

#### 1. Technical Stack Constraints
[cite_start]You must strictly adhere to the following technical stack: [cite: 2]
* [cite_start]**Frontend**: Use only Vanilla JavaScript, HTML5, and CSS3 (No React/Frameworks to ensure stability). [cite: 3]
* [cite_start]**Backend/Database**: Use Supabase (PostgreSQL). [cite: 4]
* [cite_start]**Real-time Layer**: Implement Supabase Realtime for instant dashboard updates. [cite: 5]
* [cite_start]**WhatsApp Gateway**: Utilize the Meta WhatsApp Cloud API (Free Tier). [cite: 6]
* [cite_start]**Deployment**: Prepare the frontend configuration for deployment on Vercel. [cite: 7]

#### 2. Database Schema Definition (Supabase/PostgreSQL)
[cite_start]Please provide the exact SQL DDL statements to construct the following database schema: [cite: 8]

[cite_start]**Table: profiles** [cite: 9]
* [cite_start]`id`: uuid (Primary Key - links to Supabase Auth) [cite: 10]
* [cite_start]`full_name`: text [cite: 11]
* [cite_start]`role`: text (Check constraint: 'doctor' or 'receptionist') [cite: 12]

[cite_start]**Table: patients** [cite: 13]
* [cite_start]`id`: uuid (Primary Key) [cite: 14]
* [cite_start]`phone_number`: text (Unique - used as the primary identifier for WhatsApp) [cite: 15]
* [cite_start]`name`: text [cite: 16]
* [cite_start]`created_at`: timestamp [cite: 17]

[cite_start]**Table: appointments** [cite: 18]
* [cite_start]`id`: uuid (Primary Key) [cite: 19]
* [cite_start]`patient_id`: uuid (Foreign Key to patients.id) [cite: 20]
* [cite_start]`appointment_date`: date [cite: 21]
* [cite_start]`start_time`: time [cite: 22]
* [cite_start]`end_time`: time (Calculated based on slot duration) [cite: 23]
* [cite_start]`status`: text (Check: 'pending', 'confirmed', 'cancelled') [cite: 24]
* [cite_start]`source`: text (Default: 'whatsapp') [cite: 25]
* [cite_start]`clash_detected`: boolean (Flag for the dashboard logic) [cite: 26]

[cite_start]**Table: clinic_config** [cite: 27]
* `id`: int (Primary Key) [cite: 28]
* [cite_start]`doctor_id`: uuid (Foreign Key to profiles.id) [cite: 29]
* `slot_duration`: int (e.g., 15 for 15 minutes) [cite: 30]
* [cite_start]`working_hours_start`: time [cite: 31]
* [cite_start]`working_hours_end`: time [cite: 32]

#### 3. WhatsApp Integration (Student Setup)
[cite_start]Provide the setup steps and webhook code for integrating the Meta WhatsApp Cloud API. [cite: 33, 34]
* [cite_start]**Developer Portal Setup**: Steps to create a Meta for Developers account and add the "WhatsApp" product to your app. [cite: 37]
* **Testing Protocol**: Instructions on using the provided "Test Number" to send messages to up to 5 personal WhatsApp numbers. [cite: 38]
* [cite_start]**Webhook Architecture**: Write the code to point the Meta Webhook to a Supabase Edge Function. [cite: 39]
* [cite_start]**Interactive UI Elements**: Provide the JSON payload structures for sending "List Messages" or "Reply Buttons" for the patient to pick slots. [cite: 40]

#### 4. Real-time Logic Flow Implementation (Patient -> Clinic)
[cite_start]Generate the Vanilla JS frontend code and Supabase Edge Function logic to facilitate this exact flow: [cite: 41]
* [cite_start]**Input**: A patient clicks an interactive button (e.g., "10:30 AM") on WhatsApp. [cite: 42]
* **Process**: The Webhook receives this payload and inserts the data into the appointments table with the status set to 'pending'. [cite: 43]
* [cite_start]**Real-time Event**: The Receptionist Dashboard (written in Vanilla JS) must listen for INSERT events on the table and trigger a toast notification. [cite: 44]
* [cite_start]**Conflict Resolution**: The dashboard runs a quick check using this logic: `SELECT * FROM appointments WHERE status = 'confirmed' AND start_time = '10:30:00'`. [cite: 45]
* [cite_start]**Output Presentation**: The UI displays the incoming request highlighted in Red (Clash) or Green (Available). [cite: 46]

#### 5. Appointment Confirmation Flow (Clinic -> Patient)
Generate the logic and Edge Function code for the following sequence when an appointment is accepted:
* **Dashboard Action**: The receptionist clicks "Confirm" on the Vanilla JS dashboard, updating the appointments table status to 'confirmed'.
* **Trigger**: A Supabase Database Webhook (or Edge Function) listens for this specific UPDATE event.
* **Notification Payload**: The function retrieves the patient's phone_number and the finalized appointment details (date, time, doctor's name).
* **WhatsApp Dispatch**: The function makes a POST request to the Meta WhatsApp Cloud API to send a formatted confirmation text (e.g., "Your appointment is confirmed for [Date] at [Time]") back to the patient.
