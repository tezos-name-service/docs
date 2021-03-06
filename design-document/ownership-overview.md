# Ownership Scheme

![Ownership Scheme](../.gitbook/assets/ownership.png)

The ownership scheme in the name registry is as follows \(from top to bottom\):

1. **Top-level domains** are created by the administrative multisig and then transferred to the TLD registrar contract.
2. **Second-level domains** are in turn created by the TLD registrar on behalf of buying users and transferred to them upon creation. The users are then responsible for their management.
3. **Third-and-higher-level domain** creation and management is completely in the hands of the users.

## Administrative Multisig

A [formally verified](https://arxiv.org/pdf/1909.08671.pdf) multisig contract with keys held by well-known community members is the owner of all Tezos Domains contracts. This allows for administrative actions like:

* creating new top-level domains,
* updating the [TLD configuration](top-level-domain-registrar.md#configuration),
* deploying new versions of contracts if a critical vulnerability is found.

Further details about the current multisig configuration are available [on our website](https://tezos.domains/about/keyholders).

