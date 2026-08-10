# REPRESENTATIVE DATA MODEL
Version: 1.0

---

# Purpose

This document defines the official data structure for every elected public representative stored within the Janavani platform.

This schema applies to:

• Member of Parliament (MP)

• Member of Legislative Assembly (MLA)

The schema is designed to support:

- Public transparency
- AI retrieval
- Knowledge Graph
- Analytics
- Constitutional evaluation

---

# Core Rules

Every field must satisfy ONE of the following:

✓ Official Government Source

✓ Election Commission

✓ Legislature Website

✓ Official Affidavit

✓ Official Public Disclosure

If no official source exists,
the field SHALL NOT be stored.

---

# Representative Object

Each representative has one unique record.

```
Representative
{
    id
    basic_information
    office
    constituency
    elections
    legislative_activity
    committees
    development_funds
    assets
    constitutional_analysis
    official_links
    evidence
}
```

---

# Section 1

Basic Information

Required fields

```
Representative ID

Full Name

Office

State

Constituency

Political Party

Current Status

Term Start

Term End

Official Photo

Photo Status

Last Updated
```

Photo Status values

```
Official

Masked

Placeholder
```

---

# Section 2

Office Information

```
Office Type

MP

MLA
```

```
House

Lok Sabha

Rajya Sabha

Kerala Legislative Assembly
```

```
Constituency Type

Parliament

Assembly
```

---

# Section 3

Election Information

Store one record for every election.

```
Election Year

Election Type

Votes Received

Vote %

Winning Margin

Runner Up

Result

Affidavit Link

Election Commission Link
```

---

# Section 4

Legislative Performance

```
Attendance %

Questions Asked

Debates

Bills Introduced

Private Member Bills

Committee Meetings

Zero Hour Participation

Special Mentions

Calling Attention

Last Updated
```

---

# Section 5

Committee Membership

```
Committee Name

Role

Chairperson

Member

Start Date

End Date

Status
```

---

# Section 6

Development Funds

For MPs

```
MPLADS Allocation

Released

Spent

Balance

Projects

Completed

Ongoing

Pending
```

For MLAs

```
MLA Fund Allocation

Released

Spent

Balance

Projects

Completed

Ongoing

Pending
```

---

# Section 7

Asset Declaration

One record per election.

```
Election Year

Declared Assets

Declared Liabilities

Annual Income

Profession

Education

Criminal Cases Declared

Affidavit Source
```

---

# Section 8

Official Links

Only official links.

```
Official Website

Lok Sabha Profile

Assembly Profile

Election Commission

Official Facebook

Official X

Official Instagram

Official YouTube

Official Email

Official Office Address
```

---

# Section 9

Evidence

Every data item must have evidence.

```
Source

URL

Retrieved Date

Last Updated

Verification Status

Confidence

Evidence ID
```

---

# Verification Status

Allowed values

```
Official

Verified

Pending Verification

Archived
```

---

# Data Quality Rule

No value may exist without a source.

If evidence is missing,

the value must remain empty.

---

End of Version 1.0
