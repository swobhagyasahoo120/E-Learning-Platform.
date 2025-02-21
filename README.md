# E-Learning-Platform.
E-Learning-Platform/
│── backend/                # Node.js Express API
│   ├── models/             # MongoDB Schemas
│   ├── routes/             # API Routes
│   ├── controllers/        # Business Logic
│   ├── recommendation/     # AI Recommendation System
│   ├── config/             # Database & Env Config
│   ├── server.js           # Main Server File
│
│── frontend/               # React.js Frontend
│   ├── src/                # React Components
│   ├── pages/              # Page Components
│   ├── services/           # API Calls
│   ├── App.js              # Main App Component
│   ├── index.js            # Entry Point
│
│── dataset/                # Sample Data for AI
│── README.md               # Documentation
│── package.json            # Dependencies
│── .gitignore              # Ignore Unnecessary Files


Backend (Node.js + Express + MongoDB)
Install Dependencies

cd backend
npm init -y
npm install express mongoose dotenv cors body-parser


server.js (Main Backend File)
const express = require("express");
const mongoose = require("mongoose");
const cors = require("cors");
const dotenv = require("dotenv");

dotenv.config();
const app = express();
app.use(express.json());
app.use(cors());

mongoose.connect(process.env.MONGO_URI, {
  useNewUrlParser: true,
  useUnifiedTopology: true,
}).then(() => console.log("MongoDB Connected")).catch(err => console.log(err));

app.use("/api/users", require("./routes/userRoutes"));
app.use("/api/courses", require("./routes/courseRoutes"));
app.use("/api/recommendations", require("./routes/recommendationRoutes"));

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));



User Model (MongoDB Schema):
const mongoose = require("mongoose");

const UserSchema = new mongoose.Schema({
  name: String,
  email: { type: String, unique: true },
  password: String,
  interests: [String], 
});

module.exports = mongoose.model("User", UserSchema);


User Routes:
const express = require("express");
const router = express.Router();
const User = require("../models/User");

router.post("/register", async (req, res) => {
  try {
    const user = new User(req.body);
    await user.save();
    res.status(201).json(user);
  } catch (err) {
    res.status(400).json({ error: err.message });
  }
});

module.exports = router;


import pandas as pd
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity

# Sample dataset
courses = pd.DataFrame({
    "course_id": [1, 2, 3, 4],
    "title": ["Python Basics", "Machine Learning", "Web Development", "Data Science"],
    "category": ["Programming", "AI", "Web", "Data Science"]
})

def recommend_courses(user_interest):
    vectorizer = TfidfVectorizer()
    course_vectors = vectorizer.fit_transform(courses["category"])
    
    user_vector = vectorizer.transform([user_interest])
    similarity = cosine_similarity(user_vector, course_vectors)
    
    recommendations = courses.iloc[similarity.argsort()[0][-3:]]  
    return recommendations.to_dict(orient="records")

# Example usage
user_interest = "AI"
print(recommend_courses(user_interest))



Frontend (React.js):
cd frontend
npx create-react-app .
npm install axios react-router-dom

App.js:
import React, { useState, useEffect } from "react";
import axios from "axios";

function App() {
  const [courses, setCourses] = useState([]);

  useEffect(() => {
    axios.get("http://localhost:5000/api/recommendations?interest=AI")
      .then(res => setCourses(res.data))
      .catch(err => console.log(err));
  }, []);

  return (
    <div>
      <h1>E-Learning Platform</h1>
      <h2>Recommended Courses</h2>
      <ul>
        {courses.map((course) => (
          <li key={course.course_id}>{course.title}</li>
        ))}
      </ul>
    </div>
  );
}

export default App;



