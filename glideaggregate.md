# GlideAggregate

&nbsp;&nbsp;&nbsp;&nbsp;Recently, I gave an interview, where I was asked to write a script in scripts - background to print priority-wise count of all incident records. And I could not do it. Primarily because I did never use this API for anything else but getting the COUNT. So this is the time for doing what I don't not do and probably you do not as well. So let us dig what this API is.

&nbsp;&nbsp;&nbsp;&nbsp;If you did not get what exactly I was asked to do, follow this steps:

- Navigate to **Incident > All**

![1](./images/1.png)

- Right-click the column **Priority** and select **Group By Priority**.

![2](./images/2.png)

- Your window will display something like this:

![3](./images/3.png)

&nbsp;&nbsp;&nbsp;&nbsp;Here, You will notice:

- Groups are named for the values of the field selected for grouping. For example, in our case, since we grouped by Priority, each group is a Priority's display name.
- The record count for each group appears next to the group name/Priority.
- The total number of items in the list (all groups/Priorities combined) appears near the paging controls in the list.

&nbsp;&nbsp;&nbsp;&nbsp;I had to write a script to print the output as shown in the last screenshot above. If you want to read more about "Grouped lists" you can find it [here](https://docs.servicenow.com/bundle/sandiego-platform-user-interface/page/use/using-lists/concept/c_GroupedLists.html).

## What is GlideAggregate

&nbsp;&nbsp;&nbsp;&nbsp;GlideAggregate is an extension of GlideRecord. It provides the capability to do aggregation (COUNT, SUM, MIN, MAX, AVG). These operations can be done with regular GlideRecord use, but with GlideAggregate, they are optimized around these use cases and offer significant efficiencies.

### Getting the number of records for GlideRecord query

&nbsp;&nbsp;&nbsp;&nbsp;One of the frequently used use case is finding number of records for a a GlideRecord query. This can be achieved using getRowCount for a GlideRecord query, which is not a efficient solution if no operation is performed with the returned records. Alternatively, use of GlideAggreggate can improve the performance significantly. Let us see how to script both the ways:

#### getRowCount

- Copy the below script to scripts - background, and click **Run script** button:

```js
var grInc = new GlideRecord("incident")
grInc.query()
gs.info(grInc.getRowCount())
```

![4](./images/4.png)

- You should see the output similar to the following:

![5](./images/5.png)

- Now, Navigate to **Incident > All**

![1](./images/1.png)

- Right-click the value **New** for column **State** and select **Show Matching**.

![7](./images/7.png)

- Your window will display something like below, Observe that the condition State = New has been added to the filter and the total number of records with this filter:

![8](./images/8.png)

- Right-click the end of the filter breadcrumb and select **Copy query** from the context menu:

![9](./images/9.png)

- Now, let us modify the script as below to use encoded query copied above to filter the records:

```js
var grInc = new GlideRecord("incident")
grInc.addEncodedQuery("state=1")
grInc.query()
gs.info(grInc.getRowCount())
```

- Copy the above script to scripts - background, and click **Run script** button:

![10](./images/10.png)

- You should see the output similar to the following:

![11](./images/11.png)

&nbsp;&nbsp;&nbsp;&nbsp;This is the traditional way of getting number of records for a specific filter. You can dig documentation to know more about [Copy query](https://docs.servicenow.com/bundle/rome-platform-user-interface/page/use/using-lists/task/t_GenEncodQueryStringFilter.html) & [Quick filters](https://docs.servicenow.com/bundle/sandiego-platform-user-interface/page/use/using-lists/concept/c_QuickFilters.html).

#### GlideAggregate

&nbsp;&nbsp;&nbsp;&nbsp;There is alternate way of scripting the same using GlideAggregate that can improve the performance significantly and is more efficient:

- Copy the below script to scripts - background, and click **Run script** button:

```js
var grInc = new GlideAggregate("incident")
grInc.addEncodedQuery("state=1")
grInc.addAggregate("COUNT")
grInc.query()
if (grInc.next()) {
  gs.info(grInc.getAggregate("COUNT"))
}
```

![12](./images/12.png)

- You should see the output similar to the following:

![13](./images/13.png)

&nbsp;&nbsp;&nbsp;&nbsp;In the above script,

- addAggregate() method is used to ask needed information.
- getAggregate() method gets the value of an aggregate from the current record.

### Group records

&nbsp;&nbsp;&nbsp;&nbsp;Now, we can get back to our initial requirement:

- Navigate to **Incident > All**
  ![1](./images/1.png)
- Right-click the column **Priority** and select **Group By Priority**.
  ![2](./images/2.png)
- Your window will display something like this:
  ![3](./images/3.png)

- Copy the below script to scripts - background, and click **Run script** button:

```js
var grInc = new GlideAggregate("incident")
grInc.groupBy("priority")
grInc.addAggregate("COUNT")
grInc.query()
while (grInc.next()) {
  gs.info("Priority: {0} ({1})", [
    grInc.getDisplayValue("priority"),
    grInc.getAggregate("COUNT"),
  ])
}
```

![14](./images/14.png)

- You should see the output similar to the following:

![15](./images/15.png)

&nbsp;&nbsp;&nbsp;&nbsp;In the above script, groupBy() provides the name of a field to use in grouping the aggregates. Alternatively, we can use the second parameter of addAggregate() method to achieve the same result:

- Copy the below script to scripts - background, and click **Run script** button:

```js
var grInc = new GlideAggregate("incident")
grInc.addAggregate("COUNT", "priority")
grInc.query()
while (grInc.next()) {
  gs.info("Priority: {0} ({1})", [
    grInc.getDisplayValue("priority"),
    grInc.getAggregate("COUNT", "priority"),
  ])
}
```

![16](./images/16.png)

- You should see the output similar to the following:

![17](./images/17.png)

### getTotal()

&nbsp;&nbsp;&nbsp;&nbsp;Let us modify our script to introduce another method, getTotal(), which returns the number of records by summing an aggregate:

- Copy the below script to scripts - background, and click **Run script** button:

```js
var grInc = new GlideAggregate("incident")
grInc.addAggregate("COUNT", "priority")
grInc.query()
while (grInc.next()) {
  gs.info("Priority: {0} ({1})", [
    grInc.getDisplayValue("priority"),
    grInc.getAggregate("COUNT", "priority"),
  ])
}
gs.info(grInc.getTotal("COUNT"))
```

![18](./images/18.png)

- You should see the output similar to the following:

![19](./images/19.png)

### groupBy() and COUNT

&nbsp;&nbsp;&nbsp;&nbsp;At this point you should ask if there are two ways to group, what is the difference between them? Let us modify the script a bit to use both groupBy() and COUNT together:

- Copy the below script to scripts - background, and click **Run script** button:

```js
var grInc = new GlideAggregate("incident")
grInc.groupBy("priority")
grInc.groupBy("state")
grInc.addAggregate("COUNT", "priority")
grInc.query()
while (grInc.next()) {
  gs.info("Priority: {0} & State: {1} ({2})", [
    grInc.getDisplayValue("priority"),
    grInc.getDisplayValue("state"),
    grInc.getAggregate("COUNT", "priority"),
  ])
}
gs.info(grInc.getTotal("COUNT"))
```

![20](./images/20.png)

- You should see the output similar to the following:

![21](./images/21.png)

&nbsp;&nbsp;&nbsp;&nbsp;Here We did print number of records for each priority grouped by state. Let us verify the results:

- Navigate to **Incident > All**

![1](./images/1.png)

- Right-click the column **Priority** and select **Group By Priority**.

![2](./images/2.png)

- Your window will display something like this:

![3](./images/3.png)

- Now, click the **arrow icon** next to the Priority 'Critical' to expand the group

![22](./images/22.png)

- Right-click the value **In Progress** for column **State** and select **Show Matching**.

![25](./images/25.png)

- You should see the output similar to the following:

![23](./images/23.png)

![24](./images/24.png)

### Sorting the result

#### orderBy()

&nbsp;&nbsp;&nbsp;&nbsp; Let us introduce another method called orderBy(), which provides the name of a field that should be used to order the aggregates.:

- Copy the below script to scripts - background, and click **Run script** button:

```js
var grInc = new GlideAggregate("incident")
grInc.groupBy("priority")
grInc.groupBy("state")
grInc.addAggregate("COUNT", "priority")
grInc.orderBy("priority")
grInc.orderBy("state")
grInc.query()
while (grInc.next()) {
  gs.info("Priority: {0} & State: {1} ({2})", [
    grInc.getDisplayValue("priority"),
    grInc.getDisplayValue("state"),
    grInc.getAggregate("COUNT", "priority"),
  ])
}
gs.info(grInc.getTotal("COUNT"))
```

![26](./images/26.png)

- You should see the output similar to the following:

![27](./images/27.png)

&nbsp;&nbsp;&nbsp;&nbsp; In the above example, we did sort the list by Priority and state.

#### orderByAggregate()

&nbsp;&nbsp;&nbsp;&nbsp; Another variation of orderBy() is orderByAggregate(), which orders the aggregates based on the specified aggregate and field. Let us slightly modify our script:

- Copy the below script to scripts - background, and click **Run script** button:

```js
var grInc = new GlideAggregate("incident")
grInc.groupBy("priority")
grInc.groupBy("state")
grInc.addAggregate("COUNT", "priority")
grInc.orderByAggregate("count", "priority")
grInc.query()
while (grInc.next()) {
  gs.info("Priority: {0} & State: {1} ({2})", [
    grInc.getDisplayValue("priority"),
    grInc.getDisplayValue("state"),
    grInc.getAggregate("COUNT", "priority"),
  ])
}
gs.info(grInc.getTotal("COUNT"))
```

![28](./images/28.png)

- You should see the output similar to the following:

![29](./images/29.png)

&nbsp;&nbsp;&nbsp;&nbsp; You might have already noticed that the result is now sorted by the number of records.

#### Count isn't the only function

&nbsp;&nbsp;&nbsp;&nbsp; So far we have seen some examples that utilizes COUNT for understanding GlideAggregate APIs, But Count is not the only thing that we can do with it. We can also use SUM, MAX, MIN, and AVG to get the total sum, maximum number, minimum number, and average, respectively. I would like to quote Andrew Barnes as is from his exteremely useful ServiceNow blog post "Understanding GlideAggregate" which can be found [here](https://developer.servicenow.com/blog.do?p=/post/glideaggregate/):

> Count isn’t the only function we can perform with GlideAggregate. Average, Sum, Max, Min are handy when dealing with numbers. Our favorite example table incident has several useful fields that pure counts isn’t wildly useful. Our service desk might want to know the average number of times incidents were modified sys_mod_count or reassigned reassignment_count. The service desk owner can use this information to set a baseline, and then run this every month to see if changes have been an improvement or not.

- Copy the below script to scripts - background, and click **Run script** button:

```js
var grInc = new GlideAggregate("incident")
grInc.groupBy("priority")
grInc.addAggregate("AVG", "reassignment_count")
grInc.addAggregate("AVG", "sys_mod_count")
grInc.query()
while (grInc.next()) {
  gs.info(
    "Incidents with priority {0} had {1} average modifications and {2} average reassignments",
    [
      grInc.priority.getDisplayValue(),
      grInc.getAggregate("AVG", "sys_mod_count"),
      grInc.getAggregate("AVG", "reassignment_count"),
    ]
  )
}
```

![30](./images/30.png)

- You should see the output similar to the following:

![31](./images/31.png)

#### addHaving()

&nbsp;&nbsp;&nbsp;&nbsp;addHaving() adds a "having" element to the aggregate. This method is extermely useful when you want to trigger something if record count crosses the defined threshold or to find duplicate records. You can find the awesome post on snprotips blog by Tim Woodruff [here](https://snprotips.com/blog/rvicenowprotips.com/2015/12/detecting-duplicate-records-with.html) regarding same.

- Copy the below script to scripts - background, and click **Run script** button:

```js
var grInc = new GlideAggregate("incident")
grInc.groupBy("priority")
grInc.addAggregate("COUNT", "sys_mod_count")
grInc.addHaving("COUNT", "sys_mod_count", ">", "10")
grInc.query()
while (grInc.next()) {
  gs.info("Incidents with priority {0} had {1} total modifications", [
    grInc.priority.getDisplayValue(),
    grInc.getAggregate("COUNT", "sys_mod_count"),
  ])
}
```

![32](./images/32.png)

- You should see the output similar to the following:

![33](./images/33.png)

&nbsp;&nbsp;&nbsp;&nbsp;The above script outputs the total number of times particular priority incidents modified, if modification count is greater than 10. Feel free to change the value to something less like 1 or more like 30 and re-execute the script to observe the output.

#### addTrend()

&nbsp;&nbsp;&nbsp;&nbsp;addTrend() method adds a trend for a field. Let us modify the script to display number of incidents by months.

- Copy the below script to scripts - background, and click **Run script** button:

```js
var grInc = new GlideAggregate("incident")
grInc.addTrend("opened_at", "month")
grInc.addAggregate("COUNT")
grInc.query()
while (grInc.next()) {
  gs.info("{0}: {1}", [grInc.getValue("timeref"), grInc.getAggregate("COUNT")])
}
```

![34](./images/34.png)

- You should see the output similar to the following:

![35](./images/35.png)

#### getQuery()

&nbsp;&nbsp;&nbsp;&nbsp;getQuery() method retrieves the query necessary to return the current aggregate.

- Copy the below script to scripts - background, and click **Run script** button:

```js
var grInc = new GlideAggregate("incident")
grInc.addTrend("opened_at", "month")
grInc.addAggregate("COUNT")
grInc.query()
while (grInc.next()) {
  gs.info("{0}: {1}, Query: {2}", [
    grInc.getValue("timeref"),
    grInc.getAggregate("COUNT"),
    grInc.getQuery(),
  ])
}
```

![36](./images/36.png)

- You should see the output similar to the following:

![37](./images/37.png)

---

### How can you learn more?

&nbsp;&nbsp;&nbsp;&nbsp;The best way to learn anything is hands-on exercises. Here is some of the resources which will help provide examples and use cases to furthur enhance your understanding:

- [Observations When Using GlideAggregate with Steven Bell](https://www.youtube.com/watch?v=KmxsVbnAHxk)
- [Community Code Snippets: Pondering the GlideAggregate Object (Advanced) by Steven Bell](https://community.servicenow.com/community?id=community_article&sys_id=4184de51db982348a39a0b55ca961960)
- [Understanding GlideAggregate by Andrew Barnes](https://developer.servicenow.com/blog.do?p=/post/glideaggregate/)
- [Counting with GlideAggregate by Ben Sweetser](https://developer.servicenow.com/blog.do?p=/post/training-glideagg/)
- [Detecting Duplicate Records with GlideAggregate by Tim Woodruff](https://snprotips.com/blog/rvicenowprotips.com/2015/12/detecting-duplicate-records-with.html)
- [How to get the Top 10 values from a table using the GlideAggregate function](https://support.servicenow.com/kb?id=kb_article_view&sysparm_article=KB0745198)
- [ServiceNow API documentation](https://developer.servicenow.com/dev.do#!/reference/api/sandiego/server_legacy/c_GlideAggregateAPI)
- [ServiceNow product documentation](https://docs.servicenow.com/bundle/sandiego-application-development/page/app-store/dev_portal/API_reference/GlideAggregate/concept/c_GlideAggregateAPI.html)
- [GlideAggregate Examples by GarrettNow](https://garrettnow.com/2014/02/28/glideaggregate-examples/)
- [Find duplicate user email (or anything else)](https://www.thiscodeworks.com/find-duplicate-user-email-or-anything-else-blogs-blog-servicenow-community-servicenow-javascript-duplicate/601038407a4fe30014436f5b)
- [Advance glide script in ServiceNow](https://www.learnnowlab.com/advance-glide-script/)
- [Faster API than GlideRecord?](https://snowunderground.com/blog/tag/glideaggregate)
- [GlideAggregate Examples](https://finite-partners.com/byte-2-glideaggregate-examples/)
- [Easily identifying duplicate records in ServiceNow](https://pathwayscg.com/easily-identifying-duplicate-records-in-servicenow/)
- [Background Scripts by snowscrip](http://snowscrip.blogspot.com/2019/03/background-scripts.html)
