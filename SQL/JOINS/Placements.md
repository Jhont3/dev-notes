# Placements

You are given three tables: Students, Friends and Packages. Students contains two columns: ID and Name. Friends contains two columns: ID and Friend_ID (ID of the ONLY best friend). Packages contains two columns: ID and Salary (offered salary in $ thousands per month).

#### Students
| Column | Type    |
|--------|---------|
| ID     | Integer |
| Name   | String  |

#### Friends
| Column    | Type    |
|-----------|---------|
| ID        | Integer |
| Friend_ID | Integer |

#### Packages
| Column | Type  |
|--------|-------|
| ID     | Integer |
| Salary | Float  |

Write a query to output the names of those students whose best friends got offered a higher salary than them. Names must be ordered by the salary amount offered to the best friends. It is guaranteed that no two students got same salary offer.

### Sample Input

#### Students
| ID | Name      |
|----|-----------|
| 1  | Ashley    |
| 2  | Samantha  |
| 3  | Julia     |
| 4  | Scarlet   |

#### Friends
| ID | Friend_ID |
|----|-----------|
| 1  | 2         |
| 2  | 3         |
| 3  | 4         |
| 4  | 1         |

#### Packages
| ID | Salary  |
|----|---------|
| 1  | 15.20   |
| 2  | 10.06   |
| 3  | 11.55   |
| 4  | 12.12   |
 
### Sample Output:

```terminal
Samantha
Julia
Scarlet
```

## Solution 

```sql
SELECT S.Name FROM Students S
JOIN Friends F ON S.ID = F.ID
JOIN Packages SP ON S.ID = SP.ID
JOIN Packages FP ON F.Friend_ID = FP.ID
WHERE FP.Salary > SP.Salary
ORDER BY FP.Salary;
```

## Explanation:
1. Join Students and Friends: Match each student with their best friend's ID using Friends table (S.ID = F.ID).
2. Join with Packages:
    - Join Packages (SP) to get the salary of the students (S.ID = SP.ID).
    - Join Packages again (FP) to get the salary of their best friends (F.Friend_ID = FP.ID).
3. Filter Students: Use WHERE FP.Salary > SP.Salary to select only those students whose best friend's salary is higher than their own.
4. Order by Best Friend's Salary: Ensure names are ordered by the salary of the best friends (FP.Salary).