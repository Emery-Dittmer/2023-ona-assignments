Assignment 2
================
Emery Dittmer
2023-03-09

# Excercise \#2

For this exercise we are investigating the position that we should sit
on for the bus ride to Fakebook from Downtown San Francisco!

## The Problem

We need to pick an optimal set on the bus. We can talk to people within
a fixed range so we will need to be casreful how we pick the seat.

![Bus Network Illustartion.](Bus.png)

### Data acquisition & preprocessing

We can reduce this problem to a set of coordinates and use X- Y
Cartesian plane to measure distances and proximity. We will simplify the
bus problem slightly by: 1- Ignoring the alley in the bus therefore seat
6 and D are adjacent in this model.

2- Creating a 4x6 grid where seats are either: taken, available or
un-available. The un-available seats do not exist.

``` r
library(ggplot2)
#install.packages("gt")
library(gt)
```

``` r
#These are the seats withi the rows
x_dimention=4
#These are the number of rows on the bus
y_dimention=6

Coords <- data.frame(
   Seat = rep(c(1:x_dimention),times=y_dimention), 
   Row = rep(c(1:y_dimention),each=x_dimention),
   Status=rep('Available',x_dimention*y_dimention)
   )


#set some conditions First un-available (non existent seats)
unavail="Un-Available"
#remove non existant seats on left
Coords$Status[Coords$Row != 5 & Coords$Seat == 1] <- unavail
#remove on right
Coords$Status[(Coords$Row != 5 & Coords$Seat == 4)] <- unavail
#remove driver cabin
Coords$Status[(Coords$Row < 4 & Coords$Seat > 2)] <- unavail
#remove seats over engine
Coords$Status[(Coords$Row == 6 & Coords$Seat > 2)] <- unavail

#now lets mark the occupied or taken seats
taken="Taken"
#first two rows and last row 
Coords$Status[(Coords$Row <3 | Coords$Row >5) & Coords$Status == 'Available' ] <- taken
#seat number on on the left side
Coords$Status[Coords$Seat <2 & Coords$Status == 'Available' ] <- taken
#seat number on on the left side
Coords$Status[Coords$Seat >2 & Coords$Status == 'Available' & Coords$Row >4 ] <- taken
```

### Lets Vislualize

Let’s take a look at our data!

``` r
#plotting the seats
ggplot(data=Coords,aes(x=Seat,y=Row))+
  geom_point(aes(shape = Status, color = Status),size=10)+
  scale_shape_manual(values = c(19,15,4,1))+
  scale_color_manual(values=c("#234F1E", "#FFA500", "#E3242B"))
```

![](Assginement2EmeryDittmer_files/figure-gfm/plot%20seats-1.png)<!-- -->

Looks like we have our bus, the seats available and taken! Now lets
filter our data frame to have only the useful coordiates

``` r
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
Cords_Simple_Status <- Coords %>%
  filter(Status != unavail) %>%
  rename("Y" = "Row", "X"= "Seat")

Cords_Simple <- Cords_Simple_Status[, c("X", "Y")]
```

``` r
#plotting the seats
ggplot(data=Cords_Simple,aes(x=X,y=Y))+
  geom_point()
```

![](Assginement2EmeryDittmer_files/figure-gfm/plot%20seats%202-1.png)<!-- -->
Now lest look at the distance between points to see which seat is the
most central in our network

``` r
library(cluster)
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ tibble  3.1.8     ✔ purrr   0.3.4
    ## ✔ tidyr   1.2.1     ✔ stringr 1.4.1
    ## ✔ readr   2.1.3     ✔ forcats 0.5.2
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
##  Dissimilarities using Euclidean metric
dist_seats <- daisy(Cords_Simple, metric = "euclidean")
#convert matrix to dataframe
dist_seats=as.data.frame(as.matrix(dist_seats))
#bind the status and coords to each row
dist_seats=cbind(Cords_Simple_Status,dist_seats)

#pivot to make into tabular form
dist_seats <- dist_seats %>% 
  pivot_longer(where(is.numeric) & (!contains(c("X","Y"))),names_to = "to_seat_id")

#quick change the name of value to distance
dist_seats <- dist_seats %>%
  rename("Distance" = "value")

#get the from seat id
lcords=nrow(Cords_Simple_Status)
dist_seats <- cbind(from_seat_id = rep(c(1:lcords),each=lcords), dist_seats)

#remove all duplicated rows 55 of them (10 choose 2 is 45)
dist_seats=unique(dist_seats) 
#remove all the self relationships and if the seat is taken
dist_seats <- dist_seats %>% 
  filter(to_seat_id != from_seat_id) %>%
  filter(Status != taken)
```

Now we have the distance between each of the availabel seats and the
taken or occiped seats. We just need to apply the rules of connections
(diagonal, front,back, ect) and we will be able to summarize the table
to get the stregth of each seat based on the connections. We will filter
all of the connections who are further than sqrt 2 away from the current
seat

``` r
library(visNetwork)
library(networkD3)
#get all connections within distance
dist_seats <- dist_seats %>%
  filter(Distance <= sqrt(2)) %>%
  rename(from = from_seat_id , to = to_seat_id)

#crete the edges
edges <- unique(select(dist_seats, from, to))
edges$to <- as.numeric(edges$to)
edges
```

    ##    from to
    ## 1     3  2
    ## 2     3  4
    ## 3     3  5
    ## 4     4  3
    ## 5     4  5
    ## 6     4  6
    ## 7     4  7
    ## 8     4  8
    ## 9     5  3
    ## 10    5  4
    ## 11    5  7
    ## 12    5  8
    ## 13    5  9
    ## 14    7  4
    ## 15    7  5
    ## 16    7  6
    ## 17    7  8
    ## 18    7 10

``` r
#Create the values of the seat values. we are using dist_seats since we dont care about the occupied seats at this point
seat_vals <- dist_seats %>%
  group_by(from) %>%
  summarize(value=n())

nodes <- Cords_Simple_Status
nodes <- rowid_to_column(nodes, "id")
nodes <- nodes %>% 
  left_join(seat_vals, by = c("id" = "from")) %>%
  replace(is.na(.),1)
nodes$name <- as.character(nodes$id)
nodes <- nodes %>% 
  mutate(title = paste("Coords:",paste(X, Y,sep=","),"ID:",id,"size:",value, sep = " ")) %>%
  rename(group = Status,label = name) 



visNetwork(nodes, edges)%>%
  visGroups(groupname="Available", color="#234F1E") %>%
  visGroups(groupname="Available", color="#E3242B")  %>%
  visLegend()
```

![](Assginement2EmeryDittmer_files/figure-gfm/summarize%20table%201-1.png)<!-- -->

Now let’s try to find the measures for each seat \## Centrality Measures

### Degree Centrality

Is the number of links incident upon a node (i.e., the number of ties
that a node has).

``` r
#attach(mtcars)
#relabel seats
seat_labs <- data.frame(
  label=c("A","B","C","D"),
  seat_id=c(3,4,5,7)
   )

seat_vals_table <- seat_vals %>% 
  rename(seat_id = from) %>%
  left_join(seat_labs,by='seat_id') %>%
  rename(Seat = label,Degree_Centrality = value) %>%
  select(-seat_id)

#formaint to make it look nice
seat_vals_table <- seat_vals_table[, c("Seat", "Degree_Centrality")]
seat_vals_table = seat_vals_table[order(-seat_vals_table$Degree_Centrality),]
seat_vals_table %>%gt
```

<div id="mdwrhjultl" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#mdwrhjultl .gt_table {
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

#mdwrhjultl .gt_heading {
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

#mdwrhjultl .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}

#mdwrhjultl .gt_title {
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

#mdwrhjultl .gt_subtitle {
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

#mdwrhjultl .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#mdwrhjultl .gt_col_headings {
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

#mdwrhjultl .gt_col_heading {
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

#mdwrhjultl .gt_column_spanner_outer {
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

#mdwrhjultl .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#mdwrhjultl .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#mdwrhjultl .gt_column_spanner {
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

#mdwrhjultl .gt_group_heading {
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

#mdwrhjultl .gt_empty_group_heading {
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

#mdwrhjultl .gt_from_md > :first-child {
  margin-top: 0;
}

#mdwrhjultl .gt_from_md > :last-child {
  margin-bottom: 0;
}

#mdwrhjultl .gt_row {
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

#mdwrhjultl .gt_stub {
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

#mdwrhjultl .gt_stub_row_group {
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

#mdwrhjultl .gt_row_group_first td {
  border-top-width: 2px;
}

#mdwrhjultl .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#mdwrhjultl .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#mdwrhjultl .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#mdwrhjultl .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#mdwrhjultl .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#mdwrhjultl .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#mdwrhjultl .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#mdwrhjultl .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#mdwrhjultl .gt_footnotes {
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

#mdwrhjultl .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#mdwrhjultl .gt_sourcenotes {
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

#mdwrhjultl .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#mdwrhjultl .gt_left {
  text-align: left;
}

#mdwrhjultl .gt_center {
  text-align: center;
}

#mdwrhjultl .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#mdwrhjultl .gt_font_normal {
  font-weight: normal;
}

#mdwrhjultl .gt_font_bold {
  font-weight: bold;
}

#mdwrhjultl .gt_font_italic {
  font-style: italic;
}

#mdwrhjultl .gt_super {
  font-size: 65%;
}

#mdwrhjultl .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#mdwrhjultl .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#mdwrhjultl .gt_indent_1 {
  text-indent: 5px;
}

#mdwrhjultl .gt_indent_2 {
  text-indent: 10px;
}

#mdwrhjultl .gt_indent_3 {
  text-indent: 15px;
}

#mdwrhjultl .gt_indent_4 {
  text-indent: 20px;
}

#mdwrhjultl .gt_indent_5 {
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

### Closeness centrality

is a way of detecting nodes that are able to spread information very
efficiently through a graph. The closeness centrality of a node measures
its average farness (inverse distance) to all other nodes

### Betweenness centrality

s a way of detecting the amount of influence a node has over the flow of
information in a graph.
