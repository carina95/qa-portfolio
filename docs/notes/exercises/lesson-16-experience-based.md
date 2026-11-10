# Lesson 16 — Experience-based testing (practice)

**Account used:** test1011@test.com (fresh account) password: Test1234

## Exercise 1 — Text-field checklist on the address "Name" field

Field: **Name** on Add New Address (Basket → Checkout → Add New Address).
For each item: type the value in Name, fill the other fields normally, click Submit,
then look at the saved address in the address list.

| # | Checklist item | Value I typed | Saved? | Displayed back correctly? | Notes |
|---|---|---|---|---|---|
| 1 | Empty | (nothing) | No — Submit stays greyed out | — | Red message under Name after clicking in and out of the field: "Please provide a name.". Correct behaviour. |
| 2 | Only spaces | (3 spaces) | Yes — Submit became clickable, address saved | No — name shows as empty in the address list | **Bug candidate.** Empty is blocked but spaces-only is accepted. Network: POST Addresss → **201 Created**; response `fullName: " "`, `id: 7`. |
| 3 | Leading/trailing spaces | `Ana Pop` and `Ana Pop ` (tested separately) | Yes | Saved with the spaces kept | Spaces are not trimmed at either end. Leading: saved as `Ana Pop`. Trailing: Payload and Preview both `Ana Pop `. Same root cause as item 2: the Name field never trims whitespace. |
| 4 | Very long | ~100-character name | Yes | Yes, shown in full | No limit stated; saved and displayed correctly. Pass. |
| 5 | Apostrophe | `O'Brien` | Yes | Yes, shown as `O'Brien` | Pass. |
| 6 | Romanian diacritics | `Ștefan Țăran` | Yes | Yes, Ș, Ț and ă all shown correctly | Pass. |
| 7 | Emoji | `Ana 😀` | Yes | Yes, emoji shown correctly | Pass. |
| 8 | HTML-like text | `<b>Ana</b>` | Yes | Yes, shown as text with the tags visible | Input is treated as text, not run as HTML. Pass. |
| 9 | Numbers only | `12345` | Yes | Yes | No rule says a name can't be digits, so not a bug. At most a question for the product owner. |

Anything wrong? 
- Yes — items 2 and 3 have the same cause: the Name field never trims spaces.
- Spaces-only names are accepted and saved (POST 201), and real names keep stray spaces at both ends.
- Everything else passed (empty is blocked; long, apostrophe, diacritics, emoji, HTML-like text and digits all handled correctly).

## Exercise 2 — Fault attack: double submission

| Target | What I did | Requests sent (Network) | Things created | Correct? |
|---|---|---|---|---|
| Add to Basket | double-clicked quickly | | | |
| Add New Address → Submit | double-clicked quickly | | | |
| Customer Feedback → Submit | double-clicked quickly | | | |

Conclusion: ___

## Exercise 3 — My own fault list

**(a) Guesses that found something in this course** (from your bug reports and notes):
-
-

**(b) Five new guesses for Romanian/EU users**, each with where in Juice Shop to try it:

| # | Guess | Where to try it in Juice Shop | Expected (correct) behaviour |
|---|---|---|---|
| 1 | | | |
| 2 | | | |
| 3 | | | |
| 4 | | | |
| 5 | | | |