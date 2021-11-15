# F# vs C# Cheat Sheet

- [Original source](https://docs.microsoft.com/en-gb/archive/blogs/timng/f-quick-guides-object-oriented-programming)
- [Referenced from FSharp Cheatsheet](http://dungpa.github.io/fsharp-cheatsheet/)

## Classes with properties and default constructor

<table><tr><td valign="top">

```fsharp
type Vector(x : float, y : float) =
    member this.X = x
    member this.Y = y
 
// Usage:
let v = Vector(10., 10.)
let x = v.X
let y = v.Y
```

</td><td valign="top">

```csharp
public class Vector
{
    private double x;
    private double y;
 
    public Vector(double x, double y)
    {
        this.x = x;
        this.y = y;
    }

    public double X
    {
        get =>  { return this.x; }
    }
    public double Y
    {
        get { return this.y; }
    }
}
 
// Usage:
var v = new Vector(10, 10);
var x = v.X;
var y = v.Y;
```

</td></tr></table>

## Structs with properties

<table><tr><td valign="top">

```fsharp
[<Struct>]
type Vector(x : float, y : float) =
    member this.X = x
    member this.Y = y
 
// Usage:
let v = Vector(10., 10.)
let x = v.X
let y = v.Y
```

</td><td valign="top">

```csharp
public struct Vector
{
    double x;
    double y;
 
    public Vector(double x, double y)
    {
        this.x = x;
        this.y = y;
    }
    public double X
    {
        get { return this.x; }
    }
    public double Y
    {
        get { return this.y; }
    }
}
 
// Usage:
Vector v = new Vector(10, 10);
double x = v.X;
double y = v.Y;
```

</td></tr></table>

## Multiple constructors

<table><tr><td valign="top">

```fsharp
type Vector(x : float, y : float) =
    member this.X = x
    member this.Y = y
    new(v : Vector, s) = Vector(v.X * s, v.Y * s)
 
// Usage:
let v = Vector(10., 10.)
let w = Vector(v, 0.5)
```

</td><td valign="top">

```csharp
public class Vector
{
    double x;
    double y;
    public Vector(double x, double y)
    {
        this.x = x;
        this.y = y;
    }
    public Vector(Vector v, double s) :
        this(v.x * s, v.y * s)
    {
    }
}
 
// Usage:
Vector v = new Vector(10, 10);
Vector w = new Vector(v, 0.5);
```

</td></tr></table>

## Member functions

<table><tr><td valign="top">

```fsharp
type Vector(x : float, y : float) =
    member this.Scale(s : float) =
        Vector(x * s, y * s)
 
// Usage:
let v = Vector(10., 10.)
let v2 = v.Scale(0.5)
```

</td><td valign="top">

```csharp
public class Vector
{
    double x;
    double y;
 
    public Vector(double x, double y)
    {
        this.x = x;
        this.y = y;
    }
    public double Scale(double s)
    {
        return new Vector(this.x * s,
            this.y * s);
    }
}
 
// Usage:
Vector v = new Vector(10, 10);
Vector v2 = v.Scale(0.5);
```

</td></tr></table>

## Operators

<table><tr><td valign="top">

```fsharp
type Vector(x, y) =
    member this.X = x
    member this.Y = y
    static member (*) (a : Vector, b : Vector) =
        a.X * b.X + a.Y + b.Y
 
// Usage:
let x = Vector(2., 2.)
let y = Vector(3., 3.)
let dp = x * y

```

</td><td valign="top">

```csharp
public class Vector
{
    double x;
    double y;
    public Vector(double x, double y)
    {
        this.x = x;
        this.y = y;
    }
    public double X
    {
        get { return this.x; }
    }
    public double Y
    {
        get { return this.y; }
    }
    public static double operator * (
        Vector v1, Vector v2)
    {
        return v1.x * v2.x + v1.y * v2.y;
    }
}
 
// Usage:
Vector x = new Vector(2, 2);
Vector y = new Vector(3, 3);
double dp = x * y;
```

</td></tr></table>

## Static members and properties

<table><tr><td valign="top">

```fsharp
type Vector(x, y) =
    member this.X = x
    member this.Y = y
    static member Dot(a : Vector, b : Vector) =
        a.X * b.X + a.Y + b.Y
    static member NormX = Vector(1., 0.)
 
// Usage:
let x = Vector(2., 2.)
let y = Vector.NormX
let dp = Vector.Dot(x, y)
```

</td><td valign="top">

```csharp
public class Vector
{
    double x;
    double y;
    public Vector(double x, double y)
    {
        this.x = x;
        this.y = y;
    }
    public double X
    {
        get { return this.x; }
    }
    public double Y
    {
        get { return this.y; }
    }
    public static double Dot(Vector v1,
        Vector v2)
    {
        return v1.x * v2.x + v1.y * v2.y;
    }
    public static Vector NormX
    {
        get { return new Vector(1, 0); }
    }
}
 
// Usage:
Vector x = new Vector(2, 2);
Vector y = Vector.NormX;
double dp = Vector.Dot(x, y);
```

</td></tr></table>

## Class properties that use let value computations in the constructor

<table><tr><td valign="top">

```fsharp
type Vector(x, y) =
    let mag = sqrt(x * x + y * y)
    let rad = if x = 0. && y = 0. then
                  0.
              else if x >= 0. then
                  asin(y / mag)
              else
                  (-1. * asin(y / mag)) +
                      Math.PI
    member this.Mag = mag
    member this.Rad = rad
 
// Usage:
let v = Vector(10., 10.)
let mag = v.Mag
let rad = v.Rad
```

</td><td valign="top">

```csharp
public class Vector
{
    double mag = 0.0;
    double rad = 0.0;
    public Vector(double x, double y)
    {
        this.mag = Math.Sqrt(x * x + y * y);
        if (x == 0.0 && y == 0.0) rad = 0.0;
        else if (x >= 0.0)
            rad = Math.Asin(y / mag);
        else
            rad = (-1.0 * Math.Asin(y / mag)) + Math.PI;
    }
    public double Mag
    {
        get { return this.mag; }
    }
    public double Rad
    {
        get { return this.rad; }
    }
}
 
// Usage:
Vector v = new Vector(10, 10);
double mag = v.Mag;
double rad = v.Rad;
```

</td></tr></table>

## Class members that use private function values

<table><tr><td valign="top">

```fsharp
type Vector(x, y) =
    let rotate a =
        let x' = x * cos a – y * sin a
        let y' = y * sin a + y * cos a
        new Vector(x', y')
    member this.RotateByDegrees(d) =
        rotate (d * Math.PI / 180.)
    member this.RotateByRadians(r) =
        rotate r
 
// Usage:
let v = Vector(10., 0.)
let x = v.RotateByDegrees(90.)
let y = v.RotateByRadians(Math.PI / 6.)
```

</td><td valign="top">

```csharp
public class Vector
{
    double x = 0.0;
    double y = 0.0;
    Vector rotate(double a)
    {
        double xx = this.x * Math.Cos(a) –
                    this.y * Math.Sin(a);
        double yy = this.y * Math.Sin(a) +
                    this.y * Math.Cos(a);
        return new Vector(xx, yy);
    }
    public Vector(double x, double y)
    {
        this.x = x;
        this.y = y;
    }
    public Vector RotateByDegrees(double d)
    {
        return rotate(d * Math.PI / 180);
    }
    public Vector RotateByRadians(double r)
    {
        return rotate(r);
    }
}
 
// Usage:
Vector v = new Vector(10, 10);
Vector x = v.RotateByDegrees(90.0);
Vector y = v.RotateByRadians(Math.PI / 6.0);
```

</td></tr></table>


## Overloading members

<table><tr><td valign="top">

```fsharp
type Car() =
    member this.Drive() =
        this.Drive(10)
        ()
    member this.Drive(mph : int) =
        // Do something
        ()
 
// Usage:
let c = Car()
c.Drive()
c.Drive(10)
```

</td><td valign="top">

```csharp
public class Car
{
    public void Drive()
    {
        Drive(10);
    }
    public void Drive(int mph)
    {
        // Do something
    }
}
 
// Usage:
Car c = new Car();
c.Drive();
c.Drive(10);
```

</td></tr></table>

## Mutable fields in a class with get/set properties

<table><tr><td valign="top">

```fsharp
type MutableVector(x : float, y : float) =
    let mutable cx = x
    let mutable cy = y
    member this.X with get() = cx and
                       set(v) = cx <- v
    member this.Y with get() = cy and
                       set(v) = cy <- v
    member this.Length = sqrt(x * x + y * y)
 
// Usage:
let v = MutableVector(2., 2.)
let len1 = v.Length
v.X <- 3.
v.Y <- 3.
let len2 = v.Length
```

</td><td valign="top">

```csharp
public class MutableVector
{
    public MutableVector(double x, double y)
    {
        this.X = x;
        this.Y = y;
    }
    public double X { get; set; }
    public double Y { get; set; }
    public double Length
    {
        get { return Math.Sqrt(
                  this.X * this.X +
                  this.Y * this.Y); }
    }
}
 
// Usage:
MutableVector v = new MutableVector(2.0, 2.0);
double len1 = v.Length;
v.X = 3.0;
v.Y = 3.0;
double len2 = v.Length;
```

</td></tr></table>

## Generic classes and function arguments

<table><tr><td valign="top">

```fsharp
type Factory<'T>(f : unit -> 'T) =
    member this.Create() =
        f()
 
// Usage:
let strings = Factory<string>(
    fun () -> "Hello!")
let res = strings.Create()
let ints = Factory<int>(fun () -> 10)
let res = ints.Create()
```

</td><td valign="top">

```csharp
public class Factory<T>
{
    Func<T> creator;
    public Factory(Func<T> f)
    {
        this.creator = f;
    }
    public T Create()
    {
        return this.creator();
    }
}
 
// Usage:
var strings = new
    Factory<string>(() => "Hello");
var res1 = strings.Create();
var ints = new Factory<int>(() => 10);
var res2 = ints.Create();
```

</td></tr></table>

## Generic classes and methods

<table><tr><td valign="top">

```fsharp
type Container<'T>(a : 'T) =
    member this.Convert<'U>(f : 'T -> 'U) =
        f a
 
// Usage:
let c = new Container<int>(10)
let b = c.Convert(fun a -> a.ToString())
```

</td><td valign="top">

```csharp
public class Container<T>
{
    private T value;
    public Container(T t)
    {
        this.value = t;
    }
    public U Convert<U>(Func<T, U> f)
    {
        return f(this.value);
    }
}
 
// Usage:
Container<int> c = new Container<int>(10);
string result = c.Convert(n => n.ToString());
```

</td></tr></table>

## Extension methods

<table><tr><td valign="top">

```fsharp
type List<'T> with
    member this.MyExtensionMethod() =
        this |> Seq.map (fun a -> a.ToString())
 
// Usage:
let c = [1; 2; 3]
let d = c.MyExtensionMethod()
```

</td><td valign="top">

```csharp
public static class ExtensionMethods
{
    public static IEnumerable<string>
        MyExtensionMethod<T>(this List<T> a)
    {
        return a.Select(s => s.ToString());
    }
}
 
// Usage:
List<int> l = new List<int> { 1, 2, 3 };
IEnumerable<string> res =
    l.MyExtensionMethod();
```

</td></tr></table>

## Extension properties

<table><tr><td valign="top">

```fsharp
type List<'T> with
    member this.MyExtensionProp =
        this |> Seq.map (fun a -> a.ToString())
 
// Usage:
let c = [1; 2; 3]
let d = c.MyExtensionProp
```

</td><td valign="top">

```csharp
// N/A. C# does not support this feature.
```

</td></tr></table>


## Indexers

<table><tr><td valign="top">

```fsharp
type Table() =
    member this.Item
        with get(key : string) = int key
 
// Usage:
let tab = Table()
let x = tab.["10"]
let y = tab.["12"]
```

</td><td valign="top">

```csharp
public class Table
{
    public int this[string key]
    {
        get { return Convert.ToInt32(key); }
    }
}
 
// Usage:
Table tab = new Table();
int x = tab["10"];
int y = tab["12"];
```

</td></tr></table>

## Indexed Properties

<table><tr><td valign="top">

```fsharp
type Table() =
    member this.Values
        with get(key : string) = int key
    member this.MultipleValues
        with get(key1, key2) = key1 + key2
// Usage:
let tab = Table()
let x = tab.Values("10")
let y = tab.Values("12")
let a = tab.MultipleValues(10, 5)
```

</td><td valign="top">

```csharp
// N/A. C# does not support this feature.
```

</td></tr></table>

## Abstract classes

<table><tr><td valign="top">

```fsharp
[<AbstractClass>]
type Shape() =
    abstract Name : string with get
    abstract Scale : float -> Shape
```

</td><td valign="top">

```csharp
public abstract class Shape
{
    public abstract string Name { get; }
    public abstract Shape Scale(double scale);
}
```

</td></tr></table>


## Derive from a base class and overriding base methods with generics

<table><tr><td valign="top">

```fsharp
[<AbstractClass>]
type Shape<'T>() =
    abstract Name : string with get
    abstract Scale : float -> 'T
   
type Vector(angle, mag) =
    inherit Shape<Vector>()
    member this.Angle = angle
    member this.Mag = makg
    override this.Name = "Vector"
    override this.Scale(factor) =
        Vector(angle, mag * factor)
 
// Usage:
let v = Vector(45., 10.)
let v2 = v.Scale(0.5)
```

</td><td valign="top">

```csharp
public abstract class Shape<T>
{
    public abstract string Name { get; }
    public abstract T Scale(double scale);
}
 
public class Vector : Shape<Vector>
{
    double angle;
    double mag;
    public double Angle {get{return angle;}}
    public double Mag {get{return mag;}}
    public Vector(double angle, double mag)
    {
        this.angle = angle;
        this.mag = mag;
    }
    public override string Name
    {
        get { return "Vector"; }
    }
    public override Vector Scale(double scale)
    {
        return new Vector(this.Angle,
            this.Mag * scale);
    }
}
// Usage:
Vector v = new Vector(45, 10);
Vector v2 = v.Scale(0.5);
```

</td></tr></table>

## Calling a base class method

<table><tr><td valign="top">

```fsharp
type Animal() =
    member this.Rest() =
        // Rest for the animal
        ()
           
type Dog() =
    inherit Animal()
    member this.Run() =
        // Run
        base.Rest()
// Usage:
let brian = new Dog()
brian.Run()
```

</td><td valign="top">

```csharp
public class Animal
{
    public void Rest()
    {
        // Rest for the animal
    }
}
public class Dog : Animal
{
    public void Run()
    {
        // Run
        base.Rest();
    }
}
// Usage:
Dog brian = new Dog();
brian.Run();
```

</td></tr></table>

## Implementing an interface

<table><tr><td valign="top">

```fsharp
type IVector =
    abstract Mag : double with get
    abstract Scale : float -> IVector
       
type Vector(x, y) =
    interface IVector with
        member this.Mag = sqrt(x * x + y * y)
        member this.Scale(s) =
            Vector(x * s, y * s) :> IVector
    member this.X = x
    member this.Y = y
// Usage:
let v = new Vector(1., 2.) :> IVector
let w = v.Scale(0.5)
let mag = w.Mag
```

</td><td valign="top">

```csharp
interface IVector
{
    double Mag { get; }
    IVector Scale(double s);
}
class Vector : IVector
{
    double x;
    double y;
    public Vector(double x, double y)
    {
        this.x = x;
        this.y = y;
    }
    public double X
    {
        get { return this.x; }
    }
    public double Y
    {
        get { return this.y; }
    }
    public double Mag
    {
        get { return Math.Sqrt( this.x * this.x +
                this.y * this.y); }
    }
    public IVector Scale(double s)
    {
        return new Vector(this.x * s,
            this.y * s);
    }
}
// Usage:
IVector v = new Vector(1, 2);
IVector w = v.Scale(0.5);
double mag = w.Mag;
```

</td></tr></table>

## Implementing an interface with object expressions

<table><tr><td valign="top">

```fsharp
type ICustomer =
    abstract Name : string with get
    abstract Age : int with get
       
let CreateCustomer name age =
    { new ICustomer with
        member this.Name = name
        member this.Age = age
    }
// Usage:
let c = CreateCustomer "Snoopy" 16
let d = CreateCustomer "Garfield" 5
```

</td><td valign="top">

```csharp
/// N/A. C# does not support creating object expressions.
```

</td></tr></table>

## Events

<table><tr><td valign="top">

```fsharp
type BarkArgs(msg:string) =
    inherit EventArgs()
    member this.Message = msg
 
type BarkDelegate =
    delegate of obj * BarkArgs -> unit
 
type Dog() =
    let ev = new Event<BarkDelegate, BarkArgs>()
    member this.Bark(msg) =
        ev.Trigger(this, new BarkArgs(msg))
    [<CLIEvent>]
    member this.OnBark = ev.Publish
// Usage:
let snoopy = new Dog()
snoopy.OnBark.Add(
    fun ba -> printfn "%s" (ba.Message))
snoopy.Bark("Hello")
```

</td><td valign="top">

```csharp
public class BarkArgs : EventArgs
{
    private string msg;
    public BarkArgs(string msg)
    {
        this.msg = msg;
    }
    public string Message
    {
        get { return this.msg; }
    }
}
 
public delegate void BarkDelegate(
    Object sender, BarkArgs args);
 
class Dog
{
    public event BarkDelegate OnBark;
    public void Bark(string msg)
    {
        OnBark(this, new BarkArgs(msg));
    }
}
// Usage:
Dog snoopy = new Dog();
snoopy.OnBark += new BarkDelegate(
    (sender, msg) =>
        Console.WriteLine(msg.Message));
snoopy.Bark("Hello");
```

</td></tr></table>

## Explicit class constructor

<table><tr><td valign="top">

```fsharp
type Vector =
    val mutable x : float
    val mutable y : float
    new() = {x = 0.; y = 0.}
       
// Usage:
let v = Vector()
v.x <- 10.
v.y <- 10.
```

</td></tr></table>


## Explicit public fields

<table><tr><td valign="top">

```fsharp
type Vector() =
    [<DefaultValue()>]
    val mutable x : int
    [<DefaultValue()>]
    val mutable y : int
 
// Usage:
let v = Vector()
v.x <- 10
v.y <- 10
```

</td><td valign="top">

```csharp
public class Vector
{
    public int x;
    public int y;
}
 
// Usage:
Vector v = new Vector();
v.x = 10;
v.y = 10;
```

</td></tr></table>

## Explicit struct definition

<table><tr><td valign="top">

```fsharp
[<Struct>]
type Vector =
    val mutable public x : int
    val mutable public y : int
 
// Usage:
let mutable v = Vector()
v.x <- 10
v.y <- 10
```

</td><td valign="top">

```csharp
public struct Vector
{
    public int x;
    public int y;
}
 
// Usage:
Vector v = new Vector();
v.x = 10;
v.y = 10;
```

</td></tr></table>
