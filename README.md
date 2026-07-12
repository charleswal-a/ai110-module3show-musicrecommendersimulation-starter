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
- The song recommended song will be determined by first scoring every song in the catalog against the UserProfile. The songs are then sorted descending by score and the top k songs are returned.

### Data Flow

```
data/songs.csv
      │  load_songs()
      ▼
 List[Song]                   UserProfile
      │                            │
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

## Algorithm Recipe

Components: 
- Genre match: Score of 1.0 if genres match and 0.0 otherwise.
- Mood match: Score of 1.0 if moods match and 0.0 otherwise.
- Energy closeness: Compute 1 - abs(song.energy - user.target_energy), so a song exactly at the user's target energy scores 1.0
- Acousticness match: Convert acoutsticness to a boolean based on value and compare to user's likes_acoutsticness

Combination: Multiply each component by its weight and sum to final score between 0 and 1.
- Weights: genre 0.35, energy 0.30, mood 0.25, acousticness 0.10

Selection: Rank all songs by score descending and return the top k results.

Possible biases:
- Genre Matching: Genres like "indie pop" and "pop" are treated as completely unrelated even though they are adjacent.
- Mood Matching: The mood data entry can be subjective as some users could interpret songs as having different moods than they are listed as.

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
User profile: genre=pop, mood=happy, energy=0.8, likes_acoustic=False

Top Recommendations
========================================
1. Sunrise City  (Score: 0.99)
   Because: matches your favorite genre (pop), matches your favorite mood (happy), close to your target energy (0.82), matches your acoustic preference

2. Gym Hero  (Score: 0.71)
   Because: matches your favorite genre (pop), close to your target energy (0.93), matches your acoustic preference

3. Rooftop Lights  (Score: 0.64)
   Because: matches your favorite mood (happy), close to your target energy (0.76), matches your acoustic preference

4. Night Drive Loop  (Score: 0.39)
   Because: close to your target energy (0.75), matches your acoustic preference

5. Concrete Pulse  (Score: 0.39)
   Because: close to your target energy (0.85), matches your acoustic preference  
```

**Screenshot or video** *(optional)*: <!-- Insert a screenshot or demo video link here -->

---

## Experiments You Tried

Experiment 1: Lofi and chill profile
```
User profile: genre=lofi, mood=chill, energy=0.4, likes_acoustic=True

Top Recommendations
========================================
1. Midnight Coding  (Score: 0.99)
   Because: matches your favorite genre (lofi), matches your favorite mood (chill), close to your target energy (0.42), matches your acoustic preference

2. Library Rain  (Score: 0.98)
   Because: matches your favorite genre (lofi), matches your favorite mood (chill), close to your target energy (0.35), matches your acoustic preference

3. Focus Flow  (Score: 0.75)
   Because: matches your favorite genre (lofi), close to your target energy (0.40), matches your acoustic preference

4. Spacewalk Thoughts  (Score: 0.61)
   Because: matches your favorite mood (chill), close to your target energy (0.28), matches your acoustic preference

5. Coffee Shop Stories  (Score: 0.39)
   Because: close to your target energy (0.37), matches your acoustic preference
```

Experiment 2: Nonexistent genre
```
User profile: genre=electronic, mood=energetic, energy=0.9, likes_acoustic=False

Top Recommendations
========================================
1. Concrete Pulse  (Score: 0.63)
   Because: matches your favorite mood (energetic), close to your target energy (0.85), matches your acoustic preference

2. Storm Runner  (Score: 0.40)
   Because: close to your target energy (0.91), matches your acoustic preference

3. Gym Hero  (Score: 0.39)
   Because: close to your target energy (0.93), matches your acoustic preference

4. Skyline Surge  (Score: 0.39)
   Because: close to your target energy (0.95), matches your acoustic preference

5. Iron Vein  (Score: 0.38)
   Because: close to your target energy (0.97), matches your acoustic preference
```

Experiment 3: Contradictory profile
```
User profile: genre=metal, mood=angry, energy=0.95, likes_acoustic=True

Top Recommendations
========================================
1. Iron Vein  (Score: 0.89)
   Because: matches your favorite genre (metal), matches your favorite mood (angry), close to your target energy (0.97)

2. Skyline Surge  (Score: 0.30)
   Because: close to your target energy (0.95)

3. Gym Hero  (Score: 0.29)
   Because: close to your target energy (0.93)

4. Storm Runner  (Score: 0.29)
   Because: close to your target energy (0.91)

5. Concrete Pulse  (Score: 0.27)
   Because: close to your target energy (0.85)
```

Experiment 4: Missing keys
```
User profile: genre=pop, mood=happy

Top Recommendations
========================================
1. Sunrise City  (Score: 0.60)
   Because: matches your favorite genre (pop), matches your favorite mood (happy)

2. Gym Hero  (Score: 0.35)
   Because: matches your favorite genre (pop)

3. Rooftop Lights  (Score: 0.25)
   Because: matches your favorite mood (happy)

4. Midnight Coding  (Score: 0.00)
   Because: no strong matches

5. Storm Runner  (Score: 0.00)
   Because: no strong matches
```

Experiment 5: Sparse catalog
```
User profile: genre=k-pop, mood=angry, energy=0.3, likes_acoustic=True

Top Recommendations
========================================
1. Neon Cloud Nine  (Score: 0.53)
   Because: matches your favorite genre (k-pop)

2. Wilted Fields  (Score: 0.40)
   Because: close to your target energy (0.30), matches your acoustic preference

3. Spacewalk Thoughts  (Score: 0.39)
   Because: close to your target energy (0.28), matches your acoustic preference

4. Rainwater Blues  (Score: 0.39)
   Because: close to your target energy (0.33), matches your acoustic preference

5. Library Rain  (Score: 0.39)
   Because: close to your target energy (0.35), matches your acoustic preference
```

---

## Limitations and Risks

The model only looks at tags and numbers. It doesn't know about lyrics, popularity, or past listening habits. Genres with just one song (like k-pop or classical) always get weak matches since there's nothing else to pick from. Genre and mood have to match exactly, so a typo or an unlisted genre gets zero credit, and similar genres like "pop" and "indie pop" count as totally unrelated. If a user's preferences contradict each other, the model just silently drops that part of the score instead of flagging it. Missing profile fields also score as zero, so an incomplete profile can't reach a high score even with a perfect partial match.

---

## Reflection

Read and complete `model_card.md`:

[**Model Card**](model_card.md)

Building this showed me that a recommender is really just a math formula because every "recommendation" is a weighted sum of a few number categories and string comparisons. There's no real understanding of music happening because the system doesn't know what a song sounds like, it just knows whether labels or numbers match. Turning data into a prediction is really turning a handful of features into a single score and sorting, which is a lot simpler than it feels from the outside as a listener.

The experiments also made it clear how easily bias creeps in from decisions that seem small. Whoever picks the feature weights decides what "good taste" means to the system, giving genre the highest weight meant genre mismatches were nearly impossible to overcome. Whoever builds the catalog decides whose taste gets served well, since a genre with one song can never produce a strong recommendation no matter what the user asks for. Because the matching is exact-text rather than exact-meaning, small differences in labeling can zero out a whole category of the score without ever telling the user why.