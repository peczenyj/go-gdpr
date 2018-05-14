  <a href="https://goreportcard.com/report/github.com/prebid/go-gdpr"><img src="https://goreportcard.com/badge/github.com/prebid/go-gdpr" /></a>

# Go Support for GDPR

This project includes Go APIs for working with the IAB's [GDPR Transparency & Consent Framework](https://github.com/InteractiveAdvertisingBureau/GDPR-Transparency-and-Consent-Framework/blob/master/Consent%20string%20and%20vendor%20list%20formats%20v1.1%20Final.md).

## Usage

### Consent String Parsing

```go
package main

import (
  "log"

  "github.com/prebid/go-gdpr/vendorconsent"
)

func DemoConsentStringParsing() {
  consentString := "BONciguONcjGKADACHENAOLS1rAHDAFAAEAASABQAMwAeACEAFw"
  data, _ := base64.RawURLEncoding.DecodeString(encodedString)

  consent, err := vendorconsent.Parse(data)
  if err != nil {
    log.Printf("Data was not a valid consent string: %v", err)
    return
  }

  log.Printf("There are %d vendors in this consent string.", consent.MaxVendorID())
  log.Printf("This consent string refers to version %d of the Global Vendor List.", consent.VendorListVersion())
  log.Printf("Vendor %d has the user's consent? %t", 3, consent.HasConsent(3))
}
```

### Vendor List Parsing

```go
package main

import (
  "io/ioutil"
  "log"
  "net/http"

  "github.com/prebid/go-gdpr/vendorlist"
)

func DemoVendorListParsing() {
  resp, _ := http.Get("https://vendorlist.consensu.org/vendorlist.json")
  data, _ := ioutil.ReadAll(resp.Body)

  vendors := ParseLazily(data)
  log.Printf("The Vendor List Version is %d.", vendors.Version())

  vendor := vendors.Vendor(3)
  if vendor == nil {
    log.Print("Vendor 3 did not exist in the list.")
  } else {
    log.Printf("Vendor 3 claimed a legitimate interest for Purpose 3? %t", vendor.LegitimateInterest(3))
    log.Printf("Vendor 3 was used for Purpose 1? %t", vendor.Purpose(1))
  }
}
```

## Contributing

Pull Requests are always welcome for:

1. Regression tests which demonstrate bugs
2. Bugfixes which make regression tests pass
3. Documentation improvements
4. Improved error messages
5. Support for parsing other fields of the Vendor Consent String. The only ones implemented now are ones which we needed.
6. Support for other IAB-related GDPR standards, such as the [Publisher Purposes Consent String](https://github.com/InteractiveAdvertisingBureau/GDPR-Transparency-and-Consent-Framework/blob/master/Consent%20string%20and%20vendor%20list%20formats%20v1.1%20Final.md#publisher-purposes-consent-string-format-)
7. Benchmarks
8. Optimizations which don't break the unit tests and prove to be faster through the benchmarks.

Other pull requests may also be accepted, but larger features should probably be discussed [in an Issue](https://github.com/prebid/go-gdpr/issues/new) first.