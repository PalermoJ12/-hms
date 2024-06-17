# Hospital Management System (HMS)

## Project Description

The Hospital Management System (HMS) is a web application designed to facilitate the management of hospital operations, providing a practical development environment for students. This system, built with Laravel for the backend and Vue.js for the frontend, efficiently manages patients, doctors, appointments, and medical records.

## Table of Contents

- [Features](#features)
- [Technologies Used](#technologies-used)
- [Installation](#installation)


## Features

- **User Authentication:** Role-based access for Admin, Doctor, and Patient.
- **Patient Management:** Add, edit, view, and delete patient records.
- **Doctor Management:** Add, edit, view, and delete doctor records.
- **Appointment Management:** Book, view, manage, and cancel appointments.
- **Medical Records Management:** View and update medical records.

## Technologies Used

- **Backend:** Laravel
- **Frontend:** Vue.js
- **Database:** MySQL

## Installation

To get a local copy up and running, follow these simple steps.

### Prerequisites

- **Node.js** and **npm**
- **Composer**
- **PHP** and **MySQL**

### Clone the Repository

```bash
git clone https://github.com/PalermoJ12/-hms


### How to use 

after clone 

-- cd vue
npm install
npm run serve

-- cd backend
composer install
php artisan migrate
php artisan db:seed
php artisan serve
