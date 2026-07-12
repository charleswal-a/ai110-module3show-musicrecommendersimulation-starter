# 🎵 Music Recommender Simulation

## Project Summary

In this project you will build and explain a small music recommender system.

Your goal is to:

- Represent songs and a user "taste profile" as data
- Design a scoring rule that turns that data into recommendations
- Evaluate what your system gets right and wrong
- Reflect on how this mirrors real world AI recommenders

This version is a simple content-based music recommender. Each song in the catalog (`data/songs.csv`) is described by a set of audio and tag features — genre, mood, energy, tempo, valence, danceability, and acousticness. A `UserProfile` captures a listener's taste as a favorite genre, favorite mood, target energy level, and a preference for acoustic sound. The `Recommender` scores every song by comparing it to the user's profile, then returns the top-k highest-scoring songs along with an explanation of why each one was picked.

---

## How The System Works

- The values that will be used from the Song data include genre, mood, energy, and acousticness as primary signals, with valence held as a tie-breaker.
- The information that UserProfile stores includes favorite_genre, favorite_mood, target_energy, and likes_acoustics.
- The Recommender will calculate a score for each song by first comparing song to the UserProfile in four different categories (genre, mood, energy, and acousticness). Each category will be assigned a weight and the final score will be the sum of the four categories.
- The song recommended song will be determined by first scoring every song in the catalog against the UserProfile. The songs are then sorted descending by score and the top k songs are returned. Valence will be used as a tie breaker if two scores are really close to each other.

### Data Flow

```
data/songs.csv
      │  load_songs()
      ▼
 List[Song]                    UserProfile
      │                             │
      └───────────────┬────────────┘
                       ▼
          Recommender.recommend(user, k)
                       │
                       │  for each Song:
                       ▼
              score_song(user, song)
                       │
                       ▼
        (score, [reasons]) per song ── weighted sum of:
                                        genre match, mood match,
                                        energy closeness, acousticness match
                                        (valence breaks near-ties)
                       │
                       ▼
        sort all songs by score, descending
                       │
                       ▼
              take top k songs
                       │
                       ▼
     explain_recommendation(user, song) per result
                       │
                       ▼
     Output: top-k [(Song, score, explanation), ...]
```

---

## Getting Started

### Setup

1. Create a virtual environment (optional but recommended):

   ```bash
   python -m venv .venv
   source .venv/bin/activate      # Mac or Linux
   .venv\Scripts\activate         # Windows

2. Install dependencies

```bash
pip install -r requirements.txt
```

3. Run the app:

```bash
python -m src.main
```

### Running Tests

Run the starter tests with:

```bash
pytest
```

You can add more tests in `tests/test_recommender.py`.

---

## Sample Recommendation Output

Paste a sample of your recommender's output here as a text block so a reader can see what it produces:

```
# e.g.:
# User profile: genre=indie, mood=chill, energy=low
# Recommendations:
#   1. ...
#   2. ...
#   3. ...
```

**Screenshot or video** *(optional)*: <!-- Insert a screenshot or demo video link here -->

---

## Experiments You Tried

Use this section to document the experiments you ran. For example:

- What happened when you changed the weight on genre from 2.0 to 0.5
- What happened when you added tempo or valence to the score
- How did your system behave for different types of users

---

## Limitations and Risks

Summarize some limitations of your recommender.

Examples:

- It only works on a tiny catalog
- It does not understand lyrics or language
- It might over favor one genre or mood

You will go deeper on this in your model card.

---

## Reflection

Read and complete `model_card.md`:

[**Model Card**](model_card.md)

Write 1 to 2 paragraphs here about what you learned:

- about how recommenders turn data into predictions
- about where bias or unfairness could show up in systems like this



