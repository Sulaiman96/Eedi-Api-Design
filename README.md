# Eedi Api Task - Improve Feature Spec

### Get Topics with Misconceptions
Retrieves all topics and subtopics where the student has incorrect answers.

**Endpoint**: `/api/v1/improve/topics`

**Example Request:**
``` 
GET /api/v1/improve/topics
Authorisation: Bearer JWT-Token
```

**Example Response:**
```
{
  "topics": [
    {
      "id": "112345",
      "name": "Number",
      "sub_topics": [
        {
          "id": "234235",
          "name": "Fractions",
          "misconceptions_count": 4
        },
        {
          "id": "234236",
          "name": "Decimals",
          "misconceptions_count": 2
        },
        {
          "id": "234237",
          "name": "Factors",
          "misconceptions_count": 1
        }
      ]
    },
    {
      "id": "112346",
      "name": "Algebra",
      "sub_topics": [
        {
          "id": "234245",
          "name": "Multiples",
          "misconceptions_count": 4
        },
        {
          "id": "234246",
          "name": "Primes",
          "misconceptions_count": 8
        },
        {
          "id": "234247",
          "name": "Fractions",
          "misconceptions_count": 1
        }
      ]
    }
  ]
}
```

### Get All Questions for Sub-topic
Retrives all questions that the student answered incorrectly for the specified sub-topic.

**Endpoints:** `/api/v1/improve/sub-topics/{sub_topic_id}/questions`

**Example Request:** (The ID I used is from Numbers -> Fractions)
```
GET /api/vi/improve/sub-topics/234235/questions
Authorization: Bearer <jwt-token>
```

**Example Response:**
```
{
  "questions": [
    {
      "id": "1234",
      "image_url": "https://some-signed-url-from-a-bucket-or-blob-storage.com/1234.png",
      "previous_answer": {
        "selected": "B",
        "is_correct": false,
        "timestamp": "2025-05-15T10:30:00Z"
      },
      "explanations": {
        "A": "Some explanation here...",
        "B": "Some explanation here...",
        "C": "Some explanation here...",
        "D": "Some explanation here..."
      },
      "correct_answer": "C"
    },
    {
      "id": "1235",
      "image_url": "https://some-signed-url-from-a-bucket-or-blob-storage.com/1235.png",
      "previous_answer": {
        "selected": "A",
        "is_correct": false,
        "timestamp": "2025-05-15T10:30:00Z"
      },
      "explanations": {
        "A": "Some explanation here...",
        "B": "Some explanation here...",
        "C": "Some explanation here...",
        "D": "Some explanation here..."
      },
      "correct_answer": "B"
    },
    {
      "id": "1236",
      "image_url": "https://some-signed-url-from-a-bucket-or-blob-storage.com/1236.png",
      "previous_answer": {
        "selected": "D",
        "is_correct": false,
        "timestamp": "2025-05-15T10:30:00Z"
      },
      "explanations": {
        "A": "Some explanation here...",
        "B": "Some explanation here...",
        "C": "Some explanation here...",
        "D": "Some explanation here...",
      },
      "correct_answer": "C"
    },
    {
      "id": "1237",
      "image_url": "https://some-signed-url-from-a-bucket-or-blob-storage.com/1237.png",
      "previous_answer": {
        "selected": "B",
        "is_correct": false,
        "timestamp": "2025-05-15T10:30:00Z"
      },
      "explanations": {
        "A": "Some explanation here...",
        "B": "Some explanation here...",
        "C": "Some explanation here...",
        "D": "Some explanation here...",
      },
      "correct_answer": "D"
    }
  ],
}
```

### Submit New Answer
Submits a new answer for a question the student is re-attempting.

Endpoint: `/api/v1/improve/questions/{question_id}/answer`

Example Request: 
```
PUT /api/improve/questions/1237/answer
Authorization: Bearer <jwt-token>
Content-Type: application/json

{
  "answer": "D"
}
```

Example Response:
I would just return a 204.

### Assumptions
1. **Authentication**: JWT bearer tokens are used for authentication. The student identity is extracted from the JWT token, this is why I did not send the student Id as part of thre request.
2. **Question Delivery**: I did not create an endpoint to get next question, because I will return all questions for a sub-topic in a single API call, this should allow the frontend to manage navigation and preload images for better UX (since all of the questions are images).
3. **Image Storage**: Questions I assumed will be stored in a file storage bucket (S3 or Azure Blob) and will be passed via signed url (will explain this in another point).
4. **Misconception Tracking**: Keep timestamp of when the student last answered the question. The frontend can choose to show this to the user or not.
5. **Polling or Webhooks**: When a student answers a question, I do not return anything, one of the reasons is that the list of questions returned should contain all of the information (which question is correct or not). However, how they wish to receive the update will be a design choice, they can either poll our list of question endpoint to get an updated list or if they have a need to get close to real time updates, we can implement a webhook.
6. **Pre-signed Url**: Main reason for this choice is for security and it also protects students from sharing the questions amongst one another (potentially cheating, althought nowadays they can just take a picture of it on their phone...). If security is not a concern, public urls are better as they can be cached for longer, pre-signed urls tend to have an expiry time.
7. **Paging:** I have not included paging, primarily because I would assume the students wouldn't get that many questions wrong, if this was a list of all questions then maybe its a different story. We wouldn't want to return all questions. In that case, we either implement paging to give them questions in chunk (which would still resolve the preloading of images) or an endpoint to get the next question (slower in my opinion).

There are probably more stuff to consider if I were to spend more time on it, but I think that's good enough.
