&nbsp;&nbsp;&nbsp;&nbsp;Recently, I gave an interview, where I was asked to write a script in scripts - background to print priority-wise count of all incident records. And I could not do it. Primarily because I did never use this API for anything else but getting the COUNT and I was not sure about getting column values. So this is the time for learning what I don't not know and probably you do not as well. So let us dig what this API is.

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

- addAggregate() method adds an aggregate to a database query.
- getAggregate() method gets the value of an aggregate from the current record.

### Getting

### Where can you learn more?

&nbsp;&nbsp;&nbsp;&nbsp;Here is some of the resources which will help provide examples and use cases to furthur enhance your understanding:

- [ServiceNow Developer site documentation](https://developer.servicenow.com/dev.do#!/reference/api/sandiego/server_legacy/c_GlideAggregateAPI)
- [Understanding GlideAggregate by Andrew Barnes](https://developer.servicenow.com/blog.do?p=/post/glideaggregate/)
- [ServiceNow product documentation](https://docs.servicenow.com/bundle/sandiego-application-development/page/app-store/dev_portal/API_reference/GlideAggregate/concept/c_GlideAggregateAPI.html)
- [Observations When Using GlideAggregate with Steven Bell](https://www.youtube.com/watch?v=KmxsVbnAHxk)
- [GlideAggregate Examples by GarrettNow](https://garrettnow.com/2014/02/28/glideaggregate-examples/)
