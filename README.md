# 🖼️ Image Caption Generator

> An AI-powered web application that automatically generates smart captions for any image — with tone selection, multi-language support, place exploration, and more.

---

## 📌 Table of Contents

- [About the Project](#about-the-project)
- [Live Features](#live-features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [How It Works](#how-it-works)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [Database Design](#database-design)
- [All Routes](#all-routes)
- [Screenshots](#screenshots)
- [Future Improvements](#future-improvements)

---

## 📖 About the Project

**Image Caption Generator** is a full-stack AI web application built with **Python + Flask**. Users can upload any image and instantly receive AI-generated captions powered by the **Salesforce BLIP model** (via Hugging Face Transformers).

The app is designed for:
- Social media users who want ready-made captions
- Developers exploring image-to-text AI
- Anyone who wants to explore places with photos and history

---

## ✨ Live Features

| Feature | Description |
|---|---|
| 🔐 User Authentication | Signup & Login with hashed passwords |
| 🤖 AI Image Captioning | Upload image → get 3–5 AI captions using BLIP model |
| 🎭 Tone Selection | Choose **Professional** or **Fun** (emoji-rich) style |
| 🌐 Multi-Language | Captions in **English**, **Hindi**, or **Gujarati** |
| 📊 Smart Caption Count | Returns more captions for complex images automatically |
| 🕓 Caption History | View, delete, or clear all past captions per user |
| 🗺️ Place Explorer | Enter any place name → get Wikipedia history + Unsplash photos |
| 🔍 Image Search | Search and download HD images from Unsplash |
| #️⃣ Hashtag Generator | Dedicated page to generate relevant hashtags |
| 🛡️ Admin Dashboard | Admins can view all users, all captions, and delete accounts |
| 📂 Per-User History | Admin can see caption history for any individual user |

---

## 🛠️ Tech Stack

### Backend
| Technology | Purpose |
|---|---|
| **Python 3.x** | Core programming language |
| **Flask** | Web framework — routing, sessions, templates |
| **Flask-SQLAlchemy** | ORM for database models and queries |
| **SQLite** | Embedded database (`caption.db`) |
| **Werkzeug** | Password hashing + secure file uploads |

### AI / Machine Learning
| Technology | Purpose |
|---|---|
| **Hugging Face Transformers** | Loads the BLIP image captioning model |
| **Salesforce BLIP** | `blip-image-captioning-base` — AI model for image-to-text |
| **PyTorch (torch)** | Deep learning backend used by BLIP |
| **Pillow (PIL)** | Image loading and preprocessing |

### APIs & Translation
| Technology | Purpose |
|---|---|
| **Unsplash API** | Image search and place photos |
| **Wikipedia API** | Historical/overview text for places |
| **googletrans** | Translates captions to Hindi and Gujarati |

### Frontend
| Technology | Purpose |
|---|---|
| **Jinja2 Templates** | Server-side HTML rendering |
| **HTML + CSS** | Custom UI for all pages (`static/css/style.css`) |

---

## 📁 Project Structure

```
Image_Caption/
│
├── app.py                        # Main Flask app — all routes & logic
├── caption.db                    # SQLite database (auto-created on first run)
├── requirements.txt              # All Python dependencies
│
├── static/
│   ├── css/
│   │   └── style.css             # Custom styling for all pages
│   └── uploads/                  # Stores user-uploaded images
│
└── templates/
    ├── login.html                # Login page
    ├── signup.html               # Registration / Signup page
    ├── index.html                # Main caption generation page
    ├── history.html              # User caption history
    ├── image_to_text.html        # Place explorer (Wikipedia + Unsplash)
    ├── image_search.html         # Unsplash image search
    ├── hashtags.html             # Hashtag generator page
    ├── dashboard.html            # Redirects to home
    ├── admin.html                # Admin panel
    └── user_history.html         # Admin view of specific user's history
```

---

## ⚙️ How It Works

### AI Caption Pipeline

```
User uploads image
        ↓
Flask saves file to static/uploads/
        ↓
Pillow opens image → converts to RGB
        ↓
BlipProcessor tokenizes image → tensors
        ↓
BLIP model generates 7 candidate captions
(num_beams=10, max_length=40)
        ↓
Smart selection: 3–5 captions based on complexity
        ↓
Tone applied: Professional prefix OR Fun emoji templates
        ↓
Language: translate to Hindi / Gujarati if selected
        ↓
Captions saved to database + shown to user
```

### Smart Caption Count Logic

| Image Complexity | Avg Caption Length | Captions Returned |
|---|---|---|
| Complex | > 12 words | 5 captions |
| Medium | > 8 words | 4 captions |
| Simple | ≤ 8 words | 3 captions |

---

## 🚀 Getting Started

### Prerequisites

- Python 3.8 or higher
- pip (Python package manager)
- Internet connection (for first-time BLIP model download ~900 MB)

### 1. Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/Image_Caption.git
cd Image_Caption
```

### 2. Create a Virtual Environment

```bash
python -m venv venv

# Windows
venv\Scripts\activate

# macOS / Linux
source venv/bin/activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

Or install manually:

```bash
pip install flask flask-sqlalchemy werkzeug transformers torch pillow googletrans==4.0.0rc1 requests
```

### 4. Run the Application

```bash
python app.py
```

Open your browser and go to: **http://127.0.0.1:5000**

> ⚠️ On first run, the BLIP AI model (~900 MB) downloads automatically from Hugging Face. This may take a few minutes. The SQLite database is also created automatically.

### 5. Admin Access

Sign up with the username **`shrey`** or **`jainam`** to access the admin panel at `/admin`.

---

## 🔐 Environment Variables

Currently some values are hardcoded in `app.py`. For production or public GitHub, move these to a `.env` file:

```env
SECRET_KEY=your_secret_key_here
UNSPLASH_ACCESS_KEY=your_unsplash_key_here
```

Then update `app.py`:

```python
import os
app.secret_key = os.environ.get("SECRET_KEY", "fallback-dev-key")
UNSPLASH_ACCESS_KEY = os.environ.get("UNSPLASH_ACCESS_KEY")
```

Add `.env` to your `.gitignore` so it is never uploaded to GitHub.

---

## 🗃️ Database Design

### `users` table

| Column | Type | Description |
|---|---|---|
| id | Integer (PK) | Auto-increment unique ID |
| username | String(100) | Unique username |
| email | String(120) | Unique email address |
| password | String(200) | Werkzeug hashed password |

### `image_caption` table

| Column | Type | Description |
|---|---|---|
| id | Integer (PK) | Auto-increment unique ID |
| user_id | Integer (FK) | Links to `users.id` |
| image_path | String(200) | Saved path: `uploads/filename.jpg` |
| tone | String(50) | `Professional` or `Fun` |
| language | String(50) | `English`, `Hindi`, or `Gujarati` |
| captions | Text | All captions joined by ` | ` |

---

## 🌐 All Routes

| Route | Method | Description |
|---|---|---|
| `/` | GET, POST | Login page |
| `/signup` | GET, POST | Registration page |
| `/home` | GET, POST | Main caption generation page |
| `/history` | GET | View user's caption history |
| `/clear-history` | GET | Delete all captions for current user |
| `/delete-history/<id>` | GET | Delete a single caption record |
| `/image-to-text` | GET, POST | Place explorer (Wikipedia + Unsplash) |
| `/image-search` | GET, POST | Search images via Unsplash |
| `/download-image` | GET | Download an Unsplash image |
| `/hashtags` | GET | Hashtag generator page |
| `/logout` | GET | Clear session and redirect to login |
| `/admin` | GET | Admin panel (restricted) |
| `/admin/delete-user/<id>` | GET | Admin: delete a user account |
| `/admin/user-history/<id>` | GET | Admin: view a specific user's history |

---

## 📸 Screenshots

> Add screenshots of your app here after running it locally.

```
screenshots/
├── login.png
├── home_caption.png
├── history.png
├── place_explorer.png
└── admin_panel.png
```

---

## 🔮 Future Improvements

- [ ] Move API keys and secret key to `.env` file
- [ ] Add database role column for admin instead of hardcoded names
- [ ] Add pagination for history and admin pages
- [ ] Copy-to-clipboard button for captions
- [ ] Support more languages (Tamil, Marathi, Bengali)
- [ ] Drag-and-drop image upload
- [ ] Deploy to Render / Railway with PostgreSQL
- [ ] Add CSRF protection to all forms
- [ ] Write unit tests

---

## 👨‍💻 Author

Made with ❤️ using Flask + BLIP AI

---

## 📄 License

This project is open source. Feel free to use, modify, and share.

<img width="1919" height="969" alt="Screenshot 2026-04-27 211227" src="https://github.com/user-attachments/assets/2f339599-7013-4b03-94eb-85cf2b6b186d" />
<img width="1915" height="965" alt="Screenshot 2026-04-27 211201" src="https://github.com/user-attachments/assets/71912bde-5c7f-4260-8e75-778b2d74837f" />
<img width="1919" height="976" alt="Screenshot 2026-04-27 211105" src="https://github.com/user-attachments/assets/25e640df-9244-4b49-bd9b-490dde59e013" />
<img width="1915" height="970" alt="Screenshot 2026-04-27 211048" src="https://github.com/user-attachments/assets/bae1310f-d040-4d9a-9e12-f56964cf879d" />
<img width="1908" height="963" alt="Screenshot 2026-04-27 211006" src="https://github.com/user-attachments/assets/7df198b5-169f-46d1-b87b-467f182ffa29" />
<img width="1904" height="960" alt="Screenshot 2026-04-27 210950" src="https://github.com/user-attachments/assets/c7420209-ec55-42ff-ac77-b61ce5a06a69" />
<img width="1915" height="956" alt="Screenshot 2026-04-27 210905" src="https://github.com/user-attachments/assets/980b0e8d-b376-464b-826a-6fb7a60ed6b7" />
<img width="1919" height="967" alt="Screenshot 2026-04-27 210721" src="https://github.com/user-attachments/assets/86dcd671-f89f-4d22-aa2d-9210145ffb8f" />
<img width="1909" height="970" alt="Screenshot 2026-04-27 210628" src="https://github.com/user-attachments/assets/c135ba0a-872f-46d3-b4eb-6516527ac6a0" />
<img width="1910" height="980" alt="Screenshot 2026-04-27 210613" src="https://github.com/user-attachments/assets/e011da35-d151-4f90-be33-4d5356d1a87a" />
<img width="1909" height="963" alt="Screenshot 2026-04-27 210550" src="https://github.com/user-attachments/assets/435fec2c-e129-478e-888e-f526cffdfca2" />
<img width="1917" height="972" alt="Screenshot 2026-04-27 210530" src="https://github.com/user-attachments/assets/724f6837-45e3-4462-a05b-b23673c2e2a6" />
<img width="1919" height="969" alt="Screenshot 2026-04-27 210500" src="https://github.com/user-attachments/assets/73f108a1-dc92-46cb-87a3-37c40acec45e" />
<img width="1913" height="966" alt="Screenshot 2026-04-27 210401" src="https://github.com/user-attachments/assets/f19b95d0-8fd2-4214-a5e8-f64d4afde89b" />
<img width="1919" height="972" alt="Screenshot 2026-04-27 210331" src="https://github.com/user-attachments/assets/f85dc751-4d9c-4c88-87a6-442382885b7f" />


