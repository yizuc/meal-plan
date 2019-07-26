# Meal plan Retrieval Study

This repository contains the data used in the meal plan user
study, as described in the paper  "[Item Retrieval As Utility
Estimation](https://ti.arc.nasa.gov/publications/52165/download/)".
Please refer to this paper for additional details on the data.

If you use this data in your publication, please include the following
citation:

    @inproceedings{Wolfe18itemretrieval,
     author = {Wolfe, Shawn R. and Zhang, Yi},
     title = {Item Retrieval As Utility Estimation},
     booktitle = {The 41st International ACM SIGIR Conference on Research and Development in Information Retrieval},
     year = {2018},
    } 


## [design](design) subdirectory.
---

The design subdirectory contains the data needed to run the
experiment. It consists several types of data:

  * [Topics](design/topics) subdirectory: Each file consists of a
    separate scenario that described the user need. This information
    was provided to the experimental subject to define the task. The
    file format is of the form `<xxx>.<yyy>.txt`, where `<xxx>` gives
    the core scenario which defines a specific nutritional need, and
    `<yyy>` defines specific elements of
    interest. [Background](design/topics/background.txt) provided
    additional information to inform the test subject.

  * [Meal plans](design/mealplans.csv): This file provides the meal
    plans that could be searched and chosen. It consists of a meal
    plan identifier (`id`), nutritional values and the included dishes
    as either breakfast, lunch, dinner or snack items. `NA` is used as
    a filler when there is no such dish. The dish information is given
    in the file below. 

  * [Dishes](design/dishes.csv): This file provides the information on
    the component dishes of the meal plan, consisting of a dish
    identifier, dish name and aggregate rating from 0 to 5. In
    addition, a picture of the dish was shown when available (but not
    included here). No other information on the component dishes were
    available.

  * [Facets](design/facets.txt): This file defines the facets used
    only by the `Faceted` search paradigm. With the exception of
    rating, each facet is defined as the interval including the lower
    value and not the upper value, e.g., [Low, High), so that facets
    would not have any overlap. In addition, a facet was included
    below the lowest facet value and above the highest facet value
    listed in the file. Rating facets were constructed differently,
    with each given value defining only the lower facet range
    (inclusive), with the high end of the facet unbound. Thus, rating
    facets overlapped.

  * [Standard deviations](design/sd.csv): The calculated standard
    deviations as used in the experiment.

## [responses](responses) subdirectory.
---

The responses subdirectory contains all the user response data
(queries, search results, meal plans chosen and contest sets).

  * [Queries](responses/queries.csv): this file contains all queries
    for every test subject (`worker`) and scenario pair. The
    `iteration` indicates the query in the session, for instance, 2
    indicates the third query in the session (due to 0-based
    numbering). Only the initial queries were analyed in our
    study. `EnhancedMAUT` did not use explicit orders and took single
    attribute values as query input, but for format consistency, are
    recorded as a min and max of a single point. Ascending sort orders
    have '+' prepended; descending sort orders have '-' prepended.

  * Search results: Search results are given as
    [TREC](https://trec.nist.gov)-style runs, with results separated
    by search engine. Each line has the following format:
    `queryid iteration docid rank score run-name`, where:
    1. **queryid** is the query identifier.
    2. **iteration** is the query in the session, using 0-numbering. Only the first query was used in the analysis.
    3. **docid** is the identifier of the meal plan.
    4. **rank** is the rank of the meal plan in the search results.
    5. **score** is the 'score' for meal plan. `SortedBoolean` and `Faceted` do not create scores during the ranking process, so their scores are computed only to give the subsequent ranking. Ties were broken by meal plan identifier.
    6. **run-name** is the search engine that produced the results.
    Docid `0` was used to indicate no results (a pseudo-result, as no
    meal plan was given an identifier of 0).

  * Relevance scores [qrels-first.txt](responses/qrels-first.txt) and
    [qrels-full.txt](responses/qrels-full.txt): The relevance score of
    the meal plans are given as [TREC](https://trec.nist.gov)-style
    qrel files. Each line has the following format:
    `queryid iteration docid rel`, where:
    1. **queryid** is the query identifier.
    2. **iteration** is not used and always 0, regardless of which query in the session returned the result. (The same result may have been returned by more than one query.)
    3. **docid** the identifier of the meal plan.
    4. **rel** is the relevance status of the meal plan, where 1 indicates the meal plan was selected by the test subject.
    [qrels-first.txt](responses/qrels-first.txt) gives only the results
    of the first query, along with the meal plan ultimately selected
    (which is what we used in the study), whereas
    [qrels-full.txt](responses/qrels-full.txt) gives all search results
    viewed by the test subject.

  * Community evaluations
    [community-queries.csv](responses/community-queries.csv) and
    [community-qrels.txt](responses/community-qrels.txt): These are
    the 'community' evaluations, where a search (identifier given by
    `qid`) is used to rank the results from another test subject
    (identifier given by `rid`), given the same scenario. This was
    used to eliminate bias from the search engine used in the study.

  * [Contests](responses/contests.csv): These are the contests that
    determined winning meal plans. Each entry has the following elements:
    1. **judge**: identifier of the judging assignment.
    2. **contestantA** and **contestantB**: identifier of the selection assignment (corresponds to **queryid**).
    3. **entryA** and **entryB**: corresponding selected meal plans of the contestants, matching relevance of qrel files.
    4. **entryC** and **entryD** : additional entries, randomly selected.
    5. **winner**: the winning meal plan of the four entries, as selected by the judge.

## Errata
---

The online experiment used a slightly different subutility formula
than what was used for learning in the paper, namely `1/(1+|d-q|/s)`,
where `d` is the meal plan attribute value, `q` is the query value, and `s`
is the standard deviation for the attribute.

The online implementation treated meal plan dish ratings as a set
rather than a bag. In nearly all cases, the evaluation would be the
same in either case, but for the few meal plans that included a dish
more than once would have slightly inaccurate evaluations for
`EnhancedMAUT`.

The paper excluded certain responses during a period that a bug
affected results, but this data included work during the buggy period
that did not include the specific conditions that triggered this bug.
This slightly changes the calculated results, for instance the head to
head results:

    EnhancedMAUT     Faceted          SortedBoolean
    54                                30
    34               17
                     25               27

Also, a few assignments had minor issues that did not qualitatively
affect the results:

  * 42290, 42292: the results fit the query parameters but do not follow the established default order by meal plan identifier.
  * 40824: the intial query results are correct, but the subsequent query (which was not analyzed) has a slightly incorrect order.

The cause for these is not known.
