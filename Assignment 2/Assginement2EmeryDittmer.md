Assignment 2
================
Emery Dittmer
2023-03-09

# Excercise \#2

For this exercise we are investigating the idea of centrality in
networks. We will look at what seat or position that we should sit on
for the bus ride to Fakebook from downtown San Francisco! We can network
with the people in our immediate area but not outside of that.

## The Problem

We need to pick an optimal set on the bus. We can talk to people within
a fixed range (forward, back, side or diagonal) so we will need to be
careful how we pick the seat. Here is an illustration of the problem.
seats A-D are open and avaiable to sit in whereas the number seats 1-6
are open. For now we will assume that the other seats will be filled up.

<center>

![Bus Network Illustartion.](Bus.png)

</center>

## Data acquisition & preprocessing

We can reduce this problem to a set of coordinates and use X- Y
Cartesian plane to measure distances and proximity. We will simplify the
bus problem slightly by: 1- Ignoring the alley in the bus therefore seat
6 and D are adjacent in this model.

2- Creating a 4x6 grid where seats are either: taken, available or
un-available. The un-available seats do not exist.

3-assume a 100% than all seats will be filled/

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ tibble  3.1.8     ✔ purrr   0.3.4
    ## ✔ tidyr   1.2.1     ✔ stringr 1.4.1
    ## ✔ readr   2.1.3     ✔ forcats 0.5.2
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## 
    ## Attaching package: 'igraph'
    ## 
    ## 
    ## The following objects are masked from 'package:purrr':
    ## 
    ##     compose, simplify
    ## 
    ## 
    ## The following object is masked from 'package:tidyr':
    ## 
    ##     crossing
    ## 
    ## 
    ## The following object is masked from 'package:tibble':
    ## 
    ##     as_data_frame
    ## 
    ## 
    ## The following objects are masked from 'package:dplyr':
    ## 
    ##     as_data_frame, groups, union
    ## 
    ## 
    ## The following objects are masked from 'package:stats':
    ## 
    ##     decompose, spectrum
    ## 
    ## 
    ## The following object is masked from 'package:base':
    ## 
    ##     union
    ## 
    ## 
    ## 
    ## Attaching package: 'tidygraph'
    ## 
    ## 
    ## The following object is masked from 'package:igraph':
    ## 
    ##     groups
    ## 
    ## 
    ## The following object is masked from 'package:stats':
    ## 
    ##     filter

### Making a bus coordinate system

### Lets Vislualize

Let’s take a look at our data!

![](Assginement2EmeryDittmer_files/figure-gfm/plot%20seats-1.png)<!-- -->

Looks like we have our bus, the seats available and taken! Now lets
filter our data frame to have only the useful coordinates, or seats that
exist

![](Assginement2EmeryDittmer_files/figure-gfm/plot%20seats%202-1.png)<!-- -->
We have a simplified coordinate system with the existing seats. We will
need all of this information to compute the degree of centrality for
each seat, which we can then filter out.

## Centrality Measure

Centrality indicates the influence of a node in a network. Higher
centrality means higher influence. Therefore for this problem we would
want higher centrality.

### Distance Matrix

We will need to look at the distance between each seat to see which
seats can form connection with others. Ultimately we will find the most
central in our network.

<div id="silzwkuqqh" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#silzwkuqqh .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#silzwkuqqh .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#silzwkuqqh .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}

#silzwkuqqh .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#silzwkuqqh .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#silzwkuqqh .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#silzwkuqqh .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#silzwkuqqh .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#silzwkuqqh .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#silzwkuqqh .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#silzwkuqqh .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#silzwkuqqh .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#silzwkuqqh .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  text-align: left;
}

#silzwkuqqh .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#silzwkuqqh .gt_from_md > :first-child {
  margin-top: 0;
}

#silzwkuqqh .gt_from_md > :last-child {
  margin-bottom: 0;
}

#silzwkuqqh .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#silzwkuqqh .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#silzwkuqqh .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#silzwkuqqh .gt_row_group_first td {
  border-top-width: 2px;
}

#silzwkuqqh .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#silzwkuqqh .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#silzwkuqqh .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#silzwkuqqh .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#silzwkuqqh .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#silzwkuqqh .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#silzwkuqqh .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#silzwkuqqh .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#silzwkuqqh .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#silzwkuqqh .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#silzwkuqqh .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#silzwkuqqh .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#silzwkuqqh .gt_left {
  text-align: left;
}

#silzwkuqqh .gt_center {
  text-align: center;
}

#silzwkuqqh .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#silzwkuqqh .gt_font_normal {
  font-weight: normal;
}

#silzwkuqqh .gt_font_bold {
  font-weight: bold;
}

#silzwkuqqh .gt_font_italic {
  font-style: italic;
}

#silzwkuqqh .gt_super {
  font-size: 65%;
}

#silzwkuqqh .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#silzwkuqqh .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#silzwkuqqh .gt_indent_1 {
  text-indent: 5px;
}

#silzwkuqqh .gt_indent_2 {
  text-indent: 10px;
}

#silzwkuqqh .gt_indent_3 {
  text-indent: 15px;
}

#silzwkuqqh .gt_indent_4 {
  text-indent: 20px;
}

#silzwkuqqh .gt_indent_5 {
  text-indent: 25px;
}
</style>
<table class="gt_table">
  
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" scope="col" id="from_seat_id">from_seat_id</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" scope="col" id="X">X</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" scope="col" id="Y">Y</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col" id="Status">Status</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" scope="col" id="to_seat_id">to_seat_id</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" scope="col" id="Distance">Distance</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td headers="from_seat_id" class="gt_row gt_right">1</td>
<td headers="X" class="gt_row gt_right">2</td>
<td headers="Y" class="gt_row gt_right">1</td>
<td headers="Status" class="gt_row gt_left">Taken</td>
<td headers="to_seat_id" class="gt_row gt_right">2</td>
<td headers="Distance" class="gt_row gt_right">1.000000</td></tr>
    <tr><td headers="from_seat_id" class="gt_row gt_right">1</td>
<td headers="X" class="gt_row gt_right">2</td>
<td headers="Y" class="gt_row gt_right">1</td>
<td headers="Status" class="gt_row gt_left">Taken</td>
<td headers="to_seat_id" class="gt_row gt_right">3</td>
<td headers="Distance" class="gt_row gt_right">2.000000</td></tr>
    <tr><td headers="from_seat_id" class="gt_row gt_right">1</td>
<td headers="X" class="gt_row gt_right">2</td>
<td headers="Y" class="gt_row gt_right">1</td>
<td headers="Status" class="gt_row gt_left">Taken</td>
<td headers="to_seat_id" class="gt_row gt_right">4</td>
<td headers="Distance" class="gt_row gt_right">3.000000</td></tr>
    <tr><td headers="from_seat_id" class="gt_row gt_right">1</td>
<td headers="X" class="gt_row gt_right">2</td>
<td headers="Y" class="gt_row gt_right">1</td>
<td headers="Status" class="gt_row gt_left">Taken</td>
<td headers="to_seat_id" class="gt_row gt_right">5</td>
<td headers="Distance" class="gt_row gt_right">3.162278</td></tr>
    <tr><td headers="from_seat_id" class="gt_row gt_right">1</td>
<td headers="X" class="gt_row gt_right">2</td>
<td headers="Y" class="gt_row gt_right">1</td>
<td headers="Status" class="gt_row gt_left">Taken</td>
<td headers="to_seat_id" class="gt_row gt_right">6</td>
<td headers="Distance" class="gt_row gt_right">4.123106</td></tr>
    <tr><td headers="from_seat_id" class="gt_row gt_right">1</td>
<td headers="X" class="gt_row gt_right">2</td>
<td headers="Y" class="gt_row gt_right">1</td>
<td headers="Status" class="gt_row gt_left">Taken</td>
<td headers="to_seat_id" class="gt_row gt_right">7</td>
<td headers="Distance" class="gt_row gt_right">4.000000</td></tr>
  </tbody>
  
  
</table>
</div>

This is just a sample, but the table overall contains all the distances
between seats.

Now we have the distance between each of the availabel seats and the
taken or occiped seats. We just need to apply the rules of connections
(diagonal, front,back, ect) and we will be able to summarize the table
to get the stregth of each seat based on the connections. We will filter
all of the connections who are further than sqrt 2 away from the current
seat

![](Assginement2EmeryDittmer_files/figure-gfm/unnamed-chunk-1-1.png)<!-- -->

Now let’s try to find the measures for each seat \## Centrality Measures

### Degree Centrality

Is the number of links incident upon a node (i.e., the number of ties
that a node has).

<div id="nogzwumvjt" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#nogzwumvjt .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#nogzwumvjt .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#nogzwumvjt .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}

#nogzwumvjt .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#nogzwumvjt .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#nogzwumvjt .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#nogzwumvjt .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#nogzwumvjt .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#nogzwumvjt .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#nogzwumvjt .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#nogzwumvjt .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#nogzwumvjt .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#nogzwumvjt .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  text-align: left;
}

#nogzwumvjt .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#nogzwumvjt .gt_from_md > :first-child {
  margin-top: 0;
}

#nogzwumvjt .gt_from_md > :last-child {
  margin-bottom: 0;
}

#nogzwumvjt .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#nogzwumvjt .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#nogzwumvjt .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#nogzwumvjt .gt_row_group_first td {
  border-top-width: 2px;
}

#nogzwumvjt .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#nogzwumvjt .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#nogzwumvjt .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#nogzwumvjt .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#nogzwumvjt .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#nogzwumvjt .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#nogzwumvjt .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#nogzwumvjt .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#nogzwumvjt .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#nogzwumvjt .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#nogzwumvjt .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#nogzwumvjt .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#nogzwumvjt .gt_left {
  text-align: left;
}

#nogzwumvjt .gt_center {
  text-align: center;
}

#nogzwumvjt .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#nogzwumvjt .gt_font_normal {
  font-weight: normal;
}

#nogzwumvjt .gt_font_bold {
  font-weight: bold;
}

#nogzwumvjt .gt_font_italic {
  font-style: italic;
}

#nogzwumvjt .gt_super {
  font-size: 65%;
}

#nogzwumvjt .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#nogzwumvjt .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#nogzwumvjt .gt_indent_1 {
  text-indent: 5px;
}

#nogzwumvjt .gt_indent_2 {
  text-indent: 10px;
}

#nogzwumvjt .gt_indent_3 {
  text-indent: 15px;
}

#nogzwumvjt .gt_indent_4 {
  text-indent: 20px;
}

#nogzwumvjt .gt_indent_5 {
  text-indent: 25px;
}
</style>
<table class="gt_table">
  
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col" id="Seat">Seat</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" scope="col" id="Degree_Centrality">Degree_Centrality</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td headers="Seat" class="gt_row gt_left">B</td>
<td headers="Degree_Centrality" class="gt_row gt_right">5</td></tr>
    <tr><td headers="Seat" class="gt_row gt_left">C</td>
<td headers="Degree_Centrality" class="gt_row gt_right">5</td></tr>
    <tr><td headers="Seat" class="gt_row gt_left">D</td>
<td headers="Degree_Centrality" class="gt_row gt_right">5</td></tr>
    <tr><td headers="Seat" class="gt_row gt_left">A</td>
<td headers="Degree_Centrality" class="gt_row gt_right">3</td></tr>
  </tbody>
  
  
</table>
</div>

We can validate this with the igraph package wich has a built in
functionality for centrality degree

<div id="nazldqqwra" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#nazldqqwra .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#nazldqqwra .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#nazldqqwra .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}

#nazldqqwra .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#nazldqqwra .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#nazldqqwra .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#nazldqqwra .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#nazldqqwra .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#nazldqqwra .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#nazldqqwra .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#nazldqqwra .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#nazldqqwra .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#nazldqqwra .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  text-align: left;
}

#nazldqqwra .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#nazldqqwra .gt_from_md > :first-child {
  margin-top: 0;
}

#nazldqqwra .gt_from_md > :last-child {
  margin-bottom: 0;
}

#nazldqqwra .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#nazldqqwra .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#nazldqqwra .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#nazldqqwra .gt_row_group_first td {
  border-top-width: 2px;
}

#nazldqqwra .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#nazldqqwra .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#nazldqqwra .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#nazldqqwra .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#nazldqqwra .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#nazldqqwra .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#nazldqqwra .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#nazldqqwra .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#nazldqqwra .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#nazldqqwra .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#nazldqqwra .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#nazldqqwra .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#nazldqqwra .gt_left {
  text-align: left;
}

#nazldqqwra .gt_center {
  text-align: center;
}

#nazldqqwra .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#nazldqqwra .gt_font_normal {
  font-weight: normal;
}

#nazldqqwra .gt_font_bold {
  font-weight: bold;
}

#nazldqqwra .gt_font_italic {
  font-style: italic;
}

#nazldqqwra .gt_super {
  font-size: 65%;
}

#nazldqqwra .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#nazldqqwra .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#nazldqqwra .gt_indent_1 {
  text-indent: 5px;
}

#nazldqqwra .gt_indent_2 {
  text-indent: 10px;
}

#nazldqqwra .gt_indent_3 {
  text-indent: 15px;
}

#nazldqqwra .gt_indent_4 {
  text-indent: 20px;
}

#nazldqqwra .gt_indent_5 {
  text-indent: 25px;
}
</style>
<table class="gt_table">
  
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col" id="Seat">Seat</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" scope="col" id="Centrality_Degree">Centrality_Degree</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td headers="Seat" class="gt_row gt_left">B</td>
<td headers="Centrality_Degree" class="gt_row gt_right">5</td></tr>
    <tr><td headers="Seat" class="gt_row gt_left">C</td>
<td headers="Centrality_Degree" class="gt_row gt_right">5</td></tr>
    <tr><td headers="Seat" class="gt_row gt_left">D</td>
<td headers="Centrality_Degree" class="gt_row gt_right">5</td></tr>
    <tr><td headers="Seat" class="gt_row gt_left">A</td>
<td headers="Centrality_Degree" class="gt_row gt_right">3</td></tr>
  </tbody>
  
  
</table>
</div>

    ## Warning: Removed 6 rows containing missing values (`geom_point()`).

![](Assginement2EmeryDittmer_files/figure-gfm/degree%20central%20igraph-1.png)<!-- -->

There is agreement between our calculations and the calculations for the
package therefore we can use them!

### Closeness centrality

is a way of detecting nodes that are able to spread information very
efficiently through a graph. The closeness centrality of a node measures
its average farness (inverse distance) to all other nodes

<div id="dmsqzdfbsb" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#dmsqzdfbsb .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#dmsqzdfbsb .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#dmsqzdfbsb .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}

#dmsqzdfbsb .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#dmsqzdfbsb .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#dmsqzdfbsb .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#dmsqzdfbsb .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#dmsqzdfbsb .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#dmsqzdfbsb .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#dmsqzdfbsb .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#dmsqzdfbsb .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#dmsqzdfbsb .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#dmsqzdfbsb .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  text-align: left;
}

#dmsqzdfbsb .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#dmsqzdfbsb .gt_from_md > :first-child {
  margin-top: 0;
}

#dmsqzdfbsb .gt_from_md > :last-child {
  margin-bottom: 0;
}

#dmsqzdfbsb .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#dmsqzdfbsb .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#dmsqzdfbsb .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#dmsqzdfbsb .gt_row_group_first td {
  border-top-width: 2px;
}

#dmsqzdfbsb .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#dmsqzdfbsb .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#dmsqzdfbsb .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#dmsqzdfbsb .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#dmsqzdfbsb .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#dmsqzdfbsb .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#dmsqzdfbsb .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#dmsqzdfbsb .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#dmsqzdfbsb .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#dmsqzdfbsb .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#dmsqzdfbsb .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#dmsqzdfbsb .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#dmsqzdfbsb .gt_left {
  text-align: left;
}

#dmsqzdfbsb .gt_center {
  text-align: center;
}

#dmsqzdfbsb .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#dmsqzdfbsb .gt_font_normal {
  font-weight: normal;
}

#dmsqzdfbsb .gt_font_bold {
  font-weight: bold;
}

#dmsqzdfbsb .gt_font_italic {
  font-style: italic;
}

#dmsqzdfbsb .gt_super {
  font-size: 65%;
}

#dmsqzdfbsb .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#dmsqzdfbsb .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#dmsqzdfbsb .gt_indent_1 {
  text-indent: 5px;
}

#dmsqzdfbsb .gt_indent_2 {
  text-indent: 10px;
}

#dmsqzdfbsb .gt_indent_3 {
  text-indent: 15px;
}

#dmsqzdfbsb .gt_indent_4 {
  text-indent: 20px;
}

#dmsqzdfbsb .gt_indent_5 {
  text-indent: 25px;
}
</style>
<table class="gt_table">
  
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col" id="Seat">Seat</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" scope="col" id="Closeness_Degree">Closeness_Degree</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td headers="Seat" class="gt_row gt_left">B</td>
<td headers="Closeness_Degree" class="gt_row gt_right">0.07142857</td></tr>
    <tr><td headers="Seat" class="gt_row gt_left">C</td>
<td headers="Closeness_Degree" class="gt_row gt_right">0.07142857</td></tr>
    <tr><td headers="Seat" class="gt_row gt_left">A</td>
<td headers="Closeness_Degree" class="gt_row gt_right">0.06250000</td></tr>
    <tr><td headers="Seat" class="gt_row gt_left">D</td>
<td headers="Closeness_Degree" class="gt_row gt_right">0.06250000</td></tr>
  </tbody>
  
  
</table>
</div>

    ## Warning: Removed 6 rows containing missing values (`geom_point()`).

![](Assginement2EmeryDittmer_files/figure-gfm/closeness%20centrality-1.png)<!-- -->

### Betweenness centrality

s a way of detecting the amount of influence a node has over the flow of
information in a graph.

<div id="ixrluzcmhe" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#ixrluzcmhe .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#ixrluzcmhe .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#ixrluzcmhe .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}

#ixrluzcmhe .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#ixrluzcmhe .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#ixrluzcmhe .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#ixrluzcmhe .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#ixrluzcmhe .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#ixrluzcmhe .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#ixrluzcmhe .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#ixrluzcmhe .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#ixrluzcmhe .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#ixrluzcmhe .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  text-align: left;
}

#ixrluzcmhe .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#ixrluzcmhe .gt_from_md > :first-child {
  margin-top: 0;
}

#ixrluzcmhe .gt_from_md > :last-child {
  margin-bottom: 0;
}

#ixrluzcmhe .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#ixrluzcmhe .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#ixrluzcmhe .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#ixrluzcmhe .gt_row_group_first td {
  border-top-width: 2px;
}

#ixrluzcmhe .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#ixrluzcmhe .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#ixrluzcmhe .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#ixrluzcmhe .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#ixrluzcmhe .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#ixrluzcmhe .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#ixrluzcmhe .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#ixrluzcmhe .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#ixrluzcmhe .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#ixrluzcmhe .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#ixrluzcmhe .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#ixrluzcmhe .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#ixrluzcmhe .gt_left {
  text-align: left;
}

#ixrluzcmhe .gt_center {
  text-align: center;
}

#ixrluzcmhe .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#ixrluzcmhe .gt_font_normal {
  font-weight: normal;
}

#ixrluzcmhe .gt_font_bold {
  font-weight: bold;
}

#ixrluzcmhe .gt_font_italic {
  font-style: italic;
}

#ixrluzcmhe .gt_super {
  font-size: 65%;
}

#ixrluzcmhe .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#ixrluzcmhe .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#ixrluzcmhe .gt_indent_1 {
  text-indent: 5px;
}

#ixrluzcmhe .gt_indent_2 {
  text-indent: 10px;
}

#ixrluzcmhe .gt_indent_3 {
  text-indent: 15px;
}

#ixrluzcmhe .gt_indent_4 {
  text-indent: 20px;
}

#ixrluzcmhe .gt_indent_5 {
  text-indent: 25px;
}
</style>
<table class="gt_table">
  
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col" id="Seat">Seat</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" scope="col" id="Betweenness_Degree">Betweenness_Degree</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td headers="Seat" class="gt_row gt_left">A</td>
<td headers="Betweenness_Degree" class="gt_row gt_right">14.000000</td></tr>
    <tr><td headers="Seat" class="gt_row gt_left">B</td>
<td headers="Betweenness_Degree" class="gt_row gt_right">9.033333</td></tr>
    <tr><td headers="Seat" class="gt_row gt_left">C</td>
<td headers="Betweenness_Degree" class="gt_row gt_right">8.600000</td></tr>
    <tr><td headers="Seat" class="gt_row gt_left">D</td>
<td headers="Betweenness_Degree" class="gt_row gt_right">3.266667</td></tr>
  </tbody>
  
  
</table>
</div>

    ## Warning: Removed 6 rows containing missing values (`geom_point()`).

![](Assginement2EmeryDittmer_files/figure-gfm/betweeness%20centrality-1.png)<!-- -->

### Comparison between all 3!

<div id="xqfwbdpmup" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#xqfwbdpmup .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#xqfwbdpmup .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#xqfwbdpmup .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}

#xqfwbdpmup .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#xqfwbdpmup .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#xqfwbdpmup .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#xqfwbdpmup .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#xqfwbdpmup .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#xqfwbdpmup .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#xqfwbdpmup .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#xqfwbdpmup .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#xqfwbdpmup .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#xqfwbdpmup .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  text-align: left;
}

#xqfwbdpmup .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#xqfwbdpmup .gt_from_md > :first-child {
  margin-top: 0;
}

#xqfwbdpmup .gt_from_md > :last-child {
  margin-bottom: 0;
}

#xqfwbdpmup .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#xqfwbdpmup .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#xqfwbdpmup .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#xqfwbdpmup .gt_row_group_first td {
  border-top-width: 2px;
}

#xqfwbdpmup .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#xqfwbdpmup .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#xqfwbdpmup .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#xqfwbdpmup .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#xqfwbdpmup .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#xqfwbdpmup .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#xqfwbdpmup .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#xqfwbdpmup .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#xqfwbdpmup .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#xqfwbdpmup .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#xqfwbdpmup .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#xqfwbdpmup .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#xqfwbdpmup .gt_left {
  text-align: left;
}

#xqfwbdpmup .gt_center {
  text-align: center;
}

#xqfwbdpmup .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#xqfwbdpmup .gt_font_normal {
  font-weight: normal;
}

#xqfwbdpmup .gt_font_bold {
  font-weight: bold;
}

#xqfwbdpmup .gt_font_italic {
  font-style: italic;
}

#xqfwbdpmup .gt_super {
  font-size: 65%;
}

#xqfwbdpmup .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#xqfwbdpmup .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#xqfwbdpmup .gt_indent_1 {
  text-indent: 5px;
}

#xqfwbdpmup .gt_indent_2 {
  text-indent: 10px;
}

#xqfwbdpmup .gt_indent_3 {
  text-indent: 15px;
}

#xqfwbdpmup .gt_indent_4 {
  text-indent: 20px;
}

#xqfwbdpmup .gt_indent_5 {
  text-indent: 25px;
}
</style>
<table class="gt_table">
  
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col" id="Seat">Seat</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" scope="col" id="Centrality_Degree">Centrality_Degree</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr class="gt_group_heading_row">
      <th colspan="2" class="gt_group_heading" scope="colgroup" id="Betweeness">Betweeness</th>
    </tr>
    <tr class="gt_row_group_first"><td headers="Betweeness  Seat" class="gt_row gt_left">A</td>
<td headers="Betweeness  Centrality_Degree" class="gt_row gt_right">14.00000000</td></tr>
    <tr><td headers="Betweeness  Seat" class="gt_row gt_left">B</td>
<td headers="Betweeness  Centrality_Degree" class="gt_row gt_right">9.03333333</td></tr>
    <tr><td headers="Betweeness  Seat" class="gt_row gt_left">C</td>
<td headers="Betweeness  Centrality_Degree" class="gt_row gt_right">8.60000000</td></tr>
    <tr><td headers="Betweeness  Seat" class="gt_row gt_left">D</td>
<td headers="Betweeness  Centrality_Degree" class="gt_row gt_right">3.26666667</td></tr>
    <tr class="gt_group_heading_row">
      <th colspan="2" class="gt_group_heading" scope="colgroup" id="Closesness">Closesness</th>
    </tr>
    <tr class="gt_row_group_first"><td headers="Closesness  Seat" class="gt_row gt_left">B</td>
<td headers="Closesness  Centrality_Degree" class="gt_row gt_right">0.07142857</td></tr>
    <tr><td headers="Closesness  Seat" class="gt_row gt_left">C</td>
<td headers="Closesness  Centrality_Degree" class="gt_row gt_right">0.07142857</td></tr>
    <tr><td headers="Closesness  Seat" class="gt_row gt_left">A</td>
<td headers="Closesness  Centrality_Degree" class="gt_row gt_right">0.06250000</td></tr>
    <tr><td headers="Closesness  Seat" class="gt_row gt_left">D</td>
<td headers="Closesness  Centrality_Degree" class="gt_row gt_right">0.06250000</td></tr>
    <tr class="gt_group_heading_row">
      <th colspan="2" class="gt_group_heading" scope="colgroup" id="Centrality Degree">Centrality Degree</th>
    </tr>
    <tr class="gt_row_group_first"><td headers="Centrality Degree  Seat" class="gt_row gt_left">B</td>
<td headers="Centrality Degree  Centrality_Degree" class="gt_row gt_right">5.00000000</td></tr>
    <tr><td headers="Centrality Degree  Seat" class="gt_row gt_left">C</td>
<td headers="Centrality Degree  Centrality_Degree" class="gt_row gt_right">5.00000000</td></tr>
    <tr><td headers="Centrality Degree  Seat" class="gt_row gt_left">D</td>
<td headers="Centrality Degree  Centrality_Degree" class="gt_row gt_right">5.00000000</td></tr>
    <tr><td headers="Centrality Degree  Seat" class="gt_row gt_left">A</td>
<td headers="Centrality Degree  Centrality_Degree" class="gt_row gt_right">3.00000000</td></tr>
  </tbody>
  
  
</table>
</div>
