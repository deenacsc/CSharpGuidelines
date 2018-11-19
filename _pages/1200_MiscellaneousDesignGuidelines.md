---
title: Miscellaneous Design Guidelines
permalink: /misc-design-guidelines/
classes: wide
sidebar:
  nav: "sidebar"
---

### <a name="av1200"></a> Throw exceptions rather than returning some kind of status value (AV1200) ![](/assets/images/2.png)
A code base that uses return values to report success or failure tends to have nested if-statements sprinkled all over the code. Quite often, a caller forgets to check the return value anyway. Structured exception handling has been introduced to allow you to throw exceptions and catch or replace them at a higher layer. In most systems it is quite common to throw exceptions whenever an unexpected situation occurs.

### <a name="av1202"></a> Provide a rich and meaningful exception message text (AV1202) ![](/assets/images/2.png)
The message should explain the cause of the exception, and clearly describe what needs to be done to avoid the exception.

### <a name="av1205"></a> Throw the most specific exception that is appropriate (AV1205) ![](/assets/images/3.png)
For example, if a method receives a `null` argument, it should throw `ArgumentNullException` instead of its base type `ArgumentException`.

### <a name="crd1200"></a> Always throw the original exception (CRD1200) ![](/assets/images/1.png)
In a catch statement that throws an exception, always throw the original exception (or another exception constructed from the original exception) to maintain the stack location of the original error
	
	try 
    {
    }
    catch (ArgumentOutOfRangeException exception) 
    {
        MessageBox.Show(exception.Message);
        throw;
    }

### <a name="av1210"></a> Don't swallow errors by catching generic exceptions (AV1210) ![](/assets/images/1.png) ![](/assets/images/A.png)
Catch only exceptions for which you have explicit handling. Avoid swallowing errors by catching non-specific exceptions, such as `Exception`, `SystemException`, and so on, in application code. Only in top-level code, such as a last-chance exception handler, you should catch a non-specific exception for logging purposes and a graceful shutdown of the application.

### <a name="av1215"></a> Properly handle exceptions in asynchronous code (AV1215) ![](/assets/images/2.png)
When throwing or handling exceptions in code that uses `async`/`await` or a `Task` remember the following two rules:
- Exceptions that occur within an `async`/`await` block and inside a `Task`'s action are propagated to the awaiter.
- Exceptions that occur in the code preceding the asynchronous block are propagated to the caller.

### <a name="crd1201"></a>Avoid defining event-handling delegates. Instead use `EventHandler<T>`(CRD1201) ![](/assets/images/1.png)
This provides a layer of protection on the delegate instance by preventing anyone from resetting the delegate and its invocation lists. Users can only add or remove susbcribers.
	
Consider the following example:

	public delegate void RunDelegate();
	public event RunDelegate Run;
	
	public  void Subscribe()
	{	
	Run += () => Console.WriteLine("I'm running");
	}

Anyone can write the below code to reset the earlier subscription:

	Run = () => Console.WriteLine("I'm sleeping") ; 
	
We can avoid this by using EventHandler:

	public EventHandler<EventArgs> Run;
	
	public  void Subscribe()
	{		
	// EventHandlers do not support Run = () => Console.WriteLine("I'm sleeping") ;
	Run += () => Console.WriteLine("I'm running");	
	}

### <a name="av1220"></a> Always check an event handler delegate for `null` (AV1220) ![](/assets/images/1.png) ![](/assets/images/R.png)
An event that has no subscribers is `null`. So before invoking, always make sure that the delegate list represented by the event variable is not `null`.
Invoke using the null conditional operator, because it additionally prevents conflicting changes to the delegate list from concurrent threads.

	event EventHandler<NotifyEventArgs> Notify;

	protected virtual void OnNotify(NotifyEventArgs args)
	{
		Notify?.Invoke(this, args);
	}

### <a name="av1225"></a> Use a protected virtual method to raise each event (AV1225) ![](/assets/images/2.png) ![](/assets/images/A.png)
Complying with this guideline allows derived classes to handle a base class event by overriding the protected method. The name of the protected virtual method should be the same as the event name prefixed with `On`. For example, the protected virtual method for an event named `TimeChanged` is named `OnTimeChanged`.
**Note:** Derived classes that override the protected virtual method are not required to call the base class implementation. The base class must continue to work correctly even if its implementation is not called.

### <a name="av1230"></a> Consider providing property-changed events (AV1230) ![](/assets/images/3.png)
Consider providing events that are raised when certain properties are changed. Such an event should be named `PropertyChanged`, where `Property` should be replaced with the name of the property with which this event is associated
**Note:** If your class has many properties that require corresponding events, consider implementing the `INotifyPropertyChanged` interface instead. It is often used in the [Presentation Model](http://martinfowler.com/eaaDev/PresentationModel.html) and [Model-View-ViewModel](http://msdn.microsoft.com/en-us/magazine/dd419663.aspx) patterns.

### <a name="av1235"></a> Don't pass `null` as the `sender` argument when raising an event (AV1235) ![](/assets/images/1.png) ![](/assets/images/A.png)
Often an event handler is used to handle similar events from multiple senders. The sender argument is then used to get to the source of the event. Always pass a reference to the source (typically `this`) when raising the event. Furthermore don't pass `null` as the event data parameter when raising an event. If there is no event data, pass `EventArgs.Empty` instead of `null`.
**Exception:** On static events, the sender argument should be `null`.

### <a name="av1240"></a> Use generic constraints if applicable (AV1240) ![](/assets/images/2.png)
Instead of casting to and from the object type in generic types or methods, use `where` constraints or the `as` operator to specify the exact characteristics of the generic parameter. For example:

	class SomeClass  
	{}
	
	// Don't  
	class MyClass  
	{
		void SomeMethod(T t)  
		{  
			object temp = t;  
			SomeClass obj = (SomeClass) temp;  
		}  
	}
	
	// Do  
	class MyClass where T : SomeClass  
	{
		void SomeMethod(T t)  
		{  
			SomeClass obj = t;  
		}  
	}

### <a name="crd1202"></a> Always prefer using the generics flavor (CRD1202) ![](/assets/images/1.png)
If a class or a method offers both generic and non-generic flavors, always prefer using the generics flavor. This prevents unnecessary boxing and unboxing and also performs compile time validation.

	// Do
	List<int> list1 = new List<int>();
	// No boxing, no casting:
	list1.Add(3);
	// Compile-time error:
	// list1.Add("It is raining in Redmond.");
	
	// Don't
	System.Collections.ArrayList list = new System.Collections.ArrayList();
	// Add an integer to the list.
	list.Add(3);
	// Add a string to the list. This will compile, but may cause an error later.
	list.Add("It is raining in Redmond.");
	int t = 0;
	// This causes an InvalidCastException to be returned.
	foreach (int x in list)
	{
     t += x;
	}
	
### <a name="crd1203"></a> Use explicit interface implementation on all methods when implementing a generic interface that derives from a non generic interface (CRD1203) ![](/assets/images/2.png)
When implementing a generic interface that derives from an equivalent non-generic interface (such as IEnumerable<T>) use explicit interface implementation on all methods, and implement the non-generic methods by delegating to the generic ones
	
	class MyCollection<T> : IEnumerable<T> 
    {
        IEnumerator<T> IEnumerable<T>.GetEnumerator() 
        { 
        }
        
        IEnumerator IEnumerable.GetEnumerator() 
        {
            IEnumerable<T> enumerable = this;
            return enumerable.GetEnumerator();
        }
    }	
	
### <a name="av1250"></a> Evaluate the result of a LINQ expression before returning it (AV1250) ![](/assets/images/1.png) ![](/assets/images/A.png)
Consider the following code snippet

	public IEnumerable<GoldMember> GetGoldMemberCustomers()
	{
		const decimal GoldMemberThresholdInEuro = 1_000_000;
		
		var query = 
			from customer in db.Customers
			where customer.Balance > GoldMemberThresholdInEuro
			select new GoldMember(customer.Name, customer.Balance);
		
		return query;  
	}

Since LINQ queries use deferred execution, returning `query` will actually return the expression tree representing the above query. Each time the caller evaluates this result using a `foreach` loop or similar, the entire query is re-executed resulting in new instances of `GoldMember` every time. Consequently, you cannot use the `==` operator to compare multiple `GoldMember` instances. Instead, always explicitly evaluate the result of a LINQ query using `ToList()`, `ToArray()` or similar methods.

### <a name="av1251"></a> Do not use `this` and `base` prefixes unless it is required (AV1251) ![](/assets/images/1.png)
In a class hierarchy, it is not necessary to know at which level a member is declared to use it. Refactoring derived classes is harder if that level is fixed in the code.

### <a name="crd1204"></a> Use auto properties if applicable (CRD1204) ![](/assets/images/1.png) ![](/assets/images/R.png)
Avoid explicit properties that do nothing except to access a member variable. Use automatic properties instead

	// Don't
    class MyClass 
    {
        int number;

        public int Number [assembly: InternalsVisibleTo(
        {
            get 
            {
                return number;
            }
            set 
            {
                number = value;
            }
        }
    }

    // Do 
    class MyClass 
    {
        public int Number 
        {
            get; set;
        }
    }
	
### <a name="crd1205"></a> Strings that will be presented to the end users should never be hardcoded (CRD1205) ![](/assets/images/1.png) ![](/assets/images/R.png)
Always create resource entries for all the strings that will be presented to the end users.

### <a name="crd1206"></a> Do not manually edit any machine generated code (CRD1206) ![](/assets/images/3.png)
Manual changes made to `Designer.cs` or `.resx` or similar files will be overwritten the next time the code is generated. Make changes to the original `.cs` files in such cases.

### <a name="crd1207"></a> Avoid friend assemblies (CRD1207) ![](/assets/images/2.png)	
Use friend assemblies only for unit test projects as they increase inter-assembly coupling.

### <a name="crd1208"></a> Avoid using late bindings (CRD1208) ![](/assets/images/2.png)
Do not use late-binding invocation when early-binding is possible. In other words don’t use reflection.