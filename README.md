# INFINI Integrated Testing

[Loadgen & Loadrun tutorial](https://www.infinilabs.com/docs/latest/gateway/getting-started/benchmark/)

## How to use

- Download latest `loadgen` from https://release.infinilabs.com/loadgen/stable/
- Download latest `loadrun` from https://release.infinilabs.com/loadrun/stable/
- Set-up necessary dependencies on local machine
- Update `suites/gateway-sample.yml` as needed
- Run `loadrun -config suites/gateway-sample.yml` to run the test suites

## Quick run

If you need to test individual cases manually, it's suggested to set necessary environments on you local machine, follow the same naming conventions as `suites/*.yml`. Once these environments are set-up, you could run `gateway` and `loadgen` without modify each test case.
