---
title: Performance Guidelines
permalink: /performance-guidelines/
classes: wide
sidebar:
  nav: "sidebar"
---

### <a name="av1800"></a> Consider using `Any()` to determine whether an `IEnumerable<T>` is empty (AV1800) ![](/codingguidelines/assets/images/3.png) ![](/codingguidelines/assets/images/R.png)
When a member or local function returns an `IEnumerable<T>` or other collection class that does not expose a `Count` property, use the `Any()` extension method rather than `Count()` to determine whether the collection contains items. If you do use `Count()`, you risk that iterating over the entire collection might have a significant impact (such as when it really is an `IQueryable<T>` to a persistent store).

**Note:** If you return an `IEnumerable<T>` to prevent changes from calling code as explained in AV1130, and you're developing in .NET 4.5 or higher, consider the new read-only classes.

### <a name="av1820"></a> Only use `async` for low-intensive long-running activities (AV1820) ![](/codingguidelines/assets/images/1.png)
The usage of `async` won't automagically run something on a worker thread like `Task.Run` does. It just adds the necessary logic to allow releasing the current thread, and marshal the result back on that same thread if a long-running asynchronous operation has completed. In other words, use `async` only for I/O bound operations.

### <a name="av1825"></a> Prefer `Task.Run` for CPU-intensive activities (AV1825) ![](/codingguidelines/assets/images/1.png)
If you do need to execute a CPU bound operation, use `Task.Run` to offload the work to a thread from the Thread Pool. Remember that you have to marshal the result back to your main thread manually.

### <a name="av1830"></a> Beware of mixing up `async`/`await` with `Task.Wait` (AV1830) ![](/codingguidelines/assets/images/1.png)
`await` does not block the current thread but simply instructs the compiler to generate a state-machine. However, `Task.Wait` blocks the thread and may even cause deadlocks (see AV1835).

### <a name="av1835"></a> Beware of `async`/`await` deadlocks in single-threaded environments (AV1835) ![](/codingguidelines/assets/images/1.png)
Consider the following asynchronous method:

	private async Task GetDataAsync()
	{
	    string result = await MyData.GetDataAsync();
	    return result;
	}

Now when a button click does this:

	private void OkButtonOnClick(object sender, EventArgs e)
    {
        string data = GetDataAsync().Result;
        Preview(data);
    }

You end up with a deadlock. Why? Because the `Result` property getter will block until the `async` operation has completed, but since an `async` method will automatically marshal the result back to the original thread, they'll be waiting on each other. Read more about this [here](http://blogs.msdn.com/b/pfxteam/archive/2011/01/13/10115163.aspx).

### <a name="crd1800"></a> Implement Dispose() when appropriate (CRD1800) ![](/codingguidelines/assets/images/1.png)
Always clean up unmanaged resource and unsubscribe from events by implementing Dispose() in the class.

### <a name="crd1801"></a> Use `using` or `try-finally` to release unmanaged resources  (CRD1801) ![](/codingguidelines/assets/images/1.png)
Use `using` if the object is IDisposable and the scope of the object is local to the block in which it is declared. Otherwise use `try-finally` to release unmanaged reources.

    using (OpenFileDialog openFileDialog = new OpenFileDialog()) 
    {
        openFileDialog.Show();
    }	
	
	