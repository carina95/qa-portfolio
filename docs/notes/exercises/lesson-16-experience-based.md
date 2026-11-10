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
| 3 | Leading/trailing spaces | `" Ana Pop"` and `"Ana Pop "` (tested separately) | Yes | Saved with the spaces kept | Spaces are not trimmed at either end. Leading: saved as `" Ana Pop"`. Trailing: Payload and Preview both `"Ana Pop "`. Same root cause as item 2: the Name field never trims whitespace. |
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
| Add to Basket | double-clicked quickly | 2 — POST BasketItems (200) created line id 11 with qty 1; PUT BasketItems/11 (200) raised it to qty 2 | 1 basket line, quantity 2 | Yes — both clicks counted on one line; the user sees the quantity and can lower it. Not a bug. |
| Add New Address → Submit | double-clicked quickly | 1 — POST Addresss (201) | 1 address | Yes — only one request was sent, so the front end blocks the second click. Server-side protection not tested. Not a bug. |
| Customer Feedback → Submit | double-clicked quickly | 1 — POST Feedbacks (201) | 1 feedback; success message shown | Yes — the form clears after the first submit, so the second click hits an empty form and shows "comment / CAPTCHA required" in red; nothing is sent. Front end blocks it. Not a bug. |

Conclusion: None of the three targets let a double submission create duplicates.
Add to Basket counts both clicks on one line (quantity 2), which the user can see and correct.
The address and feedback forms only ever send one request, so the browser blocks
the second click; whether the server would also reject a duplicate is untested.

Note: the saved address showed fullName "a Double Test" and mobileNum 70000000. Not checked whether that's what I typed (Payload) — possible typing slip.

Compare BUG-003: placing an order DOES allow a second submission (via Back), which
creates an extra order. So the double-submission problem is specific to checkout,
not app-wide.

## Exercise 3 — My own fault list

### (a) Guesses that found something in this course

**Areas to choose from:**
- Empty and unusual input
- Characters
- Repeating an action
- Sequence and state
- Data that's almost the same
- Locale
- Data the app adds or changes  

**My findings, and the guess behind each:**

| # | Finding | The guess that found it | Area |
|---|---|---|---|
| 1 | BUG-001 — password hint says 40, counter says 20 | Typed a very long password | Empty and unusual input |
| 2 | BUG-002 — server accepts a checkout with steps skipped | Skipped the address, delivery and payment steps and placed the order anyway | Sequence and state |
| 3 | BUG-003 — a second, empty order is created | Placed the order, pressed Back, and placed it again | Repeating an action |
| 4 | BUG-004 — address Name accepts spaces only | Typed only three spaces in the Name field | Empty and unusual input |
| 5 | BUG-005 — feedback shows the author's email | Compared what I typed (Double Comment) with the Payload; the app had added my email to the comment | Data the app adds or changes.|
| 6 | Fault masking — reused email hid the real result | Registered several accounts with the same email (test@test.com); the duplicate-email error hid how the security answer field behaved | Data that's almost the same |

**Check:**

| Area | How many of my findings came from it |
|---|---|
| Empty and unusual input | 2 |
| Characters | 0 |
| Repeating an action | 1 |
| Sequence and state | 1 |
| Data that's almost the same | 1 |
| Data the app adds or changes | 1 |
| Locale | 0 |
| **Total** | **6** |

Which area found the most? Empty and unusual input (2 findings).
Which area has found nothing yet? Characters and Locale, but for different reasons:
Characters was tested (Exercise 1, items 5–8) and everything passed; Locale has never been tested.
What does that tell me about where to look next? Locale. It's the only area I haven't tested at all,
so it's the biggest unknown. Characters is lower priority: I tested it and it held up.

### (b) Five new guesses for Romanian/EU users

| # | Guess | Where to try it in Juice Shop | Expected (correct) behaviour | Oracle |
|---|---|---|---|---|
| 1 | Romanian mobile number with a leading 0: `0712345678` | Address form → Mobile Number | The number is saved exactly as typed, including the leading 0 | User expectations (Romanian mobiles start with 07) |
| 2 | City with diacritics, e.g. `Brașov`, `Iași` | Address form → City | The city name is saved and shown exactly as typed, with diacritics | World (Romanian city names are spelled with diacritics); User expectations |
| 3 | Romanian 6-digit postal code, e.g. `700001` (Iași) | Address form → ZIP Code | The 6-digit code is accepted and saved exactly as typed | World (Romanian postal codes have 6 digits) |
| 4 | Country written in Romanian: `România` | Address form → Country | Accepted, saved and shown exactly as typed, with the diacritic | World (the country's own name is România) |
| 5 | International format with +: `+40712345678` | Address form → Mobile Number | Accepted and saved as typed, OR rejected with a clear message saying which format is required. Not silently changed. | World (+40 is Romania's international prefix); User expectations |

Prediction: rows 1 and 5 are the most likely to fail. In Exercise 2 the server returned
`mobileNum: 70000000` without quotes, so it stores the phone as a number. A number can't
keep a leading 0 or a +.

