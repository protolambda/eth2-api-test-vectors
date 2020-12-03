# eth2-api-test-vectors

Warning: experimental test vectors.

Generated with: [`protolambda/eth2-api-testgen`](https://github.com/protolambda/eth2-api-testgen)

Do not edit test vectors manually.

Used in [`protolambda/eth2api`](https://github.com/protolambda/eth2api/): Go bindings for the Eth2 API.

## Format

```
{
"description": <string>
"input": {
  <input-key>: <input-value>,
  ...
}
"path": <string>,
"code": <int>,
"post": <optional, json value>
"response": <json value, also when error>
}
```

The input can be any of:
```
state_id                  string              # See api, multiple types, but always a string
block_id                  string              # See api, multiple types, but always a string
validator_id              string              # See api, multiple types, but always a string
val_ids                   []string            # list of strings
slot                      Slot                # number (may be string encoded)
root                      Root                # hex encoded, with 0x prefix
epoch                     Epoch               # number (may be string encoded)
committee_index           CommitteeIndex      # number (may be string encoded)
parent_root               Root                # hex encoded, with 0x prefix
block                     SignedBeaconBlock   # json object presentation of a signed block
validator_statuses        []ValidatorStatus   # list of status strings
```

The test runner can then simply pick the expected inputs from the `input` dict,
 or omit them if not present in the dict (sometimes a parameter is optional).
The inputs can both be used for query parameters and POST bodies. 
Their name matches their usage, not necessarily the query param name in the spec, which is often shorter (e.g. `index`).

### Testing bindings

The idea: Test your bindings by *swapping* the transport.

For each request route, for each method, and for each of its test cases:
1. The test picks the inputs for your API method binding
2. You generate your request with your binding
3. In the mocked transport:
  - compare the generated request against the expected request path (incl query params formatting) and if it's a POST method, check the POST body.
  - return the expected response
4. Check if the binding can successfully decode the response

### Testing server

There's also [`eth2-comply`](https://github.com/INFURA/eth2-comply/) to try your server, but it's not so extensive.

So instead, you could use this generator to hit all API endpoints of your server implementation, and it will warn about unexpected API response codes.
You can then compare the outputs with those generated by other clients, everything is captured in the JSON vectors output!

Some methods may return different things, e.g. the `head` is different, or time has passe and pools have different contents.
This is expected, and indeed difficult to test. Suggestions for improvements here are welcome.
Best solution now is to check if the outputs have a similar shape (e.g. another head block).

## LICENSE

CC0 1.0 Universal, see [`LICENSE`](./LICENSE) file.

