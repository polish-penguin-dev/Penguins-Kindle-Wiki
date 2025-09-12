# Kindle Object Documentation

## Dconfig

- Dconfig stores store-specific values, accessible through `kindle.dconfig.getValue(string)`.
- Possible strings:

| String                          | Returns                                                  | Description                                                                                                                                                                 |
|---------------------------------|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `url.store`                     | `https://www.amazon.TLD/gp/digital/juno/index.html`      | [The store URL](https://github.com/polish-penguin-dev/Penguins-Kindle-Wiki/blob/main/Mesquite-WAFs.md#the-kindle-store-remote). It is loaded in an `iframe` within the WAF. |
| `amz.cor`                       | GB                                                       | Region code? Possibly to do with CORS.                                                                                                                                      |
| `url.cantilever`                | `https://digprjsurvey.amazon.TLD/csad/workflow/ed89e52d` | Settings 'Contact Us' page?                                                                                                                                                 |
| `marketplace.obfuscated.id`     | [REDACTED]                                               | 14 digit alphanumeric, possibly sensitive, used by CSAPP.                                                                                                                   |
| `url.ku.eligible`               | `/gp/kindle/ku/sign-up/ajax/is-customer-eligible`        | Check if user is eligible for Kindle Unlimited?                                                                                                                             |
| `url.ku.landing.page`           | `/gp/kindle/ku/sign-up/ui/juno/upsell/ref=`              | Kindle Unlimited 'upsell' page?                                                                                                                                             |
| `url.mysn.reap`                 | `https://rexp-auth-proxy.amazon.cn/social_device_auth`   | Used by `mysn`? Possibly something to do with China specifically, judging by TLD.                                                                                           |
| `url.odac`                      | `https://www.amazon.com`                                 | Used by `odac`                                                                                                                                                              |
| `cmd.account.registration`      | `gp/kw/land`                                             | Used by `odac`                                                                                                                                                              |
| `url.website`                   | `https://www.amazon.TLD`                                 | Returns amazon website with correct regional TLD. Used by `payment` WAF                                                                                                     |
| `url.kindlestore.kcw.metrics`   | `/mn/kcw/workflow/log-metrics`                           | Used by `payment` WAF.                                                                                                                                                      |
| `store.disableDiskFileUse`      |                                                          | Returns nothing. Output used in an `if` statement, so understandable.                                                                                                       |
| `mesquite.interceptorIndicator` | [REDACTED]                                               | 32 character alphanumeric, possibly sensitive, used by the `store` WAF                                                                                                      |