==============UNDERSTANDING DataVisualization===============
Continuous vs. categorical variables
In order to choose an appropriate type of plot to draw, you need to be able to distinguish between continuous variables (roughly: "things you can do arithmetic on") and categorical variables (roughly: "things that can be classified").

![alt](<Continuos VS Categorical.png>)
----------
Exercise
Interpreting histograms
The histogram below displays the distribution of average taxable income for various jobs in Australia. The x-axis represents the average taxable income (AUD/year), showing salary ranges. The y-axis represents the count of jobs in each income range.
![alt text](<Interpreting histograms.png>)
A histogram of Australian salaries

Tip: This left-hand pane of the exercise containing text and instructions is resizable. If the plot is too small to see clearly, making the pane wider will increase the plot size. Move your mouse in-between the left-hand pane and the drag and drop portion of the exercise so a gray vertical bar appears. In Chrome, click and drag this bar to the right. In Firefox, click the bar, move your mouse right, then click again.

Data Source: Tidy Tuesday
Instructions
--Categorize these statements about the histogram as true or false.

![alt text](<HIstograms T-F.png>)
-------------
Exercise
Adjusting bin width
BCI Wildlife Research Institute has built a histogram explorer to analyze camera trap activity patterns from Barro Colorado Island in Panama. As a data analyst, you are using it to investigate agouti activity.

The appearance of a histogram is heavily influenced by the width of its bins: the intervals that determine where each bar lies on the x-axis. If the bins are too wide, you don't see enough detail in the shape of the distribution. If the bins are too narrow, the distribution can be obscured by noise.

Adjust the slider to explore different bin widths, then determine which statement about agouti activity is true.

Data Source: Rowcliffle et al. 2014

Answer the question
Possible Answers
Select one answer
The agouti had a high level of activity from 4am to 12pm, then moderate activity from 12pm to 8pm.
The agouti were most active for a couple of hours after sunrise (6:30am to 8:30am), and before sunset (4pm to 6pm).
The agouti showed a constant level of activity throughout sunlight hours.
Press
The agouti activity was highly variable, with over a dozen peaks in activity throughout the day.

ANSWER:The agouti were most active for a couple of hours after sunrise (6:30am to 8:30am), and before sunset (4pm to 6pm).
----------------------
Exercise
Interpreting box plots
Here are box plots of cigarette consumption per person in the USA from 1985 to 1995 (Alaska and Hawaii are not included). Each observation in the dataset is the average number of packets of cigarette smoked per person in one state in one year. Thus each box plot represents the distribution of 48 data points (because there are 48 US states included in the dataset). The years are on the y-axis, and the amount of cigarettes packs smoked (per capita per year) are on the x-axis.

Box plots of cigarette consumption per capita in the USA, from 1985 to 1995.
Data Source: Stock, James H. and Mark W. Watson (2003)
Instructions
--Categorize these statements about the box plots as true or false.
![alt text](<Interpreting Box Plots.png>)
------------
Exercise
Ordering box plots
StatViz Analytics has built a state-by-state cigarette consumption explorer for a public health client. The dataset shows US cigarette consumption per capita by state from 1985 to 1995.

How you order the box plots affects the kinds of questions that are easy to answer. Sorting alphabetically makes it easy to look up a specific state. Sorting by median consumption makes it easy to find the highest or lowest consumption. Sorting by inter-quartile range (IQR) — the spread of the "middle half" of the data — makes it easy to see which states had the most variation among typical smokers.

Use the dropdown to explore different sort orders, then determine which statement is false.

