Gateway Schema
==============

The following tables will be created in the database:

* Administrator: a set of entities that have various amounts of trust to
  manage devices.  An administrator may have the super-user bit set, and may
  also have the device-admin bit set.

* Devices: a set of devices which have registered to the system.  Each device
  has a manufacturer.

* DeviceType: the device type, abstracted.

* Security-Profile: a collection of security information.

* Manufacturer: a set of entities that represent who built a device.

* DeviceCategory: how the device is used in the home. Alarm/security,
  appliance, end-user device (phone, tablet), general-purpose (PC), etc.

* Networks: a list of distinct access networks.

API endpoints
=============

All API endpoints will be at
  /api/v1/<endpoint>

They will all return JSON.
HTTPS is required.
A ClientCertificate is required.

administrators
--------------

Schema:
```
{
 "_links": {
    "self": {
      "href": "/api/v1/administrator/123",
    }
  },
"name"  : "firstname lastname",
"admin" : true,
"enabled": true,
"prospective": false
"lastlogin" : "2012-04-23T18:25:43.511Z",
"publickey" : "base64-of-subjectpublicinfo-from-rfc5280",
}
```

1. /api/v1/administrators - return an array of entities with permissions.

Supports GET, and POST.  Post creates (201) a new entity, returning Location:
for the new object.

2. /api/v1/administrator/123 - return info on admin 123.

Supports GET, PUT, UPDATE and DELETE.
Note that DELETE simply marks the item as disabled.

devices
-------

Schema:
```
{
"name"     : "Thing Upstairs",
"fqdn"     : "http://example.com/product/thing",
"url"      : "/api/v1/devices/123",
"macaddres": "aa-bb-cc-00-11-22",
"bytes" : [123478, 1245832],
"daily-bytes" : [8765, 92332],
"mud-url"  : "https://www.example.com/url/mud/thing",
"profile-applied" : "/api/v1/profiles/2345",
"device-type" : "/api/v1/devicetypes/3456",
"manufacturer": "tbd"

-cut-
"current-vlan" : "iot" | "trusted" | "appliances" ...,
"device-category" : "tbd"
"wan-enabled"  : true,
"lan-enabled"  : true,
"wan-restricted-to" : [ list-of-URLs, IPv4-subnets, IPv6-subnets ],
"lan-restricted-to" : [ list-of-URLs, IPv4-subnets, IPv6-subnets ],
"outgoing-ports"    : [ list-of-ports ],
"lan-incoming-enabled" : [ list-of-origins ],
"wan-incoming-enabled" : [ list-of-origins ],
"lan-incoming-ports"   : [ list-of-ports ],
"wan-incoming-ports"   : [ list-of-ports ],
}
```

Any of the items below the cut may be omitted or may be null, in
which case the value would be taken from the security-profile.

Daily-bytes may be better replaced with access to better stats.

device-type
-----------

Schema:
```
{
"name"     : "optional name"
"url"      : "/api/v1/devices-types/123",
"mud-url"  : "https://www.example.com/url/mud/thing",
"producturl": "https://www.example.com",   (if known)
"manufacturer": "tbd"
}
```

security-profiles
-----------------

* Security-Profile: a collection of security information.

Schema:
```
{
"name"     : "profile name"
"url"      : "/api/v1/secrity-profiles/123",
"current-vlan" : "iot" | "trusted" | "appliances" ...,
"wan-enabled"  : true,
"lan-enabled"  : true,
"wan-restricted-to" : [ list-of-URLs, IPv4-subnets, IPv6-subnets ],
"lan-restricted-to: : [ list-of-URLs, IPv4-subnets, IPv6-subnets ],
"outgoing-ports"    : [ list-of-ports ],
"lan-incoming-enabled" : [ list-of-origins ],
"wan-incoming-enabled" : [ list-of-origins ],
"lan-incoming-ports"   : [ list-of-ports ],
"wan-incoming-ports"   : [ list-of-ports ],
}
```

We should ship with a dozen or so of these profiles, ready to be applied.

networks
--------

Schema:
```
{
"name"     : "name of network (from luci interface)",
"url"      : "/api/v1/networks/123",
"v4-prefix" : "1.2.3.0/24",
"v6-prefixes" :["fe80::/10", "2607:f0b0:f:70::/64", "fd08:090a:1023:3456::/64"]
"essid" : "abcd",
}
```



