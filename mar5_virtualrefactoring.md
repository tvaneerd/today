Sometimes instead of virtualizing the differences, it is better to refactor the commonality (into helper functions):


<table>
<tr>
<th>
Original
</th>
<th>
Virtual Refactor
</th>
<th>
Subroutines
</th>
</tr>
<tr>
<td  valign="top">

<pre lang="cpp">








int D1::f()
{
    common...;
    specialized...;
    same...;
}
int D2::f()
{
    common...;
    different...;
    same...;
}

</pre>
</td>
<td  valign="top">

<pre lang="cpp">

int Base::f()
{
    common...;
    vf();
    same...;
}
int D1::vf() override
{

    specialized...;
    
}
int D2::vf() override
{

    different...;
    
}

</pre>
</td>
<td  valign="top">

<pre lang="cpp">








int D1::f()
{
    common();
    specialized...;
    same();
}
int D2::f()
{
    common();
    different...;
    same();
}
</pre>
</td>
</tr>
</table>


_Sometimes_.
