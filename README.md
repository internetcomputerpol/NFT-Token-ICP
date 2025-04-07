
# 🚀 Deploy ICRC-7 NFT na Lokalnej Replice docelowo na sieci IC  

Instrukcja krok po kroku do lokalnego uruchomienia kanistra NFT zgodnego z ICRC-7.

## 📦 1. Sklonuj repozytorium

```bash
git clone https://github.com/PanIndustrial-Org/icrc_nft.mo.git
cd icrc_nft.mo
```


##  2. Utwórz tożsamości i przypisz je do zmiennych środowiskowych

Jeżeli masz najnowszą wersję dfx to zmień wpis w sekcji dfx version w dfx.json
To jest jedna z ostatnich linijek w pliku dfx.json:

```bash
"dfx": "0.25.1"
```
alternatywnie możesz przeinstalować dfx do wersji 24




```bash
# Tworzenie tożsamości Alice
dfx identity new alice
dfx identity use alice
export ALICE_PRINCIPAL=$(dfx identity get-principal)

# Tworzenie tożsamości deployera
dfx identity new icrc7_deployer
dfx identity use icrc7_deployer
export ADMIN_PRINCIPAL=$(dfx identity get-principal)
```

##  3. Uruchom lokalną replikę IC
```bash
dfx start --clean --background
```

## 4. Wdróż kanister z argumentami przekazanymi z terminala

```bash
dfx deploy icrc7 --argument '(
  record {
    icrc7_args = opt record {
      symbol = opt "NBL";
      name = opt "NASA Nebulas";
      description = opt "A Collection of Nebulas Captured by NASA";
      logo = opt "https://www.nasa.gov/wp-content/themes/nasa/assets/images/nasa-logo.svg";
      supply_cap = null;
      allow_transfers = null;
      max_query_batch_size = opt 100;
      max_update_batch_size = opt 100;
      default_take_value = opt 1000;
      max_take_value = opt 10000;
      max_memo_size = opt 512;
      permitted_drift = null;
      tx_window = null;
      burn_account = null;
      deployer = principal "'$(dfx identity get-principal)'";
      supported_standards = null;
    };
    icrc37_args = null;
    icrc3_args = null;
  }
)' --mode reinstall
```

## 5. Zapisz ID kanistra do zmiennej środowiskowej
```bash
export ICRC7_CANISTER=$(dfx canister id icrc7)
```

## 6. Zainicjalizuj kanister
```bash
dfx canister call icrc7 init
```
## 7. Sprawdź podstawowe dane NFT
```bash
dfx canister call icrc7 icrc7_name
dfx canister call icrc7 icrc7_symbol
```
dfx canister call icrc7 icrc7_name
dfx canister call icrc7 icrc7_symbol
