# 🎧 Model Card: Music Recommender Simulation

## 1. Model Name  

**TasteMatch 1.0**

---

## 2. Intended Use  

TasteMatch takes a listener's stated taste (favorite genre, favorite mood, target energy, and whether they like acoustic sound) and returns a ranked list of the best-matching songs from a small catalog, along with a plain-language reason for each pick. It assumes the user can describe their taste as one favorite genre, one favorite mood, and a single energy level. The system will then provide the top picks from the song data that best match the user's described taste.

---

## 3. How the Model Works  

Every song has a genre, a mood, and a few numeric traits: energy, tempo, valence, danceability, and acousticness. The user's profile has a favorite genre, a favorite mood, a target energy level, and a yes/no acoustic preference.

To score a song, the model checks four things and gives credit for each: does the genre match (worth 35% of the score), does the mood match (25%), how close is the song's energy to the target energy (30%, with partial credit the closer it gets), and does the song's acoustic level match what the user said they like (10%). Those four pieces are added up into one final score between 0 and 1, and the songs with the highest scores are recommended.

Starting from the empty starter code, I implemented the CSV loading, wrote the four-part weighted scoring formula above, added logic to rank all songs and keep only the top matches, and cleaned up the terminal output so it shows the user's profile, a numbered list of recommendations, and the specific reasons behind each score.

---

## 4. Data  

The catalog has 20 songs. It started with 10 songs across 7 genres (pop, lofi, rock, ambient, jazz, synthwave, indie pop) and 6 moods (happy, chill, intense, relaxed, moody, focused). I added 10 more songs to cover genres and moods that were missing, including hip-hop, classical, country, r&b, folk, metal, reggae, EDM, blues, and k-pop, plus moods like energetic, peaceful, nostalgic, romantic, melancholic, angry, playful, triumphant, sad, and dreamy.

Even with that addition, the dataset is missing a lot of what actually shapes musical taste: no lyrics or language, no artist popularity or era, no actual listening history, and most genres still only have one song each, so the model can't show much variety within a genre.

---

## 5. Strengths  

The model gives sensible results for tastes that are well represented in the catalog. For example, a "lofi, chill" profile pulled back two very close, high-scoring matches (Midnight Coding and Library Rain) followed by reasonably ranked partial matches — the ordering matched what I'd expect a person to pick by hand. The energy-closeness scoring also behaves the way it should: songs near the target energy score higher and songs far from it score lower, instead of a hard match/no-match cutoff. In the simple pop/happy baseline test, the top result matched the user's profile on every single feature, which is exactly the intended behavior.

---

## 6. Limitations and Bias 

The model only looks at tags and numbers — it has no idea about lyrics, artist popularity, or what the user has actually listened to before. Genres with only one song in the catalog (like k-pop or classical) will always get weak recommendations, because there's nothing else to choose from even if that one song is a poor match on mood or energy. Genre and mood matching is all-or-nothing text matching, so a genre typed slightly differently, or one that isn't in the catalog at all (like "electronic"), gets zero credit for that entire 35% of the score — and closely related genres like "pop" and "indie pop" are treated as completely unrelated. When a user's preferences contradict each other (for example, wanting a genre that's never acoustic but also saying they like acoustic songs), the model just quietly drops the acoustic score to zero instead of flagging the conflict. Missing profile fields are also scored as zero rather than being skipped, so an incomplete profile is capped at a lower score even if everything it does specify is a perfect match.

---

## 7. Evaluation  

I tested five profiles: a well-supported baseline (pop, happy), a strong double match (lofi, chill), a genre that doesn't exist in the catalog (electronic), an internally contradictory profile (metal, angry, but also wanting acoustic), a profile with missing fields (only genre and mood given), and a profile for a genre with just one song in the catalog (k-pop). For each, I checked whether the top recommendation made intuitive sense, whether the listed reasons matched the score, and whether anything broke or produced a nonsense result.

The most surprising result was the "electronic" test — even though genre matching completely failed, the top result still scored a fairly confident-looking 0.63 from mood, energy, and acoustic matching alone. That could mislead a user into thinking it's a strong overall match when the genre isn't right at all. I didn't run any numeric metrics, just manual comparisons of the five experiment outputs recorded in the README.

---

## 8. Future Work  

Next steps would include letting genre and mood scoring give partial credit for closely related tags instead of exact-match-only, and folding valence in as a real scored feature instead of just an unused tie-breaker. I'd also want the explanations to mention what didn't match, not just what did, so users understand a low score's cause. Adding more variety to the top-k results (instead of several very similar songs) and supporting more than one favorite genre or mood would make the model feel more realistic.

---

## 9. Personal Reflection  

During this experience, I learned the importance of the data that you pick from the data set to use in the recommendation algorithm. The testing showed me how important some featurest (like genre and energy) were to a more accurate recommendation. Meanwhile, there were other features (like valence) that did not contribute as much are were not worth integrating into this simple recommendation system. This changed the way I thought about music recommendation because it made me think about how much data can be extracted and used from a song. Real-life recommendation systems must use hundreds of data values to be as accurate as possible with such a large song database.

My biggest learning moment during this project came from testing the music recommender using different user profiles. It showed me weaknesses in the algorithm I picked, and how the weights/features I chose affected the returned songs. AI tools were a big help in giving me feedback on the algorithm, as well as implementing the algorithm in an efficient way. I was surprised that the simple 4 values I chose would give an effective recommendation most of the time. In the future, I would've extended this project by using more features and incorporating way more songs in the dataset.