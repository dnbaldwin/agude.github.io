---
layout: post
title: "SWITRS: On What Days Do Cyclists Crash?"
description: >
  California accident data doesn't just cover cars, it covers bikes too! This
  time we look at when cyclists crash in California.
image: /files/switrs-bicycle-accidents-by-date/wilhelmina_cycle_co.jpg
image_alt: >
  An 1890s advertisement for Wilhelmina Cycle Co. Ltd. showing a family on bicycles.
use_latex: True
categories: switrs
---

{% capture file_dir %}/files/switrs-bicycle-accidents-by-date{% endcapture %}

{% include lead_image.html %}

It is time to use [SWITRS data][switrs] to look at traffic accidents in
California again. I have previously used the data to look at [when cars
crash][car_switrs]---during holidays when people both drive to work and to
parties after---and [when motorcycles crash][mc_switrs]---during the summer
when its good riding weather. Today I want to look at something a little
closer to my heart: **bicycles**.

[switrs]: http://iswitrs.chp.ca.gov/Reports/jsp/userLogin.jsp
[car_switrs]: {% post_url 2016-12-02-switrs_crashes_by_date %}
[mc_switrs]: {% post_url 2017-02-21-switrs_motorcycle_crashes_by_date %}

I have been commuting on my bike for years now, and when I was younger I used
to put in thousands of miles a year for fun. So knowing more about when
accidents happen is something I am very interested in.

As per usual, the Jupyter notebook used to perform this analysis can be found
[here][notebook] ([rendered on Github][rendered]).

{% capture notebook_uri %}{{ "SWITRS Crash Dates With Bicycles.ipynb" | uri_escape }}{% endcapture %} 

[notebook]: {{ file_dir }}/{{ notebook_uri }}
[rendered]: https://github.com/agude/agude.github.io/blob/master{{ file_dir }}/{{ notebook_uri }}

## A Simple Model

Before we dig into the data, I have a simple model for how many bicycle
accidents there are. It is:

$$ N_{\textrm{accidents}} = P_{\textrm{car-bike}} \, L_{\textrm{miles biked}} \, \lambda_{\textrm{cars per mile}} $$

That is, the number of accidents involving bicycles ($$N$$) is the probability
of an accident happening when a bike encounters a car ($$P$$) times the number
of cars encountered ($$L \lambda$$). This ignores some accidents, like solo
crashes and those that do not involve a car, but these are rare.[^1]

We won't be able to test the validity of this model with the SWITRS data
alone, but we can use it to reason about what is happening. For example, if
the number of accidents increases, that could be because there are more cars
or bikes on the road, or because the probability of collision increased
(perhaps due to distracted drivers or worse average weather).

## Data Selection

I selected accidents involving bicycles from the [SQLite database][s2s]
([discussed previously][s2s_post]) with the following query:

[s2s]: https://github.com/agude/SWITRS-to-SQLite
[s2s_post]: {% post_url 2016-11-01-switrs_to_sqlite %}

```sql
SELECT Collision_Date FROM Collision
WHERE Collision_Date IS NOT NULL
AND Bicycle_Collision == 1          -- Involves a bicycle
AND Collision_Date <= '2017-12-31'  -- 2018 is incomplete
```

This gave me 223,772 data points to examine spanning 2001 to 2017. [Just as
before][ds], accidents from the most recent year are rejected because the
database dump comes from September 2018, and so the year is incomplete.

[ds]: {% post_url 2016-12-02-switrs_crashes_by_date %}#data-selection

## Accidents per Week

For car crashes, [I found that there was a large dip in 2008][car_apw] as
people stopped driving to work during the [Great Recession][gr]. For
motorcycle crashes, [I found strong seasonality][mc_apw] as people hung up
their helmets during the winter. For bicycles, we have the following pattern:

[car_apw]: {% post_url 2016-12-02-switrs_crashes_by_date %}#accidents-per-week
[mc_apw]: {% post_url 2017-02-21-switrs_motorcycle_crashes_by_date %}#accidents-per-week
[gr]: https://en.wikipedia.org/wiki/Great_Recession

[![Line plot showing bicycles accidents per week from 2001 through
2017][per_week_plot]][per_week_plot]

[per_week_plot]: {{ file_dir }}/bicycle_accidents_per_week_in_california.svg

It shows features similar to both cars and motorcycles:

- The number of accidents increases after 2008, and then begins decreasing
after 2013, almost exactly the **opposite** of the car pattern.
- Accidents are highly seasonal, just like motorcycles. Apparently neither
cyclists nor bikers like riding in the rain.

Thinking back to [the model][model] we can try to reason about the trend. We
know the number of cars increased, so the decrease in accidents in the last
few years is either due to a decrease in the  number of cyclists---possibly
because they traded their bikes for cars as they found employment---or a
decrease in the likelihood of an accident---perhaps because drivers are more
used to cyclists and look out for them.

[model]: #a-simple-model

## Day-by-Day

Car are involved in accidents [on holidays during which the drivers also
work][car_dbd], like Halloween. Motorcycles are in accidents during summer
holidays. Bicycles, on the other hand, have no holidays with a large excess in
the number of accidents. Some holidays, like Christmas and Thanksgiving, keep
people from getting on their bikes, but none seem to motivate to get out and
ride.

[car_dbd]: {% post_url 2016-12-02-switrs_crashes_by_date %}#day-by-day
[mc_dbd]: {% post_url 2017-02-21-switrs_motorcycle_crashes_by_date %}#day-by-day

[![Line plot showing average motorcycle accidents by day of the
year][average_accidents]][average_accidents]

[average_accidents]: {{ file_dir }}/mean_bicycle_accidents_by_date.svg

New Year's Day, St. Patrick's Day, and the 4th of July are all higher than
they would be if they were not holidays, although you can't tell from this
plot. On those days, people tend to go out and celebrate with alcohol, which
leads to solo crashes. I will examine that in a future post.

## Day of the Week

For cars, [weekends show a decrease in the number of accidents][car_dotw] as
people stop commuting. For motorcycles, [weekends show an increase in the
number of accidents][mc_dotw] as people use their time off to ride. As a
recreational cyclist, I expected accidents to increase on the weekend as
people put on their Lycra and take to the back roads for fun. But this is not
the case:

[car_dotw]: {% post_url 2016-12-02-switrs_crashes_by_date %}#day-of-the-week
[mc_dotw]: {% post_url 2017-02-21-switrs_motorcycle_crashes_by_date %}#day-of-the-week

[![Violin plot showing the number of bicycle accidents by day of the
week][accident_violin_plot]][accident_violin_plot]

[accident_violin_plot]: {{ file_dir }}/bicycle_accidents_by_day_of_the_week.svg

These [violin plots][violin] show the distribution of accidents by day of the
week over the 17 year period. There is a large drop in the number of accidents
on weekends. This is surprising to me. I would have expected a lot more
cyclists to be out on the weekend, leading to more interactions with cars.

It's possible that this (an increased number of cyclists) is true but there
are just enough fewer cars that the accident rate still goes down. Or perhaps
the riders are better at avoiding accidents. Or maybe the cyclists are out in
the countryside away from the cars. Or perhaps weekend drivers are better at
avoiding cyclists. Without more data, we can't tell.

[violin]: https://en.wikipedia.org/wiki/Violin_plot

## Conclusion

This analysis of bicycle accidents surprised me a little. I expected bikes to
show a similar pattern to motorcycles, since both are used to both commute and
for fun. However, bikes show a greatly reduced accident rate on the weekend
while motorcycles show an increase. Bikes and cars also seem to trade off,
with car accidents increasing in recent years while bike accidents fall off.
Further study and additional data is necessary before I can determine the
reasons behind this trend.

---

[^1]: Of the 223,772 recorded accidents with bicycles, **89% involve a car**. There is a bias though: SWITRS reports are filled out when the police or CHP are called to the scene. As such, they skew towards worse accidents.
