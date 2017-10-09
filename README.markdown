# Code Review Basis

Standards depicted here are the standards that will be used when code reviews are given and developers are expected to follow and adhere to these code standards. During reviews, these standards will be the basis for general code organization. Developers are accountable for knowing these code standards.

# These Are Never Final

Standards are meant to be grown, evolved, and challenged at every necessary turn. If you feel that the standards do not fit with a code base you are using, or that something should be changed. Please consult someone. You probably know who.

Note: The standards noted below are intended to be used for iOS development. Mac OS development standards are not necessarily handled within this document, but these may be extended for usage on Mac as well.

# Naming Conventions

Read Apple’s official [Coding Guidelines for Cocoa](http://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html), then read Matt Gallagher’s fantastic [write-up](http://www.cocoawithlove.com/2009/06/method-names-in-objective-c.html).

## Other Quick Points

- Objective C is very wordy. Embrace the verboseness.
- Category method’s should be prefixed with the three letter prefix and then a underscore for that project to avoid any future naming conflicts.

# Constants and Macros

## Macros

Macros should be used with discretion. Longer macros should require more discretion.

Stable and verified preprocessor items will be available from the committee in a repository-stored library.

## Constants

When required to define preprocessor constants, constants are expected to be defined descriptively, using a k prefix for constants. However, using static constants is preferred.

```objc
#define kMaximumArrayLength 7
```

Please use static definitions of objects to maintain that constants are typed and evaluated at compile time rather than at runtime.

```objc
static const CGFloat kFloatValue = 2.f;
static NSString *const kConstantStringValue = @"Constant Value";
```

# Namespacing

Objective-C does not have native support for namespacing. Instead, Apple has recommended a naming-based namespacing method. Namespacing should be handled by defining a naming abbreviation that would be appended to the beginning of any defined classes or C level structs.

```objc
CG = Core Graphics  
NS = NextStep  
UI = UIKit
All classes created should be namespaced by a three letter prefix, as Apple reserves the right to all two letter prefixes.
```

# Expressions

Expressions should include proper spacing for all characters providing whitespace between all necessary items.

```objc
// General  
NSInteger value = 22 + 30 * 4 + (200 - 30);

// NOT  
NSInteger messyValue=22+30*4+(200-30);
```

## Property/Setter Assignment

Dot notation is the preferred method for setting properties.

```objc
// Preferred
object.value = 2;

// Still ok. Not as preferred.
[object setValue:2];
```

Additionally, using setters and getters is encouraged rather than using the ivar directly.

```objc
// PREFERRED
self.objectName = [[NSObject alloc] init];

// NOT OK
_objectName = [[NSObject alloc] init];
```

# Control Blocks

Control blocks all should follow the pattern of hugged control block curly braces. Spacing should be between all items within any control block declaration.

```objc
if (parameter) {
    // Logic
}
```

## If Statements

Basic

If statements should always use curly braces that hug the control block. If statement spacing should be handled with spacing between keywords, conditions, and control blocks.

```objc
if (i < 0) {
    // Logic
}
```

If statements should never be written on a single line or without curly braces. Doing so produces hard to maintain code. If a developer was to add logic to a single line control block, a mistake could be made which would result in additional time or possible spaghetti code while trying to update or fix.

```objc
// DO NOT
if (i < 0) // Logic
if (i < 0) { /* Logic */ }
if (i < 0)
    // Logic
```

## Multiple Conditional If Statements

Using the Supreme Court’s definition of obscenity, “You will know it when you see it”, keep multiple conditional if statements sane. If it’s getting long, create boolean expressions outside the if block.

## Else Blocks

When if blocks require else statements or else if statements, else blocks should behave and appear similar to if statements. Control block curly braces should hug else statements on the same line, and control block endings that result with an else statement should follow the same pattern.

```objc
if (/* Conditions */) {
    // Logic
} else {
    // Logic
}
```

## Else If Blocks

When else statements are followed with an immediate if statement, if statements should follow the same rules as a singular if statement.

```objc
// Code
} else if ( /* Conditions */ ) {
    // Logic
}
```

## For Statements

### For Loop

For statements should always use curly braces that hug the control block. For statements are expected to use any indexes defined and declared within the for loop.

```objc
for (NSUInteger i = 0; i < objectLength; i++) {
    // Logic
}
```

### Fast Enumeration (NSFastEnumeration) – For Each Loop

For each blocks should be handled similarly to the standard for control blocks. **Fast Enumeration is preferred** to iteration based on index loops (it’s faster).

```
for (NSString *currentString in stringList) {
    // Logic
}
```

## Switch

Switch statements should avoid using magic numbers as the case variables. typedef `NS_ENUM` should be used as clang will throw errors if elements are forgotten.

```objc
switch (switchValue) {
  // preferred
    case NRDIsThisThingOn:
        // Code
        break;
    // not so good
    case 1:
        // Code
        break;
    // required
    default:
      break;
}
```

# Variable Typing

## Primitives

Primitives can be defined in their C based forms, or in their Cocoa-based forms. When implementing in objective-C based source files, always follow Cocoa namespaced variable types.

```objc
int = NSInteger
unsigned int = NSUInteger
float = CGFloat
```

In the event that more control is needed in the byte size of the variables, the base primitives may be used. Use only when necessary, as the Objective-C based primitives are device-optimized.

## Pointers

Pointer typing is based on Objective-C based classes or any pointers to C based structures. Defined pointers in Objective-C source files should be denoted with the * symbol hugging the variable name.

```objc
NSObject *objectName;
```

## Blocks

- Blocks should be typedef’d when used as properties or in method signatures.

```objc
typedef BlockType (void)(^)(arguments);
```

- It’s encouraged that blocks are not declared within a method signature, but are delcared beforehand. Exceptions should be made for UIView animations or exceptionally short blocks.
- Blocks in method signatures should always be the last argument(s).

```objc
// SAD PANDA
- (void)methodWithArgument:(^)(arguments) otherArgument:(NSInteger)steak;

// MR PANDA IS THE WIN
- (void)methodWithArgument:(NSInteger)beef otherArgument:(^)(arguments);

// SUPER HAPPY MAGIC FUNTIME PANDA!
- (void)methodWithArgument:(NSInteger)beef otherArgument:(BlockType)block;
```

## Nullability annotations

Whenever possible, try to use nullability annotations. The base premise can be found at Apple’s Swift Blog on [Nullability and Objective-C](https://developer.apple.com/swift/blog/?id=25).

## Lightweight generics.

Xcode 7 introduced lightweight generics. The release notes state:

> Lightweight generics now allow you to specify type information for collection classes such as NSArray, NSSet, and NSDictionary. The type information improves Swift access when you bridge from Objective-C, and simplifies the code you have to write.

Lightweight generics should be used whenever using a collection type of NSArray, NSSet, or NSDictionary.

```objc
// WITHOUT GENERICS
@property (nonatomic, strong, nonnull) NSArray *dates;

// WITH GENERICS
@property (nonatomic, strong, nonnull) NSArray<NSDate *> *dates;
```

## Enumeration/Enumerated Type/enum

Enumerations should be used whenever it is necessary to have multiple states within a given type. When enumerations will be used across different class types it is recommended to typedef these enumerations. Enumeration declaration should have its parentheses hugging the type definition.

Enumeration values can be specified to start with any value, and are expected to be used in order to avoid *magic numbers*. Enumeration values follow the same naming patterns of their enumeration type.

Enumeration value definitions should include a comma ‘,’ at the end of their declaration/definition. On the final enumeration value, a comma should still be included to allow for easier updates in later code bases.

Defined enums should be placed into specified C-based source documents. Doing so should allow for minimization of the inclusion of extraneous header files.

```objc
typedef NS_ENUM(NSUInteger, EnumerationState) {
    EnumerationStateOne = 2,
    EnumerationStateTwo,
    EnumerationStateThree,
};
```

# Forward Declarations

Forward declarations should be used when needing to import classes inside of header files. Doing so ensures that the compiler cannot generate a circular compilation cycle, which causes an infinite loop. Class Forward Declarations should appear before interface declaration, and after the source import. Multiple forward declarations should be on multiple lines, not on a single one.

```objc
// Import
#import <UIKit/UIKit.h>

// Objective-C
@class ForwardDeclaredClass;
@class SecondForwardDeclaredClass;

// Interface

```

# Source Files

## Class Files

### Header

Header files written for Objective-C should use the .h extension. All classes should have one class interface per header, no more than one should be defined.

### Source

Source files written for Objective-C should use the .m extension. All source files should be limited to a single class. In the event that private methods are required, please define an interface within the source.

### C++ Source

Source files written for Objective-C++ should use the .mm extension. Implementation of an Objective-C++ source file should be handled no different from the original source file.

### Protocol

Delegate protocols should be included in the header file for the class with the delegate property. Other protocols should be put in their own file.

# Class Interface & Implementation

## Class Extensions

All properties and methods that are not meant to be publicly consumed should be declared in a class extension.

.h
```objc
@interface ClassName
@property (nonatomic, readonly, strong) NSArray *array;
```

.m
```objc
@interface ClassName ()

// Property is redeclared as readwrite & mutable
@property (nonatomic, readwrite, strong) NSMutableArray *array;

- (void)hiddenMethodName;

@end
```

## Instance Variables

### Synthesize / Dynamic

With the release of Xcode 4.4, @synthesize blocks are unnecessary and uncool. The compiler will auto-generate the @synthesize variable = _variable for you.

## Property

Memory management should always be explicitly declared within the property.

Setting a property with readonly will force the synthesis to only create a getter.

```objc
@property (nonatomic, strong, readonly) NSString *newString;
@property (nonatomic, assign) UIImage *newImage;
@property (nonatomic, copy) UIView *newView;
```

## IBOutlet

Properties that will connect to the interface accessed using the interface builder must be declared as an IBOutlet. IBOutlet should be declared on the property declaration, not the ivar. IBOutlets should almost always be `weak`.

```objc
@property (nonatomic, weak) IBOutlet UILabel *currentLabel;
``` 

`IBOutletCollection` should be declared as strong.

## Methods

### Definition Implementation

When the definition is made, the beginning code block curly brace should appear one the line after the line of the method name. Definitions and declarations that go beyond one line should be indented.

All logic within the method block should be indented by one whitespacing indent group. This group being 4 spaces.

```objc
// Single line
- (void)singleLineMethod
{
    // Code
}
```

### Multiple Line Method Calls

Multiple lines should be handled using Xcode’s standard formatting so that the parameter indicator of the colon ‘:’ all line up on each line.

```objc
[NSTimer scheduledTimerWithTimeInterval:1.f
                                 target:self
                               selector:@selector(timerCallback)
                               userInfo:nil
                                repeats:YES];
```
If a method call is short and does not wrap, the preference is to leave it on one line.

```objc
// Preferred
[myObject setColor:[UIColor redColor] animated:NO];

// Less okay
[myObject setColor:[UIColor redColor]
          animated:NO];
```

# Protocols

All protocols should inherit from the NSObject protocol or one that does.

## Optional

Protocol methods that are optional must be declared with the optional preprocessor directive. All classes calling out to a class implementing the protocol must call the responder method to verify that it is an accessible method.

```objc
id<DeclaredProtocol> objectWithDeclaredProtocol;

if ([objectWithDeclaredProtocol respondsToSelector:@selector(protocolMethod)]) {
    [objectWithDeclaredProtocol protocolMethod];
}
```

# Documentation

In each header file, there should be a class-level comment, as well as comments for methods, properties, constants, enums, etc. These comments should be use the styling that is built into Xcode `⌥⌘/`.

## NIBs

### UI Component Naming

When using IB – use Xcode specific labels to help describe the function of the object. This is to avoid having several UILabels that have no context.

# Implementation Standards

## Delegate Instances

Delegate instances are to be declared with properties using weak, to avoid creating a memory leak due to a retain cycle or crashes due to an invalid pointer.

```objc
@property (weak, nonatomic) id<MyClassDelegate> delegate;
```

## Delegate Method Callbacks

### Optional Methods

When optional delegate protocol methods are called, the method call should always be wrapped in a short-circuited conditional check against the delegate and the delegate’s responder.

```objc
// Optional Methods
if ([delegate respondsToSelector:@selector(methodName)]) {
    [delegate methodName];
}
```

### Required Methods

If a delegate object is passed throughout the application for use within another object using polymorphism, this object could become obscured and may not be a responder to the required method. In order to maintain this interface, please verify that the method does conform to a given protocol.

```objc
// Required Methods
if ([delegate conformsToProtocol:@protocol(ProtocolName)]) {
    [delegate requiredMethodName];
}
```

# Singleton Pattern

Singleton implementation should be based off of a loose implementation and use of the Grand Central Dispatch of dispatch_once in order to maintain that an object can have only one instance. Overridden memory management methods is not required as the need to release singletons may be useful in a given architecture. Maintaining the singleton’s lifecycle is the responsibility of the developer.

```objc
// Optional placement: within the sharedSingleton method or outside  
static SingletonClass *sharedSingleton = nil;

+ (SingletonClass *)sharedSingleton {
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        sharedSingleton = [[SingletonClass alloc] init];
    });

    return sharedSingleton;
}
```
