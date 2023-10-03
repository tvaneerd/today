[4:09 PM] Van Eerd, Tony




<table>
<tr>
<th>
Typical
</th>
<th>
By Value
</th>
</tr>
<tr>
<td  valign="top">

<pre lang="cpp">

class ByConstRef
{
    std::string m_str;
        
public:
    ByConstRef(std::string const & crs) : m_str(crs)
    {
    }
};

</pre>
</td>
<td  valign="top">

<pre lang="cpp">


class ByValue
{
    std::string m_str;
        
public:
    ByValue(std::string v) : m_str(std::move(v))
    {
    }
};

</pre>
</td>
</tr>
</table>

```cpp

static std::string getS()
{
    std::string w = "world";
    return w;
}

int main()
{
    std::string s = "hello";
    ByConstRef a{s};       // COPIES s into m_str
    ByConstRef b{getS()};  // COPIES w (in getString) into m_str 
    ByValue    c{s};       // COPIES s into v, MOVES v into m_str
    ByValue    d{getS()};  // MOVES w into v, MOVES v into m_str. NO COPIES
}

```

Passing by `const &` is pretty normal in C++ (and overused on small objects, but that is another story), but if you know the function that takes the value is going to make a copy anyhow,
then there is a slight advantage to taking the param by _value_ instead of const reference, and potentially save a copy (at the cost of a move, but moves are typically "free").
