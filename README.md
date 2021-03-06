hlr-lookup-api-ruby-sdk
=======================

Official HLR Lookup API Ruby SDK by www.hlr-lookups.com

This SDK implements the REST API documented at https://www.hlr-lookups.com/en/api-docs

For SDKs in different languages, see https://www.hlr-lookups.com/en/sdks

Requirements
------------
* rest_client
* json
* sinatra

Installation with gem
---------------------
```bash
gem install ruby_hlr_client
```

**Usage Client**
```ruby
require 'ruby_hlr_client/hlr_client'

# Initializes the HLR Lookup Client
# @param username - www.hlr-lookups.com username
# @param password - www.hlr-lookups.com password
# @param ssl - set to false to disable SSL
# @constructor
client = RubyHlrClient::HlrClient.new(
    'username',
    'password'
)

# Submits a synchronous HLR Lookup request. The HLR is queried in real time and results presented in the response body.
# @param msisdn - An MSISDN in international format, e.g. +491788735000
# @param route - An optional route assignment, see: http://www.hlr-lookups.com/en/routing-options
# @param storage - An optional storage assignment, see: http://www.hlr-lookups.com/en/storages
# @returns string (JSON)
#
# Return example: {"success":true,"results":[{"id":"e1fdf26531e4","msisdncountrycode":"DE","msisdn":"+491788735000","statuscode":"HLRSTATUS_DELIVERED","hlrerrorcodeid":null,"subscriberstatus":"SUBSCRIBERSTATUS_CONNECTED","imsi":"262031300000000","mccmnc":"26203","mcc":"262","mnc":"03","msin":"1300000000","servingmsc":"140445","servinghlr":null,"originalnetworkname":"E-Plus","originalcountryname":"Germany","originalcountrycode":"DE","originalcountryprefix":"+49","originalnetworkprefix":"178","roamingnetworkname":"Fixed Line Operators and Other Networks","roamingcountryname":"United States","roamingcountrycode":"US","roamingcountryprefix":"+1","roamingnetworkprefix":"404455","portednetworkname":null,"portedcountryname":null,"portedcountrycode":null,"portedcountryprefix":null,"portednetworkprefix":null,"isvalid":"Yes","isroaming":"Yes","isported":"No","usercharge":"0.0100","inserttime":"2014-12-28 06:22:00.328844+08","storage":"SDK-TEST-SYNC-API","route":"IP1"}]}
print client.submit_sync_lookup_request('+491788735000', 'IP4', 'SDK-TEST')

# Submits a synchronous number type lookup request. Results are presented in the response body.
# @param number - An number in international format, e.g. +4989702626
# @param route - An optional route assignment, see: http://www.hlr-lookups.com/en/routing-options
# @param storage - An optional storage assignment, see: http://www.hlr-lookups.com/en/storages
# @returns string (JSON)
#
# Return example: { "success":true, "results":[ { "id":"2ed0788379c6", "number":"+4989702626", "numbertype":"LANDLINE", "state":"COMPLETED", "isvalid":"Yes", "ispossiblyported":"No", "isvalidshortnumber":"No", "isvanitynumber":"No", "qualifiesforhlrlookup":"No", "originalcarrier":null, "mccmnc":null, "mcc":null, "mnc":null, "countrycode":"DE", "region":"Munich", "timezones":[ "Europe\/Berlin" ], "infotext":"This is a landline number.", "usercharge":"0.0050", "inserttime":"2015-12-04 10:36:41.866283+00", "storage":"SYNC-API-NT-2015-12", "route":"LC1" } ] }
print client.submit_sync_number_type_lookup_request('+4989702626', 'LC1', 'SDK-TEST')

# Sets the callback URL for asynchronous lookups. Read more about the concept of asynchronous HLR lookups @ http://www.hlr-lookups.com/en/asynchronous-hlr-lookup-api
# @param url - callback url on your server
# @returns string (JSON)
#
# Return example: {"success":true,"messages":[],"results":{"url":"http:\/\/user:pass@www.your-server.com\/path\/file"}}
print client.set_async_callback_url('http://user:pass@www.your-server.com/path/file')

# Sets the callback URL for asynchronous number type lookups.
# @param url - callback url on your server
# @returns string (JSON)
#
# Return example: {"success":true,"messages":[],"results":{"url":"http:\/\/user:pass@www.your-server.com\/path\/file"}}
print client.set_nt_async_callback_url('http://user:pass@www.your-server.com/path/file')

# Submits asynchronous HLR Lookups containing up to 1,000 MSISDNs per request. Results are sent back asynchronously to a callback URL on your server.
# @param msisdns - A list of MSISDNs in international format, e.g. +491788735000
# @param route - An optional route assignment, see: http://www.hlr-lookups.com/en/routing-options
# @param storage - An optional storage assignment, see: http://www.hlr-lookups.com/en/storages
# @returns string (JSON)
#
# Return example: {"success":true,"messages":[],"results":{"acceptedMsisdns":[{"id":"e489a092eba7","msisdn":"+491788735000"},{"id":"23ad48bf0c26","msisdn":"+491788735001"}],"rejectedMsisdns":[],"acceptedMsisdnCount":2,"rejectedMsisdnCount":0,"totalCount":2,"charge":0.02,"storage":"SDK-TEST-ASYNC-API","route":"IP4"}}
print client.submit_async_lookup_request(['+491788735000', '+491788735001'])

# Submits asynchronous number type lookups containing up to 1,000 numbers per request. Results are sent back asynchronously to a callback URL on your server.
# @param numbers - A list of numbers in international format, e.g. +4989702626,+491788735000
# @param route - An optional route assignment, see: http://www.hlr-lookups.com/en/routing-options
# @param storage - An optional storage assignment, see: http://www.hlr-lookups.com/en/storages
# @returns string (JSON)
#
# Return example: { "success":true, "messages":[], "results":{ "acceptedNumbers":[ { "id":"f09b30014d5e", "number":"+4989702626" }, { "id":"364c0ad33c02", "number":"+491788735000" } ], "rejectedNumbers":[ { "id":null, "number":"asdf" } ], "acceptedNumberCount":2, "rejectedNumberCount":1, "totalCount":3, "charge":0.01, "storage":"ASYNC-API-NT-2015-12", "route":"LC1" } }
print client.submit_async_number_type_lookup_request(['+4989702626','+491788735000'])

# Returns the remaining balance (EUR) in your account.
# @returns string (JSON)
#
# Return example: {"success":true,"messages":[],"results":{"balance":"5878.24600"}}
print client.get_balance

# Performs a health check on the system status, the user account and availability of each route.
# @returns string (JSON)
#
# Return example: { "success":true, "results":{ "system":{ "state":"up" }, "routes":{ "states":{ "IP1":"up", "ST2":"up", "SV3":"up", "IP4":"up", "XT5":"up", "XT6":"up", "NT7":"up", "LC1":"up" } }, "account":{ "lookupsPermitted":true, "balance":"295.23000" } } }
print client.do_health_check    
```

**Usage Callback Handler**
```ruby
require 'sinatra'
require 'ruby_hlr_client/hlr_callback_handler'

set :run, true

get '/' do

  client = RubyHlrClient::HlrCallbackHandler.new

  # Parses an asynchronous HLR Lookup callback and returns a JSON string with the results.
  # @param params
  # @returns string (JSON)
  #
  # Return example: {"success":true,"results":[{"id":"40ebb8d9e7cc","msisdncountrycode":"DE","msisdn":"+491788735001","statuscode":"HLRSTATUS_DELIVERED","hlrerrorcodeid":null,"subscriberstatus":"SUBSCRIBERSTATUS_CONNECTED","imsi":"262032000000000","mccmnc":"26203","mcc":"262","mnc":"03","msin":"2000000000","servingmsc":"491770","servinghlr":null,"originalnetworkname":"178","originalcountryname":"Germany","originalcountrycode":"DE","originalcountryprefix":"+49","originalnetworkprefix":"178","roamingnetworkname":null,"roamingcountryname":null,"roamingcountrycode":null,"roamingcountryprefix":null,"roamingnetworkprefix":null,"portednetworkname":null,"portedcountryname":null,"portedcountrycode":null,"portedcountryprefix":null,"portednetworkprefix":null,"isvalid":"Yes","isroaming":"No","isported":"No","usercharge":"0.0100","inserttime":"2014-12-28 05:53:03.765798+08","storage":"ASYNC-API","route":"IP4"}]}
  print client.parse_callback(params)

  # send HTTP response body (OK)
  content_type :text
  return client.send_response

end
```

With that you should be ready to go!

Tests
-----

The code contains annotations and you can find usage examples as tests in `tests/`:
* `bin/test_client.rb`
* `bin/test_callback_handler.rb`

Please refer to https://www.hlr-lookups.com/en/sdk-ruby for further documentation or send us an email to service@hlr-lookups.com.

Support and Feedback
--------------------
Your feedback is appreciated! If you have specific problems or bugs with this SDK, please file an issue on Github. For general feedback and support requests, send an email to service@hlr-lookups.com.

Contributing
------------

1. Fork it ( https://github.com/vmgltd/hlr-lookup-api-ruby-sdk/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request

