---
title: "Introduction"
teaching: 20
exercises: 10
questions:
- "What is machine learning?"
- "What is the relationship between machine learning, AI, and statistics?"
- "What is meant by supervised learning?"

objectives:
- "Recognise what is meant by machine learning."
- "Have an appreciation of the difference between supervised and unsupervised learning."

keypoints:
- "Machine learning borrows heavily from fields such as statistics and computer science."
- "In machine learning, models learn rules from data."
- "In supervised learning, the target in our training data is labelled."
- "A.I. has become a synonym for machine learning."
- "A.G.I. is the loftier goal of achieving human-like intelligence."

---

## Rule-based programming

We are all familiar with the idea of applying rules to data to gain insights and make decisions. For example, we learn that the normal body temperature is ~37 °C (~98.5 °F), and that higher or lower temperatures can be cause for concern.

As programmers we understand how to codify these rules. If we were developing software for a hospital to flag patients at risk of deterioration, we might create early-warning rules such as those below:

```python
def has_fever(temp_c):
    if temp_c > 38:
        return True
    else:
        return False
```
