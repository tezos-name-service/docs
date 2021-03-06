# Buys & Renewals

### Contract: TLDRegistrar.Commit

Creates a commitment to buy a second-level domain without disclosing the actual name. This is implemented according to our [commit&reveal](https://en.wikipedia.org/wiki/Commitment_scheme) scheme.

**Entrypoint**: `commit`

| Parameter Type | Description |
| :--- | :--- |
| `bytes` | SHA-512 hash of a packed tuple of **label**, **owner**, ****and a **random nonce** corresponding to the intended buy \(see [TLDRegistrar.Buy](buys-and-renewals.md#contract-tldregistrar-buy)\). The hashed tuple is of the Michelson type`pair (pair bytes address) nat`. Having a random nonce prevents susceptibility to dictionary attacks. |

{% tabs %}
{% tab title="CamelLIGO" %}
```ocaml
type commit_param = bytes

| Commit of bytes
```
{% endtab %}

{% tab title="Michelson" %}
```
parameter (or
  (bytes %commit)
  # ... more entrypoints outside of this interoperability spec
);
```
{% endtab %}
{% endtabs %}

| Error | Description |
| :--- | :--- |
| AMOUNT\_NOT\_ZERO | The transferred **amount** of _tez_ is not zero. |
| COMMITMENT\_EXISTS | The given commitment exists. A commitment with a new nonce has to be generated. |

### Contract: TLDRegistrar.Buy

Buys a second-level domain based on previous commitment \(see [TLDRegistrar.Commit](buys-and-renewals.md#contract-tldregistrar-commit)\).

**Entrypoint**: `buy`

**Amount restriction**: The amount sent with this call has to be equal to the price of the domain. The price in mutez is calculated as `standard_price_per_day * duration / 1000000`.

| Parameter | Type | Description |
| :--- | :--- | :--- |
| **label** | `bytes` | The UTF-8 encoded label of the second-level domain to buy. |
| **duration** | `nat` | Ownership duration represented in days. |
| **owner** | `address` | The new owner of the given domain. |
| **address** | `address option` | The optional address the given domain resolves to. |
| **data** | `(string, bytes) map` | A map of any additional data clients wish to store with the given domain. |
| **nonce** | `nat` | The chosen commitment nonce. |

{% tabs %}
{% tab title="CamelLIGO" %}
```ocaml
type buy_param = {
    label: bytes;
    duration: nat;
    owner: address;
    address: address option;
    data: (string, bytes) map;
    nonce: nat;
}

| Buy of buy_param michelson_pair_left_comb
```
{% endtab %}

{% tab title="Michelson" %}
```
parameter (or
  (pair %buy (bytes %label)
    (pair (nat %duration)
      (pair (address %owner)
        (pair (option %address address)
          (pair (map %data string bytes) (nat %nonce))))))
  # ... more entrypoints outside of this interoperability spec
);
```
{% endtab %}
{% endtabs %}

| Error | Description |
| :--- | :--- |
| COMMITMENT\_DOES\_NOT\_EXIST | Corresponding commitment \(see [TLDRegistrar.Commit](buys-and-renewals.md#contract-tldregistrar-commit)\) was not created before. |
| COMMITMENT\_TOO\_OLD | The commitment is too old \(older than configured age\). Try recreating it again. |
| COMMITMENT\_TOO\_RECENT | The commitment is too recent \(younger than configured age\). Wait for some time. |
| LABEL\_TAKEN | The requested **label** already exists and it is not expired. |
| LABEL\_NOT\_AVAILABLE | The requested **label** is currently not available for registration. |
| LABEL\_IN\_AUCTION | The requested **label** is currently only available in auction. |
| INVALID\_LABEL | The given **label** is not valid. See [Label Validation](domain-operations.md#label-validation). |
| LABEL\_EMPTY | The given label is empty. |
| LABEL\_TOO\_LONG | The label is too long. |
| NAME\_TOO\_LONG | The name \(label + parent\) is too long. |
| DURATION\_TOO\_LOW | The requested **duration** is too low \(lower than the configured minimum\). |
| AMOUNT\_TOO\_LOW | The transferred **amount** is lower than the actual price. |
| AMOUNT\_TOO\_HIGH | The transferred **amount** is higher than the actual price. |

### Contract: TLDRegistrar.Renew

Renews second-level domain for requested duration.

**Entrypoint**: `renew`

**Amount restriction**: The amount sent with this call has to be equal to the price of the domain. The price in mutez is calculated as `standard_price_per_day * duration / 1000000`.

| Parameter | Type | Description |
| :--- | :--- | :--- |
| **label** | `bytes` | The UTF-8 encoded label of the second-level domain to buy. |
| **duration** | `nat` | The renewal duration represented in days. |

{% tabs %}
{% tab title="CamelLIGO" %}
```ocaml
type renew_param = {
    label: bytes;
    duration: nat;
}

| Renew of renew_param michelson_pair_left_comb
```
{% endtab %}

{% tab title="Michelson" %}
```
parameter (or
  (pair %renew
    (bytes %label)
    (nat %duration))
  # ... more entrypoints outside of this interoperability spec
);
```
{% endtab %}
{% endtabs %}

| Error | Description |
| :--- | :--- |
| LABEL\_NOT\_FOUND | The requested **label** does not exist. |
| LABEL\_EXPIRED | The requested **label** exists but it is expired. Therefore it can be bought, not renewed. |
| DURATION\_TOO\_LOW | The specified **duration** is too low \(lower than the configured minimum\). |
| AMOUNT\_TOO\_LOW | The transferred **amount** is lower than the actual price. |
| AMOUNT\_TOO\_HIGH | The transferred **amount** is higher than the actual price. |

