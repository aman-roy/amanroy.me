---
title: "Keeping track of IELTS progress through code"
tags: [python, matplotlib]
style:
color:
image: "/assets/images/post_8_ielts_progress/cover.jpg"
description: "A terminal based solution for keeping track of your scores in IELTS practice sessions."
permalink: keeping-track-of-ielts-through-code
---

The very first requirement which you will have if you are planning to go abroad for studies is the IELTS test. It is an English language proficiency test for non-native English language speakers.

The test has four sections: **Listening, Reading, Writing** and **Speaking**. IELTS is scored on a nine-band scale.

I am also planning to give the test anytime soon. The only rule to succeed in
this test is through **practice**. The more practice you do, the higher chance will
be there to get a good band in the test.

### The problem

I am the kind of person who wants to keep track of everything. While practicing
for the test, I wanted to have a record of the marks and have a statistician
view of the data.

Another problem was to know the band I was getting. After the test, you can
check your answers and know your marks. From the marks, you can check the band
from the table provided on the internet. This was very tedious.

There is a thing with the IELTS test. Among the four sections, only the listening and reading section can be checked from the answer sheet because for the other two, i.e. writing and speaking, there is no one answer and the band depends on various factors. So, tracking them was out of the scope of my solution.

### The solution

Whenever any problem comes into my life, my head whirls for a solution through code. Any process with structure can be automated. The code ahead is not something fancy, but it works and this is all that matters.

Before running the script, make sure that you have matplotlib installed.

`pip install matplotlib`

#### code

{%- gist e06d5b85d5faeff0801d6622911ed678 %}

#### Usage

For listening

```shell
python ielts.py l N           #saves listening marks to the record and shows the band
python ielts.py l             #shows overall result of your listening
```

For reading

```shell
python ielts.py r N          #saves reading marks to the record and shows the band
python ielts.py r            #show overall result of your reading
```

You can also add alias in your `.bashrc` or `.zshrc` file if you wish.

```shell
# ielts alias
alias il="./ielts.py l"
alias ir="./ielts.py r"
```

<small>The above code assumes that you have kept your python script in your home directory. Also don't forget to make the script executable, i.e. `chmod a+x ielts.py`</small>

### Final thoughts

Writing code is pure craftsmanship. At its core, it's an art and I truly believe that no art is small. If you find any issue, please do let me know.
