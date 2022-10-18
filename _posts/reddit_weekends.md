# IFT 6758 - Assignment 3 

### Pt 1. Reddit Weekends

*This assignment is based off of Greg Baker's data science course at SFU*

Evaluation on this notebook:

- Histograms for base counts, transformed counts, and central limit theorem counts
- Short answers (last section)


```python
%load_ext autoreload
%autoreload 2
```


```python
from pathlib import Path
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
sns.set()
```


```python
from datetime import date
import scipy.stats as sp
```


```python
import reddit_weekends
```

## 1. Load Data

Read the JSON data and filter/clean the dataframe


```python
raw_df = reddit_weekends.read_data("data/reddit-counts.json.gz")
```


```python
# TODO: complete these implementations in reddit_weekends.py
df = reddit_weekends.process_data(raw_df)
wd, we = reddit_weekends.split_data(df)
```

### T-Test


```python
# TODO: complete this implementations in reddit_weekends.py
p_ttest, p_wdNormal, p_weNormal, p_vartest = reddit_weekends.tests(wd, we, verbose=True)
```

    p_value:	0.0
    WD normality:	0.0
    WE normality:	0.00152
    Variance test:	0.04379


### Fix 1: transforming data might save us.

Have a look at a histogram of the data. You will notice that it's skewed: that's the reason it wasn't normally-distributed in the last part. Try to transform the counts so the data doesn't fail the normality test. Consider the following transforms:

    np.log, np.exp, np.sqrt, counts**2
    
For each transformation, plot the new histogram (`reddit_weekends.draw_histogram()`) and run the `reddit_weekends.tests()` method to see if you can now use the T-test. 
    
Note: none of them will make both distributions pass the normality test. The best I can get one variable with normality problems, one okay; no equal-variance problems.


```python
fig = reddit_weekends.draw_histogram(df, title="No transform")
```


    
![png](reddit_weekends_files/reddit_weekends_12_0.png)
    



```python
tmp_df = df.copy()

# TODO: apply transform to the copied data (don't overwrite original!)
tmp_df["comment_count"] = np.sqrt(tmp_df["comment_count"])

# TODO: draw histogram
reddit_weekends.draw_histogram(tmp_df, title="Square Root")

# TODO: re-run tests
wd, we = reddit_weekends.split_data(tmp_df)
reddit_weekends.tests(wd, we, verbose=True)
```

    p_value:	0.0
    WD normality:	0.03687
    WE normality:	0.10761
    Variance test:	0.55605





    (3.456473928419179e-64,
     0.03687221613365365,
     0.10760562894666933,
     0.5560544297516696)




    
![png](reddit_weekends_files/reddit_weekends_13_2.png)
    



```python
# TODO: repeat the above for the other transforms

tmp_df = df.copy()

# TODO: apply transform to the copied data (don't overwrite original!)
tmp_df["comment_count"] = np.log(tmp_df["comment_count"]) 

# TODO: draw histogram
reddit_weekends.draw_histogram(tmp_df, title="Log")

# TODO: re-run tests
wd, we = reddit_weekends.split_data(tmp_df)
reddit_weekends.tests(wd, we, verbose=True)
```

    p_value:	0.0
    WD normality:	0.0004
    WE normality:	0.31494
    Variance test:	0.00042





    (2.4771620792045563e-68,
     0.00040159142006827235,
     0.31493886820667,
     0.0004190759393372205)




    
![png](reddit_weekends_files/reddit_weekends_14_2.png)
    



```python
tmp_df = df.copy()
#suppress warnings
import warnings
#warnings.filterwarnings('ignore')

# TODO: apply transform to the copied data (don't overwrite original!)
tmp_df["comment_count"] = np.exp(tmp_df["comment_count"])

# TODO: draw histogram

reddit_weekends.draw_histogram(tmp_df, title="Exponential")

# TODO: re-run tests
#wd, we = reddit_weekends.split_data(tmp_df)
#reddit_weekends.tests(wd, we, verbose=True)
```


    ---------------------------------------------------------------------------

    KeyError                                  Traceback (most recent call last)

    Cell In [34], line 11
          7 tmp_df["comment_count"] = np.exp(tmp_df["comment_count"])
          9 # TODO: draw histogram
    ---> 11 reddit_weekends.draw_histogram(tmp_df, title="Exponential")


    File ~/Documents/DS/hw03en/reddit_weekends.py:45, in draw_histogram(df, title)
         42 def draw_histogram(df: pd.DataFrame, title: str = None) -> Figure:
         43     # do not modify
         44     fig, ax = plt.subplots(1, 1, dpi=100)
    ---> 45     ret = sns.histplot(data=df, x='comment_count', hue='is_weekend', ax=ax)
         46     if title:
         47         ret.set(title=title)


    File ~/opt/anaconda3/envs/ds/lib/python3.9/site-packages/seaborn/distributions.py:1438, in histplot(data, x, y, hue, weights, stat, bins, binwidth, binrange, discrete, cumulative, common_bins, common_norm, multiple, element, fill, shrink, kde, kde_kws, line_kws, thresh, pthresh, pmax, cbar, cbar_ax, cbar_kws, palette, hue_order, hue_norm, color, log_scale, legend, ax, **kwargs)
       1427 estimate_kws = dict(
       1428     stat=stat,
       1429     bins=bins,
       (...)
       1433     cumulative=cumulative,
       1434 )
       1436 if p.univariate:
    -> 1438     p.plot_univariate_histogram(
       1439         multiple=multiple,
       1440         element=element,
       1441         fill=fill,
       1442         shrink=shrink,
       1443         common_norm=common_norm,
       1444         common_bins=common_bins,
       1445         kde=kde,
       1446         kde_kws=kde_kws,
       1447         color=color,
       1448         legend=legend,
       1449         estimate_kws=estimate_kws,
       1450         line_kws=line_kws,
       1451         **kwargs,
       1452     )
       1454 else:
       1456     p.plot_bivariate_histogram(
       1457         common_bins=common_bins,
       1458         common_norm=common_norm,
       (...)
       1468         **kwargs,
       1469     )


    File ~/opt/anaconda3/envs/ds/lib/python3.9/site-packages/seaborn/distributions.py:553, in _DistributionPlotter.plot_univariate_histogram(self, multiple, element, fill, common_norm, common_bins, shrink, kde, kde_kws, color, legend, line_kws, estimate_kws, **plot_kws)
        550 for sub_vars, _ in self.iter_data("hue", reverse=True):
        552     key = tuple(sub_vars.items())
    --> 553     hist = histograms[key].rename("heights").reset_index()
        554     bottom = np.asarray(baselines[key])
        556     ax = self._get_axes(sub_vars)


    KeyError: (('hue', False),)



    
![png](reddit_weekends_files/reddit_weekends_15_1.png)
    



```python
tmp_df = df.copy()

# TODO: apply transform to the copied data (don't overwrite original!)
tmp_df["comment_count"] = tmp_df["comment_count"]**2 

# TODO: draw histogram
reddit_weekends.draw_histogram(tmp_df, title="Square")

# TODO: re-run tests
wd, we = reddit_weekends.split_data(tmp_df)
reddit_weekends.tests(wd, we, verbose=True)
```

    p_value:	0.0
    WD normality:	0.0
    WE normality:	0.0
    Variance test:	0.0



    
![png](reddit_weekends_files/reddit_weekends_16_1.png)
    


### Fix 2: the Central Limit Theorem might save us.

The central limit theorem says that if our numbers are large enough, and we look at sample means, then the result should be normal. 
Let's try that: we will combine all weekdays and weekend days from each year/week pair and take the mean of their (non-transformed) counts.

Hints: you can get a “year” and “week number” from the first two values returned by date.isocalendar(). This year and week number will give you an identifier for the week. Use Pandas to group by that value, and aggregate taking the mean. Note: the year returned by isocalendar isn't always the same as the date's year (around the new year). Use the year from isocalendar, which is correct for this.

Check these values for normality and equal variance. Apply a T-test if it makes sense to do so. (Hint: yay!)

We should note that we're subtly changing the question here. It's now something like “do the number of comments on weekends and weekdays for each week differ?”


```python
# TODO: complete this implementation in reddit_weekends.py
clt = reddit_weekends.central_limit_theorem(df)
```


```python
reddit_weekends.draw_histogram(clt, "Central Limit Theorem")

_wd, _we = reddit_weekends.split_data(clt)
_ = reddit_weekends.tests(_wd, _we, verbose=True)
```

    p_value:	0.0
    WD normality:	0.30826
    WE normality:	0.15295
    Variance test:	0.20384



    
![png](reddit_weekends_files/reddit_weekends_19_1.png)
    


### Fix 3: a non-parametric test might save us.

The other option we have in our toolkit: a statistical test that doesn't care about the shape of its input as much. The Mann–Whitney U-test does not assume normally-distributed values, or equal variance.

Perform a U-test on the (original non-transformed, non-aggregated) counts. Note that we should do a two-sided test here, which will match the other analyses. Make sure you get the arguments to the function correct.

Again, note that we're subtly changing the question again. If we reach a conclusion because of a U test, it's something like “it's not equally-likely that the larger number of comments occur on weekends vs weekdays.”


```python
# TODO: complete this implementation in reddit_weekends.py
p_utest = reddit_weekends.mann_whitney_u_test(wd, we)
print(f"Mann-Whitney U-test p-value: {p_utest}")
```

    Mann-Whitney U-test p-value: 8.6244532347343e-53


# Short Answers

1. Which of the four transforms suggested got you the closest to satisfying the assumptions of a T-test?

- Answer: Amonge those transform The Log is satisfying the closest T-Test


2. I gave imprecise English translations of what the by-week test, and the Mann-Whitney test were actually testing. Do the same for the original T-test, and for the transformed data T-test. That is, describe what the conclusion would be if you could reject the null hypothesis in those tests.

- Answer:The null hypothesis for the test is that the probability is 50% that a randomly drawn member of the weekday will exceed a weekend.
 The Mann-Whitney U test between the weekday and weekend data is the nonparametric equivalent of the two sample (the weekday and weekend) t-test. While the t-test makes an assumption about the distribution of a population (i.e. that the sample came from a t-distributed population), 
the Mann Whitney U Test makes no such assumption.



3. Of the four approaches, which do you think actually does a better job of getting an answer for the original question: “are there a different number of Reddit comments posted on weekdays than on weekends?” 
   Briefly explain why. (It's not clear that there is a single correct answer to this question, but there are wrong ones!)

- Answer: In my opinion, the fix 1 (transforming data) can get a better answer. Because for the fix 3 (a non-parametric test), it already has changed the question. Compared with fix 1, the fix 2 (the Central Limit Theorem) can get closed to the satisfying assumptions by using different functions.



4. When are more Reddit comments posted in /r/canada, on average: weekdays or weekends? 

- Answer:In weekday, there are more Reddit comments posted in Canada. Please see the following table. 




```python
df.groupby(["is_weekend"]).sum()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>comment_count</th>
      <th>year</th>
    </tr>
    <tr>
      <th>is_weekend</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>False</th>
      <td>951908</td>
      <td>1050525</td>
    </tr>
    <tr>
      <th>True</th>
      <td>265327</td>
      <td>420612</td>
    </tr>
  </tbody>
</table>
</div>




```python

```
