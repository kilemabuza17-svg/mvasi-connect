# SRC Campus Portal

> A secure, centralised web platform for student democracy and campus life — combining SRC elections, society management, and campus events into one official student hub.

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)

---

## Overview

The SRC Campus Portal solves three real problems at our university:

- **SRC elections** are currently run via paper ballots or Google Forms — both insecure and inaccessible to off-campus students
- **Society information** is scattered across WhatsApp groups and physical notice boards
- **Event announcements** get lost in mass emails with no central feed

This platform gives students one trusted place to vote, follow societies, and RSVP to events — all managed by the SRC from a single admin dashboard.

### Key Technical Highlight — Ballot Anonymity

The voting module implements a genuine anonymity architecture: the system proves a student voted *without* recording *what* they voted for. The `voter_registry` and `ballots` tables are permanently separated with no foreign key link between them — meaning no query can ever trace a ballot back to a specific student.

---

## Features

###  Voting Module
- Secure login via university email + OTP verification
- Browse candidate profiles, bios, and manifestos
- Cast anonymous ballots per position
- One-vote-per-position enforcement with database transactions
- Live voter turnout dashboard for admin
- Results published only after election closes

###  Societies & Events Module
- Browse and follow student societies
- Personalised events feed based on followed societies
- RSVP to events with capacity enforcement
- Society leaders manage their own pages and post events
- Push/email notifications for new events

###  Admin Dashboard
- Create and configure elections (positions, dates, eligibility)
- Approve or reject candidate registrations
- Approve new society registrations
- View real-time entry logs and generate reports
- Manage all user roles from one panel

---

## Course Information

**Course:** Practices in Software Engineering   CSC392
**Institution:** University of Eswatini  
**Year:** 2026  
**Supervisor:** [Dr Enock Dube]


---

> Built as part of a Software Engineering course project. The SRC Campus Portal is designed to be practically adoptable by any university running SRC elections and managing student societies in the long term.
