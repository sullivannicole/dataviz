Biden Visualization
================
Soo Wan Kim
April 8, 2017

Static data visualization: Biden warmth, age, party identification & gender
---------------------------------------------------------------------------

``` r
################
#Transform data#
###############

biden <- biden %>%
  #generate partyID factor variable showing party identification in words
  mutate(partyID = ifelse(dem == 1, "Democrat", ifelse(rep == 1, "Republican", "Independent"))) %>%
  transform(partyID = as.factor(partyID)) %>%
  #generate gender variable
  mutate(gender = ifelse(female == 1, "F", "M")) %>%
  #generate age group variable
  mutate(ageGroup = ifelse(age < 31, 1, ifelse(age < 51, 2, ifelse(age < 66, 3, 4))))

#summarize average biden warmth by age group, gender, and party idetification
biden_ageGroup <- biden %>%
  group_by(ageGroup, gender, partyID) %>%
  summarize(bidenMean = mean(biden))

######
#Plot#
#####

ggplot(biden_ageGroup, aes(ageGroup, bidenMean)) + 
  #points showing average biden warmth for each age group
  ##in each partyID and gender combo
  geom_point(aes(color = partyID), size = 1.4) + 
  #lines connecting the points
  geom_line(aes(color = partyID, linetype = gender), size = 1.3) + 
  #add gray horizonal line showing the middle/neutral temperature (50)
  geom_hline(aes(yintercept = 50), alpha = 0.5) + 
  #color scheme according to party ID - red for Republicans, blue for Democrats
  scale_color_manual(name = "Party ID",
                     labels = c("Democrat",
                                "Independent",
                                "Republican"),
                     values = c("Democrat" = "blue",
                                "Independent" = "green",
                                "Republican" = "red")) + 
  #attach age group labels
  scale_x_continuous(breaks = c(1, 2, 3, 4), labels = c("18 - 35", "36 - 50", "51 - 65", "65+")) + 
  labs(title = "Attitudes toward Joe Biden", 
       subtitle = "Average feeling temperature for each age group broken down by party identification and gender",
       x = "Age Group",
       y = "Avg. Biden Warmth (0-100)",
       caption = "Source: 2008 American National Election Studies",
       linetype = "Gender",
       fill = "Feeling\temperature") + 
  #get rid of grid lines and set the font face for age group labels to italic
  theme_classic() + 
  theme(axis.text.x = element_text(face = "italic"))
```

![](biden_viz_files/figure-markdown_github/plot-1.png)

Design Description
------------------

### Story and Audience

The story I wanted to tell through this graph is threefold:

1.  **Party identification plays a major role (possibly the most important) in Americans' atttitudes toward Biden.** Regardless of gender and age group, Democrats are significantly more warmly disposed toward Joe Biden than non-Democrats, and Republicans are significantly less warmly disposed than non-Republicans.
2.  **Biden is popular among women.** Across age groups and party lines, women are more warmly disposed to Biden than men.
3.  **Biden is popular.** Even though Republicans are less warm toward Biden compared to non-Republicans, the difference between the middle value of 50 and the low extreme is significantly less than the difference between 50 and the high extreme.

The intended audience of my visualization is the general public. As such, I wanted to create a relatively simple graph that is intuitive and easy to draw insights from while still being truthful.

Below I show how I used the different elements of the graph to convey my story.

### Graphical form

I chose to make a point and line chart with multiple trend lines corresponding to different party identification and gender combinations. I plotted the points on the same x and y axes to highlight their relative positions along the y-axis (i.e. how much of a gap there is between men and women in each party ID group, how much of a gap there is between Democrats and Republicans, etc.). Although the age groups are technically categorical variables, I connected points in the same grouping with lines to make the relationship between them more apparent as well as to highlight age-related trends for each group.

Faceting by gender or party identification could have made for a clearer visualization if there were more noise or overlapping lines across groups, but with the transformation I used the trends were cleanly separated such that faceting was not necessary. In fact, with this format the relative positions of the trend lines along the y-axis are easier to interpret because the lines are directly above or below each other rather than placed side by side in different panels.

Alternative plot formats considered were the box plot and the violin plot. Admittedly, this is subjective, but box plots look too clinical and "academic" for a lay audience in my opinion, and violin plots distract from the visualization's story with their interesting and suggestive shapes (much like Rorschach ink blots). In any case, the point and line plot is a better format for directly comparing relative positions along an axis. In box plots, separate boxes are placed next to each other rather than above or below each other, and so the viewer needs to evaluate both the vertical distance and the horizontal distance. Violin plots have the same issue.

### Color and line type

I chose the line colors according to colors popularly associated with each political party - blue for Democrats and red for Republicans. This color scheme makes interpreting the graph more intuitive for anyone familiar with American politics. I chose green for Independents simply because the color contrasts well with the light background and is easily distinguishable from both blue and red. Line type distinguishes male groups from female groups (e.g. male Democrats from female Democrats). Using solid and slashed lines provides enough contrast such that one can look at the graph and quickly distinguish between the gender groups. It also does not excessively divert attention to one group over another, such as if I used line thickness to distinguish between men and women.

### Data transformation

Instead of using the raw data for Biden warmth, I used averages across age groups for each combination of party identification and gender. Generating a scatterplot of Biden warmth across all the ages or even averaging across ages would produce more "noise" or up-and-down movements across the graph and intersections among trend lines. While understanding the variation across individuals and other more narrow groupings is valuable to avoid false judgments, looking at a tangled mass of squiggly lines does not help generate useful theory or stories. Grouping observations into age groups allows the graph to be easily interpretable in terms of trends while still capturing important variation.

I could have depicted more variation by age by making the age groups smaller and more numerous by, say, having age groups with a range of 5 or 10 years. I did not, for two reasons. First, for aethetic purposes, as more groups would make for a more cluttered graph. Second, people are accustomed to grouping people along generational lines (millenials vs. Gen Xers vs. Baby Boomers, ect.). While there is no precise agreed-upon way of splitting ages into generational groups, I felt age groups of roughly 15 years in range would be fairly intuitive for most.

### Embellishments

To better depict Biden's overall popularity, I added a horizonal line crossing the y-axis at the middle feeling temperature of 50. Dividing the graph this way is meant to highlight the relative area of the regions above and below 50. This in turn highlights the finding that even those who dislike Biden generally do not dislike him to an extreme degree, whereas many Democrats are quite enthusiastic admirers. I removed the background grid lines using `theme_classic()` so that the horizonal line would not be obscured, as well as to simplify the graph and remove distracting details.
