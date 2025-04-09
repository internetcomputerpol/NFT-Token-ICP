
# 🚀 Deploy ICRC-7 NFT na Lokalnej Replice docelowo na sieci IC  

Instrukcja krok po kroku do lokalnego uruchomienia kanistra NFT zgodnego z ICRC-7.

## 📦 1. Sklonuj repozytorium

```bash
git clone https://github.com/PanIndustrial-Org/icrc_nft.mo.git
cd icrc_nft.mo
```


##  2. Utwórz tożsamości i przypisz je do zmiennych środowiskowych

Jeżeli masz najnowszą wersję dfx to zmień wpis w sekcji dfx version w dfx.json

Polecenie dfx sprawdza lokalny plik dfx więc jeżeli jest tam wpisana inna 
wersja niż bieżąca
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

MOPS ( Jeżeli nie masz skonfigurowanego Menadżera Pakietów Motoko MOPS to zrób to będą nam 
potrzebne biblioteki do obsługi tokenów: 

Zainstaluj MOPS
```bash
curl -fsSL cli.mops.one/install.sh | sh
```
```bash
npm i -g ic-mops
```
Zainicjalizuj mops 
```bash
mops init
```
Zależności powinny się podłączyć i zainstalować z pliku dfx.json 
W razie przebudowy kanistra i komunikatu 

YOU WILL LOSE ALL DATA IN THE CANISTER.


Do you want to proceed? yes/No

Wpisz yes 
! To nadpisze ustawienia kanistra 

3) 
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



# 🍃 Mintowanie NFT na ICP

Do utworzenia tokenów NFT potrzebujemy uruchomić skrypt mintujący kanistra
Najpierw jednak warto sprawdzić jakim principalem uruchamiamy polecenie

```bash
dfx identity whoami
```
jeżeli to principal, którego użyliśmy do deployowania kanistra ledgerowego dla NFT to 
wpisujemy 

```bash
dfx identity get-principal
```

Wpisujemy to identity do poniższego skryptu ( mintowanie 2 NFT z obrazkami na Arwave ) 



```bash
dfx canister call icrc7 icrcX_mint '(
  vec {
    record {
      token_id = 0;
      owner = opt record {
        owner = principal "itpot-bmatb-ujxhf-uzu67-cfx25-ncntc-xfkt5-fsyof-ak55b-gqpnc-nqe";
        subaccount = null
      };
      metadata = variant {
        Class = vec {
          record {
            name = "icrc7:metadata:uri:image";
            value = variant {
              Text = "https://pgtyiowtrumukealn3uruh72iox2iioblf7nbhnluqi4llxrkvbq.arweave.net/eaeEOtONGUUQC27pGh_6Q6-kIcFZftCdq6QRxa7xVUM"
            };
            immutable = true
          }
        }
      };
      memo = opt blob "\00\01";
      override = true;
      created_at_time = null
    };
    record {
      token_id = 1;
      owner = opt record {
        owner = principal "itpot-bmatb-ujxhf-uzu67-cfx25-ncntc-xfkt5-fsyof-ak55b-gqpnc-nqe";
        subaccount = null
      };
      metadata = variant {
        Class = vec {
          record {
            name = "icrc7:metadata:uri:image";
            value = variant {
              Text = "https://2otgruq6peqdmte7fvcuyshul2zgkt74omubctzxxr2542gi2wxq.arweave.net/06Zo0h55IDZMny1FTEj0XrJlT_xzKBFPN7x13mjI1a8"
            };
            immutable = true
          }
        }
      };
      memo = opt blob "\00\01";
      override = true;
      created_at_time = null
    }
  }
)'
```


## 8. Sprawdzanie Jaki Principal ma NFT i  czy pod indeksami 0..5 znajdują się przypisane NFT z metadanymi 

Sprawdzanie jaki Principal ma NFT 

```bash
dfx canister call icrc7 icrc7_owner_of '(vec {0;1;2;3;4})'
```
