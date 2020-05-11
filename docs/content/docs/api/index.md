---
title: 'API Documentation'
date: 2019-02-11T19:27:37+10:00
draft: false
weight: 2
---

## Geoclient v2

Geoclient is software which provides developer-friendly API’s for *geocoding* New York City location information by proxying requests to Geosupport which is the City's official geocoder of record.

Although, the names are similar, Geoclient and Geosupport are completely different systems built and maintained by two different teams, from two separate City of New York agencies:

* Geoclient (this project) is maintained by [DoITT/DMI/GIS](https://maps.nyc.gov).
* Geosupport is maintained by the [Department of City Planning](https://www1.nyc.gov/site/planning/index.page).

The Geoclient service provides the following endpoints:

| Resource       | Description                                                                                                                                                                                                                                                                                                                                                                            | Geosupport Function                                                                                                                    |
| --------------:| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| Address[^1]    | Given a valid address, provides blockface-level, property-level, and political information ([source](http://a030-goat.nyc.gov/goat/Function1B)).                                                                                                                                                                                                                                       | [`1B`](https://nycplanning.github.io/Geosupport-UPG/appendices/appendix13#work-area-2-cow-function-1b)                                   |
| Address Point  | "Function AP finds the address point for a given address. Address points are point locations located approximately five feet inside the building along the corresponding street frontage. Address points do not exist for all administrative address ranges assigned to a building, but usually only reflect the posted address." ([source](http://a030-goat.nyc.gov/goat/FunctionAP)) | [`AP`](https://nycplanning.github.io/Geosupport-UPG/appendices/appendix13#work-area-2-cow-function-ap)                                   |
| BBL            | Given a valid borough, block, and lot provides property-level information. ([source](http://a030-goat.nyc.gov/goat/FunctionBL))                                                                                                                                                                                                                                                        | [`BL`](https://nycplanning.github.io/Geosupport-UPG/appendices/appendix13#work-area-2-cow-functions-1a-bl-bn-extended)                   |
| BIN            | Given a valid building identification number provides property-level information. ([source](http://a030-goat.nyc.gov/goat/FunctionBN))                                                                                                                                                                                                                                                 | [`BN`](https://nycplanning.github.io/Geosupport-UPG/appendices/appendix13#work-area-2-cow-functions-1a-bl-bn-extended)                   |
| Blockface      | Given a valid borough, "on street" and cross streets provides blockface-level information. ([source](http://a030-goat.nyc.gov/goat/Function3))                                                                                                                                                                                                                                         | [`3`](https://nycplanning.github.io/Geosupport-UPG/appendices/appendix13#work-area-2-cow-function-3#work-area-2-cow-function-3-extended) |
| Intersection   | Given a valid borough and cross streets returns information for the point defined by the two streets. ([source](http://a030-goat.nyc.gov/goat/Function2))                                                                                                                                                                                                                              | [`2`](https://nycplanning.github.io/Geosupport-UPG/appendices/appendix13#work-area-2-cow-function-2)                                     |
| Place[^1]      | Same as 'Address' above using well-known NYC place name for input (instead of a house number and street).                                                                                                                                                                                                                                                                              | [`1B`](https://nycplanning.github.io/Geosupport-UPG/appendices/appendix13#work-area-2-cow-function-1b)                                   |
| Search         | Provides parsing and search algorithm customization for several of the other endpoints listed in this section. Typically used for geocoding unstructured text to support "single-field" user searches. See [Section 1.3](#section-1.3) for details.                                                                                                                                    | `Address`, `BBL`, `BIN`, `Blockface`, `Intersection`, `Place`                                                                          |
| Streetcode     | Translates a Geosupport street code (`B5SC`, `B7SC`, or `B10SC`) to a street name and `B10SC`. ([source](http://a030-goat.nyc.gov/goat/FunctionD))                                                                                                                                                                                                                                     | [`D`, `DG`, `DN`](https://nycplanning.github.io/Geosupport-UPG/appendices/appendix13#character-only-work-area-1-cow-all-functions)           |
| Normalize      | Normalizes a street name (but does **not** verify that it actually exists. [source](http://a030-goat.nyc.gov/goat/FunctionN))                                                                                                                                                                                                                                                          | [`N`](https://nycplanning.github.io/Geosupport-UPG/appendices/appendix13#character-only-work-area-1-cow-all-functions)                   |
| Version        | Provides software version information about the Geoclient endpoint itself and Geosupport version/release info directly from the Geosupport instance this endpoint is currently using.                                                                                                                                                                                                  | HR (undocumented Geosupport metadata function)                                                                                         |

[^1]:See [`1EX`](https://nycplanning.github.io/Geosupport-UPG/appendices/appendix13#work-area-2-cow-functions-1-1e-extended) and [`1AX`](https://nycplanning.github.io/Geosupport-UPG/appendices/appendix13#work-area-2-cow-functions-1a-bl-bn-extended)

## [Roadmap](#roadmap)

| Section             | Title                                | Description |
|:------------------- | ------------------------------------ | ----------- |
| [Intro](#intro)     | Before Using Geoclient               | Describes the purpose and high-level design of this project. |
| [1.0](#section-1.0) | Calling the Geoclient API            | Documents how application clients call the Geoclient service. It also provides example responses. |
| [2.0](#section-2.0) | Understanding Geoclient Return Codes | Provides reference information for the HTTP and application-level return codes which are provided for all requests. |
| [3.0](#section-3.0) | Geosupport Documentation             | Provides links to Department of City Planning's documentation of Geosupport. |
| [4.0](#section-4.0) | Version Information                  | Provides real-time software version information about the current node serving this page. |
| [5.0](#section-5.0) | Contact Us                           | Provides contact information for the Citywide GIS group. |

## [Before Using Geoclient](#intro)

Once upon a time, the developers at DoITT/DMI/GIS found themselves repeatedly copying a fairly large amount of platform-specific `Java/JNI` and `C` client code between their own projects which depended on Geosupport for geocoding services.

Geoclient was built to provide a simplified service API for interacting with Geosupport from any runtime capable of making simple `HTTP GET` requests. In other words, Geoclient is just a proxy API for calling Geosupport which works dilligently behind the scenes doing the actual geocoding with its own data carefully researched and maintained by the Department of City Planning.

Geoclient itself contains no additional spatial logic or reference data and by design tries only to make it easier for applications to call Geosupport. The one exception is the `/search` endpoint which adds NYC location-aware, natural language parsing functionality appropriate for user-driven, "single-field search" e.g., via Ajax calls from an web page. See [Section 1.3](#section-1.3) for details.

## [1.0 - Calling the Geoclient API](#section-1.0)

>
> Geoclient currently uses a somewhat outdated URI template that was common for REST*ish* APIs a few years ago. Work has begun to make Geoclient's web interface service more REST*ful*, but until that's complete, this section describes what exists in version 2.x.
>

All geocoding requests are made as simple `HTTP GET` requests with input arguments specified as URL query parameters. Except for the `doc` resource which returns `text/html`, all Geoclient resources support two media types: `application/json` (JSON) and `application/xml` (XML).
which the client specifies by adding `.<media_type>` between the resource name and `?` character that begins the query string.

```http
GET /<base_uri>/<path>/<resource>.<media_type>?<p1>=<v1>&<p2>=<v2> HTTP/1.1
                                 ^            ^
                                 |            |
                                 .media type  |
                                              ?query string
```

For example, geocode an `address` resource be returned as *JSON*:

```http
GET /geoclient/v2/address.json?houseNumber=4448&street=broadway&borough=manhattan&app_key=s3cret&app_id=foobar HTTP/1.1
                         ^^^^^
```

Same as above, but request *XML* instead:

```http
GET /geoclient/v2/address.xml?houseNumber=4448&street=broadway&borough=manhattan&app_key=s3cret&app_id=foobar HTTP/1.1
                         ^^^^
```

Any response from Geoclient is composed almost entirely from the results of the proxied Geosupport function call. This documentation no longer attempts to provide in-depth information about these functions. Instead, links are provided to the Department of City Planning's [detailed documentation](https://nycplanning.github.io/Geosupport-UPG/) about their geocoding logic and return data. Here's some high-level guidelines that will allow your code to remain loosely coupled with this API:

* By default, Geoclient does not serialize fields returned from Geosupport with `NULL` values in order to reduce unecessary network I/O.
* The `JSON` and `XML` responses shown below are for example purposes only and do **not** include all possible fields that may be returned.  Even for successfully recognized input, certain data attributes may not be available for some locations. Similarly, some attributes only make sense for certain input types.
* Although the XML and JSON example responses are "pretty-printed" in this document, calling applications should not depend on any particular formatting of responses.
* The order of returned data elements is unspecified. Client code which parses responses from Geoclient *should not rely upon significant whitespace or element/attribute ordering*.
* Occasionally, Geosupport fails to recognize a valid location or there is a bug in the Geoclient code. See [Section 5.0](#section-5.0) for information on when and how to contact the appropriate team.

### [1.1 - Common Request Parameters](section-1.1)

The following request parameters are required for all operations (except
where noted):

* API authorization parameters (required on all DoITT/GIS-hosted environments):
  * `app_id` - application id
  * `app_key` - application key
* `borough` (required[^2],[^3],[^4]) parameter - must be one of the following (case-insensitive):
  * `Manhattan`
  * `Bronx`
  * `Brooklyn`
  * `Queens`
  * `Staten Island`

[^2]: Not applicable to BIN calls
[^3]: Not required for ADDRESS or PLACE calls if zip is given
[^4]: Some caveats apply for single-field search requests

### [1.2 - Request Parameters by Type](#section-1.2)

The following sections describe how to call the Geoclient API. Required and optional query parameters are listed for each type of supported call. Although the actual Geosupport API can be called with a vast number of optional parameters, at this time this service only supports what's listed below.

**Note:** Example `GET` requests are shown unencoded (for example, with space characters instead of '%20'). When making these requests programmatically, you may need to URL-encode the URL if your tool or platform does not do so automatically. See this [article](https://en.wikipedia.org/wiki/Url_encoding) for more details.

### [1.2.1 - Address](#section-1.2.1)

**URI:** address.{format}

**Parameters:**

| Parameter Name | Required/Optional               | Comments                                                                                                                                                               |
| -------------- | ------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| houseNumber    | required                        | House number of the address.                                                                                                                                           |
| street         | required                        | Street name or 7-digit street code.                                                                                                                                    |
| borough        | required if 'zip' not given     | Valid values defined in [section 1.1](#section-1.1).                                                                                                                   |
| zip            | required if 'borough' not given | Standard USPS 5-digit zip code or zip+4 (see this [article](http://en.wikipedia.org/wiki/ZIP_code)). Must be a valid zip code for an area within New York City limits. |

**Example Requests:**

```txt
/geoclient/v2/address.json?houseNumber=314&street=west 100 st&borough=manhattan&app_id=abc123&app_key=def456
/geoclient/2/address.xml?houseNumber=109-20&street=71st rd&borough=queens&app_id=abc123&app_key=def456
```

[Example JSON response](#example-address-json-response)

```json
    {
      "address": {
        "assemblyDistrict": "69",
        "bbl": "1018887502",
        "bblBoroughCode": "1",
        "bblTaxBlock": "01888",
        "bblTaxLot": "7502",
        "boeLgcPointer": "1",
        "boePreferredStreetName": "WEST  100 STREET",
        "boePreferredstreetCode": "13577001",
        "boroughCode1In": "1",
        "buildingIdentificationNumber": "1057093",
        "censusBlock2000": "6000",
        "censusBlock2010": "2000",
        "censusTract1990": "187",
        "censusTract2000": "187",
        "censusTract2010": "187",
        "cityCouncilDistrict": "09",
        "civilCourtDistrict": "05",
        "coincidenceSegmentCount": "1",
        "communityDistrict": "107",
        "communityDistrictBoroughCode": "1",
        "communityDistrictNumber": "07",
        "communitySchoolDistrict": "03",
        "condominiumBillingBbl": "1018887502",
        "condominiumFlag": "C",
        "congressionalDistrict": "10",
        "cooperativeIdNumber": "0000",
        "crossStreetNamesFlagIn": "E",
        "dcpPreferredLgc": "01",
        "dofCondominiumIdentificationNumber": "1981",
        "dotStreetLightContractorArea": "1",
        "dynamicBlock": "601",
        "electionDistrict": "049",
        "fireBattalion": "11",
        "fireCompanyNumber": "022",
        "fireCompanyType": "L",
        "fireDivision": "03",
        "firstBoroughName": "MANHATTAN",
        "firstStreetCode": "13577001010",
        "firstStreetNameNormalized": "WEST  100 STREET",
        "fromLionNodeId": "0023422",
        "fromPreferredLgcsFirstSetOf5": "01",
        "genericId": "0060351",
        "geosupportFunctionCode": "1B",
        "geosupportReturnCode": "00",
        "geosupportReturnCode2": "00",
        "gi5DigitStreetCode1": "35770",
        "giBoroughCode1": "1",
        "giBuildingIdentificationNumber1": "1057093",
        "giDcpPreferredLgc1": "01",
        "giHighHouseNumber1": "316",
        "giLowHouseNumber1": "314",
        "giSideOfStreetIndicator1": "L",
        "giStreetCode1": "13577001",
        "giStreetName1": "WEST  100 STREET",
        "healthArea": "3110",
        "healthCenterDistrict": "16",
        "highBblOfThisBuildingsCondominiumUnits": "1018881233",
        "highCrossStreetB5SC1": "129690",
        "highCrossStreetCode1": "12969001",
        "highCrossStreetName1": "RIVERSIDE DRIVE",
        "highHouseNumberOfBlockfaceSortFormat": "000398000AA",
        "houseNumber": "314",
        "houseNumberIn": "314",
        "houseNumberSortFormat": "000314000AA",
        "interimAssistanceEligibilityIndicator": "I",
        "internalLabelXCoordinate": "0991892",
        "internalLabelYCoordinate": "0230017",
        "legacySegmentId": "0037349",
        "lionKeyBoroughCode": "1",
        "lionKeyFaceCode": "5345",
        "lionKeyForVanityAddressBoroughCode": "1",
        "lionKeyForVanityAddressFaceCode": "5345",
        "lionKeyForVanityAddressSequenceNumber": "00060",
        "lionKeySequenceNumber": "00060",
        "listOf4Lgcs": "01",
        "lowBblOfThisBuildingsCondominiumUnits": "1018881201",
        "lowCrossStreetB5SC1": "144990",
        "lowCrossStreetCode1": "14499001",
        "lowCrossStreetName1": "WEST END AVENUE",
        "lowHouseNumberOfBlockfaceSortFormat": "000300000AA",
        "lowHouseNumberOfDefiningAddressRange": "000314000AA",
        "nta": "MN12",
        "ntaName": "Upper West Side",
        "numberOfCrossStreetB5SCsHighAddressEnd": "1",
        "numberOfCrossStreetB5SCsLowAddressEnd": "1",
        "numberOfCrossStreetsHighAddressEnd": "1",
        "numberOfCrossStreetsLowAddressEnd": "1",
        "numberOfEntriesInListOfGeographicIdentifiers": "0001",
        "numberOfExistingStructuresOnLot": "0001",
        "numberOfStreetFrontagesOfLot": "01",
        "physicalId": "0069454",
        "policePatrolBoroughCommand": "2",
        "policePrecinct": "024",
        "returnCode1a": "00",
        "returnCode1e": "00",
        "roadwayType": "1",
        "rpadBuildingClassificationCode": "R4",
        "rpadSelfCheckCodeForBbl": "5",
        "sanbornBoroughCode": "1",
        "sanbornPageNumber": "034",
        "sanbornVolumeNumber": "07",
        "sanbornVolumeNumberSuffix": "S",
        "sanitationCollectionSchedulingSectionAndSubsection": "5B",
        "sanitationDistrict": "107",
        "sanitationRecyclingCollectionSchedule": "ET",
        "sanitationRegularCollectionSchedule": "TTHS",
        "sanitationSnowPriorityCode": "P",
        "segmentAzimuth": "151",
        "segmentIdentifier": "0037349",
        "segmentLengthInFeet": "00574",
        "segmentOrientation": "W",
        "segmentTypeCode": "U",
        "selfCheckCodeOfBillingBbl": "5",
        "sideOfStreetIndicator": "L",
        "sideOfStreetOfVanityAddress": "L",
        "splitLowHouseNumber": "000300000AA",
        "stateSenatorialDistrict": "31",
        "streetName1In": "west 100 st",
        "streetStatus": "2",
        "taxMapNumberSectionAndVolume": "10703",
        "toLionNodeId": "0023852",
        "toPreferredLgcsFirstSetOf5": "01",
        "trafficDirection": "A",
        "underlyingstreetCode": "13577001",
        "workAreaFormatIndicatorIn": "C",
        "xCoordinate": "0992059",
        "xCoordinateHighAddressEnd": "0991683",
        "xCoordinateLowAddressEnd": "0992186",
        "xCoordinateOfCenterofCurvature": "0000000",
        "yCoordinate": "0230011",
        "yCoordinateHighAddressEnd": "0230221",
        "yCoordinateLowAddressEnd": "0229944",
        "yCoordinateOfCenterofCurvature": "0000000",
        "zipCode": "10025"
      }
    }
```

[Example XML response](#example-address-xml-response)

```xml
    <geosupportResponse>
      <address>
        <assemblyDistrict>28</assemblyDistrict>
        <attributeBytes>H</attributeBytes>
        <bbl>4022250006</bbl>
        <bblBoroughCode>4</bblBoroughCode>
        <bblTaxBlock>02225</bblTaxBlock>
        <bblTaxLot>0006</bblTaxLot>
        <boeLgcPointer>1</boeLgcPointer>
        <boePreferredStreetName>71 ROAD</boePreferredStreetName>
        <boePreferredstreetCode>41504001</boePreferredstreetCode>
        <boroughCode1In>4</boroughCode1In>
        <buildingIdentificationNumber>4052426</buildingIdentificationNumber>
        <censusBlock2000>4005</censusBlock2000>
        <censusBlock2010>5001</censusBlock2010>
        <censusTract1990>739</censusTract1990>
        <censusTract2000>739</censusTract2000>
        <censusTract2010>739</censusTract2010>
        <cityCouncilDistrict>29</cityCouncilDistrict>
        <civilCourtDistrict>04</civilCourtDistrict>
        <coincidenceSegmentCount>1</coincidenceSegmentCount>
        <communityDistrict>406</communityDistrict>
        <communityDistrictBoroughCode>4</communityDistrictBoroughCode>
        <communityDistrictNumber>06</communityDistrictNumber>
        <communitySchoolDistrict>28</communitySchoolDistrict>
        <condominiumBillingBbl>0000000000</condominiumBillingBbl>
        <congressionalDistrict>06</congressionalDistrict>
        <cooperativeIdNumber>0000</cooperativeIdNumber>
        <cornerCode>SE</cornerCode>
        <crossStreetNamesFlagIn>E</crossStreetNamesFlagIn>
        <dcpPreferredLgc>01</dcpPreferredLgc>
        <dotStreetLightContractorArea>4</dotStreetLightContractorArea>
        <dynamicBlock>410</dynamicBlock>
        <electionDistrict>029</electionDistrict>
        <fireBattalion>50</fireBattalion>
        <fireCompanyNumber>151</fireCompanyNumber>
        <fireCompanyType>L</fireCompanyType>
        <fireDivision>13</fireDivision>
        <firstBoroughName>QUEENS</firstBoroughName>
        <firstStreetCode>41504001010</firstStreetCode>
        <firstStreetNameNormalized>71 ROAD</firstStreetNameNormalized>
        <fromLionNodeId>0067082</fromLionNodeId>
        <genericId>0070010</genericId>
        <geosupportFunctionCode>1B</geosupportFunctionCode>
        <geosupportReturnCode>00</geosupportReturnCode>
        <geosupportReturnCode2>00</geosupportReturnCode2>
        <gi5DigitStreetCode1>15040</gi5DigitStreetCode1>
        <gi5DigitStreetCode2>59990</gi5DigitStreetCode2>
        <giBoroughCode1>4</giBoroughCode1>
        <giBoroughCode2>4</giBoroughCode2>
        <giBuildingIdentificationNumber1>4052426</giBuildingIdentificationNumber1>
        <giBuildingIdentificationNumber2>4052426</giBuildingIdentificationNumber2>
        <giDcpPreferredLgc1>01</giDcpPreferredLgc1>
        <giDcpPreferredLgc2>01</giDcpPreferredLgc2>
        <giHighHouseNumber1>109-20</giHighHouseNumber1>
        <giHighHouseNumber2>109-05</giHighHouseNumber2>
        <giLowHouseNumber1>109-06</giLowHouseNumber1>
        <giLowHouseNumber2>109-05</giLowHouseNumber2>
        <giSideOfStreetIndicator1>R</giSideOfStreetIndicator1>
        <giSideOfStreetIndicator2>L</giSideOfStreetIndicator2>
        <giStreetCode1>41504001</giStreetCode1>
        <giStreetCode2>45999001</giStreetCode2>
        <giStreetName1>71 ROAD</giStreetName1>
        <giStreetName2>QUEENS BOULEVARD</giStreetName2>
        <healthArea>1920</healthArea>
        <healthCenterDistrict>46</healthCenterDistrict>
        <highBblOfThisBuildingsCondominiumUnits>4022250006</highBblOfThisBuildingsCondominiumUnits>
        <highCrossStreetB5SC1>420390</highCrossStreetB5SC1>
        <highCrossStreetCode1>42039001</highCrossStreetCode1>
        <highCrossStreetName1>110 STREET</highCrossStreetName1>
        <highHouseNumberOfBlockfaceSortFormat>100109098AA</highHouseNumberOfBlockfaceSortFormat>
        <houseNumber>109-20</houseNumber>
        <houseNumberIn>109-20</houseNumberIn>
        <houseNumberSortFormat>100109020AA</houseNumberSortFormat>
        <interimAssistanceEligibilityIndicator>I</interimAssistanceEligibilityIndicator>
        <internalLabelXCoordinate>1028016</internalLabelXCoordinate>
        <internalLabelYCoordinate>0202046</internalLabelYCoordinate>
        <legacySegmentId>0113813</legacySegmentId>
        <lionKeyBoroughCode>4</lionKeyBoroughCode>
        <lionKeyFaceCode>5820</lionKeyFaceCode>
        <lionKeyForVanityAddressBoroughCode>4</lionKeyForVanityAddressBoroughCode>
        <lionKeyForVanityAddressFaceCode>5820</lionKeyForVanityAddressFaceCode>
        <lionKeyForVanityAddressSequenceNumber>02020</lionKeyForVanityAddressSequenceNumber>
        <lionKeySequenceNumber>02020</lionKeySequenceNumber>
        <listOf4Lgcs>01</listOf4Lgcs>
        <lowBblOfThisBuildingsCondominiumUnits>4022250006</lowBblOfThisBuildingsCondominiumUnits>
        <lowCrossStreetB5SC1>435780</lowCrossStreetB5SC1>
        <lowCrossStreetCode1>435780</lowCrossStreetCode1>
        <lowCrossStreetName1>BEND</lowCrossStreetName1>
        <lowHouseNumberOfBlockfaceSortFormat>100109000AA</lowHouseNumberOfBlockfaceSortFormat>
        <lowHouseNumberOfDefiningAddressRange>100109006AA</lowHouseNumberOfDefiningAddressRange>
        <nta>QN17</nta>
        <ntaName>Forest Hills</ntaName>
        <numberOfCrossStreetB5SCsHighAddressEnd>1</numberOfCrossStreetB5SCsHighAddressEnd>
        <numberOfCrossStreetB5SCsLowAddressEnd>1</numberOfCrossStreetB5SCsLowAddressEnd>
        <numberOfCrossStreetsHighAddressEnd>1</numberOfCrossStreetsHighAddressEnd>
        <numberOfCrossStreetsLowAddressEnd>1</numberOfCrossStreetsLowAddressEnd>
        <numberOfEntriesInListOfGeographicIdentifiers>0002</numberOfEntriesInListOfGeographicIdentifiers>
        <numberOfExistingStructuresOnLot>0001</numberOfExistingStructuresOnLot>
        <numberOfStreetFrontagesOfLot>02</numberOfStreetFrontagesOfLot>
        <physicalId>0080824</physicalId>
        <policePatrolBoroughCommand>6</policePatrolBoroughCommand>
        <policePrecinct>112</policePrecinct>
        <returnCode1a>00</returnCode1a>
        <returnCode1e>00</returnCode1e>
        <roadwayType>1</roadwayType>
        <rpadBuildingClassificationCode>D1</rpadBuildingClassificationCode>
        <rpadSelfCheckCodeForBbl>8</rpadSelfCheckCodeForBbl>
        <sanbornBoroughCode>4</sanbornBoroughCode>
        <sanbornPageNumber>066</sanbornPageNumber>
        <sanbornVolumeNumber>19</sanbornVolumeNumber>
        <sanitationCollectionSchedulingSectionAndSubsection>1E</sanitationCollectionSchedulingSectionAndSubsection>
        <sanitationDistrict>406</sanitationDistrict>
        <sanitationRecyclingCollectionSchedule>EW</sanitationRecyclingCollectionSchedule>
        <sanitationRegularCollectionSchedule>WS</sanitationRegularCollectionSchedule>
        <sanitationSnowPriorityCode>P</sanitationSnowPriorityCode>
        <segmentAzimuth>021</segmentAzimuth>
        <segmentIdentifier>0113813</segmentIdentifier>
        <segmentLengthInFeet>00455</segmentLengthInFeet>
        <segmentOrientation>1</segmentOrientation>
        <segmentTypeCode>U</segmentTypeCode>
        <sideOfStreetIndicator>R</sideOfStreetIndicator>
        <sideOfStreetOfVanityAddress>R</sideOfStreetOfVanityAddress>
        <splitLowHouseNumber>100109000AA</splitLowHouseNumber>
        <stateSenatorialDistrict>16</stateSenatorialDistrict>
        <streetAttributeIndicator>H</streetAttributeIndicator>
        <streetName1In>71st rd</streetName1In>
        <streetStatus>2</streetStatus>
        <taxMapNumberSectionAndVolume>41204</taxMapNumberSectionAndVolume>
        <toLionNodeId>0050496</toLionNodeId>
        <toPreferredLgcsFirstSetOf5>01</toPreferredLgcsFirstSetOf5>
        <trafficDirection>A</trafficDirection>
        <underlyingstreetCode>41504001</underlyingstreetCode>
        <workAreaFormatIndicatorIn>C</workAreaFormatIndicatorIn>
        <xCoordinate>1027969</xCoordinate>
        <xCoordinateHighAddressEnd>1028268</xCoordinateHighAddressEnd>
        <xCoordinateLowAddressEnd>1027844</xCoordinateLowAddressEnd>
        <xCoordinateOfCenterofCurvature>0000000</xCoordinateOfCenterofCurvature>
        <yCoordinate>0202112</yCoordinate>
        <yCoordinateHighAddressEnd>0202233</yCoordinateHighAddressEnd>
        <yCoordinateLowAddressEnd>0202066</yCoordinateLowAddressEnd>
        <yCoordinateOfCenterofCurvature>0000000</yCoordinateOfCenterofCurvature>
        <zipCode>11375</zipCode>
      </address>
    </geosupportResponse>
```

### [1.2.2 - BBL](#section-1.2.2)

**URI:** bbl.{format}

**Parameters:**

| Parameter Name | Required/Optional | Comments                                  |
| -------------- | ----------------- | ----------------------------------------- |
| borough        | required          | Valid values [section 1.1](#section-1.1). |
| block          | required          | Tax block.                                |
| lot            | required          | Tax lot.                                  |

**Example Requests:**

```txt
  <baseuri>/bbl.json?borough=manhattan&block=1889&lot=1&app_id=abc123&app_key=def456
  <baseuri>/bbl.xml?borough=manhattan&block=67&lot=1&app_id=abc123&app_key=def456
```

[Example JSON response](#example-bbl-json-response)

```json
    {
       "bbl":
       {
           "bbl": "1018890001",
           "bblBoroughCode": "1",
           "bblBoroughCodeIn": "1",
           "bblTaxBlock": "01889",
           "bblTaxBlockIn": "1889",
           "bblTaxLot": "0001",
           "bblTaxLotIn": "1",
           "buildingIdentificationNumber": "1057127",
           "condominiumBillingBbl": "0000000000",
           "cooperativeIdNumber": "0000",
           "cornerCode": "NE",
           "crossStreetNamesFlagIn": "E",
           "firstBoroughName": "MANHATTAN",
           "geosupportFunctionCode": "BL",
           "geosupportReturnCode": "00",
           "gi5DigitStreetCode1": "29690",
           "gi5DigitStreetCode2": "35770",
           "giBoroughCode1": "1",
           "giBoroughCode2": "1",
           "giBuildingIdentificationNumber1": "1057127",
           "giBuildingIdentificationNumber2": "1057127",
           "giDcpPreferredLgc1": "01",
           "giDcpPreferredLgc2": "01",
           "giGeographicIdentifier1": "V",
           "giHighHouseNumber1": "280",
           "giHighHouseNumber2": "337",
           "giLowHouseNumber1": "280",
           "giLowHouseNumber2": "327",
           "giSideOfStreetIndicator1": "R",
           "giSideOfStreetIndicator2": "R",
           "giStreetCode1": "12969001",
           "giStreetCode2": "13577001",
           "highBblOfThisBuildingsCondominiumUnits": "1018890001",
           "internalLabelXCoordinate": "0991817",
           "internalLabelYCoordinate": "0230239",
           "lowBblOfThisBuildingsCondominiumUnits": "1018890001",
           "lowHouseNumberOfDefiningAddressRange": "000280000AA",
           "numberOfEntriesInListOfGeographicIdentifiers": "0002",
           "numberOfExistingStructuresOnLot": "0001",
           "numberOfStreetFrontagesOfLot": "02",
           "rpadBuildingClassificationCode": "D3",
           "rpadSelfCheckCodeForBbl": "3",
           "sanbornBoroughCode": "1",
           "sanbornPageNumber": "034",
           "sanbornVolumeNumber": "07",
           "sanbornVolumeNumberSuffix": "S",
           "taxMapNumberSectionAndVolume": "10703",
           "workAreaFormatIndicatorIn": "C"
       }
    }
```

[Example XML response](#example-bbl-xml-response)

```xml
    <geosupportResponse>
      <bbl>
        <bbl>1000670001</bbl>
        <bblBoroughCode>1</bblBoroughCode>
        <bblBoroughCodeIn>1</bblBoroughCodeIn>
        <bblTaxBlock>00067</bblTaxBlock>
        <bblTaxBlockIn>67</bblTaxBlockIn>
        <bblTaxLot>0001</bblTaxLot>
        <bblTaxLotIn>1</bblTaxLotIn>
        <buildingIdentificationNumber>1079043</buildingIdentificationNumber>
        <condominiumBillingBbl>0000000000</condominiumBillingBbl>
        <cooperativeIdNumber>0000</cooperativeIdNumber>
        <cornerCode>CR</cornerCode>
        <crossStreetNamesFlagIn>E</crossStreetNamesFlagIn>
        <dcpCommercialStudyArea>11004</dcpCommercialStudyArea>
        <firstBoroughName>MANHATTAN</firstBoroughName>
        <geosupportFunctionCode>BL</geosupportFunctionCode>
        <geosupportReturnCode>00</geosupportReturnCode>
        <gi5DigitStreetCode1>24050</gi5DigitStreetCode1>
        <gi5DigitStreetCode2>25630</gi5DigitStreetCode2>
        <gi5DigitStreetCode3>45440</gi5DigitStreetCode3>
        <gi5DigitStreetCode4>45440</gi5DigitStreetCode4>
        <giBoroughCode1>1</giBoroughCode1>
        <giBoroughCode2>1</giBoroughCode2>
        <giBoroughCode3>1</giBoroughCode3>
        <giBoroughCode4>1</giBoroughCode4>
        <giBuildingIdentificationNumber1>1079043</giBuildingIdentificationNumber1>
        <giBuildingIdentificationNumber2>1079043</giBuildingIdentificationNumber2>
        <giBuildingIdentificationNumber3>1079043</giBuildingIdentificationNumber3>
        <giBuildingIdentificationNumber4>1079043</giBuildingIdentificationNumber4>
        <giDcpPreferredLgc1>01</giDcpPreferredLgc1>
        <giDcpPreferredLgc2>01</giDcpPreferredLgc2>
        <giDcpPreferredLgc3>01</giDcpPreferredLgc3>
        <giDcpPreferredLgc4>01</giDcpPreferredLgc4>
        <giHighHouseNumber1>68</giHighHouseNumber1>
        <giHighHouseNumber2>65</giHighHouseNumber2>
        <giHighHouseNumber3>99</giHighHouseNumber3>
        <giHighHouseNumber4>105</giHighHouseNumber4>
        <giLowHouseNumber1>50</giLowHouseNumber1>
        <giLowHouseNumber2>41</giLowHouseNumber2>
        <giLowHouseNumber3>85</giLowHouseNumber3>
        <giLowHouseNumber4>101</giLowHouseNumber4>
        <giSideOfStreetIndicator1>R</giSideOfStreetIndicator1>
        <giSideOfStreetIndicator2>L</giSideOfStreetIndicator2>
        <giSideOfStreetIndicator3>L</giSideOfStreetIndicator3>
        <giSideOfStreetIndicator4>L</giSideOfStreetIndicator4>
        <giStreetCode1>12405001</giStreetCode1>
        <giStreetCode2>12563001</giStreetCode2>
        <giStreetCode3>14544001</giStreetCode3>
        <giStreetCode4>14544001</giStreetCode4>
        <highBblOfThisBuildingsCondominiumUnits>1000670001</highBblOfThisBuildingsCondominiumUnits>
        <internalLabelXCoordinate>0982039</internalLabelXCoordinate>
        <internalLabelYCoordinate>0197441</internalLabelYCoordinate>
        <lowBblOfThisBuildingsCondominiumUnits>1000670001</lowBblOfThisBuildingsCondominiumUnits>
        <lowHouseNumberOfDefiningAddressRange>000050000AA</lowHouseNumberOfDefiningAddressRange>
        <numberOfEntriesInListOfGeographicIdentifiers>0004</numberOfEntriesInListOfGeographicIdentifiers>
        <numberOfExistingStructuresOnLot>0001</numberOfExistingStructuresOnLot>
        <numberOfStreetFrontagesOfLot>03</numberOfStreetFrontagesOfLot>
        <rpadBuildingClassificationCode>O3</rpadBuildingClassificationCode>
        <rpadSelfCheckCodeForBbl>7</rpadSelfCheckCodeForBbl>
        <sanbornBoroughCode>1</sanbornBoroughCode>
        <sanbornPageNumber>011</sanbornPageNumber>
        <sanbornVolumeNumber>01</sanbornVolumeNumber>
        <sanbornVolumeNumberSuffix>S</sanbornVolumeNumberSuffix>
        <taxMapNumberSectionAndVolume>10102</taxMapNumberSectionAndVolume>
        <workAreaFormatIndicatorIn>C</workAreaFormatIndicatorIn>
      </bbl>
    </geosupportResponse>
```

### [1.2.3 - BIN](#section-1.2.3)

**URI:** bin.{format}

**Parameters:**

| Parameter Name | Required/Optional | Comments                        |
| -------------- | ----------------- | ------------------------------- |
| bin            | required          | Building identification number. |

**Example Requests:**

```txt
  <baseuri>/bin.json?bin=1079043&app_id=abc123&app_key=def456
  <baseuri>/bin.xml?bin=1057127&app_id=abc123&app_key=def456
```

[Example JSON response](#example-bin-json-response)

```json
    {
       "bin":
       {
           "bbl": "1000670001",
           "bblBoroughCode": "1",
           "bblTaxBlock": "00067",
           "bblTaxLot": "0001",
           "buildingIdentificationNumber": "1079043",
           "buildingIdentificationNumberIn": "1079043",
           "condominiumBillingBbl": "0000000000",
           "cooperativeIdNumber": "0000",
           "cornerCode": "CR",
           "crossStreetNamesFlagIn": "E",
           "dcpCommercialStudyArea": "11004",
           "firstBoroughName": "MANHATTAN",
           "geosupportFunctionCode": "BN",
           "geosupportReturnCode": "00",
           "gi5DigitStreetCode1": "24050",
           "gi5DigitStreetCode2": "25630",
           "gi5DigitStreetCode3": "45440",
           "gi5DigitStreetCode4": "45440",
           "giBoroughCode1": "1",
           "giBoroughCode2": "1",
           "giBoroughCode3": "1",
           "giBoroughCode4": "1",
           "giBuildingIdentificationNumber1": "1079043",
           "giBuildingIdentificationNumber2": "1079043",
           "giBuildingIdentificationNumber3": "1079043",
           "giBuildingIdentificationNumber4": "1079043",
           "giDcpPreferredLgc1": "01",
           "giDcpPreferredLgc2": "01",
           "giDcpPreferredLgc3": "01",
           "giDcpPreferredLgc4": "01",
           "giHighHouseNumber1": "68",
           "giHighHouseNumber2": "65",
           "giHighHouseNumber3": "99",
           "giHighHouseNumber4": "105",
           "giLowHouseNumber1": "50",
           "giLowHouseNumber2": "41",
           "giLowHouseNumber3": "85",
           "giLowHouseNumber4": "101",
           "giSideOfStreetIndicator1": "R",
           "giSideOfStreetIndicator2": "L",
           "giSideOfStreetIndicator3": "L",
           "giSideOfStreetIndicator4": "L",
           "giStreetCode1": "12405001",
           "giStreetCode2": "12563001",
           "giStreetCode3": "14544001",
           "giStreetCode4": "14544001",
           "highBblOfThisBuildingsCondominiumUnits": "1000670001",
           "internalLabelXCoordinate": "0982039",
           "internalLabelYCoordinate": "0197441",
           "lowBblOfThisBuildingsCondominiumUnits": "1000670001",
           "lowHouseNumberOfDefiningAddressRange": "000050000AA",
           "numberOfEntriesInListOfGeographicIdentifiers": "0004",
           "numberOfExistingStructuresOnLot": "0001",
           "numberOfStreetFrontagesOfLot": "03",
           "rpadBuildingClassificationCode": "O3",
           "rpadSelfCheckCodeForBbl": "7",
           "sanbornBoroughCode": "1",
           "sanbornPageNumber": "011",
           "sanbornVolumeNumber": "01",
           "sanbornVolumeNumberSuffix": "S",
           "taxMapNumberSectionAndVolume": "10102",
           "workAreaFormatIndicatorIn": "C"
       }
    }
```

[Example XML response](#example-bin-xml-response)

```xml
    <geosupportResponse>
      <bin>
        <bbl>1018890001</bbl>
        <bblBoroughCode>1</bblBoroughCode>
        <bblTaxBlock>01889</bblTaxBlock>
        <bblTaxLot>0001</bblTaxLot>
        <buildingIdentificationNumber>1057127</buildingIdentificationNumber>
        <buildingIdentificationNumberIn>1057127</buildingIdentificationNumberIn>
        <condominiumBillingBbl>0000000000</condominiumBillingBbl>
        <cooperativeIdNumber>0000</cooperativeIdNumber>
        <cornerCode>NE</cornerCode>
        <crossStreetNamesFlagIn>E</crossStreetNamesFlagIn>
        <firstBoroughName>MANHATTAN</firstBoroughName>
        <geosupportFunctionCode>BN</geosupportFunctionCode>
        <geosupportReturnCode>00</geosupportReturnCode>
        <gi5DigitStreetCode1>29690</gi5DigitStreetCode1>
        <gi5DigitStreetCode2>35770</gi5DigitStreetCode2>
        <giBoroughCode1>1</giBoroughCode1>
        <giBoroughCode2>1</giBoroughCode2>
        <giBuildingIdentificationNumber1>1057127</giBuildingIdentificationNumber1>
        <giBuildingIdentificationNumber2>1057127</giBuildingIdentificationNumber2>
        <giDcpPreferredLgc1>01</giDcpPreferredLgc1>
        <giDcpPreferredLgc2>01</giDcpPreferredLgc2>
        <giGeographicIdentifier1>V</giGeographicIdentifier1>
        <giHighHouseNumber1>280</giHighHouseNumber1>
        <giHighHouseNumber2>337</giHighHouseNumber2>
        <giLowHouseNumber1>280</giLowHouseNumber1>
        <giLowHouseNumber2>327</giLowHouseNumber2>
        <giSideOfStreetIndicator1>R</giSideOfStreetIndicator1>
        <giSideOfStreetIndicator2>R</giSideOfStreetIndicator2>
        <giStreetCode1>12969001</giStreetCode1>
        <giStreetCode2>13577001</giStreetCode2>
        <highBblOfThisBuildingsCondominiumUnits>1018890001</highBblOfThisBuildingsCondominiumUnits>
        <internalLabelXCoordinate>0991817</internalLabelXCoordinate>
        <internalLabelYCoordinate>0230239</internalLabelYCoordinate>
        <lowBblOfThisBuildingsCondominiumUnits>1018890001</lowBblOfThisBuildingsCondominiumUnits>
        <lowHouseNumberOfDefiningAddressRange>000280000AA</lowHouseNumberOfDefiningAddressRange>
        <numberOfEntriesInListOfGeographicIdentifiers>0002</numberOfEntriesInListOfGeographicIdentifiers>
        <numberOfExistingStructuresOnLot>0001</numberOfExistingStructuresOnLot>
        <numberOfStreetFrontagesOfLot>02</numberOfStreetFrontagesOfLot>
        <rpadBuildingClassificationCode>D3</rpadBuildingClassificationCode>
        <rpadSelfCheckCodeForBbl>3</rpadSelfCheckCodeForBbl>
        <sanbornBoroughCode>1</sanbornBoroughCode>
        <sanbornPageNumber>034</sanbornPageNumber>
        <sanbornVolumeNumber>07</sanbornVolumeNumber>
        <sanbornVolumeNumberSuffix>S</sanbornVolumeNumberSuffix>
        <taxMapNumberSectionAndVolume>10703</taxMapNumberSectionAndVolume>
        <workAreaFormatIndicatorIn>C</workAreaFormatIndicatorIn>
      </bin>
    </geosupportResponse>
```

### [1.2.4 - Blockface](#section-1.2.4)

**URI:** blockface.{format}

**Parameters:**

| Parameter Name        | Required/Optional | Comments                                                                                               |
| --------------------- | ----------------- | ------------------------------------------------------------------------------------------------------ |
| onStreet              | required          | "On street" (street name of target blockface).                                                         |
| crossStreetOne        | required          | First cross street of blockface.                                                                       |
| crossStreetTwo        | required          | Second cross street of blockface.                                                                      |
| borough               | required          | Borough of `onStreet`. Valid values [section 1.1](#section-1.1).                                       |
| boroughCrossStreetOne | optional          | Borough of first cross street. Defaults to value of `borough` parameter if not supplied.               |
| boroughCrossStreetTwo | optional          | Borough of second cross street. Defaults to value of `borough` parameter if not supplied.              |
| compassDirection      | optional          | Used to request information about only one side of the street. Valid values are: `N`, `S`, `E` or `W`. |

**Example Requests:**

```txt
    <baseuri>/blockface.json?onStreet=amsterdam ave&crossStreetOne=w 110 st&crossStreetTwo=w 111 st&borough=manhattan&app_id=abc123&app_key=def456
    <baseuri>/blockface.xml?onStreet=amsterdam ave&crossStreetOne=w 110 st&crossStreetTwo=w 111 st&borough=manhattan&compassDirection=e&app_id=abc123&app_key=def456
    <baseuri>/blockface.xml?onStreet=eldert ln&crossStreetOne=etna street&crossStreetTwo=ridgewood ave&borough=queens&boroughCrossStreetOne=brooklyn&boughCrossStreetTwo=brooklyn&compassDirection=e&app_id=abc123&app_key=def456
```

[Example JSON response](#example-blockface-json-response)

```json
    {
       "blockface":
       {
           "boroughCode1In": "1",
           "coincidentSegmentCount": "1",
           "crossStreetNamesFlagIn": "E",
           "dcpPreferredLgcForStreet1": "01",
           "dcpPreferredLgcForStreet2": "01",
           "dcpPreferredLgcForStreet3": "01",
           "dotStreetLightContractorArea": "1",
           "firstBoroughName": "MANHATTAN",
           "firstStreetCode": "11171001010",
           "firstStreetNameNormalized": "AMSTERDAM AVENUE",
           "fromNode": "0023924",
           "generatedRecordFlag": "L",
           "geosupportFunctionCode": "3",
           "geosupportReturnCode": "00",
           "highAddressEndCrossStreet1": "135990",
           "leftSegment1990CensusTract": "199",
           "leftSegment2000CensusBlock": "2001",
           "leftSegment2000CensusTract": "199",
           "leftSegment2010CensusBlock": "6000",
           "leftSegment2010CensusTract": "199",
           "leftSegmentAssemblyDistrict": "69",
           "leftSegmentCommunityDistrict": "109",
           "leftSegmentCommunityDistrictBoroughCode": "1",
           "leftSegmentCommunityDistrictNumber": "09",
           "leftSegmentCommunitySchoolDistrict": "03",
           "leftSegmentDynamicBlock": "202",
           "leftSegmentElectionDistrict": "088",
           "leftSegmentFireBattalion": "11",
           "leftSegmentFireCompanyNumber": "047",
           "leftSegmentFireCompanyType": "E",
           "leftSegmentFireDivision": "03",
           "leftSegmentHealthArea": "2310",
           "leftSegmentHighHouseNumber": "0001034",
           "leftSegmentInterimAssistanceEligibilityIndicator": "I",
           "leftSegmentLowHouseNumber": "0001020",
           "leftSegmentNta": "MN09",
           "leftSegmentPolicePatrolBoroughCommand": "2",
           "leftSegmentPolicePrecinct": "026",
           "leftSegmentZipCode": "10025",
           "lengthOfSegmentInFeet": "00273",
           "lionBoroughCode": "1",
           "lionFaceCode": "0535",
           "lionSequenceNumber": "02360",
           "locationalStatusOfSegment": "X",
           "lowAddressEndCrossStreet1": "114210",
           "numberOfCrossStreetsHighAddressEnd": "1",
           "numberOfCrossStreetsLowAddressEnd": "1",
           "numberOfStreetCodesAndNamesInList": "02",
           "rightSegment1990CensusTract": "19701",
           "rightSegment2000CensusBlock": "1003",
           "rightSegment2000CensusTract": "19701",
           "rightSegment2010CensusBlock": "1002",
           "rightSegment2010CensusTract": "19701",
           "rightSegmentAssemblyDistrict": "69",
           "rightSegmentCommunityDistrict": "109",
           "rightSegmentCommunityDistrictBoroughCode": "1",
           "rightSegmentCommunityDistrictNumber": "09",
           "rightSegmentCommunitySchoolDistrict": "03",
           "rightSegmentDynamicBlock": "104",
           "rightSegmentElectionDistrict": "089",
           "rightSegmentFireBattalion": "11",
           "rightSegmentFireCompanyNumber": "047",
           "rightSegmentFireCompanyType": "E",
           "rightSegmentFireDivision": "03",
           "rightSegmentHealthArea": "2320",
           "rightSegmentHighHouseNumber": "0001035",
           "rightSegmentInterimAssistanceEligibilityIndicator": "I",
           "rightSegmentLowHouseNumber": "0001021",
           "rightSegmentNta": "MN09",
           "rightSegmentPolicePatrolBoroughCommand": "2",
           "rightSegmentPolicePrecinct": "026",
           "rightSegmentZipCode": "10025",
           "sanitationSnowPriorityCode": "P",
           "secondStreetCode": "11421003010",
           "secondStreetNameNormalized": "WEST 110 STREET",
           "segmentAzimuth": "061",
           "segmentIdentifier": "0223271",
           "segmentOrientation": "N",
           "segmentTypeCode": "U",
           "streetCode1": "11421003",
           "streetCode6": "13599001",
           "streetName1": "WEST 110 STREET",
           "streetName1In": "amsterdam ave",
           "streetName2In": "w 110 st",
           "streetName3In": "w 111 st",
           "streetName6": "WEST 111 STREET",
           "thirdStreetCode": "13599001010",
           "thirdStreetNameNormalized": "WEST 111 STREET",
           "toNode": "0023926",
           "workAreaFormatIndicatorIn": "C"
       }
    }
```

[Example XML response](#example-blockface-xml-response)

```xml
    <geosupportResponse>
      <blockface>
        <boroughCode1In>1</boroughCode1In>
        <coincidentSegmentCount>1</coincidentSegmentCount>
        <compassDirectionIn>e</compassDirectionIn>
        <crossStreetNamesFlagIn>E</crossStreetNamesFlagIn>
        <dcpPreferredLgcForStreet1>01</dcpPreferredLgcForStreet1>
        <dcpPreferredLgcForStreet2>01</dcpPreferredLgcForStreet2>
        <dcpPreferredLgcForStreet3>01</dcpPreferredLgcForStreet3>
        <dotStreetLightContractorArea>1</dotStreetLightContractorArea>
        <firstBoroughName>MANHATTAN</firstBoroughName>
        <firstStreetCode>11171001010</firstStreetCode>
        <firstStreetNameNormalized>AMSTERDAM AVENUE</firstStreetNameNormalized>
        <fromNode>0023924</fromNode>
        <generatedRecordFlag>L</generatedRecordFlag>
        <geosupportFunctionCode>3</geosupportFunctionCode>
        <geosupportReturnCode>01</geosupportReturnCode>
        <highAddressEndCrossStreet1>135990</highAddressEndCrossStreet1>
        <leftSegment1990CensusTract>199</leftSegment1990CensusTract>
        <leftSegment2000CensusBlock>2001</leftSegment2000CensusBlock>
        <leftSegment2000CensusTract>199</leftSegment2000CensusTract>
        <leftSegment2010CensusBlock>6000</leftSegment2010CensusBlock>
        <leftSegment2010CensusTract>199</leftSegment2010CensusTract>
        <leftSegmentAssemblyDistrict>69</leftSegmentAssemblyDistrict>
        <leftSegmentCommunityDistrict>109</leftSegmentCommunityDistrict>
        <leftSegmentCommunityDistrictBoroughCode>1</leftSegmentCommunityDistrictBoroughCode>
        <leftSegmentCommunityDistrictNumber>09</leftSegmentCommunityDistrictNumber>
        <leftSegmentCommunitySchoolDistrict>03</leftSegmentCommunitySchoolDistrict>
        <leftSegmentDynamicBlock>202</leftSegmentDynamicBlock>
        <leftSegmentElectionDistrict>088</leftSegmentElectionDistrict>
        <leftSegmentFireBattalion>11</leftSegmentFireBattalion>
        <leftSegmentFireCompanyNumber>047</leftSegmentFireCompanyNumber>
        <leftSegmentFireCompanyType>E</leftSegmentFireCompanyType>
        <leftSegmentFireDivision>03</leftSegmentFireDivision>
        <leftSegmentHealthArea>2310</leftSegmentHealthArea>
        <leftSegmentHighHouseNumber>0001034</leftSegmentHighHouseNumber>
        <leftSegmentInterimAssistanceEligibilityIndicator>I</leftSegmentInterimAssistanceEligibilityIndicator>
        <leftSegmentLowHouseNumber>0001020</leftSegmentLowHouseNumber>
        <leftSegmentNta>MN09</leftSegmentNta>
        <leftSegmentPolicePatrolBoroughCommand>2</leftSegmentPolicePatrolBoroughCommand>
        <leftSegmentPolicePrecinct>026</leftSegmentPolicePrecinct>
        <leftSegmentZipCode>10025</leftSegmentZipCode>
        <lengthOfSegmentInFeet>00273</lengthOfSegmentInFeet>
        <lionBoroughCode>1</lionBoroughCode>
        <lionFaceCode>0535</lionFaceCode>
        <lionSequenceNumber>02360</lionSequenceNumber>
        <locationalStatusOfSegment>X</locationalStatusOfSegment>
        <lowAddressEndCrossStreet1>114210</lowAddressEndCrossStreet1>
        <message>THESE STREETS INTERSECT ONCE-COMPASS DIRECTION IGNORED</message>
        <numberOfCrossStreetsHighAddressEnd>1</numberOfCrossStreetsHighAddressEnd>
        <numberOfCrossStreetsLowAddressEnd>1</numberOfCrossStreetsLowAddressEnd>
        <numberOfStreetCodesAndNamesInList>02</numberOfStreetCodesAndNamesInList>
        <reasonCode>H</reasonCode>
        <rightSegment1990CensusTract>19701</rightSegment1990CensusTract>
        <rightSegment2000CensusBlock>1003</rightSegment2000CensusBlock>
        <rightSegment2000CensusTract>19701</rightSegment2000CensusTract>
        <rightSegment2010CensusBlock>1002</rightSegment2010CensusBlock>
        <rightSegment2010CensusTract>19701</rightSegment2010CensusTract>
        <rightSegmentAssemblyDistrict>69</rightSegmentAssemblyDistrict>
        <rightSegmentCommunityDistrict>109</rightSegmentCommunityDistrict>
        <rightSegmentCommunityDistrictBoroughCode>1</rightSegmentCommunityDistrictBoroughCode>
        <rightSegmentCommunityDistrictNumber>09</rightSegmentCommunityDistrictNumber>
        <rightSegmentCommunitySchoolDistrict>03</rightSegmentCommunitySchoolDistrict>
        <rightSegmentDynamicBlock>104</rightSegmentDynamicBlock>
        <rightSegmentElectionDistrict>089</rightSegmentElectionDistrict>
        <rightSegmentFireBattalion>11</rightSegmentFireBattalion>
        <rightSegmentFireCompanyNumber>047</rightSegmentFireCompanyNumber>
        <rightSegmentFireCompanyType>E</rightSegmentFireCompanyType>
        <rightSegmentFireDivision>03</rightSegmentFireDivision>
        <rightSegmentHealthArea>2320</rightSegmentHealthArea>
        <rightSegmentHighHouseNumber>0001035</rightSegmentHighHouseNumber>
        <rightSegmentInterimAssistanceEligibilityIndicator>I</rightSegmentInterimAssistanceEligibilityIndicator>
        <rightSegmentLowHouseNumber>0001021</rightSegmentLowHouseNumber>
        <rightSegmentNta>MN09</rightSegmentNta>
        <rightSegmentPolicePatrolBoroughCommand>2</rightSegmentPolicePatrolBoroughCommand>
        <rightSegmentPolicePrecinct>026</rightSegmentPolicePrecinct>
        <rightSegmentZipCode>10025</rightSegmentZipCode>
        <sanitationSnowPriorityCode>P</sanitationSnowPriorityCode>
        <secondStreetCode>11421003010</secondStreetCode>
        <secondStreetNameNormalized>WEST 110 STREET</secondStreetNameNormalized>
        <segmentAzimuth>061</segmentAzimuth>
        <segmentIdentifier>0223271</segmentIdentifier>
        <segmentOrientation>N</segmentOrientation>
        <segmentTypeCode>U</segmentTypeCode>
        <streetCode1>11421003</streetCode1>
        <streetCode6>13599001</streetCode6>
        <streetName1>WEST 110 STREET</streetName1>
        <streetName1In>amsterdam ave</streetName1In>
        <streetName2In>w 110 st</streetName2In>
        <streetName3In>w 111 st</streetName3In>
        <streetName6>WEST 111 STREET</streetName6>
        <thirdStreetCode>13599001010</thirdStreetCode>
        <thirdStreetNameNormalized>WEST 111 STREET</thirdStreetNameNormalized>
        <toNode>0023926</toNode>
        <workAreaFormatIndicatorIn>C</workAreaFormatIndicatorIn>
      </blockface>
    </geosupportResponse>
```

### [1.2.5 - Intersection](#section-1.2.5)

**URI:** intersection.{format}

**Parameters:**

| Parameter Name        | Required/Optional          | Comments                                                                                         |
| --------------------- | -------------------------- | ------------------------------------------------------------------------------------------------ |
| crossStreetOne        | required                   | First cross street                                                                               |
| crossStreetTwo        | required                   | Second cross street                                                                              |
| borough               | required                   | Borough of first cross street or of all cross streets if no other borough parameter is supplied. |
| boroughCrossStreetTwo | optional                   | Borough of second cross street. If not supplied, assumed to be same as `borough` parameter.      |
| compassDirection      | optional for most requests | Required for streets that intersect more than once. Valid values are: `N`, `S`, `E` or `W`.      |

**Example Requests:**

```txt
    <baseuri>/intersection.json?crossStreetOne=broadway&crossStreetTwo=w 99 st&borough=manhattan&app_id=abc123&app_key=def456
    <baseuri>/intersection.xml?crossStreetOne=rsd&crossStreetTwo=w 97 st&borough=manhattan&compassDirection=e&app_id=abc123&app_key=def456
    <baseuri>/intersection.json?crossStreetOne=jamaica ave&crossStreetTwo=eldert ln&borough=brooklyn&boroughCrossStreetTwo=queens&app_id=abc123&app_key=def456
```

[Example JSON response](#example-intersection-json-response)

```json
    {
       "intersection":
       {
           "assemblyDistrict": "69",
           "boroughCode1In": "1",
           "censusTract1990": "187",
           "censusTract2000": "187",
           "censusTract2010": "187",
           "cityCouncilDistrict": "08",
           "civilCourtDistrict": "05",
           "communityDistrict": "107",
           "communityDistrictBoroughCode": "1",
           "communityDistrictNumber": "07",
           "communitySchoolDistrict": "03",
           "congressionalDistrict": "10",
           "crossStreetNamesFlagIn": "E",
           "dcpPreferredLgcForStreet1": "01",
           "dcpPreferredLgcForStreet2": "01",
           "dotStreetLightContractorArea": "1",
           "fireBattalion": "11",
           "fireCompanyNumber": "022",
           "fireCompanyType": "L",
           "fireDivision": "03",
           "firstBoroughName": "MANHATTAN",
           "firstStreetCode": "11361001010",
           "firstStreetNameNormalized": "BROADWAY",
           "geosupportFunctionCode": "2",
           "geosupportReturnCode": "00",
           "healthArea": "3110",
           "healthCenterDistrict": "16",
           "interimAssistanceEligibilityIndicator": "I",
           "intersectingStreet1": "113610",
           "intersectingStreet2": "135750",
           "lionNodeNumber": "0023424",
           "listOfPairsOfLevelCodes": "**MM",
           "numberOfIntersectingStreets": "2",
           "numberOfStreetCodesAndNamesInList": "02",
           "policePatrolBoroughCommand": "2",
           "policePrecinct": "024",
           "sanbornBoroughCode1": "1",
           "sanbornBoroughCode2": "1",
           "sanbornPageNumber1": "035",
           "sanbornPageNumber2": "036",
           "sanbornVolumeNumber1": "07",
           "sanbornVolumeNumber2": "07",
           "sanbornVolumeNumberSuffix1": "S",
           "sanbornVolumeNumberSuffix2": "S",
           "sanitationCollectionSchedulingSectionAndSubsection": "4B",
           "sanitationDistrict": "107",
           "secondStreetCode": "13575001010",
           "secondStreetNameNormalized": "WEST 99 STREET",
           "stateSenatorialDistrict": "30",
           "streetCode1": "11361001",
           "streetCode2": "13575001",
           "streetName1": "BROADWAY",
           "streetName1In": "broadway",
           "streetName2": "WEST 99 STREET",
           "streetName2In": "w 99 st",
           "workAreaFormatIndicatorIn": "C",
           "xCoordinate": "0992454",
           "yCoordinate": "0229500",
           "zipCode": "10025"
       }
    }
```

[Example XML response](#example-intersection-xml-response)

```xml
 <geosupportResponse>
  <intersection>
    <assemblyDistrict>69</assemblyDistrict>
    <attributeBytes>M</attributeBytes>
    <boroughCode1In>1</boroughCode1In>
    <censusTract1990>183</censusTract1990>
    <censusTract2000>183</censusTract2000>
    <censusTract2010>183</censusTract2010>
    <cityCouncilDistrict>09</cityCouncilDistrict>
    <civilCourtDistrict>05</civilCourtDistrict>
    <communityDistrict>107</communityDistrict>
    <communityDistrictBoroughCode>1</communityDistrictBoroughCode>
    <communityDistrictNumber>07</communityDistrictNumber>
    <communitySchoolDistrict>03</communitySchoolDistrict>
    <compassDirection>E</compassDirection>
    <compassDirectionIn>e</compassDirectionIn>
    <congressionalDistrict>10</congressionalDistrict>
    <crossStreetNamesFlagIn>E</crossStreetNamesFlagIn>
    <dcpPreferredLgcForStreet1>01</dcpPreferredLgcForStreet1>
    <dcpPreferredLgcForStreet2>01</dcpPreferredLgcForStreet2>
    <dotStreetLightContractorArea>1</dotStreetLightContractorArea>
    <fireBattalion>11</fireBattalion>
    <fireCompanyNumber>022</fireCompanyNumber>
    <fireCompanyType>L</fireCompanyType>
    <fireDivision>03</fireDivision>
    <firstBoroughName>MANHATTAN</firstBoroughName>
    <firstStreetCode>12969001020</firstStreetCode>
    <firstStreetNameNormalized>RSD</firstStreetNameNormalized>
    <geosupportFunctionCode>2</geosupportFunctionCode>
    <geosupportReturnCode>00</geosupportReturnCode>
    <healthArea>3110</healthArea>
    <healthCenterDistrict>16</healthCenterDistrict>
    <interimAssistanceEligibilityIndicator>I</interimAssistanceEligibilityIndicator>
    <intersectingStreet1>129690</intersectingStreet1>
    <intersectingStreet2>135710</intersectingStreet2>
    <intersectionReplicationCounter>2</intersectionReplicationCounter>
    <lionNodeNumber>0066281</lionNodeNumber>
    <listOfPairsOfLevelCodes>MMMM</listOfPairsOfLevelCodes>
    <numberOfIntersectingStreets>2</numberOfIntersectingStreets>
    <numberOfStreetCodesAndNamesInList>02</numberOfStreetCodesAndNamesInList>
    <policePatrolBoroughCommand>2</policePatrolBoroughCommand>
    <policePrecinct>024</policePrecinct>
    <sanbornBoroughCode1>1</sanbornBoroughCode1>
    <sanbornBoroughCode2>1</sanbornBoroughCode2>
    <sanbornPageNumber1>033</sanbornPageNumber1>
    <sanbornPageNumber2>104</sanbornPageNumber2>
    <sanbornVolumeNumber1>07</sanbornVolumeNumber1>
    <sanbornVolumeNumber2>07</sanbornVolumeNumber2>
    <sanbornVolumeNumberSuffix1>S</sanbornVolumeNumberSuffix1>
    <sanbornVolumeNumberSuffix2>N</sanbornVolumeNumberSuffix2>
    <sanitationCollectionSchedulingSectionAndSubsection>4B</sanitationCollectionSchedulingSectionAndSubsection>
    <sanitationDistrict>107</sanitationDistrict>
    <secondStreetCode>13571001010</secondStreetCode>
    <secondStreetNameNormalized>WEST 97 STREET</secondStreetNameNormalized>
    <stateSenatorialDistrict>31</stateSenatorialDistrict>
    <streetAttributeIndicator>M</streetAttributeIndicator>
    <streetCode1>12969001</streetCode1>
    <streetCode2>13571001</streetCode2>
    <streetName1>RSD</streetName1>
    <streetName1In>rsd</streetName1In>
    <streetName2>WEST 97 STREET</streetName2>
    <streetName2In>w 97 st</streetName2In>
    <workAreaFormatIndicatorIn>C</workAreaFormatIndicatorIn>
    <xCoordinate>0991418</xCoordinate>
    <yCoordinate>0229475</yCoordinate>
    <zipCode>10025</zipCode>
  </intersection>
</geosupportResponse>
```

### [1.2.6 - Place](#section-1.2.6)

**URI:** place.{format}

**Parameters:**

| Parameter Name | Required/Optional               | Comments                                                                                                                                                               |
| -------------- | ------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name           | required                        | Place name of well-known NYC location.                                                                                                                                 |
| borough        | required if 'zip' not given     | Valid values defined in [section 1.1](#section-1.1).                                                                                                                   |
| zip            | required if 'borough' not given | Standard USPS 5-digit zip code or zip+4 (see this [article](http://en.wikipedia.org/wiki/ZIP_code)). Must be a valid zip code for an area within New York City limits. |

**Example Requests:**

```txt
    <baseuri>/place.json?name=empire state building&borough=manhattan&app_id=abc123&app_key=def456
    <baseuri>/place.xml?name=rfk bridge&borough=queens&app_id=abc123&app_key=def456
```

[Example JSON response](#example-place-json-response)

```json
    {
       "place":
       {
           "assemblyDistrict": "75",
           "attributeBytes": "N",
           "bbl": "1008350041",
           "bblBoroughCode": "1",
           "bblTaxBlock": "00835",
           "bblTaxLot": "0041",
           "boeLgcPointer": "1",
           "boePreferredStreetName": "EMPIRE STATE BUILDING",
           "boePreferredstreetCode": "12032001",
           "boroughCode1In": "1",
           "buildingIdentificationNumber": "1015862",
           "businessImprovementDistrict": "113090",
           "censusBlock2000": "1001",
           "censusBlock2010": "1001",
           "censusTract1990": "76",
           "censusTract2000": "76",
           "censusTract2010": "76",
           "cityCouncilDistrict": "03",
           "civilCourtDistrict": "09",
           "coincidenceSegmentCount": "1",
           "communityDistrict": "105",
           "communityDistrictBoroughCode": "1",
           "communityDistrictNumber": "05",
           "communitySchoolDistrict": "02",
           "condominiumBillingBbl": "0000000000",
           "congressionalDistrict": "12",
           "cooperativeIdNumber": "0000",
           "cornerCode": "CR",
           "crossStreetNamesFlagIn": "E",
           "dcpCommercialStudyArea": "11021",
           "dcpPreferredLgc": "01",
           "dotStreetLightContractorArea": "1",
           "dynamicBlock": "110",
           "electionDistrict": "050",
           "fireBattalion": "07",
           "fireCompanyNumber": "024",
           "fireCompanyType": "L",
           "fireDivision": "01",
           "firstBoroughName": "MANHATTAN",
           "firstStreetCode": "12032001020",
           "firstStreetNameNormalized": "EMPIRE STATE BUILDING",
           "fromLionNodeId": "0021443",
           "fromPreferredLgcsFirstSetOf5": "0101",
           "genericId": "0001703",
           "geosupportFunctionCode": "1B",
           "geosupportReturnCode": "01",
           "geosupportReturnCode2": "00",
           "gi5DigitStreetCode1": "20320",
           "gi5DigitStreetCode2": "10410",
           "gi5DigitStreetCode3": "34430",
           "gi5DigitStreetCode4": "34450",
           "giBoroughCode1": "1",
           "giBoroughCode2": "1",
           "giBoroughCode3": "1",
           "giBoroughCode4": "1",
           "giBuildingIdentificationNumber1": "1015862",
           "giBuildingIdentificationNumber2": "1015862",
           "giBuildingIdentificationNumber3": "1015862",
           "giBuildingIdentificationNumber4": "1015862",
           "giDcpPreferredLgc1": "01",
           "giDcpPreferredLgc2": "01",
           "giDcpPreferredLgc3": "01",
           "giDcpPreferredLgc4": "01",
           "giGeographicIdentifier1": "N",
           "giHighHouseNumber2": "350",
           "giHighHouseNumber3": "31",
           "giHighHouseNumber4": "20",
           "giLowHouseNumber2": "338",
           "giLowHouseNumber3": "1",
           "giLowHouseNumber4": "2",
           "giSideOfStreetIndicator1": "L",
           "giSideOfStreetIndicator2": "L",
           "giSideOfStreetIndicator3": "R",
           "giSideOfStreetIndicator4": "L",
           "giStreetCode1": "12032001",
           "giStreetCode2": "11041001",
           "giStreetCode3": "13443001",
           "giStreetCode4": "13445001",
           "giStreetName1": "EMPIRE STATE BUILDING",
           "giStreetName2": "5 AVENUE",
           "giStreetName3": "WEST 33 STREET",
           "giStreetName4": "WEST 34 STREET",
           "healthArea": "5200",
           "healthCenterDistrict": "15",
           "highBblOfThisBuildingsCondominiumUnits": "1008350041",
           "highCrossStreetB5SC1": "117670",
           "highCrossStreetB5SC2": "134450",
           "highCrossStreetCode1": "11767001",
           "highCrossStreetCode2": "134450",
           "highCrossStreetName1": "EAST 34 STREET",
           "highCrossStreetName2": "WEST 34 STREET",
           "highHouseNumberOfBlockfaceSortFormat": "000000000AA",
           "interimAssistanceEligibilityIndicator": "I",
           "internalLabelXCoordinate": "0988196",
           "internalLabelYCoordinate": "0211970",
           "legacySegmentId": "0034023",
           "lionKeyBoroughCode": "1",
           "lionKeyFaceCode": "2470",
           "lionKeyForVanityAddressBoroughCode": "1",
           "lionKeyForVanityAddressFaceCode": "2470",
           "lionKeyForVanityAddressSequenceNumber": "01150",
           "lionKeySequenceNumber": "01150",
           "listOf4Lgcs": "01",
           "lowBblOfThisBuildingsCondominiumUnits": "1008350041",
           "lowCrossStreetB5SC1": "117650",
           "lowCrossStreetB5SC2": "134430",
           "lowCrossStreetCode1": "11765001",
           "lowCrossStreetCode2": "13443001",
           "lowCrossStreetName1": "EAST 33 STREET",
           "lowCrossStreetName2": "WEST 33 STREET",
           "lowHouseNumberOfBlockfaceSortFormat": "000000000AA",
           "lowHouseNumberOfDefiningAddressRange": "000001000AA",
           "message": "350 5 AVENUE IS THE UNDERLYING ADDRESS OF EMPIRE STATE BUILDING",
           "nta": "MN17",
           "ntaName": "Midtown-Midtown South",
           "numberOfCrossStreetB5SCsHighAddressEnd": "3",
           "numberOfCrossStreetB5SCsLowAddressEnd": "2",
           "numberOfCrossStreetsHighAddressEnd": "2",
           "numberOfCrossStreetsLowAddressEnd": "2",
           "numberOfEntriesInListOfGeographicIdentifiers": "0004",
           "numberOfExistingStructuresOnLot": "0001",
           "numberOfStreetFrontagesOfLot": "03",
           "physicalId": "0001934",
           "policePatrolBoroughCommand": "1",
           "policePrecinct": "014",
           "reasonCode": "V",
           "reasonCode1e": "V",
           "returnCode1a": "00",
           "returnCode1e": "01",
           "roadwayType": "1",
           "rpadBuildingClassificationCode": "O4",
           "rpadSelfCheckCodeForBbl": "3",
           "sanbornBoroughCode": "1",
           "sanbornPageNumber": "016",
           "sanbornVolumeNumber": "04",
           "sanitationCollectionSchedulingSectionAndSubsection": "1A",
           "sanitationDistrict": "105",
           "sanitationRecyclingCollectionSchedule": "EF",
           "sanitationRegularCollectionSchedule": "MWF",
           "sanitationSnowPriorityCode": "P",
           "segmentAzimuth": "061",
           "segmentIdentifier": "0034023",
           "segmentLengthInFeet": "00277",
           "segmentOrientation": "N",
           "segmentTypeCode": "U",
           "sideOfStreetIndicator": "L",
           "sideOfStreetOfVanityAddress": "L",
           "specialAddressGeneratedRecordFlag": "N",
           "splitLowHouseNumber": "000001000AA",
           "stateSenatorialDistrict": "27",
           "streetAttributeIndicator": "N",
           "streetName1In": "empire state building",
           "streetStatus": "2",
           "taxMapNumberSectionAndVolume": "10306",
           "toLionNodeId": "0021445",
           "toPreferredLgcsFirstSetOf5": "01",
           "trafficDirection": "A",
           "underlyingHnsOnTrueStreet": "000350000AA",
           "underlyingstreetCode": "11041001",
           "workAreaFormatIndicatorIn": "C",
           "xCoordinate": "0988205",
           "xCoordinateHighAddressEnd": "0988528",
           "xCoordinateLowAddressEnd": "0988394",
           "xCoordinateOfCenterofCurvature": "0000000",
           "yCoordinate": "0211959",
           "yCoordinateHighAddressEnd": "0211953",
           "yCoordinateLowAddressEnd": "0211711",
           "yCoordinateOfCenterofCurvature": "0000000",
           "zipCode": "10018"
       }
    }
```

[Example XML response](#example-place-xml-response)

```xml
    <geosupportResponse>
      <place>
        <assemblyDistrict>36</assemblyDistrict>
        <attributeBytes>B</attributeBytes>
        <boeLgcPointer>1</boeLgcPointer>
        <boePreferredStreetName>ROBERT F KENNEDY BRIDGE</boePreferredStreetName>
        <boePreferredstreetCode>49730001</boePreferredstreetCode>
        <boroughCode1In>4</boroughCode1In>
        <censusBlock2000>1004</censusBlock2000>
        <censusBlock2010>1004</censusBlock2010>
        <censusTract1990>99</censusTract1990>
        <censusTract2000>99</censusTract2000>
        <censusTract2010>99</censusTract2010>
        <cityCouncilDistrict>22</cityCouncilDistrict>
        <civilCourtDistrict>01</civilCourtDistrict>
        <coincidenceSegmentCount>1</coincidenceSegmentCount>
        <communityDistrict>401</communityDistrict>
        <communityDistrictBoroughCode>4</communityDistrictBoroughCode>
        <communityDistrictNumber>01</communityDistrictNumber>
        <communitySchoolDistrict>30</communitySchoolDistrict>
        <congressionalDistrict>12</congressionalDistrict>
        <crossStreetNamesFlagIn>E</crossStreetNamesFlagIn>
        <dcpPreferredLgc>01</dcpPreferredLgc>
        <dotStreetLightContractorArea>N</dotStreetLightContractorArea>
        <dynamicBlock>111</dynamicBlock>
        <electionDistrict>014</electionDistrict>
        <fireBattalion>49</fireBattalion>
        <fireCompanyNumber>312</fireCompanyNumber>
        <fireCompanyType>E</fireCompanyType>
        <fireDivision>14</fireDivision>
        <firstBoroughName>QUEENS</firstBoroughName>
        <firstStreetCode>49730001090</firstStreetCode>
        <firstStreetNameNormalized>RFK BRIDGE</firstStreetNameNormalized>
        <fromLionNodeId>0042193</fromLionNodeId>
        <fromPreferredLgcsFirstSetOf5>0176</fromPreferredLgcsFirstSetOf5>
        <genericId>0019558</genericId>
        <geosupportFunctionCode>1B</geosupportFunctionCode>
        <geosupportReturnCode>00</geosupportReturnCode>
        <geosupportReturnCode2>58</geosupportReturnCode2>
        <healthArea>0110</healthArea>
        <healthCenterDistrict>41</healthCenterDistrict>
        <highCrossStreetB5SC1>462090</highCrossStreetB5SC1>
        <highCrossStreetCode1>46209001</highCrossStreetCode1>
        <highCrossStreetName1>SHORE BOULEVARD</highCrossStreetName1>
        <highHouseNumberOfBlockfaceSortFormat>000000000AA</highHouseNumberOfBlockfaceSortFormat>
        <interimAssistanceEligibilityIndicator>I</interimAssistanceEligibilityIndicator>
        <legacySegmentId>0068651</legacySegmentId>
        <lionKeyBoroughCode>4</lionKeyBoroughCode>
        <lionKeyFaceCode>4647</lionKeyFaceCode>
        <lionKeyForVanityAddressBoroughCode>4</lionKeyForVanityAddressBoroughCode>
        <lionKeyForVanityAddressFaceCode>4647</lionKeyForVanityAddressFaceCode>
        <lionKeyForVanityAddressSequenceNumber>04040</lionKeyForVanityAddressSequenceNumber>
        <lionKeySequenceNumber>04040</lionKeySequenceNumber>
        <listOf4Lgcs>01</listOf4Lgcs>
        <lowCrossStreetB5SC1>197720</lowCrossStreetB5SC1>
        <lowCrossStreetB5SC2>462250</lowCrossStreetB5SC2>
        <lowCrossStreetCode1>19772001</lowCrossStreetCode1>
        <lowCrossStreetCode2>46225076</lowCrossStreetCode2>
        <lowCrossStreetName1>ROBERT F KENNEDY BRIDGE</lowCrossStreetName1>
        <lowCrossStreetName2>QN-NY CNTY SHORELINE</lowCrossStreetName2>
        <lowHouseNumberOfBlockfaceSortFormat>000000000AA</lowHouseNumberOfBlockfaceSortFormat>
        <message2>NON-ADDRESSABLE PLACE NAME, BRIDGE, TUNNEL OR MISC STRUCTURE NOT FOUND</message2>
        <nta>QN99</nta>
        <ntaName>park-cemetery-etc-Queens</ntaName>
        <numberOfCrossStreetB5SCsHighAddressEnd>1</numberOfCrossStreetB5SCsHighAddressEnd>
        <numberOfCrossStreetB5SCsLowAddressEnd>2</numberOfCrossStreetB5SCsLowAddressEnd>
        <numberOfCrossStreetsHighAddressEnd>1</numberOfCrossStreetsHighAddressEnd>
        <numberOfCrossStreetsLowAddressEnd>2</numberOfCrossStreetsLowAddressEnd>
        <numberOfStreetCodesAndNamesInList>03</numberOfStreetCodesAndNamesInList>
        <policePatrolBoroughCommand>6</policePatrolBoroughCommand>
        <policePrecinct>114</policePrecinct>
        <returnCode1a>58</returnCode1a>
        <returnCode1e>00</returnCode1e>
        <roadwayType>3</roadwayType>
        <sanitationCollectionSchedulingSectionAndSubsection>1D</sanitationCollectionSchedulingSectionAndSubsection>
        <sanitationDistrict>401</sanitationDistrict>
        <sanitationRecyclingCollectionSchedule>EF</sanitationRecyclingCollectionSchedule>
        <sanitationRegularCollectionSchedule>TF</sanitationRegularCollectionSchedule>
        <sanitationSnowPriorityCode>P</sanitationSnowPriorityCode>
        <segmentAzimuth>299</segmentAzimuth>
        <segmentIdentifier>0068651</segmentIdentifier>
        <segmentLengthInFeet>00047</segmentLengthInFeet>
        <segmentOrientation>4</segmentOrientation>
        <segmentTypeCode>G</segmentTypeCode>
        <sideOfStreetIndicator>R</sideOfStreetIndicator>
        <sideOfStreetOfVanityAddress>R</sideOfStreetOfVanityAddress>
        <specialAddressGeneratedRecordFlag>N</specialAddressGeneratedRecordFlag>
        <splitLowHouseNumber>000001000AA</splitLowHouseNumber>
        <stateSenatorialDistrict>12</stateSenatorialDistrict>
        <streetAttributeIndicator>B</streetAttributeIndicator>
        <streetCode1>19772001</streetCode1>
        <streetCode2>46225001</streetCode2>
        <streetCode6>46209001</streetCode6>
        <streetName1>ROBERT F KENNEDY BRIDGE</streetName1>
        <streetName1In>rfk bridge</streetName1In>
        <streetName2>BODY OF WATER</streetName2>
        <streetName6>SHORE BOULEVARD</streetName6>
        <streetStatus>2</streetStatus>
        <toLionNodeId>0042192</toLionNodeId>
        <toPreferredLgcsFirstSetOf5>01</toPreferredLgcsFirstSetOf5>
        <trafficDirection>T</trafficDirection>
        <underlyingHnsOnTrueStreet>000000000AA</underlyingHnsOnTrueStreet>
        <underlyingstreetCode>49730001</underlyingstreetCode>
        <workAreaFormatIndicatorIn>C</workAreaFormatIndicatorIn>
        <xCoordinate>1004824</xCoordinate>
        <xCoordinateHighAddressEnd>1004838</xCoordinateHighAddressEnd>
        <xCoordinateLowAddressEnd>1004815</xCoordinateLowAddressEnd>
        <xCoordinateOfCenterofCurvature>0000000</xCoordinateOfCenterofCurvature>
        <yCoordinate>0222858</yCoordinate>
        <yCoordinateHighAddressEnd>0222839</yCoordinateHighAddressEnd>
        <yCoordinateLowAddressEnd>0222880</yCoordinateLowAddressEnd>
        <yCoordinateOfCenterofCurvature>0000000</yCoordinateOfCenterofCurvature>
        <zipCode>11102</zipCode>
      </place>
    </geosupportResponse>
```

### [1.3 - Single-field Search](#section-1.3)

Begining with version 1.10, any of the six request types documented in [section 1.2](#section-1.2) can be accessed using a single unparsed location string. Assuming that the Geoclient parser can guess the location type requested and the given single-field input parameter contains contains enough information to generate a successful Geosupport call, the service will return one or more sets of geocodes corresponding to the type of request that was made.

The single-field search will attempt to recognize the type of request being made using the following (simplified) parsing strategies listed in the order in which they are executed :

1. A ten-digit number where the first digit is 1, 2, 3, 4 or 5 is recognized as a BBL request:

   `1000670001`

2. A seven-digit number where the first digit is 1, 2, 3, 4 or 5 is recognized as a BIN request:

   `1079043`

3. If the input string ends with a United States-related country identifier it is recognized and removed:

   `314 W 100 ST, NY, NY 10025 [USA]`

4. If the input string ends with a five or seven-digit zipcode it is removed and kept as a \<ZIP\> token:

   `314 W 100 ST, NY, NY [10025]`

5. If the input string ends with a valid two-character U.S. state abreviation it is recognized and removed:[^5]

   `314 W 100 ST, NY, [NY]`

6. If the input string ends with a known N.Y.C. city name it is removed and kept as a \<CITY\_NAME\> token:

   `93-02 69th Ave, [Forest Hills]`

7. If the input string ends with a known borough name or abreviation it is removed and kept as a \<BOROUGH\_NAME\> token:

   `80 Monroe Ave [SI]`

8. A BLOCKFACE request is identified using the pattern \<ON\_STREET\> "between" \<CROSS\_STREET\_ONE\> "and" \<CROSS\_STREET\_TWO\>:

   `[Broadway] between [W 100 St] and [W 101 St]`

9. A INTERSECTION request is identified using the pattern \<CROSS\_STREET\_ONE\> "and" \<CROSS\_STREET\_TWO\>:

   `[Broadway] and [W 100 St]`

10. If the input string starts with a known house number format, it is recognized and kept as \<BASIC\_HOUSE\_NUMBER\> and (optionally) \<HOUSE\_NUMBER\_SUFFIX\> token(s):

    `[93-02] 69th Ave`

11. Any remaining input is treated as an input street. If a \<BASIC\_HOUSE\_NUMBER\> token has been parsed, the input is recognized as an ADDRESS request, otherwise it is defaulted to a PLACE request

[^5]: Input ending in only a single instance of "NY" or "New York" (case-insensitive) is treated as a city name alias for Manhattan and not the state of New York.

All matching is case-insensitive. Leading/trailing whitespace and any punctuation characters are ignored.

After the input has been parsed into tokens and the request type has been determined, the service resolves the BOROUGH\_NAME and/or CITY\_NAME token(s) into a New York City borough code (1=Manhattan, 2=Bronx, 3=Brooklyn, 4=Queens, 5=Staten Island).

* If a valid borough code has been resolved, a single request is made to Geosupport using the specified borough code. The request is assigned a "level" of zero.
* If a valid borough code cannot be derived (either because no borough was provided or the provided borough/city name is not recognized), five requests are automatically submitted - one for each borough.  Each request is assigned a "level" of one.

The Geoclient service uses the concept of search levels to classify the degree to which any user input has been modified by the program before making the request to Geosupport.  Roughly speaking, each additional level indicates a round of sub-searches undertaken when the service does not find an exact match.

### [1.3.1 - SFS (Single-field search)](#section-1.3.1)

**URI:** search.{format}

**Parameters:**

| Parameter Name             | Required/Optional | Comments                                                                                                                                                                                                        |
| -------------------------- | ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| input                      | required          | Unparsed location input.                                                                                                                                                                                        |
| exactMatchForSingleSuccess | optional          | Whether a search returning only one possible successfully geocoded location is considered an exact match. Defaults to false.                                                                                    |
| exactMatchMaxLevel         | optional          | The maximum number of sub-search levels to perform if Geosupport rejects the input but suggests alternative street names, etc. Defaults to 3. Maximum is allowable value is 6.                                  |
| returnPolicy               | optional          | Whether to return information on the search policy used to perform the search. Defaults to false.                                                                                                               |
| returnPossiblesWithExact   | optional          | Whether to also return successfully geocoded possible matches when available in addition to the exact match. Defaults to false.                                                                                 |
| returnRejections           | optional          | Whether to return rejected response data from Geosupport. Defaults to false.                                                                                                                                    |
| returnTokens               | optional          | Whether to return the parsed input tokens recognized by the parser. Defaults to false.                                                                                                                          |
| similarNamesDistance       | optional          | Maximum allowable Levenshtein distance between user input and a similar name suggestion from Geosupport. Defaults to 8. A higher number will allow more "guesses" to be made about an unrecognized street name. |

**Example Requests:**

```txt
    <baseuri>/search.json?input=314 west 100 st manhattan&app_id=abc123&app_key=def456
    <baseuri>/search.xml?input=broadway and w 100 st&app_id=abc123&app_key=def456
```

## [2.0 - Understanding Geoclient Return Codes](#section-2.0)

There are two ways in which the Geoclient service communicates call status information: HTTP status codes and Geosupport API return codes. The former will always be provided; the only time in which latter will not be available is when the service itself is down or system error prevents the application from returning data to the client.

### [2.1 - HTTP Status Codes](#section-2.1)

The HTTP protocol implementation used by clients to call the Geoclient service will always provide status codes for all requests made to a valid Geoclient service URL. Full documentation of possible HTTP status codes are beyond the scope of this document, but [Mozilla](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status) provides an easy to understand reference site. For more detailed information, please see [section 10](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) of [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616.html).

In brief, here are the most commonly returned HTTP status codes:

| HTTP Status Code | Meaning                                                                                                                                                                         |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 200              | The call successfully reached the Geoclient application. Application-level return codes contain information on success or failure of the call (see [section 2.2](#section-2.2)) |
| 400              | A required query parameter is missing. See [section 1.2](#section-1.2) for information on call parameters.                                                                      |
| 401              | Unauthorized: indicates that the request has not been applied because it lacks valid authentication credentials for the target resource.                                        |
| 403              | Forbidden: The HTTP 403 Forbidden client error status response code indicates that the server understood the request but refuses to authorize it.                               |
| 404              | An incorrect URL has been used. There is no service mapped to it.                                                                                                               |
| 500              | The Geoclient service could not process the request due to an internal server error.                                                                                            |

### [2.2 - Geosupport Return Codes](#section-2.2)

The following table summarizes the meaning of the codes returned by Geosupport to indicate the processing status of a given function call. This is only a very high-level summary. Please see DCP's official [return code documentation](https://nycplanning.github.io/Geosupport-UPG/appendices/appendix04/) for a complete explanation of all codes and their meaning.

>
> The return codes come *directly* from the Geo*support* application.
>
> The Geo*client* service uses standard `HTTP` status codes to report on it's own request processing (see [section 2.1](#section-2.1)).
>

| Return Code         | Description           | Response Fields                                                      |
| ------------------- | --------------------- | -------------------------------------------------------------------- |
| 00                  | Success               | `geosupportReturnCode`, (`reasonCode` and `message` will be *blank*) |
| 01                  | Success with warnings | `geosupportReturnCode`, `reasonCode`, `message`                      |
| GRC greater than 01 | Reject or error       | `geosupportReturnCode`, `reasonCode`, `message`                      |

Some Geosupport functions are actually just the combined results of two "sub-function" calls. At this time, function `1B` (exposed by the Geoclient `address` endpoint) is the only case where this applies.

Results returned by function `1B` are composed of calls to functions `1EX`(theoretical address that may or may not exist in reality computed using the house number range based off of segment information from the street's centerline) and `1AX` (real address and associated property-level information based on tax lot information).

#### Function 1B

Although it is uncommon, there are a significant number of locations where data is valid and/or available for only one of these two sub-function calls. Therefore, please check the following fields when calling Geoclient's `address` endpoint:

| 1B Sub-function | Field                   | Alias          |
| --------------- | ----------------------- | -------------- |
| `1EX`           | `geosupportReturnCode`  | `returnCode1e` |
| `1EX`           | `reasonCode`            | `reasonCode1e` |
| `1EX`           | `message`               |                |
| `1AX`           | `geosupportReturnCode2` | `returnCode1a` |
| `1AX`           | `reasonCode2`           | `reasonCode1a` |
| `1AX`           | `message2`              |                |

## [3.0 - Geosupport Documentation](#section-3.0)

This section provides links to some of the Department of City Planning's online Geosupport documentation.

| Source                                                                                  | Description                                                                                                                                                                        |
| --------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [GOAT](http://a030-goat.nyc.gov/goat) - Geographic Online Address Translator            | Department of City Planning website which provides direct, web-based access to Geosupport. This site also includes information for contacting DCP with questions about Geosupport. |
| [GOAT User Guide](http://a030-goat.nyc.gov/goat/UserGuide)                              | Brief but precise documentation of Geosupport's logic, data and function APIs                                                                                                      |
| [Geosupport Universal Programming Guide](https://nycplanning.github.io/Geosupport-UPG/) | Comprehensive documentation of Geosupport's logic, data, function APIs, data dictionary, and more                                                                                  |

## [4.0 - Version Information](#section-4.0)

This section provides version information about the Geoclient and Geosupport software providing this service.

### [4.1 - Geoclient Version](#section-4.1)

The Geoclient service provides a simple API for calling Geosupport functions.

| Geoclient Component        | Version    |
| -------------------------- | ---------- |
| Geoclient Service API      | 2.0.0-rc.5 |
| Geoclient Library          | 2.0.0-rc.5 |
| Geoclient Parser           | 2.0.0-rc.5 |
| Geoclient Native Interface | 2.0.0-rc.5 |
| Geosupport Access Method   | Local/JNI  |

### [4.2 - Geosupport Version](#section-4.2)

Geosupport is the underlying system that does the actual geocoding for the Geoclient service.  It is written and maintained by the Department of City Planning.  Geosupport uses "version" to refer to software component versioning and "release" to refer to data changes.

| Geosupport | Release/Version |
| ---------- | --------------- |
| Release    | 20A             |
| Version    | 20.1            |

## [5.0 - Contact Us](#section-5.0)

For Geoclient API usage, bug reporting and enhancement requests, please contact the [Citywide GIS Development](mailto:gis-development@doitt.nyc.gov) group.

For questions about or issues with geocoding accuracy, with the data returned from Geosupport, or alternatives to this project please contact [GSS_Software@planning.nyc.gov](mailto:GSS_Software@planning.nyc.gov).

For general information about the City of New York/Department of City Planning, including other services they provide, please visit the official [Department of City Planning](http://www.nyc.gov/dcp) site.

The look and feel of this documentation page was generated on [GitHub Pages](https://pages.github.com/) using the Dinky theme.

©2014 The City of New York - [Citywide GIS](http://maps.nyc.gov/doitt/nycitymap)
