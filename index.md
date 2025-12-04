---
layout: default
---

## What are multilevel models?
Some data have a **hierarchical** or **clustered** structure. For example, children who grew up in the same households with the same parents tend to be more alike than random individuals from the same age groups. <br>
<br>
Similarly, students from the same school or neighborhoods may perform more similarly on standardized tests. In this case, individual students' test scores are considered "nested" within institutions or geographical areas. <br>
<br>
![data-hierarchy](assets/images/data_hierarchy.png)
<br>

## Why use multilevel models?
Nested data violate key assumptions of Ordinary Least Squares (OLS) in linear regression, because: <br>
* Observations are not independent <br>
* Error terms are not independent <br>
<br>
Also, some data structures that do not appear to be clustered can actually be. Here is an extreme example:
 ![data-structure](assets/images/data-structure.png)
 
 Source: [A Fun Intro to Multilevel Models in R](https://favstats.github.io/intro_multilevel/slides/#14)
 <br>







```
Long, single-line code blocks should not wrap. They should horizontally scroll if they are too long. This line should be long enough to demonstrate this.
```

```
The final element.
```
