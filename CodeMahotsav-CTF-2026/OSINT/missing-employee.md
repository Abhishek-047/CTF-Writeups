## **CH1-OSINT-01 - Missing Employee**

### **100**

Created by Sujhal Gurav

1 (100% liked)  0

One of Black Lotus Technologies' employees hasn't logged in, hasn't answered internal messages, and hasn't been seen in two days. All the SOC has is a partial lead - a role, not a name. Evidence Card: "Employee - Infrastructure Operations" (redacted lead) Find out who this is. Cross-reference what's public - the company's own people, and the places employees leave traces without meaning to. Submit the employee's full name, lowercase, underscore between first and last, in the flag format below.



# Missing Employee

- **CTF:** Cyber Madness CTF 2026

- **Category:** OSINT

- **Points:** 100

- **Difficulty:** Easy

- **Status:** Solved ✅

## 📝 Description

> One of Black Lotus Technologies' employees hasn't logged in, hasn't answered internal messages, and hasn't been seen in two days.

## 🔍 Initial Analysis

The challenge hinted that the answer could be found by correlating publicly available information. Since I had already solved a previous challenge in the same storyline, I noticed an email address that appeared to belong to a Black Lotus Technologies employee.

The email found previously was:

```plain text
karan.mehta@blacklotus.com
```

## 🛠️ Tools Used

- Web Browser

## 🚀 Approach

Instead of performing a full OSINT investigation, I tested the employee name extracted from the previously discovered corporate email address.

The challenge required the flag format:

```plain text
firstname_lastname
```

From the email:

```plain text
karan.mehta@blacklotus.com
```

I inferred the employee's full name as:

```plain text
karan_mehta
```

## 🏁 Solving Steps

1. Read the challenge description and noted that it required the full employee name.

1. Recalled the email address discovered in an earlier challenge:

1. Converted the email username into the required format:

1. Submitted the flag:

1. The submission was accepted.

## 🚩 Flag

```plain text
CM26{karan_mehta}
```

## 💡 What I Learned

- OSINT challenges often build on information discovered in earlier stages of the investigation.

- Corporate email addresses can reveal employee identities.

- Always pay attention to details from previous challenges, as they may become useful later in the storyline.

## 🔗 References

- [https://osintframework.com/](https://osintframework.com/)

- [https://github.com/lockfale/OSINT-Framework](https://github.com/lockfale/OSINT-Framework)
