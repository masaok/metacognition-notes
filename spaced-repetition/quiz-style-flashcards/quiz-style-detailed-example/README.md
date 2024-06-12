- [Spaced-Repetition Algorithm with R, E, and I](#spaced-repetition-algorithm-with-r-e-and-i)
- [Detailed Example](#detailed-example)
- [Summary](#summary)

Let's break down the algorithm for displaying flashcards based on the following parameters: repetitions (R), ease factor (E), and interval (I). This explanation will cover how these parameters are used to determine the scheduling of card reviews in a spaced-repetition system.

### Spaced-Repetition Algorithm with R, E, and I

1. **Initialization**:

   - **Ease Factor (E)**: Initialize to 2.5. This factor adjusts based on user performance and influences the interval between reviews.
   - **Interval (I)**: Initialize to 1 day. This is the time until the card is next reviewed.
   - **Repetitions (R)**: Initialize to 0. This counts the number of times the card has been reviewed correctly in succession.

2. **Review Session**:

   - During each review session, the user is presented with a card and asked to recall the information.
   - The user's response is evaluated as correct or incorrect.

3. **Determine the Next Interval**:

   - Based on whether the user's response is correct or incorrect, the algorithm updates the parameters R, E, and I:
     - **If the answer is incorrect**:
       - Set repetitions (R) to 0.
       - Set interval (I) to 1 day.
       - Decrease the ease factor (E) by 0.2 (ensuring E does not fall below 1.3).
     - **If the answer is correct**:
       - Increment repetitions (R) by 1.
       - Increase the ease factor (E) by 0.1.
       - Determine the next interval (I):
         - If repetitions (R) = 1, set interval (I) to 1 day.
         - If repetitions (R) = 2, set interval (I) to 6 days.
         - If repetitions (R) > 2, set interval (I) to the previous interval multiplied by the ease factor (E).

4. **Update the Next Review Date**:

   - Calculate the next review date by adding the interval (I) to the current date.
   - Save the updated card parameters (R, E, I, and next review date) back to the database.

5. **Fetch and Display Due Cards**:
   - Fetch cards from the database where the next review date is due (i.e., less than or equal to the current date).
   - Sort the cards by their next review date, so the most overdue cards are reviewed first.
   - Display the due cards to the user for review.

### Detailed Example

1. **Initialization**:

   - Card is created with:
     - Ease factor (E) = 2.5
     - Interval (I) = 1 day
     - Repetitions (R) = 0

2. **First Review**:

   - User reviews the card and answers correctly.
   - Update:
     - Repetitions (R) = 1
     - Interval (I) = 1 day
     - Ease factor (E) = 2.5 + 0.1 = 2.6
     - Next review date = current date + 1 day

3. **Second Review**:

   - User reviews the card and answers correctly.
   - Update:
     - Repetitions (R) = 2
     - Interval (I) = 6 days
     - Ease factor (E) = 2.6 + 0.1 = 2.7
     - Next review date = current date + 6 days

4. **Third Review**:

   - User reviews the card and answers correctly.
   - Update:
     - Repetitions (R) = 3
     - Interval (I) = 6 days \* 2.7 ≈ 16 days
     - Ease factor (E) = 2.7 + 0.1 = 2.8
     - Next review date = current date + 16 days

5. **Fourth Review**:
   - User reviews the card and answers incorrectly.
   - Update:
     - Repetitions (R) = 0
     - Interval (I) = 1 day
     - Ease factor (E) = 2.8 - 0.2 = 2.6
     - Next review date = current date + 1 day

### Summary

This algorithm dynamically adjusts the review schedule for each card based on user performance, using the parameters R (repetitions), E (ease factor), and I (interval). Cards answered correctly have their intervals extended based on the ease factor, promoting efficient long-term retention. Incorrect answers reset the repetition count and shorten the interval, ensuring frequent review of difficult cards.
