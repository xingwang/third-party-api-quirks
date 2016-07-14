# third-party-api-quirks
Just worked with an API so bad, I had to write this down to make sure I don't fall for it again if I need to come back

## UPS Locator API quirks

1) In the payload that you POST to their API, the order of the keys matter. The key, "AccessRequest", needs to be the first node. Their API was made to accept 2 XML documents and they slapped a convert from JSON thing on top of it.

2) In the above scenario, you get the only inconsistent UPS error message response object that doesn't conform to the structure of starting with the "LocatorResponse" key.

3) For the "AccessRequest", the "UserId" and "Password" can be any non-empty string. They are required to be present, just have to be non-empty. They only use "AccessLicenseNumber" for authentication.

4) Unless you can break their system, all errors will be returned with a status code of 200 with an error object with ResponseStatusCode of 0. To be fair, they do give you an ErrorDescription.

5) For several Hawaii addresses, the body of the response changes from JSON to string. This is due to special characters (line breaks) present in the "AddressKeyFormat.AddressLine" value.

6) For addresses UPS can't get a match for, unless you add a "SearchRadius" key with a value less "85.1" to the "LocationSearchCriteria" node in your request payload, it will return a default location in Salina, KS with a distance of 85.1 miles. If you restrict the distance, it will give you a UPS error message with status code 200.

## UPS Tracking API

Taking what I learned from the UPS Locator API, I found that the UPS Tracking API behaves in a similar fashion.

## UPS Tracking Web API for Shipping Reference Number

There are multiple ways to get tracking info when you have a tracking number. However, I could only find 1 way to get it if you are only provided a shipping reference number. The url to get a link directly to their Tracking page using a reference number is as follows:
``` https://www.ups.com/WebTracking/reference?loc=en_US&track.x=Track&ReferenceNumber=<YOUR_REFERENCE_NUMBER>&trackref.x=Track&FromDatepicker=06152016&ToDatepicker=07132016 ```

Some fun notes include:
* From and To dates are required. If you are curious, their max To date input is 12312100. Beyond that, it returns an error.
* You can add more filters by viewing their source and append the corresponding input's name property to the same named url parameter.
* If you don't want it to go directly to the shipment info and just auto-fill the form without submitting it, do not include the ```track.x=Track``` parameter in the url.
