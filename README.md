**RACIAL DISPARITIES IN PRISON DISCIPLINE AND PAROLE**

A New York Times investigation found minority inmates in New York State prisons are [punished for violating prison rules] (http://nyti.ms/2gYsvei) at a higher rate than white inmates, a gap that is especially pronounced at certain prisons. The investigation also [found a racial disparity in parole decisions](http://nyti.ms/2gEbBVp) made by the New York Board of Parole particularly among burglars, thieves and other non-violent offenders.

For those interested in exploring the data used in the investigation, here is a detailed explanation of the methodology, along with relevant data files.

It's important to put the data analysis in the proper context. The Times investigation relied primarily on interviews with inmates and expert sources by journalists who have covered the New York justice system for years. The data analysis helped confirm and quantify the findings of this traditional form of reporting, but the issues raised and conclusions in the stories were not based on numbers alone.

It's also important to note, as the stories do, the limits of the data analysis. The underlying data, obtained from the state Department of Corrections via the Freedom of Information Law (FOIL), did not contain some important variables a researcher might want to know about the inmates, and cannot fully explain the reasons for the disparities in discipline and parole beyond showing the extent to which the disparities exist. It also made it difficult to conduct more advanced statistical measures, such as regression analysis.

DISCIPLINE

Our approach was to akin to calculating crime rates. We used disciplinary incident data for a single year as the numerator, and the prison population for the year as the denominator.

We did not rely on one single metric. We looked at how many disciplinary tickets were issued, how many specific violations were listed on each of those tickets, how many of the tickets resulted in solitary confinement, and how much solitary time was sentenced. We looked at all metrics by race and analyzed the data separately using other variables – gender, age, facility and the type of offender.

The disparity in punishment persists in many different ways. There is not one subgroup that drives the difference. In certain prisons, and among certain groups, the gap grows larger or smaller but for the most part, doesn't go away.

In response to a FOIL request, the state provided a log of 59,394 adjudicated disciplinary incidents from calendar year 2015. The table included only Tier II (moderate) and Tier III (severe) incidents for which the inmate was found guilty and punished.

Each incident included a personal identifier for the inmate, which we have converted here to our own identifier to avoid posting personally identifiable information, as well as the place and time of the incident, a list of specific violations the inmate was found guilty of for that incident, and the resulting punishment, including the number of days, if any, the inmate was sentenced to solitary confinement.

The disciplinary data contained no demographic characteristics for inmates. But using the personal identifier, The Times was able to attach a number of key variables by joining the disciplinary data to other databases we had obtained from the state. A key source of this was point-in-time prison "snapshots" that list all inmates currently incarcerated, along with demographic information about the inmate and the crime of imprisonment.

 We used two of these snapshots – one from the middle of 2015, one from the end – to create our population file, which we used as the denominator in our calculations.  While the demographic characteristics of the inmate population do not change much over the course of a single year, we used two different snapshots and averaged them to smooth out any possible anomalies.

In our analysis, we collapsed the race and ethnicity of inmates into four categories – non-Hispanic White, non-Hispanic Black, Hispanic and Other. (Less than five percent of the inmate population is Asian, Native American or unclassified).

We also reshaped the incident file into a relational dataset. The state provided specific violations involved in each incident as columns in the primary table (violation 1, violation 2, etc), which made it difficult to look at the specific offenses for which inmates were being punished. So we converted this into a separate table of violations, where each of 139,759 specific infractions became its own row.

These tables -- the disciplinary tickets and the specific violations - in tandem provided us with raw counts of incidents that could be grouped by race, age, gender, offender type etc.

We paid particular attention to the type of offender, to see if, for example, people in prison for violent offenses were more likely to get punished in prison. We used three different measures. One being the severity of the crime considered the "primary offense" leading to the inmate's imprisonment; second was the overall severity, as measured in the number of total crimes committed and the combined severity of these crimes; and lastly, based on state standards, we placed offenders into three categories used in state reports based on their primary crime: Violent offenders, property criminals, and those considered to have committed "other coercive" offenses, which are mostly lower-levels of violent offenses.

The snapshot data, provided in a file called "inmates" has all of the same demographic fields about inmates so it's easy to write queries to produce a denominator matching the numerator. Users should remember, though, that because the inmates file consists of two snapshots at different points of the year, they should always divide the results by 2 to get a proper population estimate.

To best illustrate how our analysis worked, here are some query examples written in the SQL query language. Users should first import the .csv files provided into a database manager or other statistical software before proceeding.

One thing we observed is that the disparity in rates held for all age groups. To generate the numbers that show this, we can first query the overall inmate population, group by race and age group, and divide the count by 2 to average the two snapshots that comprise our inmate file:

SELECT race,age\_group,COUNT(id)/2 as inmates FROM inmates
GROUP BY 1,2
ORDER BY 1,2


| race | age\_group |  Inmates |
| --- | --- | --- |
| B | 25-29 |  4,384 |
| B | 30-39 |  7,162 |
| B | 40+ |  9,963 |
| B | <25 |  3,736 |
| H | 25-29 |  1,903 |
| H | 30-39 |  3,614 |
| H | 40+ |  4,766 |
| H | <25 |  1,483 |
| O | 25-29 |  278 |
| O | 30-39 |  426 |
| O | 40+ |  623 |
| O | <25 |  256 |
| W | 25-29 |  2,089 |
| W | 30-39 |  3,648 |
| W | 40+ |  5,791 |
| W | <25 |  1,212 |

We then can run the same query on the discipline table to count tickets:

SELECT race, age\_group, COUNT(id) as tickets FROM disciplines
GROUP BY 1,2
ORDER BY 1,2

| crace | age\_group | tickets |
| --- | --- | --- |
| B | 25-29 |  7,358 |
| B | 30-39 |  8,004 |
| B | 40+ |  6,577 |
| B | <25 |  9,483 |
| H | 25-29 |  2,945 |
| H | 30-39 |  4,104 |
| H | 40+ |  3,259 |
| H | <25 |  3,739 |
| O | \N |  1 |
| O | 25-29 |  388 |
| O | 30-39 |  356 |
| O | 40+ |  338 |
| O | <25 |  568 |
| W | 25-29 |  2,951 |
| W | 30-39 |  3,677 |
| W | 40+ |  3,231 |
| W | <25 |  2,375 |

We can then combine the results of these two queries to generate rates:

|   |  White | White |   | Black | Black |   |   |
| --- | --- | --- | --- | --- | --- | --- | --- |
| AGE |  Inmates | Tickets | Rate | Inmates | Tickets | Rate | Diff |
| <25 |  1,212 |  2,375 |  1.96 |  3,736 |  9,483 |  2.54 | 30% |
| 25-29 |  2,089 |  2,951 |  1.41 |  4,384 |  7,358 |  1.68 | 19% |
| 30-39 |  3,648 |  3,677 |  1.01 |  7,162 |  8,004 |  1.12 | 11% |
| 40+ |  5,791 |  3,231 |  0.56 |  9,963 |  6,577 |  0.66 | 18% |
| TOTAL |  12,739 |  12,234 |  0.96 |  25,244 |  31,422 |  1.24 | 30% |

This shows us that the discipline rate is higher for young people whether they are white or Black, but that the gap in rates persists for all age groups. It also shows us that the young inmate population skews Black (3,736/1,212 under 25 vs. 9,963/5,791 for 40+), which contributes to the overall disparity but does not explain it entirely.

We can then run the same queries but limit the results to a specific facility, such as Clinton Correctional Facility, by adding "where nyt\_facility='Clinton'" to the where clause:

|   |  White | White |   | Black | Black |   |   |
| --- | --- | --- | --- | --- | --- | --- | --- |
| AGE |  Inmates | Tickets | Rate | Inmates | Tickets | Rate | Diff |
| <25 |  44 | 71 |  1.63 |  147 | 347 |  2.36 | 45% |
| 25-29 |  66 | 66 |  1.00 |  227 | 391 |  1.73 | 73% |
| 30-39 |  161 | 119 |  0.74 |  410 | 528 |  1.29 | 74% |
| 40+ |  360 | 168 |  0.47 |  512 | 374 |  0.73 | 56% |
| TOTAL |  630 |  424 |  0.67 |  1,295 |  1,640 |  1.27 | 88% |



This shows a much higher level of ticketing disparity than the state as a whole, mostly because whites were punished at lower rates than in other prisons (0.67 rate at Clinton, 0.96 statewide). It also shows, once again, that younger inmates are punished at higher rates than older inmates, and that younger inmates are disproportionately black (when compared to the white inmate population).

We found that the disparity in 2015 punishment seemed to grow with the severity of what we were measuring. Because minority inmates averaged more individual violations than whites each time a ticket was issued, the disparity for violations was greater than the disparity for tickets alone. To see this, let's count violations instead of tickets by joining the discipline and violation tables:

SELECT race, COUNT(id)/2 AS inmates FROM inmates
GROUP BY 1

SELECT race, COUNT(violations.id) as violations FROM violations
INNER JOIN disciplines ON disciplines.incident\_id=violations.incident\_id
GROUP BY 1

|

| Race | Inmates | Violations | Rate | Rate vs White |
| --- | --- | --- | --- | --- |
| B |  25,244 |  76,598 |  3.03 | 47% |
| --- | --- | --- | --- | --- |
| H |  11,765 |  33,113 |  2.81 | 37% |
| --- | --- | --- | --- | --- |
| O |  1,582 |  3,831 |  2.42 |   |
| --- | --- | --- | --- | --- |
| W |  12,739 |  26,217 |  2.06 |   |
| --- | --- | --- | --- | --- |


 The disparity was even greater for tickets that resulted in solitary confinement:

SELECT race, COUNT(id) AS shu\_tickets FROM disciplines
WHERE current\_net\_shu>0
GROUP BY 1
ORDER BY 1



| race | Inmates | shu\_tickets | Rate | Rate vs White |
| --- | --- | --- | --- | --- |
| B |  25,244 |  6,641 |  0.26 | 65% |
| H |  11,765 |  3,074 |  0.26 | 64% |
| O |  1,582 |  302 |  0.19 |   |
| W |  12,739 |  2,029 |  0.16 |   |



These queries are just examples of how we approached the data. Here is documentation for the data files we are posting so readers can write their own queries.



FILES: disciplines.csv

This is a table of 59,394 disciplinary tickets for which a New York State prison inmate was punished in 2015. Each row is a distinct incident, and the incident\_id field joins to the violations table, which includes one or more specific infractions for which the inmate was punished. The file layout, a sample record and field descriptions are below:

| Id | 1 | The primary key for this table |
| --- | --- | --- |
| inmate\_id | 16144 | A unique identifier for individual inmates |
| incident\_id | 45341 | A unique identifier for disciplinary tickets. Join this field to the violations table to see the specific infractions involved with individual incidents |
| Age | 33 | The inmate's age at the time of the incident |
| age\_group | 30-39 | A convenience field for quick age grouping |
| Race | B | W= non-Hispanic White; B= non-Hispanic Black; H=Hispanic; O=Other; |
| Sex | M | M=Male, F=Female |
| primary\_crime | MURDER 2ND | While inmates can be convicted of more than one crime, this is primary crime of conviction. |
| crime\_class | A | New York felonies range from Class A -- the most severe -- to Class E, the least severe. Most crime types are usually placed into the same classification, but some can be bumped up to higher felony levels under certain circumstances. |
| Severity | A1 | This summarizes the severity by incorporating multiple counts and crimes into a single string. For example,. A1B1 means the inmate is serving time for one count of a class A felony and one count of a class B crime ; C2D4+ means two counts of a class C felony, 4 or more counts of a class D felony |
| official\_crime\_type | VFO | This indicates whether the primary crime is, under state standards, a violent offense (VFO), a property offense (PDO), or an "other coercive" offense (CVO), which are mostly lower-severity  (3rd degree or lower) of violent crimes. |
| incident\_date | 1/20/15 | Reported date of incident in prison |
| incident\_time | 1520 | Reported time of incident |
| incident\_facility | UPSTATE SHU | Facility name listed in state records -- this includes the name of the prison and whether the inmate was in a specific program within the prison. For example, some prisons have an annex, or a drug treatment program. |
| facility\_id | 46 | This is a New York Times ID for facilities |
| nyt\_facility | Upstate | This is a New York Times standardized name for facilities |
| program\_id | 121 | This ID is unique for specific programs within prisons |
| tier | 3 | This indicates whether the disciplinary incident was moderate (Tier II) or severe (Tier III) |
| current\_net\_shu | 30 | This shows how much solitary confinement time, if any, was sentenced as a result of this incident. To count tickets where the inmate was sent to solitary, set current\_net\_shu>0 |



Violations.csv

This table lists one or more violations associated with every disciplinary ticket – it joins to the disciplines table via the incident\_id field. The original source data listed each violation horizontally across the disciplinary records. By reshaping the data, it is easier to focus on specific violations. In reshaping, the first violation listed was given a rule\_id of 1, the second, 2, and so on.  The Times analysis found in general that white inmates racked up fewer violations in general, and much fewer in certain categories.



| id | 1 | Primary key for the table |
| --- | --- | --- |
| incident\_id | 45341 | A unique identifier for disciplinary tickets, joins to the disciplines table |
| incident\_date | 1/20/15 | The date of the incident |
| rule\_id | 1 | This shows the order in which the violation was listed on the disciplinary ticket. In queries that simultaneously count tickets and violations, sum all records for violations but for tickets count only those where rule\_id=1. |
| rule | 102.1 | This is administrative code for the rule that was violated -- see http://www.legal-aid.org/media/121933/standards-of-inmate-behavior%20(2).pdf |
| rule\_label | THREATS | This is the description of the violation. See the link above for a more robust description. |





Inmates.csv

This table provides the denominator for rate calculations. It combines the rosters of all inmates in New York prisons on 5/31/2015 and January 1, 2016. Combined, this represents the average population for the system and for individual institutions for 2015. Because this file combines two different inmate rosters, users must divide any counts by 2 to get a proper count of any demographic group being queried.

| Id | 1 | Primary key for the table |
| --- | --- | --- |
| nyt\_facility | Upstate | This is a New York Times standardized name for facilities |
| facility\_id | 46 | This is a New York Times ID for facilities |
| facility\_name | UPSTATE SHU | Facility name listed -- this includes the name of the prison and whether the inmate was in a specific program within the prison. |
| program\_id | 121 | This ID is unique for specific programs within prisons |
| Sex | M | M=Male, F=Female |
| Age | 34 | The inmate's age at the time the snapshot (either the middle or end of 2015) |
| age\_group | 30-39 | A convenience field for quick age grouping |
| Race | B | W= non-Hispanic White; B= non-Hispanic Black; H=Hispanic; O=Other; |
| primary\_crime | MURDER 2ND | While inmates can be convicted of more than one crime, this is the first listed in state records |
| crime\_class | A | New York felonies range from Class A -- the most severe -- to Class E, the least severe. Most crime types are usually the same classification but some can be bumped up under certain circumstances. |
| Severity | A1 | This summarizes the severity by incorporating multiple counts and crimes into a single string. For example,. A1B1 means the inmate is serving time for one count of a class A felony and one count of a class B crime ; C2D4+ means two counts of a class C felony, 4 or more counts of a class D felony |
| official\_crime\_type | VFO | This indicates whether the primary crime is, under state standards, a violent offense (VFO), a property offense (PDO), or an "other coercive" offense(CVO) , which are mostly lower-severity  (3rd degree or lower) of violent crimes. |





PAROLE

There are two primary differences in methodology for the analysis of decisions by the New York Board of Parole. For one, the source data was downloaded from the agency's Web site and not obtained via FOIL; and secondly, the initial analysis showed that the racial disparity in decisions did not persist for every group, but was concentrated in cases involving lower-level offenders.

The board does not provide an easy-to-use download link for anybody that wants to study cases, so instead, the Times programmatically downloaded decisions from the board's [calendar](http://www.doccs.ny.gov/calendar.html). The analysis considered all cases posted from May of 2013 through May of 2016.

That data includes decisions for various hearing types, but the Times focused on the initial hearings parole-eligible inmates have upon completing their minimum sentence. For the bulk of inmates, this hearing represents the earliest possible release date, and success or failure here has a major impact on the amount of time the inmate ultimately serves.

We further whittled down the dataset by eliminating cases where no decision was rendered – listed in the data as "or other" decisions. Often, the case is postponed because of missing paperwork or some other administrative snafu.

This left us with cases where the interview type was listed as "initial" and the decision was actually rendered, listed as either "denied", "open date" – parole granted – or "paroled". We also decided to focus on male inmates. While there was also a racial disparity for women, the overall release rate was much higher, suggesting women have a totally different experience with the Board than men.

Within this core dataset, we at first observed that the disparity was particularly large for offenders who had been convicted of felonies that in New York State are classified as "C", "D" or "E"  -- less serious than "A" or "B" felonies, which are mostly comprised of violent crimes.

When we later attached the state's official crime code table (see page 34, [http://www.doccs.ny.gov/Research/Reports/2016/Statistical\_Overview\_2015\_Discharges.pdf)](http://www.doccs.ny.gov/Research/Reports/2016/Statistical_Overview_2015_Discharges.pdf)), we gained the further insight that the board rarely paroled violent offenders of any race, but that there was a racial disparity among inmates who had committed property crimes.

In the story, we use third-degree burglary as an example of this, but it also applies to inmates doing time for larceny and lower-level robbery charges. These inmates, by the way, comprise a significant share of the board's work. Due to changes in sentencing laws, a growing share of violent offenders are no-longer parole eligible, and if they are, they have to serve more years behind bars before reaching their initial hearing date.

The Times had also requested from the state variables from the Compas system, an algorithm that synthesizes numerous characteristics about each inmate into a set of "risk" scores. These variables may have provided additional clarity, since they include such important factors as the inmate's full criminal history and complete disciplinary record while behind bars. The state, citing inmate confidentiality, refused to release these variables.

Here are some examples of how we queried the data:

To get the overall release rate by race:

SELECT race, COUNT(id) AS hearings, SUM(IF(decision='yes',1,0)) AS releases,  SUM(IF(decision='yes',1,0))/ COUNT(id) AS release\_rate FROM paroles
GROUP BY 1
ORDER BY 1

| race | hearings | releases | release\_rate |
| --- | --- | --- | --- |
| B |  5,874 |  870 | 14.8% |
| H |  3,004 |  475 | 15.8% |
| O |  454 |  104 | 22.9% |
| W |  4,544 |  1,124 | 24.7% |

One way to break this down is by offender type, which shows that most of the disparity occurs among lower-level property offenders:

SELECT race, official\_crime\_type, COUNT(id) AS hearings, SUM(IF(decision='yes',1,0)) AS releases, SUM(IF(decision='yes',1,0))/ COUNT(id) AS release\_rate FROM paroles
GROUP BY 1,2
ORDER BY 2,1

| race | official\_crime\_type | hearings | releases | release\_rate |
| --- | --- | --- | --- | --- |
| B | CVO |  1,135 |  167 | 14.7% |
| H | CVO |  559 |  85 | 15.2% |
| O | CVO |  95 |  20 | 21.1% |
| W | CVO |  811 |  147 | 18.1% |
|   |   |   |   |   |
| B | PDO |  2,663 |  482 | 18.1% |
| H | PDO |  1,487 |  276 | 18.6% |
| O | PDO |  263 |  73 | 27.8% |
| W | PDO |  2,964 |  895 | 30.2% |
|   |   |   |   |   |
| B | VFO |  2,076 |  221 | 10.7% |
| H | VFO |  958 |  114 | 11.9% |
| O | VFO |  96 |  11 | 11.5% |
| W | VFO |  768 |  82 | 10.7% |



FILE: paroles.csv

| id | 1 | Primary key for the table |
| --- | --- | --- |
| age | 40 | Age at time of hearing |
| age\_group | 40+ | A convenience field for quick age grouping |
| race | H | W= white non-Hispanic, B= black non-Hispanic, H=Hispanic, O=Other |
| dins | 2 | Number of  prison terms for this inmate for distinct crimes (does not include prison stints for parole violations) |
| primary\_crime | ATT MURDER-2 | Primary crime listed on the parole calendar |
| crime\_class | B | New York felonies range from Class A -- the most severe -- to Class E, the least severe. Most crime types are usually the same classification but some can be bumped up under certain circumstances. |
| min\_years | 20 | Minimum sentence in years |
| min\_months | 0 | Additional months of minimum sentence |
| max\_years | 40 | Maximum sentence in years (99=Life) |
| max\_months | 0 | Additional months of maximum sentence |
| severity | B1B1D2 | This summarizes the severity by incorporating multiple counts and crimes into a single string. For example,. A1B1 means the inmate is serving time for one count of a class A felony and one count of a class B crime ; C2D4+ means two counts of a class C felony, 4 or more counts of a class D felony |
| official\_crime\_type | VFO | This indicates whether the primary crime is, under state standards, a violent offense (VFO), a property offense (PDO), or an "other coercive" offense9CVO), which are mostly lower-severity  (3rd degree or lower) of violent crimes. |
| decision | NO | Whether the Parole Board voted to release the inmate |
|   |   |   |
