## About Me

My name is Wasti Khan and this is my website of recent projects

<!-- Upload your own photo and change the path -->

<p style="text-align:center;">
  <img class="img-circle" src="/images/logo.png" width="25%">
</p>

---

## Portfolio

<!-- You can link to other websites, PDFs in this repo, and other pages in this repo -->

_**[Natural language processing 10-Ks to identify risks](analysis_report.ipynb)**_

In this project we answer some questions about what types of firms were hurt more or less by covid. We use the firms' 10-Ks to find possible risks and convert them into measurements. We then use return data to see the correlation between our defined risk measures and firms' returns for key dates around the pandemic
- download_text_files.ipynb does the following
    - Downloads data on the S&P500 firms from wikipedia
    - Creates a folder to store 10-Ks.
    - Downloads 10-Ks for firms in the list using 
        (1) Ticker symbol
        (2) CIK
- measure_risk.ipynb does the following:
    - Defines 5 risk measures
    - Uses near_regex() to find instances where terms defined in our risk measures occurs in each firms' 10-K
    - Sums the value as the risk for each firm and adds it to the dataset
- explore_ugly.ipynb does preliminary analysis that is used in the analysis report
- The analysis_report.ipynb is a summary of our findings. 
    - It amalgamates all the data we need
    - Calculates weekly returns for key dates around the onset of the pandemic
    - Discusses the econonomic reasoning behind our risk measures, why they were chosen and what they hope to capture
    - Uses visualization techniques to explore the correlation between risk values and stock returns around key dates around the onset of the COVID-19 pandemic.
---

_**[Regression Interpretation](Regression_interpretation.md)**_


## Career Objectives

I am currently a senior year student at Lehigh studying Math, Finance and Data Science. I will be continuing at Lehigh as a MS in Financial Engineering candidate. I intend to work as a quantitative analyst in the future.

---

## Hobbies

I make music. You can listen here:<br>
[Spotify](https://open.spotify.com/artist/3nqBuf8SD2i9PhMJjeOGGm)<br>
[SoundCloud](https://soundcloud.com/wasti-farzan-khan/then-i-saw-you)<br>
[A preview of a new project I am currently working on](https://soundcloud.com/wasti-farzan-khan/sept-10-5/s-zW49GxHfy1j?utm_source=clipboard&utm_medium=text&utm_campaign=social_sharing)

---
<p style="font-size:11px">Page template forked from <a href="https://github.com/evanca/quick-portfolio">evanca</a></p>
<!-- Remove above link if you don't want to attibute -->
