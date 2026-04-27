# 1. FRONT-END

## 1.1 Overview

The frontend of the application is implemented as a modern single-page application using React and Vite. It provides the user interface for interacting with the system, including uploading book images, displaying analysis results, and managing saved history.

The frontend communicates with the backend via REST API endpoints and is designed to be modular, responsive, and user-friendly.

---

## 1.2 Analysis of the Problem

The main goal of the frontend is to provide a simple and intuitive interface for:

- uploading an image of a book
- displaying analysis results
- allowing the user to save results
- viewing and managing saved history

The application must handle asynchronous communication with external services, display loading states, and gracefully handle errors.

---

## 1.3 Technology Choices

### React

React was chosen because:
- it supports component-based architecture
- it simplifies state management
- it enables reusable UI components
- it is suitable for dynamic applications

### Vite

Vite is used for:
- fast development server
- simple configuration
- efficient production builds

### Axios / Fetch API

Used for:
- communication with backend API
- sending requests and receiving responses

### LocalStorage (Fallback)

Used as a fallback when backend/database is unavailable:
- ensures the application still works offline
- stores user history locally

---

## 1.4 Application Structure

The frontend is structured into pages and reusable components.

### Pages

- **HomePage.jsx**
  - main entry point
  - image upload interface

- **ResultPage.jsx**
  - displays analysis result

- **HistoryPage.jsx**
  - displays saved results
  - allows deletion

---

### Components

- **Navbar.jsx**
  - navigation between pages

- **UploadBox.jsx**
  - handles image selection and upload

- **ImagePreview.jsx**
  - displays selected image before submission

- **LoadingSpinner.jsx**
  - shows loading state during API calls

- **ErrorMessage.jsx**
  - displays user-friendly errors

- **ResultCard.jsx**
  - displays analyzed book result

- **BookCard.jsx**
  - reusable component for displaying a book

- **HistoryList.jsx**
  - renders list of saved items

---

## 1.5 Communication with Backend

All communication is handled in `api.js`.

### Endpoints used:

- `POST /api/analyze`
  - sends image
  - receives analysis result

- `POST /api/history`
  - saves result to database

- `GET /api/history`
  - loads saved results

- `DELETE /api/history/:id`
  - deletes saved item

### Fallback logic

If backend is unavailable:

- saving history falls back to `localStorage`
- retrieving history reads from `localStorage`

---

## 1.6 Application Flow

1. User opens Home page
2. Uploads an image using UploadBox
3. Image preview is shown
4. User submits image
5. Loading spinner is displayed
6. Backend processes request
7. User is redirected to Result page
8. ResultCard displays:
   - title
   - author
   - book details
   - AI analysis
9. User can save result
10. History page displays saved results

---

## 1.7 Input and Output

### Input

- image file (JPG, PNG)

### Output

- recognized title and author
- book information
- genre
- summary
- recommendations

---

## 1.8 Error Handling

The frontend handles errors by:

- displaying ErrorMessage component
- preventing application crashes
- providing user feedback when API fails

---

## 1.9 State Management

State in the application represents dynamic data that changes during user interaction, such as selected files, loading status, API responses, and saved history.

In this application, state is managed locally within individual React components using React hooks, primarily `useState` and `useEffect`.

Each component is responsible for storing and updating its own state.

### Types of State Used

- **Upload state** – stores selected image
- **Loading state** – indicates ongoing requests
- **Error state** – stores error messages
- **Result state** – contains analyzed book data
- **History state** – stores saved results

### Why This Approach Was Used

- simple and easy to understand
- avoids unnecessary complexity
- sufficient for application scope
- improves maintainability

---

## 1.10 Styling

Styling is implemented using CSS:

- `main.css`
- responsive layout
- simple design

---

## 1.11 Setup and Running

### Install dependencies

```bash
npm install
```
### Run development server

```bash
npm run dev
```

---

## 1.12 Environment Variables

```env
VITE_API_BASE_URL=https://your-backend-url
```

---

## 1.13 Summary

The frontend is designed as a modular and maintainable system using React. It provides a clear user interface, handles communication with backend services, and ensures a smooth user experience through proper loading and error handling mechanisms.

The use of reusable components improves maintainability and scalability, while fallback mechanisms ensure robustness even when external services are unavailable.

# 2. BACK-END

## 2.1 Overview

The backend of the application is implemented using Node.js and Express. It serves as the core processing layer that handles incoming requests from the frontend, communicates with external APIs, and returns structured responses.

The backend is responsible for processing uploaded images, extracting book information using AI, retrieving book data from external services, and preparing results for display on the frontend.

The backend also provides endpoints for managing saved history, acting as an interface between the frontend and the database layer.

---

## 2.2 Analysis of the Problem

The main goal of the backend is to process user input (an uploaded book image) and transform it into meaningful structured data.

This involves several steps:

- receiving and validating uploaded files
- extracting relevant information from images
- retrieving matching book data from external APIs
- selecting the most relevant result
- generating additional analysis
- formatting a consistent response for the frontend

The backend must also handle asynchronous operations, manage communication with multiple external services, and ensure reliable error handling.

---

## 2.3 Technology Choices

### Node.js

Node.js was chosen because:
- it allows building scalable server-side applications
- it supports asynchronous operations efficiently
- it integrates well with REST APIs

### Express

Express is used for:
- routing and API endpoint creation
- handling HTTP requests and responses
- structuring backend logic in a clear and modular way

### Multer

Multer is used for:
- handling file uploads from the frontend
- processing multipart/form-data requests
- storing uploaded images in memory for further processing

### Axios

Axios is used for:
- making HTTP requests to external APIs
- communicating with Google Books API

### Google Gemini API

Used for:
- extracting book title and author from image
- selecting the best matching book
- generating analysis (genre, summary, recommendations)

---

## 2.4 Backend Structure

The backend is organized into separate modules for better maintainability.

### Main Server

- **server.js**
  - initializes Express server
  - configures middleware (CORS, JSON parsing)
  - registers API routes
  - starts the application

---

### Routes

- **analyze.js**
  - handles `/api/analyze`
  - processes uploaded image
  - integrates AI and external APIs
  - returns analysis result

- **history.js**
  - handles `/api/history`
  - provides endpoints for:
    - saving results
    - retrieving saved history
    - deleting history items

---

### Services

- **geminiService.js**
  - communicates with Gemini API
  - performs image analysis
  - generates structured JSON responses

- **booksService.js**
  - communicates with Google Books API
  - retrieves book candidates
  - formats API response data

---

### Utilities

- **parseJson.js**
  - extracts valid JSON from AI responses
  - ensures safe parsing of model output

---

### Database Connection Layer

- **supabase.js**
  - initializes database client
  - provides connection to Supabase service

Note: The database schema and configuration are described in a separate section of the documentation.

---

## 2.5 API Endpoints

### POST /api/analyze

Processes an uploaded image and returns analysis.

Steps:
1. Receive image from frontend
2. Extract title and author using AI
3. Query Google Books API
4. Select best matching book
5. Generate analysis
6. Return structured JSON response

---

### GET /api/history

- retrieves saved results

---

### POST /api/history

- saves a new result

---

### DELETE /api/history/:id

- deletes a specific history item

---

## 2.6 Processing Flow

1. Frontend sends image to backend
2. Backend receives image using multer
3. Image is converted to Base64 format
4. Gemini API extracts title and author
5. Google Books API returns candidate books
6. Gemini selects the best match
7. Gemini generates additional analysis
8. Backend formats the result
9. Response is sent to frontend

---

## 2.7 Input and Output

### Input

- image file (multipart/form-data)

### Output

- recognized book title and author
- book metadata (from Google Books)
- AI-generated:
  - genre
  - summary
  - recommendations
  - original publication year (if available)

---

## 2.8 Error Handling

The backend handles errors by:

- validating input data
- catching API errors
- returning appropriate HTTP status codes
- preventing crashes of the application

Examples:
- missing image → 400 Bad Request  
- no book found → 404 Not Found  
- internal error → 500 Internal Server Error  

---

## 2.9 Communication with Database Layer

The backend provides API endpoints for working with saved history.

These endpoints act as a communication layer between the frontend and the database. The backend does not define the database structure itself, but sends and retrieves data through the database client.

The detailed design of the database schema, data storage, and cloud configuration is described separately as part of the database and deployment section.

---

## 2.10 Summary

The backend is designed as a modular and scalable system that handles data processing, external API integration, and communication with the frontend.

It ensures that complex operations such as image analysis and data aggregation are performed efficiently and that the frontend receives clean, structured, and consistent responses.

By separating logic into routes, services, and utilities, the backend remains maintainable and easy to extend.

# 3. DATABASE IMPLEMENTATION

## 3.1 Overview

The database layer of the application is implemented using Supabase, which provides a cloud-hosted PostgreSQL database along with an API for interacting with stored data.

The database is used to persist user-generated data, specifically the history of analyzed books. It ensures that results can be stored, retrieved, and managed across sessions.

This layer is designed to be independent from the backend logic, with the backend acting only as an interface for sending and receiving data.

---

## 3.2 Purpose of the Database

The main purpose of the database is to:

- store analyzed book results
- allow retrieval of saved history
- support deletion of stored records
- provide persistence beyond the current browser session

Without the database, the application would rely only on local storage, which is limited to the user's device.

---

## 3.3 Technology Choice

### Supabase

Supabase was selected because:

- it provides a fully managed PostgreSQL database
- it includes built-in REST API capabilities
- it is easy to integrate with JavaScript applications
- it offers free-tier cloud hosting suitable for development and testing
- it simplifies authentication and data access (even though authentication is not used in this project)

---

## 3.4 Database Structure

The application uses a single main table:

### Table: `book_analyses`

This table stores all relevant information about analyzed books.

#### Main fields:

- `id`  
  Unique identifier for each record

- `created_at`  
  Timestamp of when the record was created

- `recognized_title`  
  Title detected from the image

- `recognized_author`  
  Author detected from the image

- `book_title`  
  Final selected book title

- `book_authors`  
  List of authors associated with the book

- `google_books_id`  
  Identifier from Google Books API

- `published_date`  
  Publication date from Google Books

- `display_published_year`  
  Year shown to the user

- `original_publication_year`  
  AI-estimated original publication year

- `genre`  
  AI-generated genre

- `summary`  
  AI-generated summary

- `recommendations`  
  AI-generated list of similar books

- `thumbnail`  
  URL to book cover image

- `description`  
  Book description from Google Books

- `selected_reason`  
  Explanation of why the book was selected

- `image_url`  
  Placeholder for storing uploaded image reference (optional)

- `raw_data`  
  Full JSON response stored for debugging and flexibility

---

## 3.5 Integration with Backend

The database is accessed through a dedicated connection module:

- `supabase.js`

This module:

- initializes the Supabase client
- uses environment variables for secure configuration
- provides a reusable database connection

The backend interacts with the database through API routes defined in:

- `history.js`

These routes perform:

- insertion of new records (`POST /api/history`)
- retrieval of records (`GET /api/history`)
- deletion of records (`DELETE /api/history/:id`)

---

## 3.6 Data Flow

1. User performs book analysis  
2. Backend returns result to frontend  
3. User chooses to save the result  
4. Frontend sends data to `/api/history`  
5. Backend inserts record into database  
6. Stored data becomes available for retrieval  
7. History page requests data via `/api/history`  
8. Backend returns stored records  

---

## 3.7 Environment Configuration

Database connection requires the following environment variables:

```env
SUPABASE_URL=your_supabase_url
SUPABASE_ANON_KEY=your_supabase_key
```
These values are used to securely connect to the Supabase project.

---

## 3.8 Error Handling

The database layer includes basic error handling to ensure stability and reliability of data operations.

The following mechanisms are implemented:

- validation of database connection (ensuring the Supabase client is properly initialized)
- handling errors during insert operations
- handling errors during read (query) operations
- handling errors during delete operations
- returning structured error responses to the backend

Example cases:

- missing configuration → database functionality is disabled and fallback mechanisms may be used  
- insert failure → backend returns an error response and data is not stored  
- query failure → backend returns an error response and no data is retrieved  
- delete failure → backend returns an error response and record remains unchanged  

---

## 3.9 Design Considerations

The database layer is intentionally designed as a separate component from the backend logic.

This separation ensures:

- clear responsibility boundaries  
- easier maintenance and debugging  
- improved scalability for future extensions  
- flexibility to replace or modify the database without major backend changes  

Additionally, the design supports:

- storing both structured and semi-structured data (via JSON fields like `raw_data`)  
- incremental extension of schema without breaking existing functionality  
- efficient querying of historical records  

This modular approach aligns with best practices for modern web application architecture.

---

## 3.10 Summary

The database implementation provides a reliable and scalable solution for storing analyzed book data using Supabase.

It enables persistent storage of user results, supports efficient retrieval and management of history, and integrates cleanly with the backend through well-defined API endpoints.

By separating concerns between frontend, backend, and database layers, the overall system remains modular, maintainable, and adaptable for future development.