# REPORT — what the measurement showed, who it affects, and whose responsibility it is

## 1. Measurement read-out

I ran the supplied agent with no controls on 200 matched pairs: 40 pairs for each of the three attributes and 40 for each of the two floors, 400 profiles and 400 model calls. None of the 400 replies was unparseable, and 356 of 400 profiles were shortlisted, so the agent shortlists almost everyone.

Flips against the floors:

| attribute | flips (of 40) | flip rate [95% CI] | its floor | floor flips |
|---|---|---|---|---|
| name_language | 2 | 5.0% [1.4%, 16.5%] | null_name | 0/40 |
| gender_coded_name | 4 | 10.0% [4.0%, 23.1%] | null_name | 0/40 |
| university | 1 | 2.5% [0.4%, 12.9%] | null_university | 1/40 |

No attribute stands clear of its floor: all three intervals overlap the floor's interval. So this run does not support a claim that any of them moved the decision.

Direction and size:

- **Gender-coded name.** All 4 flips favoured the female-coded name (0 the other way, exact McNemar p = 0.125). Female-coded names were shortlisted 90.0% of the time and male-coded names 80.0% (40 profiles each). The signed score gap was small, -0.4 [-1.2, +0.6], and 6 of 40 pairs had different scores, with a mean absolute difference of 1.12 against 2.00 for null_name. The direction is consistent, but the count is below what the test can detect.
- **University.** Only 1 flip, the same as its floor. But the scores leaned one way: flagship applicants scored +2.5 [+1.1, +4.1] points higher than their regional twins, sign test p = 0.002. This is the only interval in the run that does not include zero. The mean absolute difference was 2.50 [1.12, 4.12] against 2.12 [0.88, 3.62] for null_university, so the shift is small and about the same size as the floor; what is different is that it always points the same way. It almost never pushed anyone across the line.
- **Name language.** 2 flips, both favouring the Kazakh-language name. The score gaps all include zero: Kazakh +0.7 [-1.8, +3.8], Russian +0.2 [-1.7, +2.1], English -1.0 [-4.2, +1.9]. Nothing to report beyond noise.

How much could be noise. Decoding is greedy, so there is no run-to-run noise; the floors measure how much the output moves when an irrelevant detail changes. null_name changes which name of the same language group and gender coding is shown; null_university changes which university of the same tier is shown. They leave untested other wordings of the prompt, the order of the lines, and other irrelevant details, any of which could move the model more. A stronger estimate would need more pairs, several prompt wordings and the order probe.

What n = 40 can detect. The exact McNemar test needs at least 6 one-way flips out of 40 (15.0%) to call a result significant with none the other way, 8 with one the other way, 10 with two. The gender result (4 to 0) is below that line. So "no measured effect" here means "nothing larger than about 6 one-way flips per 40 pairs"; a smaller real effect could be present and this run would miss it. I also ran several tests, so even the university p = 0.002 should be read with care.

Limits. The profiles are synthetic and every name is in Latin script. A different prompt, a different model or real applicants could behave differently, and this run cannot say how.

## 2. Who is affected

**Wrongly rejected applicants.** In the bare run 44 of 400 profiles were rejected. If the gender pattern is real, male-coded applicants are the ones rejected more often: 80.0% shortlisted against 90.0%. If the university shift is real, regional applicants sit about 2.5 points lower, which matters only for those close to the cut-off near a score of 45, where 33 rejected and 31 shortlisted profiles shared the 40-49 range. A wrong rejection costs the applicant the scholarship money and possibly the place at university, and they are not told why.

**Wrongly shortlisted applicants, and who they displace.** The fund has a limited number of awards. Every applicant pushed up by a name or a university pushes someone else down, and that someone never learns it.

**Applicants hurt by the harness itself.** Blinding changed the prompt, and 7 profiles went from shortlist to reject without any person looking at them, because their new score fell outside the escalation band. Scores changed for 166 of 400 profiles, by 14.01 points on average. These applicants bear a cost that has nothing to do with their name or university.

**People the brief does not mention.**
- The reviewers, who get 38 of 400 cases (9.5%) to read.
- Applicants whose name is not in Latin script: my refusal rule would refuse a name typed in Kazakh Cyrillic, although the person did nothing wrong. My test set could not show this, because every synthetic name is Latin.
- Graduates of universities the model has never seen, and people who never applied because they expected the system to reject them.

**If there is an effect my sample could not detect.** With 40 pairs, an effect of fewer than 6 one-way flips would stay hidden. On a real intake of thousands, that could still be dozens of people treated differently by name or university.

**Compounding.** If the agent is used year after year, a small lean towards flagship universities rewards the same institutions again and again. If its shortlist feeds a later review or a published ranking, the bias travels with it.

## 3. Professional responsibility

My responsibilities as the developer of the harness:

- **Check before it runs.** Measure matched pairs against floors, as I did, and repeat the measurement after every change to the prompt, the model or the band. I did this once before and once after.
- **Tell the deployer what I found and what I did not.** No attribute cleared its floor; the university shift is consistent but small; n = 40 cannot see effects below about 6 one-way flips; the band I set from the bare scores sent 9.5% to a person, not the 27.3% I planned.
- **Not claim more than the run shows.** The 0 flips after blinding are zero by construction, not proof the model is fair. My citations to EU law are benchmarks; the fictional fund is not in the EU.
- **Leave a record.** The decision log and the control register are in the repository.

What is left: recalibrating the band on the harness's own scores, testing names in Cyrillic and other scripts, and a larger sample.

**The institution's responsibilities.** The fund sets the purpose, decides who reviews escalated cases and how fast, handles appeals, and tells applicants that an automated system is involved and how to ask for a human decision.

**Where we could conflict.** If the fund wants escalation switched off because 38 reviews per 400 applicants cost money, I would show them the 7 shortlist-to-reject cases and the 166 changed scores: without escalation nobody sees those applicants at all. I can explain the cost, but I should not ship a version without a human route and still call it safe.
