# JobAssistant Full Stack AI App

#### Video Demo: [https://www.loom.com/share/982d776693534feea5998fc751310716?sid=12ddb0f7-72c3-4804-a333-a80dba22531c]


### Description:
As part of my CS50 milestone project, I developed an AI-powered tool designed to assist job seekers with their interview preparation. This innovative application blends my business acumen, mentorship experience, and technical expertise in Python (Flask) and JavaScript (Express.js) with advanced prompt engineering techniques. The result is a sophisticated platform that offers a personalized coaching experience similar to one-on-one mentorship.

### Key Features:

1. **Self-Presentation:**
   - The app guides users in crafting a compelling self-presentation by framing their experience as a concise and specific story related to the job. Utilizing the "SHE" formula (Succinct, Honest, Engaging), it highlights the necessary skills and experiences while providing examples that showcase problem-solving and interpersonal abilities.

2. **Company's Challenges & Candidate as a Solution:**
   - The tool analyzes potential challenges the company might face and explains how the candidate’s skills and experiences make them an ideal solution. It includes relevant qualifications and experiences, positioning the candidate as the answer to the company's needs.

3. **Tailored Questions and Answers:**
   - The app generates five pertinent interview questions based on the user's profile and job description, offering detailed and personalized sample answers. Each response is designed to highlight the candidate's qualifications and alignment with the job requirements.

4. **Disqualifying Question Preparation:**
   - Recognizing the importance of the question "Do you have any questions for me?", the app provides guidance on how to respond effectively. It offers tips on demonstrating engagement, intelligence, and interest, along with specific questions related to the job, team, or company.

5. **Key Strategies and Advice:**
   - The tool delivers practical advice to help candidates impress their interviewers. This includes highlighting relevant experiences, demonstrating problem-solving abilities, showcasing adaptability, and providing balanced, concise answers.

### Technical Overview:

**Backend:**
   - Built with **Express.js**, the backend handles API requests and orchestrates communication between different components of the app. It uses the **axios** library for making HTTP requests to the local GPT-4 API endpoint.

**Frontend:**
   - Designed with **Embedded JavaScript (EJS)**, the frontend renders dynamic content based on user inputs and API responses. The user interface is intuitive, ensuring a seamless experience.

**Local GPT-4 API:**
   - The app leverages the **g4f open-source library** from GitHub to run a local instance of the GPT-4 API. This setup ensures robust performance and data privacy as the processing happens locally without relying on external servers.

**Integration and Communication:**
   - The backend server, written in Express.js, listens for incoming requests and processes user inputs. It constructs detailed prompts by combining the job description and resume provided by the user. These prompts are then sent to the local GPT-4 API, which generates the interview preparation guide.

### Code Overview:

#### server.js (Backend Implementation):
```javascript
const express = require('express');
const bodyParser = require('body-parser');
const axios = require('axios');
const app = express();

app.use(bodyParser.urlencoded({ extended: true }));
app.use(express.json());
app.set('view engine', 'ejs');
app.use(express.static('public'));

app.get('/', (req, res) => {
    res.render('index');
});

app.post('/generate', async (req, res) => {
    const { jobDescription, cv } = req.body;

    if (!jobDescription || !cv) {
        return res.status(400).send('Job description and CV are required');
    }

    const prompt = createPrompt(jobDescription, cv);

    try {
        const response = await axios.post('http://localhost:6000/api/gpt-4', {
            role: 'user',
            content: prompt
        });

        res.render('result', { result: response.data.content.trim().split('\n') });
    } catch (error) {
        console.error(error.message);
        res.status(500).send('An error occurred while generating the interview guide');
    }
});

const createPrompt = (jobDescription, cv) => `
You are an expert career coach who provides personalized and humanized responses. Given the following job description and CV, generate a comprehensive interview preparation guide. Focus on helping the candidate tell their experience and skills as a story that addresses the company's pain points. Address the candidate personally as "you" to create a coaching-like feel.

### Sections:

1. **Self-Presentation:**
   - Present the candidate's experience as a concise and specific story related to the job.
   - Use the “SHE” formula: Succinct, Honest, Engaging.
   - Highlight required skills and experiences from the job description.
   - Provide examples that show problem-solving and interpersonal skills.

2. **Company's Challenges & Candidate as a Solution:**
   - Analyze potential challenges the company might face.
   - Explain how the candidate’s skills and experiences make them a solution to these challenges.
   - Include three to four qualifications and experiences relevant to the job.

3. **Tailored Questions and Answers:**
   - Create the 5 most relevant interview questions for the candidate based on their profile and the job description.
   - Provide detailed and personalized sample answers for each question.
   - Ensure each answer showcases the candidate's qualifications and how they align with the job requirements.

4. **Disqualifying Question: "Do you have any questions for me?":**
   - Emphasize the importance of this question.
   - Provide tips on how to show engagement, intelligence, and interest.
   - Suggest specific questions related to the job, team, or company that demonstrate the candidate's interest and understanding.

5. **Key Points and Strategies:**
   - Highlight specific experiences that align with the job requirements.
   - Demonstrate understanding of the company's challenges and how the candidate's skills can address them.
   - Showcase problem-solving abilities with concrete examples.
   - Emphasize adaptability and willingness to learn.
   - Avoid generic answers and negative comments about past employers.
   - Maintain a balance between modesty and confidence.
   - Provide concise and to-the-point answers.

### Job Description:
${jobDescription}

### Candidate's CV:
${cv}
`;

app.listen(4000, () => console.log('Server running on port 4000'));
```

#### app.py (Local GPT-4 API Server):
```python
from flask import Flask, request, jsonify
import openai

app = Flask(__name__)

openai.api_key = 'your_openai_api_key'

@app.route('/api/gpt-4', methods=['POST'])
def generate_response():
    data = request.json
    role = data['role']
    content = data['content']

    response = openai.Completion.create(
        engine="text-davinci-003",
        prompt=f"{role}: {content}",
        max_tokens=1500,
        n=1,
        stop=None,
        temperature=0.7,
    )

    return jsonify(response.choices[0].text.strip())

if __name__ == '__main__':
    app.run(port=6000)
```

This project illustrates the seamless integration of Flask and Express.js to create a responsive, AI-driven web application. It highlights how advanced AI models like GPT-4 can be leveraged to provide valuable, personalized services in the realm of career coaching.
