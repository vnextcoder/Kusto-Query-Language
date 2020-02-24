# ipv4_compare()

Compares two IPv4 strings.

<!-- csl -->
```
ipv4_compare("127.0.0.1", "127.0.0.1") == 0
ipv4_compare('192.168.1.1', '192.168.1.255') < 0
ipv4_compare('192.168.1.1/24', '192.168.1.255/24') == 0
ipv4_compare('192.168.1.1', '192.168.1.255', 24) == 0
```

**Syntax**

`ipv4_compare(`*Expr1*`, `*Expr2*`[ ,`*PrefixMask*`])`

**Arguments**

* *Expr1*, *Expr2*: A string expression representing an IPv4 address. IPv4 strings can be masked using [IP-prefix notation](#if-prefix-notation).
* *PrefixMask*: An integer from 0 to 32 representing the number of most-significant bits that are taken into account.

### IP-prefix notation

It is a common practice to define IP addresses using `IP-prefix notation` using a slash (`/`) character.
The IP address to the LEFT of the slash (`/`) is the base IP address, and the number (1 to 32) to the RIGHT of the slash (`/`) is the number of contiguous 1 bits in the netmask. 

Example:
192.168.2.0/24 will have an associated net/subnetmask containing 24 contiguous bits or 255.255.255.0 in dotted decimal format.

**Returns**

The two IPv4 strings are parsed and compared while accounting for the combined IP-prefix mask calculated from argument prefixes, and the optional `PrefixMask` argument.

Returns:
* `0`: If the long representation of the first IPv4 string argument is equal to the second IPv4 string argument
* `1`: If the long representation of the first IPv4 string argument is greater than the second IPv4 string argument
* `-1`: If the long representation of the first IPv4 string argument is less than the second IPv4 string argument

If conversion for one of the two IPv4 strings was not successful, the result will be `null`.

## Examples: IPv4 comparison equality cases

The following example compares various IPs using the IP-prefix notation specified inside the IPv4 strings.

<!-- csl: https://help.kusto.windows.net/Samples -->
```
datatable(ip1_string:string, ip2_string:string)
[
 '192.168.1.0',    '192.168.1.0',       // Equal IPs
 '192.168.1.1/24', '192.168.1.255',     // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
 '192.168.1.1/30', '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_compare(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.0|192.168.1.0|0|
|192.168.1.1/24|192.168.1.255|0|
|192.168.1.1|192.168.1.255/24|0|
|192.168.1.1/30|192.168.1.255/24|0|

The following example compares various IPs using IP-prefix notation specified inside the IPv4 strings and as additional argument of the `ipv4_compare()` function.

<!-- csl: https://help.kusto.windows.net/Samples -->
```
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_compare(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|0|
|192.168.1.1/24|192.168.1.255|31|0|
|192.168.1.1|192.168.1.255|24|0|