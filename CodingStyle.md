C# Coding Style
===============

General Rules
1. We use [Allman style](http://en.wikipedia.org/wiki/Indent_style#Allman_style) braces, where each brace begins on a new line. However, we enforce braces even on single line statements. One exception is that a `using` statement is permitted to be nested within another `using` statement by starting on the following line at the same indentation level, even if the nested `using` contains a controlled block.
2. We use one tab for indentation.
3. We use `m_camelCase` for internal and private fields and use `readonly` where possible. Prefix internal and private instance fields with `m_`, static fields with `s_`, constant fields with `k_` and thread static fields with `t_`. When used on static fields, `readonly` should come after `static` (e.g. `static readonly` not `readonly static`).  Public fields should be used sparingly and should use PascalCasing with no prefix when used.
4. We avoid `this.` unless absolutely necessary.
5. We always specify the visibility, even if it's the default (e.g.
   `private string m_foo` not `string m_foo`). Visibility should be the first modifier (e.g.
   `public abstract` not `abstract public`).
6. Namespace imports should be specified at the top of the file, *outside* of
   `namespace` declarations, and should be sorted by library/package. Namespaces starting with `System.*`  are to be placed on top of all others.
7. Avoid more than one empty line at any time. For example, do not have two
   blank lines between members of a type.
8. Avoid spurious free spaces.
   For example avoid `if ( someVar == 0 )...`, where the dots mark the spurious free spaces.
   Consider enabling "View White Space (Ctrl+R, Ctrl+W)" or "Edit -> Advanced -> View White Space" if using Visual Studio to aid detection.
9. We only use `var` when the type is explicitly named on the right-hand side, typically due to either `new` or an explicit cast, e.g. `var stream = new FileStream( ... )` not `var stream = OpenStandardInput()`.
10. We use language keywords instead of BCL types (e.g. `int, string, float` instead of `Int32, String, Single`, etc) for both type references as well as method calls (e.g. `int.Parse` instead of `Int32.Parse`). See issue [#13976](https://github.com/dotnet/runtime/issues/13976) for examples.
11. We use PascalCasing to name all our constant local variables and fields. The only exception is for interop code where the constant value should exactly match the name and value of the code you are calling via interop.
12. We use PascalCasing for all method names, including local functions.
13. We use ```nameof( ... )``` instead of ```"..."``` whenever possible and relevant. More about [nameof()](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/nameof).
14. Fields should be specified at the top within type declarations.
15. When using labels (for goto), indent the label one less than the current indentation.
16. When using #if #else #elif #endif, indent the expression one less than the current indentation.
17. When using a single-statement if, we follow these conventions:
    - Never use single-line form (for example: `if ( source == null ) throw new ArgumentNullException( "source" );`)
    - Always use braces, even when statement body is a single line.    
18. Make all internal and private types static or sealed unless derivation from them is required.  As with any implementation detail, they can be changed if/when derivation is required in the future.

Whitespaces
1. A maximum of one statement per line.
2. A maximum of one assignment per statement.
3. Indentation using 1 tab.
4. Column limit: 150
5. Line break before opening brace.
6. Line break between closing brace and else.
7. Braces used even when optional.
8. Space after if/for/while etc., and after commas.
9. Space after an opening parenthesis and before closing.
10. Space after an opening square bracket and before closing.
11. Space between a unary operator and its operand. One space between the operator and each operand of all other operators.
12. Line continuations are indented.
13. Line breaks with braces (e.g. list initializers, lambdas, object initializers, etc) do not count as continuations.
14. For function definitions and calls, if the arguments do not all fit on one line they should be broken up onto multiple lines, with each subsequent line aligned with the first argument. If there is not enough room for this, arguments may instead be placed on subsequent lines with a four space indent.

An [EditorConfig](https://editorconfig.org "EditorConfig homepage") file (`.editorconfig`) has been provided at the root of the runtime repository, enabling C# auto-formatting conforming to the above guidelines.

### Example Enum:

```
enum Days
{
	Saturday,
	Sunday,
	Monday,
	...
};
```

### Example Preprocessor Symbols and Conditionals:

```
#define DEBUG
#define FAST

class MyClass
{
#if DEBUG
	int func( int x )
	{
	#if FAST
		...
	#else
		...
	#endif
	}
#endif
}
```

### Example File:

``ObservableLinkedList.cs:``

```C#
using System;
using System.Collections;
using System.Collections.Generic;
using System.Collections.Specialized;
using System.ComponentModel;
using System.Diagnostics;
using Microsoft.Win32;

namespace System.Collections.Generic
{
	public partial class ObservableLinkedList<T> : INotifyCollectionChanged, INotifyPropertyChanged
	{
		private ObservableLinkedListNode<T> m_head;
		private int m_count;
		
		public int Count 
		{
			get { return m_count; }
		}
		
		public event NotifyCollectionChangedEventHandler CollectionChanged;

		public ObservableLinkedList( IEnumerable<T> items )
		{
			if ( items == null )
			{
				throw new ArgumentNullException( nameof( items ) );
			}

			foreach ( T item in items )
			{
				AddLast( item );
			}
		}

		public ObservableLinkedListNode AddLast( T value )
		{
			var node = new LinkedListNode<T>( this, value );
			
			InsertNodeBefore( m_head, node );
		}

		protected virtual void OnCollectionChanged( NotifyCollectionChangedEventArgs e )
		{
			NotifyCollectionChangedEventHandler handler = CollectionChanged;
			
			if ( handler != null )
			{
				handler( this, e );
			}
		}

		private void InsertNodeBefore( LinkedListNode<T> node, LinkedListNode<T> newNode )
		{
			...
		}

		...
	}
}
```

``ObservableLinkedList.Node.cs:``

```C#
using System;

namespace System.Collections.Generics
{
	partial class ObservableLinkedList<T>
	{
		public class ObservableLinkedListNode
		{
			private readonly ObservableLinkedList<T> m_parent;
			private readonly T m_value;
			
			public T Value
			{
				get { return m_value; }
			}

			internal ObservableLinkedListNode( ObservableLinkedList<T> parent, T value )
			{
				Debug.Assert( parent != null );

				m_parent = parent;
				m_value = value;
			}
		}

		...
	}
}
```
