# third-party-api-quirks
Just worked with an API so bad, I had to write this down to make sure I don't fall for it again if I need to come back

## UPS Locator API quirks

1) In the payload that you POST to their API, the order of the keys matter. The key, "AccessRequest", needs to be the first node. Their API was made to accept 2 XML documents and they slapped a convert from JSON thing on top of it.
2) In the above scenario, you get the only inconsistent UPS error message response object that doesn't conform to the structure of starting with the "LocatorResponse" key.
2) For the "AccessRequest", the "UserId" and "Password" can be any non-empty string. They are required to be present, just have to be non-empty. They only use "AccessLicenseNumber" for authentication.
3) Unless you can break their system, all errors will be returned with a status code of 200 with an error object with ResponseStatusCode of 0. To be fair, they do give you an ErrorDescription.
4) For several Hawaii addresses, the body of the response changes from JSON to string. This is due to special characters (line breaks) present in the "AddressKeyFormat.AddressLine" value.
5) For addresses UPS can't get a match for, unless you add a "SearchRadius" key with a value less "85.1" to the "LocationSearchCriteria" node in your request payload, it will return a default location in Salina, KS with a distance of 85.1 miles. If you restrict the distance, it will give you a UPS error message with status code 200.
