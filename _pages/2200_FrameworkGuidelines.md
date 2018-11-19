---
title: Framework Guidelines
permalink: /framework-guidelines/
classes: wide
sidebar:
  nav: "sidebar"
---

### <a name="av2201"></a> Use C# type aliases instead of the types from the `System` namespace (AV2201) ![](/assets/images/1.png) ![](/assets/images/R.png)
For instance, use `object` instead of `Object`, `string` instead of `String`, and `int` instead of `Int32`. These aliases have been introduced to make the primitive types first class citizens of the C# language, so use them accordingly.
**Exception:** When referring to static members of those types, it is custom to use the full CLS name, e.g. `Int32.Parse()` instead of `int.Parse()`. The same applies to members that need to specify the type they return, e.g. `ReadInt32`, `GetUInt16`. 

### <a name="av2202"></a> Prefer language syntax over explicit calls to underlying implementations (AV2202) ![](/assets/images/1.png) ![](/assets/images/A.png) ![](/assets/images/R.png)
Language syntax makes code more concise. The abstractions make later refactorings easier (and sometimes allow for extra optimizations).

Prefer:

	(string, int) tuple = ("", 1);

rather than:

	ValueTuple<string, int> tuple = new ValueTuple<string, int>("", 1);

Prefer:

	DateTime? startDate;

rather than:

	Nullable<DateTime> startDate;

Prefer:

	if (startDate != null) ...

rather than:

	if (startDate.HasValue) ...

Prefer:

	if (startDate > DateTime.Now) ...

rather than:

	if (startDate.HasValue && startDate.Value > DateTime.Now) ...

Prefer:

	(DateTime startTime, TimeSpan duration) tuple1 = GetTimeRange();
	(DateTime startTime, TimeSpan duration) tuple2 = GetTimeRange();

	if (tuple1 == tuple2) ...

rather than:

	if (tuple1.startTime == tuple2.startTime && tuple1.duration == tuple2.duration) ...

### <a name="av2210"></a> Build with the highest warning level (AV2210) ![](/assets/images/1.png) ![](/assets/images/A.png)
Configure the development environment to use **Warning Level 4** for the C# compiler, and enable the option **Treat warnings as errors** . This allows the compiler to enforce the highest possible code quality.

### <a name="av2220"></a> Avoid LINQ for simple expressions (AV2220) ![](/assets/images/3.png) ![](/assets/images/R.png) ![](/assets/images/A.png)
Rather than:

	var query = from item in items where item.Length > 0 select item;

prefer the use of extension methods from the `System.Linq` namespace:

	var query = items.Where(item => item.Length > 0);

Since LINQ queries should be written out over multiple lines for readability, the second example is a bit more compact.

### <a name="av2221"></a> Use lambda expressions instead of anonymous methods (AV2221) ![](/assets/images/2.png) ![](/assets/images/R.png)

Lambda expressions provide a more elegant alternative for anonymous methods. So instead of:

	Customer customer = Array.Find(customers, delegate(Customer customer)
	{
		return customer.Name == "Tom";
	});

use a lambda expression:

	Customer customer = Array.Find(customers, customer => customer.Name == "Tom");

Or even better:

	var customer = customers.Where(customer => customer.Name == "Tom").FirstOrDefault();

### <a name="av2230"></a> Do not use `dynamic` keyword (AV2230) ![](/assets/images/1.png)  ![](/assets/images/A.png)
The `dynamic` keyword has been introduced for working with dynamic languages. Using it introduces a serious performance bottleneck because the compiler has to generate some complex Reflection code.

### <a name="av2235"></a> Favor `async`/`await` over `Task` continuations (AV2235) ![](/assets/images/1.png)  ![](/assets/images/A.png)
Using the new C# 5.0 keywords results in code that can still be read sequentially and also improves maintainability a lot, even if you need to chain multiple asynchronous operations. For example, rather than defining your method like this:

	public Task<Data> GetDataAsync()
	{
	  return MyData.FetchDataAsync()
	    .ContinueWith(t => new Data(t.Result));
	}

define it like this:

	public async Task<Data> GetDataAsync()
	{
	  string result = await MayData.FetchDataAsync();
	  return new Data(result);
	}
	
### <a name="crd2200"></a> Use `string.Empty` instead of "" (CRD2200) ![](/assets/images/1.png)  ![](/assets/images/R.png)
	
	  // Don't
     string name = "";

     // Do
     string name = string.Empty;
	 
### <a name="crd2201"></a> Defensively query for a type using pattern matching (CRD2201) ![](/assets/images/1.png) ![](/assets/images/R.png)
Avoid explicit casting and use pattern matching to avoid exceptions and improve readability.

	if (user is RemoteUser remoteUser)
	{
	}
	 
### <a name="crd2202"></a> Use `StringBuilder` class to build  strings over several lines of code or within a loop (CRD2202) ![](/assets/images/1.png) 
You would end up creating temporary strings in memory if you used either + style concatenation or String.Concat. Refer to this [blog](http://geekswithblogs.net/johnsperfblog/archive/2005/05/27/40777.aspx) for more information.
