## Trustworthy Mail

`trustymail` is a tool that evaluates SPF/DMARC records set in a domain's DNS. It also checks the mail servers listed in a domain's MX records for STARTTLS support. It saves its results to CSV or JSON.

#### Installed as a module

`trustymail` can be installed directly via pip:

```bash
pip install trustymail
```

It can then be run directly:

```bash
trustymail [options] example.com
```

#### Running directly

To run the tool locally from the repository, without installing, first install the requirements:

```bash
pip install -r requirements.txt
```

Then run it as a module via `python -m`:

```bash
python -m trustymail.cli [options] example.com
```


#### Usage and examples

```bash
trustymail [options] INPUT

trustymail dhs.gov
trustymail --output=homeland.csv --debug dhs.gov us-cert.gov usss.gov
trustymail agencies.csv
```

Note: if INPUT ends with `.csv`, domains will be read from CSV. CSV output will always be written to disk, defaulting to `results.csv`.

#### Options

```bash
  -h --help                   Show this message.
  -o --output=OUTFILE         Name of output file. (Default results)
  -t --timeout=TIMEOUT        The DNS lookup timeout in seconds. (Default is 5.)
  --smtp-timeout=TIMEOUT      The SMTP connection timeout in seconds. (Default 
                              is 5.)
  --smtp-localhost=HOSTNAME   The hostname to use when connecting to SMTP 
                              servers.  (Default is the FQDN of the host from
                              which trustymail is being run.)
  --smtp-ports=PORTS          A comma-delimited list of ports at which to look 
                              for SMTP servers.  (Default is "25,465,587".)
  --no-smtp-cache             Do not cache SMTP results during the run.  This 
                              may results in slower scans due to testing the 
                              same mail servers multiple times.
  --mx                        Only check mx records
  --starttls                  Only check mx records and STARTTLS support. 
                              (Implies --mx.)
  --spf                       Only check spf records
  --dmarc                     Only check dmarc records
  --debug                     Output should include error messages.
```

## What's Checked?

For a given domain, MX records, SPF records (TXT), DMARC (TXT, at
\_dmarc.<domain>), and support for STARTTLS are checked.

The following values are returned in `results.csv`:

#### Domain and redirect info

* `Domain` - The domain you're scanning!
* `Base Domain` - The base domain of `Domain`. For example, for a
  Domain of `sub.example.gov`, the Base Domain will be
  `example.gov`. Usually this is the second-level domain, but
  `trustymail` will download and factor in the [Public Suffix
  List](https://publicsuffix.org) when calculating the base domain.
* `Live` - The domain is actually published in the DNS.

#### Mail sending

* `MX Record` - If an MX record was found that contains at least a
  single mail server.
* `Mail Servers` - The list of hosts found in the MX record.
* `Mail Server Ports Tested` - A list of the ports tested for SMTP and
  STARTTLS support.
* `Domain Supports SMTP` - True if and only if __any__ mail servers
  specified in a MX record associated with the domain supports SMTP.
* `Domain Supports SMTP Results` - A list of the mail server and port
  combinations that support SMTP.
* `Domain Supports STARTTLS` - True if and only if __all__ mail
  servers that support SMTP also support STARTTLS.
* `Domain Supports STARTTLS Results` - A list of the mail server and
  port combinations that support STARTTLS.

#### SPF

* `SPF Record` - Whether or not a SPF record was found.
* `Valid SPF` - Whether the SPF record found is syntactically correct,
  per RFC 4408.
* `SPF Results` - The textual representation of any SPF record found
  for the domain.

#### DMARC

* `DMARC Record` - True/False whether or not a DMARC record was found.
* `Valid DMARC` - Whether the DMARC record found is syntactically
  correct.
* `DMARC Results` - The DMARC record that was discovered when querying
  DNS.
* `DMARC Record on Base Domain`, `Valid DMARC Record on Base Domain`,
  `DMARC Results on Base Domain` - Same definition as above, but
  returns the result for the Base Domain. This is important in DMARC
  because if there isn't a DMARC record at the domain, the base domain
  (or "Organizational Domain", per [RFC
  7489](https://tools.ietf.org/html/rfc7489#section-6.6.3)), is
  checked and applied.
* `DMARC Policy` - An adjudication, based on any policies found in
  `DMARC Results` and `DMARC Results on Base Domain`, of the relevant
  DMARC policy that applies.

#### Etc.

* `Syntax Errors` - A list of syntax errors that were detected when
  scanning DMARC or SPF records, or checking for STARTTLS support.

## Public domain

This project is in the worldwide [public domain](LICENSE.md).

This project is in the public domain within the United States, and
copyright and related rights in the work worldwide are waived through
the [CC0 1.0 Universal public domain
dedication](https://creativecommons.org/publicdomain/zero/1.0/).

All contributions to this project will be released under the CC0
dedication. By submitting a pull request, you are agreeing to comply
with this waiver of copyright interest.
