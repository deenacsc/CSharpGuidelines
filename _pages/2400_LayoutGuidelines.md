---
title: Layout Guidelines
permalink: /layout-guidelines/
classes: wide
sidebar:
  nav: "sidebar"
---

### <a name="av2400"></a> Use a common layout (AV2400) ![](/codingguidelines/assets/images/1.png)
- Use an indentation of 4 spaces, and don't use tabs
- Keep one space between keywords like `if` and the expression, but don't add spaces after `(` and before `)` such as: `if (condition == null)`.
- Add a space around operators like `+`, `-`, `==`, etc.
- Always put opening and closing curly braces on a new line.
- Don't indent object/collection initializers and initialize each property on a new line, so use a format like this: 

		var dto = new ConsumerDto
		{
		   Id = 123,
		   Name = "Microsoft",
		   PartnerShip = PartnerShip.Gold,
		   ShoppingCart =
		   {
		       ["VisualStudio"] = 1
		   }
		};

- Don't indent lambda statement blocks and use a format like this:

		methodThatTakesAnAction.Do(x =>
		{ 
		   // do something like this 
		}

- Keep expression-bodied-members on one line. Break long lines after the arrow sign, like this:

		private string GetLongText =>
			"ABC ABC ABC ABC ABC ABC ABC ABC ABC ABC ABC ABC ABC ABC ABC ABC ABC ABC ABC ABC ABC";

- Put the entire LINQ statement on one line, or start each keyword at the same indentation, like this:

		var query =  
		    from product in products  
		    where product.Price > 10  
		    select product;

- Start the LINQ statement with all the `from` expressions and don't interweave them with restrictions.

### <a name="av2402"></a> Order and group namespaces according to the company (AV2402) ![](/codingguidelines/assets/images/3.png)

	// Microsoft namespaces are first
	using System;
	using System.Collections.Generic;
	using System.XML;
	
	// Then any other namespaces in alphabetic order
	using Crd.BusinessRule;
	using Crd.Standard;
	using Telerik.WebControls;
	using Telerik.Ajax;

Using static directives and using alias directives should be written below regular using directives.
Always place these directives at the top of the file, before any namespace declarations (not inside them).

### <a name="av2406"></a> Place members in a well-defined order (AV2406) ![](/codingguidelines/assets/images/1.png)
Maintaining a common order allows other team members to find their way in your code more easily. In general, a source file should be readable from top to bottom, as if reading a book, to prevent readers from having to browse up and down through the code file.

1. Private fields and constants (in a region)
2. Public constants
3. Public static readonly fields
4. Factory methods
5. Constructors and the finalizer
6. Events 
7. Public properties
8. Other methods and private properties in calling order

Declare local functions at the bottom of their containing method bodies (after all executable code).

### <a name="av2410"></a> Use expression-bodied members appropriately (AV2410) ![](/codingguidelines/assets/images/1.png)
Favor expression-bodied member syntax over regular member syntax only when:

- the body consists of a single statement and
- the body fits on a single line.

### <a name="crd2400"></a> Respect existing indentation and formatting of the source file you are working on (CRD2400) ![](/codingguidelines/assets/images/2.png)
Do not automatically reformat existing source without consulting the team lead for that area. When creating a new file, preserve the formatting of the other files in the same package or namespace

### <a name="crd2401"></a> Only use in-line Lambda expressions when they contain a single statement (CRD2401) ![](/codingguidelines/assets/images/1.png)
Avoid multiple statements that require a curly brace or a return statement with in-line expressions. Omit parentheses.

    delegate void SomeDelegate(string someString);

    void MyMethod(SomeDelegate someDelegate)
    {
	}

    // Correct
    MyMethod(name=>MessageBox.Show(name)); 
    // Avoid
    MyMethod((name)=>{Debug.WriteLine(name);MessageBox.Show(name);});




