- [Adapting the Spaced-Repetition Algorithm](#adapting-the-spaced-repetition-algorithm)
- [Example TypeScript Implementation](#example-typescript-implementation)
- [Notes:](#notes)

Here's how you can adapt the spaced-repetition algorithm to factor in correct or incorrect answers:

### Adapting the Spaced-Repetition Algorithm

1. **Initialization**:

   - When a card is created, initialize its ease factor (E) to 2.5, interval (I) to 1 day, and repetitions (R) to 0.

2. **Review Session**:

   - When the user reviews the card, they answer a question (front of the card).
   - Record whether the answer was correct or incorrect.

3. **Update Interval and Ease Factor**:

   - Based on the user's answer (correct or incorrect), update the card's ease factor and interval:
     - If the answer is incorrect:
       - Set the repetitions (R) to 0.
       - Set the interval (I) to 1 day.
       - Adjust the ease factor (E) by decreasing it (e.g., by 0.2).
     - If the answer is correct:
       - Increment the repetitions (R) by 1.
       - Adjust the ease factor (E) using the formula:
         \[
         E = E + 0.1
         \]
       - If the repetitions (R) are 1, set the interval (I) to 1 day.
       - If the repetitions (R) are 2, set the interval (I) to 6 days.
       - If the repetitions (R) are greater than 2, set the interval (I) to:
         \[
         I = I * E
         \]
       - Ensure the ease factor (E) is at least 1.3 to prevent it from dropping too low.

4. **Next Review Date**:

   - Calculate the next review date by adding the interval (I) to the current date.

5. **Repeat**:
   - Repeat steps 2-4 for each review session.
   - Over time, the interval between reviews will increase for cards the user answers correctly, and cards answered incorrectly will be reviewed more frequently.

### Example TypeScript Implementation

```typescript
import { getRepository, LessThanOrEqual } from "typeorm";
import { Card } from "./entities/Card";

async function getDueCards(deckId: number): Promise<Card[]> {
  const cardRepository = getRepository(Card);
  const now = new Date();

  return cardRepository.find({
    where: { deck: deckId, dueDate: LessThanOrEqual(now) },
    order: { dueDate: "ASC" },
    take: 10, // Fetch a limited number of due cards
  });
}

async function updateCard(card: Card, correct: boolean): Promise<void> {
  if (correct) {
    card.repetitions += 1;
    card.ease = Math.max(1.3, card.ease + 0.1);

    if (card.repetitions === 1) {
      card.interval = 1;
    } else if (card.repetitions === 2) {
      card.interval = 6;
    } else {
      card.interval = Math.ceil(card.interval * card.ease);
    }
  } else {
    card.repetitions = 0;
    card.interval = 1;
    card.ease = Math.max(1.3, card.ease - 0.2);
  }

  card.dueDate = new Date(Date.now() + card.interval * 24 * 60 * 60 * 1000);
  await getRepository(Card).save(card);
}

async function displayCards(deckId: number): Promise<void> {
  const dueCards = await getDueCards(deckId);

  for (const card of dueCards) {
    // Display card to user (e.g., via console or web interface)
    console.log(`Front: ${card.front}`);

    // Simulate user answering the card (correct or incorrect)
    const correct = promptUserForAnswer(card.back); // Function to check if the user's answer is correct

    await updateCard(card, correct);
  }
}

function promptUserForAnswer(correctAnswer: string): boolean {
  // Placeholder function to simulate user input and check if the answer is correct
  const userAnswer = prompt("Answer the card: ");
  return userAnswer.trim().toLowerCase() === correctAnswer.trim().toLowerCase();
}

// Example usage
(async () => {
  await displayCards(1); // Display cards for deck with ID 1
})();
```

### Notes:

1. **Database Setup**: Ensure your PostgreSQL database is set up and you have a connection established using TypeORM.
2. **Prompt Function**: Replace `promptUserForAnswer()` with actual user input logic.
3. **Error Handling**: Add proper error handling as needed.
4. **Optimizations**: This is a basic implementation. For a production environment, consider additional features like user authentication, better error handling, performance optimizations, and a more robust UI/UX for interacting with the flashcards.

This adaptation ensures that cards the user answers incorrectly are reviewed more frequently, while cards answered correctly have their review intervals extended, optimizing study efficiency and retention.
