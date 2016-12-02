# INotifyPropertyChanged
## Liquidate backing fields 

Let's consider we have a property. Smth like this:


```cs
    private int _quantity;

    public int Quantity
    {
        get { return _quantity; }
        set
        {
            if (_quantity != value)
            {
                _quantity = value;
                OnPropertyChanged();
            }
        }
    }

```

Instead of using such approach, there is a better way to consume/support properties.
With help of dictionary and two methods, which encapsulates comparsion and storing/retrieving the values.

```cs

    private readonly Dictionary<string, object> _backingDictionary = new Dictionary<string, object>();

    protected T GetValue<T>([CallerMemberName] string propertyName = null)
    {
        if (propertyName == null) throw new ArgumentNullException(nameof(propertyName));

        object value;
        if (_backingDictionary.TryGetValue(propertyName, out value))
        {
            return (T)value;
        }

        return default(T);
    }

    protected bool SetValue<T>(T newValue, [CallerMemberName] string propertyName = null)
    {
        if (propertyName == null) throw new ArgumentNullException(nameof(propertyName));

        if (EqualityComparer<T>.Default.Equals(newValue, GetValue<T>(propertyName))) return false;

        _backingDictionary[propertyName] = newValue;

        OnPropertyChanged(propertyName);
        return true;
    }

```


After that our properties are great to create/change/support, which is very handy:

```cs

    public int Quantity
    {
        get { return GetValue<int>(); }
        set { SetValue(value); }
    }

```

Also calling SetValue will return a boolean indicating if the value has changed or not.


Try it and good luck!



