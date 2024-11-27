# Interviews

Samantha interviews many candidates from different colleges using coding challenges and contests. Write a query to print the contest_id, hacker_id, name, and the sums of total_submissions, total_accepted_submissions, total_views, and total_unique_views for each contest sorted by contest_id. Exclude the contest from the result if all four sums are .

Note: A specific contest can be used to screen candidates at more than one college, but each college only holds  screening contest.

#### Input Format

The following tables hold interview data:

__Contests:__ The contest_id is the id of the contest, hacker_id is the id of the hacker who created the contest, and name is the name of the hacker. 

![1](/public/sql/joins/interview_contests.png)

__Colleges:__ The college_id is the id of the college, and contest_id is the id of the contest that Samantha used to screen the candidates. 

![2](/public/sql/joins/interview_colleges.png)

__Challenges:__ The challenge_id is the id of the challenge that belongs to one of the contests whose contest_id Samantha forgot, and college_id is the id of the college where the challenge was given to candidates. 

![3](/public/sql/joins/interview_challenges.png)

__View_Stats:__ The challenge_id is the id of the challenge, total_views is the number of times the challenge was viewed by candidates, and total_unique_views is the number of times the challenge was viewed by unique candidates. 

![4](/public/sql/joins/interview_view_stats.png)

__Submission_Stats:__ The challenge_id is the id of the challenge, total_submissions is the number of submissions for the challenge, and total_accepted_submission is the number of submissions that achieved full scores. 

![5](/public/sql/joins/interview_submission_stats.png)

#### Sample Input

Contests Table:  Colleges Table:  Challenges Table:  View_Stats Table:  Submission_Stats Table: 

#### Sample Output

```terminal
66406 17973 Rose 111 39 156 56
66556 79153 Angela 0 0 11 10
94828 80275 Frank 150 38 41 15
```

### Explanation

The contest  is used in the college . In this college , challenges  and  are asked, so from the view and submission stats:

Sum of total submissions 

Sum of total accepted submissions 

Sum of total views 

Sum of total unique views 

Similarly, we can find the sums for contests  and .

```sql
WITH total_submit AS (
    SELECT
        coll.contest_id,
        SUM(ss.total_submissions) AS sum_total_submissions,
        SUM(ss.total_accepted_submissions) AS sum_total_accepted_submissions
    FROM
        Colleges coll
    INNER JOIN Challenges chall ON chall.college_id = coll.college_id
    INNER JOIN Submission_Stats ss ON chall.challenge_id = ss.challenge_id
    GROUP BY
        coll.contest_id
),

total_view AS (
    SELECT
        coll.contest_id,
        SUM(vs.total_views) AS sum_total_views,
        SUM(vs.total_unique_views) AS sum_total_unique_views
    FROM
        Colleges coll
    INNER JOIN Challenges chall ON chall.college_id = coll.college_id
    INNER JOIN View_Stats vs ON chall.challenge_id = vs.challenge_id
    GROUP BY
        coll.contest_id
)

SELECT
    cont.contest_id,
    cont.hacker_id,
    cont.name,
    ISNULL(ts.sum_total_submissions, 0) AS total_submissions_sum,
    ISNULL(ts.sum_total_accepted_submissions, 0) AS total_accepted_submissions_sum,
    ISNULL(tv.sum_total_views, 0) AS total_views_sum,
    ISNULL(tv.sum_total_unique_views, 0) AS total_unique_views_sum
FROM
    Contests cont
    LEFT JOIN total_submit ts ON ts.contest_id = cont.contest_id
    LEFT JOIN total_view tv ON tv.contest_id = cont.contest_id
WHERE
    ISNULL(ts.sum_total_submissions, 0) > 0
    OR ISNULL(ts.sum_total_accepted_submissions, 0) > 0
    OR ISNULL(tv.sum_total_views, 0) > 0
    OR ISNULL(tv.sum_total_unique_views, 0) > 0
ORDER BY
    cont.contest_id;
```

```terminal
Correct Output:
845 579 Rose 1987 580 1635 566
858 1053 Angela 703 160 1002 384
883 1055 Frank 1121 319 1217 338
1793 2655 Patrick 1337 360 1216 412
2374 2765 Lisa 2733 815 3368 904
2963 2845 Kimberly 4306 1221 3603 1184
3584 2873 Bonnie 2492 652 3019 954
4044 3067 Michael 1323 449 1722 528
4249 3116 Todd 1452 376 1767 463
4269 3256 Joe 1018 372 1766 530
4483 3386 Earl 1911 572 1644 477
4541 3608 Robert 1886 516 1694 504
4601 3868 Amy 1900 639 1738 548
4710 4255 Pamela 2752 639 2378 705
4982 5639 Maria 2705 759 2558 711
5913 5669 Joe 2646 790 3181 835
5994 5713 Linda 3369 967 3048 954
6939 6550 Melissa 2842 859 3574 1004
7266 6947 Carol 2758 665 3044 835
7280 7030 Paula 1963 554 886 259
7484 7033 Marilyn 3217 934 3795 1061
7734 7386 Jennifer 3780 1015 3637 1099
7831 7787 Harry 3190 883 2933 1012
7862 8029 David 1738 476 1475 472
8812 8147 Julia 1044 302 819 266
8825 8438 Kevin 2624 772 2187 689
9136 8727 Paul 4205 1359 3125 954
9613 8762 James 3438 943 3620 1046
10568 8802 Kelly 1907 620 2577 798
11100 8809 Robin 1929 613 1883 619
12742 9203 Ralph 1523 413 1344 383
12861 9644 Gloria 1596 536 2089 623
12865 10108 Victor 2076 597 1259 418
13503 10803 David 924 251 584 167
13537 11390 Joyce 1381 497 1784 538
13612 12592 Donna 1981 550 1487 465
14502 12923 Michelle 1510 463 1830 545
14867 13017 Stephanie 2471 676 2291 574
15164 13256 Gerald 2570 820 2085 607
15804 13421 Walter 1454 459 1396 476
15891 13569 Christina 2188 710 2266 786
16063 14287 Brandon 1804 580 1621 521
16415 14311 Elizabeth 4535 1366 3631 1071
18477 14440 Joseph 1320 391 1419 428
18855 16973 Lawrence 2967 1020 3371 1011
19097 17123 Marilyn 2956 807 2554 750
19575 17562 Lori 2590 863 2627 760
```

```terminal
1 SQ
18855 2967 1020
4483 1911 572
2963 4306 1221
13537 1381 497
15804 1454 459
3584 2492 652
8825 2624 772
12861 1596 536
845 1987 580
5994 3369 967
6939 2842 859
4982 2705 759
2374 2733 815
13612 1981 550
4249 1452 376
13503 924 251
9613 3438 943
14502 1510 463
12865 2076 597
4541 1886 516
8812 1044 302
883 1121 319
12742 1523 413
4269 1018 372
4601 1900 639
1793 1337 360
7266 2758 665
4710 2752 639
7280 1963 554
19097 2956 807
16063 1804 580
858 703 160
5913 2646 790
19575 2590 863
15891 2188 710
9136 4205 1359
16415 4535 1366
7862 1738 476
7484 3217 934
15164 2570 820
11100 1929 613
14867 2471 676
18477 1320 391
7734 3780 1015
10568 1907 620
7831 3190 883
4044 1323 449
```

```terminal
2 SQ
18855 3371 1011
4483 1644 477
2963 3603 1184
13537 1784 538
15804 1396 476
3584 3019 954
8825 2187 689
12861 2089 623
845 1635 566
5994 3048 954
6939 3574 1004
4982 2558 711
2374 3368 904
13612 1487 465
4249 1767 463
13503 584 167
9613 3620 1046
14502 1830 545
4541 1694 504
12865 1259 418
8812 819 266
12742 1344 383
883 1217 338
4601 1738 548
4269 1766 530
1793 1216 412
7266 3044 835
4710 2378 705
7280 886 259
19097 2554 750
16063 1621 521
858 1002 384
5913 3181 835
19575 2627 760
15891 2266 786
16415 3631 1071
9136 3125 954
7862 1475 472
7484 3795 1061
15164 2085 607
11100 1883 619
14867 2291 574
18477 1419 428
7734 3637 1099
10568 2577 798
7831 2933 1012
4044 1722 528
```