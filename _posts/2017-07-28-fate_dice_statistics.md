---
layout: post
title: "Fate Dice Statistics"
description: >
  My friends and I played a Fate RPG for over two years. During that time we
  rolled a lot of dice and developed a lot of superstitions, but were any of
  them correct?
image: /files/fate-dice-statistics/fate_dice.jpg
---

{% capture file_dir %}{{ site.url }}/files/fate-dice-statistics/{% endcapture %}

{% comment %}![A painting of Fate by Alphonse Mucha, 1920]({{ file_dir }}/alphonse-mucha-fate-1920.jpg) {% endcomment %}
![The black, blue, and red sets of dice.]({{ file_dir }}/fate_dice.jpg)

My friends and I played [Fate][fate], a [role-playing game][rpg], for a few
years during graduate school. Over that time we developed superstitions about
the various dice we rolled. Since we were (are) huge nerds we decided to
record (almost) all of the roles so we could see if the dice were really
biased. We looked at the data a bit when we finished playing, but I thought it
would be interesting to dig it back out and play around with it some more.

[rpg]: https://en.wikipedia.org/wiki/Tabletop_role-playing_game
[fate]: http://www.evilhat.com/home/fate-core/

## Fate Dice

[Fate dice][dice] (also called Fudge dice) have six sides and three values
that each show up equally: plus, blank, and minus. These represent +1, 0, and
-1 respectively. Four dice are rolled at a time and their results are summed
giving a range of -4 to 4. The [notation][dice_notation] for this type of roll
is `4dF`.

[dice]: https://en.wikipedia.org/wiki/Fudge_(role-playing_game_system)#Fudge_dice
[dice_notation]: https://en.wikipedia.org/wiki/Dice_notation

Figuring out the probability of rolling a value is just simple combinatorics.
These probabilities are:

|----------+-------------+
| Value    | Probability |
|:---------|------------:|
| 0        |       19/81 |
|----------+-------------+
| 1 xor -1 |       16/81 |
|----------+-------------+
| 2 xor -2 |       10/81 |
|----------+-------------+
| 3 xor -3 |        4/81 |
|----------+-------------+
| 4 xor -4 |        1/81 |
|----------+-------------+

## Rolls

We had four sets of Fate dice, colored blue, red, black, and white. We wrote
down only the sum of each roll, since the individual dice in the set are
indistinguishable. This means that if one of the die is biased, it will take
longer to show up than if we had been able to explore the results
individually. As per usual, you can find the Jupyter notebook used to make
these schedules [here][notebook] ([rendered on Github][rendered]). The data is
[here][data].

[notebook]: {{file_dir}}/Fate Dice Statistics.ipynb
[rendered]: https://github.com/agude/agude.github.io/blob/master/files/fate-dice-statistics/Fate%20Dice%20Statistics.ipynb
[data]: {{file_dir}}/fate_dice_data.csv

Here are the distributions of rolls for each of the four sets of dice. The
points indicate the number of rolls that came up with a certain value, while
the grey area is the range in which we would expect to find a result produced
by a fair set of dice 99% of the time.

[![The results of our roles during out Fate campaign.][results_plot]][results_plot]

[results_plot]: {{ file_dir }}/fate_dice_rolls.svg

The blue dice were rolled the most (because we thought the red and black set
were unlucky), but estimating by eye it looks like it was actually biased!
The "cursed" red and black dice seem to be fine. The white dice have one bin
(very) high, but it's hard to tell by eye if that is significant.

## Significance

To check whether the dice are biased, a [chi-squared test][chi2] is required.
The chi-squared test essentially looks at how far each point in a distribution
is away from the expected value for that point, and normalizes by the
variance. The test statistic is then compared to the results expected from a
[chi-squared distribution][chi2_dist] and a significance is obtained. Running
this test on our dice yields the following results:

[chi2]: https://en.wikipedia.org/wiki/Pearson%27s_chi-squared_test
[chi2_dist]: https://en.wikipedia.org/wiki/Chi-squared_distribution

|-------+-------------+---------+
| Dice  | chi-squared | p-value |
|:------|------------:|--------:|
| Blue  |       26.31 |   0.001 | 
|-------+-------------+---------|
| Black |        9.32 |   0.315 |
|-------+-------------+---------|
| Red   |       10.77 |   0.215 |
|-------+-------------+---------|
| White |       19.07 |   0.014 |
|-------+-------------+---------|

The chi-squared test has some [caveats about low expected values][caveats],
but at worst we only have two (out of nine) bins below five expected entries.
Looking at the p-values we conclude roughly the same as our "_chi-by-eye_"
test above: the blue dice are significantly biased, while the black and red
dice show no evidence of being unfair. The white dice are not biased at the `p
< 0.01` level, but that single high bin is odd and to be absolutely sure I
would want to roll them a lot more and check.

[caveats]: https://stats.stackexchange.com/q/93212