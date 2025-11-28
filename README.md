# NotebookLM Flashcard Downloader


Hello, below you will find my script to extract NoteBookLM's flashcards. This code will print the flashcards into the browser's console. You will have to copy & paste it into a text document. Please follow the instructions!   

Happy studying!
## !! As of November 2025, NotebookLM now includes a download button for flash cards in a .csv format !!

## Instructions
1. Press the ">" next to view code below the instructions
2. Inside the code box, you will see what looks like 2 squares on the top right, that is the copy button.. click that.
3. Go to your NotbookLM notebook, and open up the flash cards
4. Right click your mouse, and press "inspect"
5. Go to the console tab, press "Control" + "V" on windows, or "Command" + "V" on mac to paste the code
6. Press the "Enter" key, and let the script do it's thing. The cards will shuffle.
7. Once you see "✅ EXTRACTION SUCCESSFUL! ... cards extracted.", find the very big text box that will hold all of the flashcard data. At the very end of that message you will see a "copy" button, _CLICK IT_ :)
8. Paste it in a text document to save it.

Still confused? Watch this youtube video! 
https://youtu.be/vkjNwI6udoE

## Understanding the exported text
Below is an example of an exported flash card. To the LEFT of the "-" is the front of the flash card, to the RIGHT of the "-" is the rear / hidden part of the flash card.    
You can paste this text document into your flashcard software like Quizlet
```
The muscular middle layer of the uterine wall, whose contractions help expel the fetus, is the _____. - myometrium
```
<details>
  <summary>View Quizlet Example</summary>

<img width="1693" height="645" alt="image" src="https://github.com/user-attachments/assets/1669e04e-5467-4992-9458-5bade0a98452" />

</details>

## Download / Code

<details>
  <summary>View Code</summary>

  ```js
/**
 * NotebookLM Flashcard Extractor (Console Output Only)
 *
 * This version extracts the data and PRINTS IT to the console.
 * Made by ItsKarev - 10/09/2025
 */

function sleep(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
}

async function extractFlashcards() {
    console.log("Starting flashcard extraction. Please do not interact with the page...");

    const cards = new Set();
    
    // --- CONFIRMED SELECTORS ---
    const CARD_CLICK_AREA_SELECTOR = '#flashcard';
    const QUESTION_SELECTOR = '.card-front-content > .card-text';
    const ANSWER_SELECTOR = '.card-back-content > .card-text';
    const NEXT_CARD_BUTTON_SELECTOR = 'button[aria-label="Next card"]'; 
    const CARD_COUNT_ELEMENTS = document.querySelectorAll('span'); 

    let totalCards = 0;
    
    // Determine the total number of cards
    for (const el of CARD_COUNT_ELEMENTS) {
        if (el.textContent.includes('/')) {
            const parts = el.textContent.split('/');
            if (parts.length === 2) {
                const count = parseInt(parts[1].trim(), 10);
                if (count > 0 && count < 1000) { 
                    totalCards = count;
                    break;
                }
            }
        }
    }

    if (totalCards === 0 || isNaN(totalCards)) {
        console.warn("Could not determine total number of cards. Running for a default of 200 cards.");
        totalCards = 200; 
    }

    console.log(`Expecting to extract ${totalCards} cards.`);
    
    try {
        const cardFlipper = document.querySelector(CARD_CLICK_AREA_SELECTOR);
        if (!cardFlipper) {
            console.error("The main flashcard element (#flashcard) was not found. Stopping.");
            return;
        }

        // Loop through all cards
        for (let i = 0; i < totalCards; i++) {
            
            await sleep(150); 

            // Flip the card to reveal the answer
            cardFlipper.click();
            await sleep(100); 
            
            // Extract Question and Answer
            const questionElement = document.querySelector(QUESTION_SELECTOR);
            const answerElement = document.querySelector(ANSWER_SELECTOR);
            
            const question = questionElement ? questionElement.textContent.trim().replace(/\s+/g, ' ') : '';
            const answer = answerElement ? answerElement.textContent.trim().replace(/\s+/g, ' ') : '';
            
            if (question && answer) {
                const cardEntry = `${question} | ${answer}`;
                cards.add(cardEntry); 
                // Log progress without flooding the console
                if (i % 20 === 0 || i === totalCards - 1) {
                    console.log(`...Progress: Extracted ${cards.size} cards.`);
                }
            } else {
                console.warn(`Could not extract Q/A for card ${i + 1}. Skipping.`);
            }

            // Flip the card back to the front before moving to the next card
            cardFlipper.click();
            await sleep(50); 

            // Click the next card button
            const nextButton = document.querySelector(NEXT_CARD_BUTTON_SELECTOR);
            if (nextButton && i < totalCards - 1) {
                nextButton.click();
            } else if (i >= totalCards - 1) {
                break;
            } else {
                 console.error("Next card button not found or error in loop logic. Exiting.");
                 break;
            }
        }

    } catch (e) {
        console.error("An unexpected error occurred during extraction:", e);
    }
    
    const finalCardsArray = Array.from(cards);
    console.log(`\n✅ EXTRACTION SUCCESSFUL! ${finalCardsArray.length} cards extracted.`);
    console.log("------------------------------------------------------------------");
    console.log("--- COPY THE TEXT BELOW AND PASTE INTO A FILE ---");
    console.log("------------------------------------------------------------------");
    console.log(finalCardsArray.join('\n'));
    console.log("------------------------------------------------------------------");
}
extractFlashcards();
  ```
</details>

## Troubleshooting 
If you have an error message about couldn't find tag flashcard, close out of NotebookLM and reopen it, it might take a few attempts of closing & reopening the tab!
