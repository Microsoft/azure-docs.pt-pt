---
title: Lista de classificações apoiadas
description: Esta página lista as classificações suportadas do sistema em Azure Purview.
author: anmuk601
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: reference
ms.date: 02/05/2021
ms.openlocfilehash: 60aede65f8217d46844398d0199ff7edca7f36a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200807"
---
# <a name="supported-classifications-in-azure-purview"></a>Classificações apoiadas em Azure Purview

Este artigo lista as classificações suportadas e definidas do sistema em Azure Purview (Preview).


- **Limiar de correspondência distinto**: O número total de valores de dados distintos que precisam de ser encontrados numa coluna antes que o scanner executa o padrão de dados nele. As nossas regras de classificação do sistema exigem que existam pelo menos 8 valores distintos em cada coluna para os submeter à classificação. O sistema requer este valor para se certificar de que a coluna contém dados suficientes para que o scanner o classifique com precisão. Por exemplo, uma coluna que contenha várias linhas que contenham o valor 1 não será classificada. Colunas que contêm uma linha com um valor e o resto das linhas têm valores nulos também não serão classificados. Se especificar vários padrões, este valor aplica-se a cada um deles.

- **Limiar mínimo de correspondência**: É a percentagem mínima de correspondências de valor de dados numa coluna que deve ser encontrada pelo scanner para a classificação a aplicar. O valor de classificação do sistema é fixado em 60%.


## <a name="defined-system-classifications"></a>Classificações definidas do sistema

A Azure Purview classifica os dados por [RegEx](https://wikipedia.org/wiki/Regular_expression) e [Bloom Filter](https://wikipedia.org/wiki/Bloom_filter). As listas que se seguem descrevem o formato, padrão e palavras-chave para as classificações definidas do sistema Azure Purview.

Cada nome de classificação é pré-fixado pela MICROSOFT.

## <a name="bloom-filter-classifications"></a>Classificações de filtros de florescimento

## <a name="city-country-and-place"></a>Cidade, País e Lugar

Os filtros City, Country e Place foram preparados utilizando os melhores conjuntos de dados disponíveis para a preparação dos dados.

## <a name="person"></a>Pessoa

O filtro de flor de pessoa foi preparado utilizando os dois conjuntos de dados abaixo.

- [2010 Us Census Data for Last Names (entradas de 162-K)](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [Nomes populares dos bebés (da SSN), utilizando todos os anos 1880-2019 (entradas de 98-K)](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>Classificações RegEx

## <a name="aba-routing"></a>Encaminhamento ABA

### <a name="format"></a>Formato

Nove dígitos, que podem estar num padrão formatado ou não equipado

### <a name="pattern"></a>Padrão

Formatado:

- quatro dígitos começando com 0, 1, 2, 3, 6, 7 ou 8
- um hífen
- quatro dígitos
- um hífen
- um dígito

Não formatados: nove dígitos consecutivos a começar com 0, 1, 2, 3, 6, 7 ou 8

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_aba_routing"></a>Keyword_aba_routing

```
amba number
aba #
aba
abarouting#
abaroutingnumber
americanbankassociationrouting#
americanbankassociationroutingnumber
bank routing#
bankroutingnumber
routing #
routing no
routing number
routing transit number
routing#
RTN
```

## <a name="argentina-national-identity-dni-number"></a>Número de identidade nacional argentina (DNI)

### <a name="format"></a>Formato

Oito dígitos com ou sem períodos

### <a name="pattern"></a>Padrão

Oito dígitos:

- dois dígitos
- um período opcional
- três dígitos
- um período opcional
- três dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_argentina_national_id"></a>Keyword_argentina_national_id

```
Argentina National Identity number
cedula
cédula
dni
documento nacional de identidad
documento número
documento numero
registro nacional de las personas
rnp
```

## <a name="australia-bank-account-number"></a>Número de conta bancária na Austrália

### <a name="format"></a>Formato

Seis a 10 dígitos com ou sem número de uma sucursal do Estado

### <a name="pattern"></a>Padrão

O número da conta é de 6 a 10 dígitos.

Número da sucursal do banco da Austrália:

- três dígitos
- um hífen
- três dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_australia_bank_account_number"></a>Keyword_australia_bank_account_number

```
swift bank code
correspondent bank
base currency
usa account
holder address
bank address
information account
fund transfers
bank charges
bank details
banking information
full names
iaea
```

## <a name="australia-drivers-license-number"></a>Número da carta de condução da Austrália

### <a name="format"></a>Formato
Nove letras e dígitos

### <a name="pattern"></a>Padrão
nove letras e dígitos:

- dois dígitos ou letras (não sensíveis a casos)
- dois dígitos
- cinco dígitos ou letras (não sensíveis a casos)

OR

- uma a duas letras opcionais (não sensíveis a casos)
- quatro a nove dígitos

OR

- nove dígitos ou letras (não sensíveis a casos)

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_australia_drivers_license_number"></a>Keyword_australia_drivers_license_number

```
international driving permits
australian automobile association
international driving permit
DriverLicence
DriverLicences
Driver Lic
Driver License
Driver Licenses
DriversLic
DriversLicence
DriversLicences
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
DriverLic#
DriverLics#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
```

#### <a name="keyword_australia_drivers_license_number_exclusions"></a>Keyword_australia_drivers_license_number_exclusions

```
aaa
DriverLicense
DriverLicenses
Driver License
Driver Licenses
DriversLicense
DriversLicenses
Drivers License
Drivers Licenses
Driver'License
Driver'Licenses
Driver' License
Driver' Licenses
Driver'sLicense
Driver'sLicenses
Driver's License
Driver's Licenses
DriverLicense#
DriverLicenses#
Driver License#
Driver Licenses#
DriversLicense#
DriversLicenses#
Drivers License#
Drivers Licenses#
Driver'License#
Driver'Licenses#
Driver' License#
Driver' Licenses#
Driver'sLicense#
Driver'sLicenses#
Driver's License#
Driver's Licenses#
```

## <a name="australian-medicare-number"></a>Número de medicare australiano

### <a name="format"></a>Formato

10-11 dígitos

### <a name="pattern"></a>Padrão

10-11 dígitos:

- primeiro dígito está na gama 2-6
- nono dígito é um dígito de verificação
- décimo dígito é o dígito de emissão
- décimo primeiro dígito (opcional) é o número individual

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_australia_medicare_number"></a>Keyword_Australia_Medicare_Number

```
bank account details
medicare payments
mortgage account
bank payments
information branch
credit card loan
department of human services
local service
medicare
```

## <a name="australia-passport-number"></a>Número de passaporte da Austrália

### <a name="format"></a>Formato

Uma carta seguida de sete dígitos

### <a name="pattern"></a>Padrão

Uma carta (não sensível a casos) seguida de sete dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_passport"></a>Passaporte de palavra-chave \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

#### <a name="keyword_australia_passport_number"></a>Palavra-chave \_ \_ número do passaporte da Austrália \_

```
passport
passport details
immigration and citizenship
commonwealth of australia
department of immigration
residential address
department of immigration and citizenship
visa
national identity card
passport number
travel document
issuing authority
```

## <a name="australia-tax-file-number"></a>Número de ficheiros fiscais da Austrália

### <a name="format"></a>Formato

oito a nove dígitos

### <a name="pattern"></a>Padrão

oito a nove dígitos normalmente apresentados com espaços da seguinte forma:

- três dígitos
- um espaço opcional
- três dígitos
- um espaço opcional
- dois a três dígitos onde o último dígito é um dígito de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_australia_tax_file_number"></a>Número de \_ ficheiro de imposto da Austrália \_ \_ \_

```
australian business number
marginal tax rate
medicare levy
portfolio number
service veterans
withholding tax
individual tax return
tax file number
tfn
```

## <a name="belgium-national-number"></a>Número nacional da Bélgica

### <a name="format"></a>Formato

11 dígitos mais delimitadores opcionais

### <a name="pattern"></a>Padrão

11 dígitos mais limítrodulites:

- seis dígitos e dois períodos opcionais no formato YY. MM.DD para data de nascimento
- Um delimitador opcional de ponto, traço, espaço
- três dígitos sequenciais (ímpar para machos, mesmo para as fêmeas)
- Um delimitador opcional de ponto, traço, espaço
- dois dígitos de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_belgium_national_number"></a>Palavra-chave \_ número nacional da Bélgica \_ \_

```
be lasting aantal
bnn#
bnn
carte d'identité
identifiant national
identifiantnational#
identificatie
identification
identifikation
identifikationsnummer
identifizierung
identité
identiteit
identiteitskaart
identity
inscription
national number
national register
national number#
national number
nif#
nif
numéro d'assuré
numéro de registre national
numéro de sécurité
numéro d'identification
numéro d'immatriculation
numéro national
numéronational#
personal ID number
personalausweis
personalidnumber#
registratie
registration
registrationsnumme
registrierung
social security number
ssn#
ssn
steuernummer
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="brazil-cpf-number"></a>Número do CPF do Brasil

### <a name="format"></a>Formato

11 dígitos que incluem um dígito de verificação e podem ser formatados ou não equipados

### <a name="pattern"></a>Padrão

Formatado:

- três dígitos
- um período
- três dígitos
- um período
- três dígitos
- um hífen
- dois dígitos, que são dígitos de verificação

Não formatado:

- 11 dígitos onde os dois últimos dígitos são dígitos de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_brazil_cpf"></a>Palavra-chave \_ do \_ CPF

```
CPF
Identification
Registration
Revenue
Cadastro de Pessoas Físicas
Imposto
Identificação
Inscrição
Receita
```

## <a name="brazil-legal-entity-number-cnpj"></a>Número de entidade jurídica do Brasil (CNPJ)

### <a name="format"></a>Formato

14 dígitos que incluem um número de registo, número de filial e dígitos de verificação, além delimiters

### <a name="pattern"></a>Padrão

14 dígitos, mais delimiters:

- dois dígitos
- um período
- três dígitos
- um período
- três dígitos (estes primeiros oito dígitos são o número de registo)
- um corte para a frente
- número de ramo de quatro dígitos
- um hífen
- dois dígitos, que são dígitos de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_brazil_cnpj"></a>Palavra-chave \_ do \_ cnpj brasileiro

```
CNPJ
CNPJ/MF
CNPJ-MF
National Registry of Legal Entities
Taxpayers Registry
Legal entity
Legal entities
Registration Status
Business
Company
CNPJ
Cadastro Nacional da Pessoa Jurídica
Cadastro Geral de Contribuintes
CGC
Pessoa jurídica
Pessoas jurídicas
Situação cadastral
Inscrição
Empresa
```

## <a name="brazil-national-identification-card-rg"></a>Cartão de identificação nacional do Brasil (RG)

### <a name="format"></a>Formato

:::no-loc text="Registro Geral (old format): Nine digits":::

:::no-loc text="Registro de Identidade (RIC) (new format): 11 digits":::

### <a name="pattern"></a>Padrão

:::no-loc text="Registro Geral (old format):":::

- dois dígitos
- um período
- três dígitos
- um período
- três dígitos
- um hífen
- um dígito, que é um dígito de verificação

:::no-loc text="Registro de Identidade (RIC) (new format):":::

- 10 dígitos
- um hífen
- um dígito, que é um dígito de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_brazil_rg"></a>Palavra-chave \_ brasil \_ rg

```
Cédula de identidade
identity card
national ID
número de rregistro
registro de Iidentidade
registro geral
RG (this keyword is case-sensitive)
RIC (this keyword is case-sensitive)
```

## <a name="canada-bank-account-number"></a>Número da conta bancária do Canadá

### <a name="format"></a>Formato

7 ou 12 dígitos

### <a name="pattern"></a>Padrão

Um número de conta bancária do Canadá é de 7 ou 12 dígitos.

Um número de trânsito de conta bancária no Canadá é:

- cinco dígitos
- um hífen
- três dígitos OU
- um zero &quot; 0&quot;
- oito dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_canada_bank_account_number"></a>Número da \_ conta bancária do Canadá \_ \_ \_

```
canada savings bonds
canada revenue agency
canadian financial institution
direct deposit form
canadian citizen
legal representative
notary public
commissioner for oaths
child care benefit
universal child care
canada child tax benefit
income tax benefit
harmonized sales tax
social insurance number
income tax refund
child tax benefit
territorial payments
institution number
deposit request
banking information
direct deposit
```

## <a name="canada-drivers-license-number"></a>Número da carta de condução do Canadá

### <a name="format"></a>Formato

Varia por província

### <a name="pattern"></a>Padrão

Vários padrões que abrangem Alberta, Colúmbia Britânica, Manitoba, New Brunswick, Terra Nova/Labrador, Nova Escócia, Ontário, Ilha do Príncipe Eduardo, Quebec e Saskatchewan

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_province_name_drivers_license_name"></a>Palavra-chave \_ \_ [nome da província] \_ nome da carta de \_ condução \_

- A abreviatura da província, por exemplo AB
- O nome da província, por exemplo Alberta

#### <a name="keyword_canada_drivers_license"></a>\_Carta-chave da carta de \_ condução do Canadá \_

```
DL
DLS
CDL
CDLS
DriverLic
DriverLics
DriverLicense
DriverLicenses
DriverLicence
DriverLicences
Driver Lic
Driver Lics
Driver License
Driver Licenses
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicence
DriversLicences
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
Driver's License
Driver's Licenses
Permis de Conduire
ID
IDs
ID card number
ID card numbers
ID card #
ID card #s
ID card
ID cards
ID card
identification number
identification numbers
identification #
identification #s
identification card
identification cards
identification
DL#
DLS#
CDL#
CDLS#
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
Driver's License#
Driver's Licenses#
Permis de Conduire#
ID#
IDs#
ID card#
ID cards#
ID card#
identification card#
identification cards#
identification#
```

## <a name="canada-health-service-number"></a>Número do serviço de saúde do Canadá

### <a name="format"></a>Formato

10 dígitos

### <a name="pattern"></a>Padrão

10 dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_canada_health_service_number"></a>Número do \_ \_ serviço de saúde do Canadá \_ de palavras-chave \_

```
personal health number
patient information
health services
specialty services
automobile accident
patient hospital
psychiatrist
workers compensation
disability
```

## <a name="canada-passport-number"></a>Número do passaporte do Canadá

### <a name="format"></a>Formato

duas letras maiúsculas seguidas de seis dígitos

### <a name="pattern"></a>Padrão

duas letras maiúsculas seguidas de seis dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_canada_passport_number"></a>Número de \_ passaporte do Canadá \_ \_ de palavra-chave

```
canadian citizenship
canadian passport
passport application
passport photos
certified translator
canadian citizens
processing times
renewal application
```

#### <a name="keyword_passport"></a>Passaporte de palavra-chave \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="canada-personal-health-identification-number-phin"></a>Número de identificação pessoal da saúde do Canadá (PHIN)

### <a name="format"></a>Formato

nove dígitos

### <a name="pattern"></a>Padrão

nove dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_canada_phin"></a>Palavra-chave \_ do \_ canadá phin

```
social insurance number
health information act
income tax information
manitoba health
health registration
prescription purchases
benefit eligibility
personal health
power of attorney
registration number
personal health number
practitioner referral
wellness professional
patient referral
health and wellness
```

#### <a name="keyword_canada_provinces"></a>Palavra-chave \_ \_ das províncias do Canadá

```
Nunavut
Quebec
Northwest Territories
Ontario
British Columbia
Alberta
Saskatchewan
Manitoba
Yukon
Newfoundland and Labrador
New Brunswick
Nova Scotia
Prince Edward Island
Canada
```

## <a name="canada-social-insurance-number"></a>Número do seguro social do Canadá

### <a name="format"></a>Formato

nove dígitos com hífenes ou espaços opcionais

### <a name="pattern"></a>Padrão

Formatado:

- três dígitos
- um hífen ou espaço
- três dígitos
- um hífen ou espaço
- três dígitos

Não formatado: nove dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_sin"></a>Pecado de palavra-chave \_

```
sin
social insurance
numero d'assurance sociale
sins
ssn
ssns
social security
numero d'assurance social
national identification number
national ID
sin#
soc ins
social ins
```

#### <a name="keyword_sin_collaborative"></a>Palavra-chave \_ pecado \_ colaborativo

```
driver's license
drivers license
driver's license
drivers license
DOB
Birthdate
Birthday
Date of Birth
```

## <a name="chile-identity-card-number"></a>Número do bilhete de identidade do Chile

### <a name="format"></a>Formato

sete a oito dígitos mais delimitadores um dígito de cheque ou letra

### <a name="pattern"></a>Padrão

sete a oito dígitos mais limítrodulites:

- um a dois dígitos
- um período opcional
- três dígitos
- um período opcional
- três dígitos
- um traço
- um dígito ou letra (não sensível a caso) que é um dígito de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_chile_id_card"></a>Cartão \_ de id de \_ palavra-chave chile \_

```
cédula de identidad
identificación
national identification
national identification number
national ID
número de identificación nacional
rol único nacional
rol único tributario
RUN
RUT
tarjeta de identificación
Rol Unico Nacional
Rol Unico Tributario
RUN#
RUT#
nationaluniqueroleID#
nacional identidad
número identificación
identidad número
numero identificacion
identidad numero
Chilean identity no.
Chilean identity number
Chilean identity #
Unique Tax Registry
Unique Tributary Role
Unique Tax Role
Unique Tributary Number
Unique National Number
Unique National Role
National unique role
Chile identity no.
Chile identity number
Chile identity #
```

## <a name="china-resident-identity-card-prc-number"></a>Número do bilhete de identidade residente na China (RPC)

### <a name="format"></a>Formato

18 dígitos

### <a name="pattern"></a>Padrão

18 dígitos:

- seis dígitos, que são um código de endereço
- oito dígitos na forma YYYYMMDD, que são a data de nascimento
- três dígitos, que são um código de ordem
- um dígito, que é um dígito de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_china_resident_id"></a>Palavra-chave \_ china \_ residente \_ id

```
Resident Identity Card
PRC
National Identification Card
身份证
居民身份证
居民身份证
鉴定
身分證
居民身份證
鑑定
```

## <a name="credit-card-number"></a>Número do cartão de crédito

### <a name="format"></a>Formato

14 a 16 dígitos, que podem ser formatados ou nãoformados (dddd) e que devem passar no teste de Luhn.

### <a name="pattern"></a>Padrão

Padrão complexo e robusto que deteta cartões de todas as grandes marcas em todo o mundo, incluindo Visa, MasterCard, Discover Card, JCB, American Express, cartões oferta e cartões de jantar.

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_cc_verification"></a>Verificação de palavras-chave \_ cc \_

```
card verification
card identification number
cvn
cid
cvc2
cvv2
pin block
security code
security number
security no
issue number
issue no
cryptogramme
numéro de sécurité
numero de securite
kreditkartenprüfnummer
kreditkartenprufnummer
prüfziffer
prufziffer
sicherheits Kode
sicherheitscode
sicherheitsnummer
verfalldatum
codice di verifica
cod. sicurezza
cod sicurezza
n autorizzazione
código
codigo
cod. seg
cod seg
código de segurança
codigo de seguranca
codigo de segurança
código de seguranca
cód. segurança
cod. seguranca
cod. segurança
cód. seguranca
cód segurança
cod seguranca
cod segurança
cód seguranca
número de verificação
numero de verificacao
ablauf
gültig bis
gültigkeitsdatum
gultig bis
gultigkeitsdatum
scadenza
data scad
fecha de expiracion
fecha de venc
vencimiento
válido hasta
valido hasta
vto
data de expiração
data de expiracao
data em que expira
validade
valor
vencimento
transaction
transaction number
reference number
セキュリティコード
セキュリティ コード
セキュリティナンバー
セキュリティ ナンバー
セキュリティ番号
```

#### <a name="keyword_cc_name"></a>Nome cc palavra-chave \_ \_

```
amex
Americans express
Americans express
americano espresso
Visa
Mastercard
master card
mc
master cards
master cards
diner's Club
diners club
diners club
discover
discover card
discover card
discover cards
JCB
BrandSmart
japanese card bureau
carte blanche
carteblanche
credit card
cc#
cc#:
expiration date
exp date
expiry date
date d'expiration
date d'exp
date expiration
bank card
bank card
card number
card num
card number
card numbers
card numbers
credit card
credit cards
credit cards
ccn
card holder
cardholder
card holders
cardholders
check card
check card
check cards
check cards
debit card
debit card
debit cards
debit cards
atm card
atmcard
atm cards
atmcards
enroute
en route
card type
Cardmember Acct
cardmember account
Card no
Corporate Card
Corporate cards
Type of card
card account number
card member account
Cardmember Acct.
card no.
card no
card number
carte bancaire
carte de crédit
carte de credit
numéro de carte
numero de carte
nº de la carte
nº de carte
kreditkarte
karte
karteninhaber
karteninhabers
kreditkarteninhaber
kreditkarteninstitut
kreditkartentyp
eigentümername
kartennr
kartennummer
kreditkartennummer
kreditkarten-nummer
carta di credito
carta credito
n. carta
n carta
nr. carta
nr carta
numero carta
numero della carta
numero di carta
tarjeta credito
tarjeta de credito
tarjeta crédito
tarjeta de crédito
tarjeta de atm
tarjeta atm
tarjeta debito
tarjeta de debito
tarjeta débito
tarjeta de débito
nº de tarjeta
no. de tarjeta
no de tarjeta
numero de tarjeta
número de tarjeta
tarjeta no
tarjetahabiente
cartão de crédito
cartão de credito
cartao de crédito
cartao de credito
cartão de débito
cartao de débito
cartão de debito
cartao de debito
débito automático
debito automatico
número do cartão
numero do cartão
número do cartao
numero do cartao
número de cartão
numero de cartão
número de cartao
numero de cartao
nº do cartão
nº do cartao
nº. do cartão
no do cartão
no do cartao
no. do cartão
no. do cartao
クレジットカード番号
クレジットカードナンバー
クレジットカード＃
クレジットカード
クレジット
クレカ
カード番号
カードナンバー
カード＃
アメックス
アメリカンエクスプレス
アメリカン エクスプレス
Visaカード
Visa カード
マスターカード
マスター カード
マスター
ダイナースクラブ
ダイナース クラブ
ダイナース
有効期限
期限
キャッシュカード
キャッシュ カード
カード名義人
カードの名義人
カードの名義
デビット カード
デビットカード
```

## <a name="croatia-drivers-license-number"></a>Número da carta de condução da Croácia

Esta entidade sensível do tipo informação só está disponível no tipo de informação sensível ao número de carta de condução da UE.

### <a name="format"></a>Formato

oito dígitos sem espaços e delimiters

### <a name="pattern"></a>Padrão

oito dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keywords_eu_drivers_license_number"></a>Palavras-chave \_ número da carta de \_ \_ condução da UE \_

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_croatia_eu_drivers_license_number"></a>Palavras-chave \_ número da carta de \_ \_ \_ condução da Ue \_

```
vozačka dozvola
vozačke dozvole
```

## <a name="croatia-identity-card-number"></a>Número do bilhete de identidade da Croácia

Esta entidade sensível do tipo informação está incluída no tipo de informação sensível ao número de identificação nacional da UE e está disponível como entidade de tipo informação sensível autónoma.

### <a name="format"></a>Formato

nove dígitos

### <a name="pattern"></a>Padrão

nove dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_croatia_id_card"></a>\_ \_ Cartão de id da croácia palavra-chave \_

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="croatia-personal-identification-oib-number"></a>Número de identificação pessoal da Croácia (OIB)

### <a name="format"></a>Formato

11 dígitos

### <a name="pattern"></a>Padrão

11 dígitos:

- 10 dígitos
- último dígito é um dígito de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_croatia_oib_number"></a>Palavra-chave \_ \_ \_ croácia oib número

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax Id number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="denmark-personal-identification-number"></a>Número de identificação pessoal da Dinamarca

### <a name="format"></a>Formato

10 dígitos contendo um hífen

### <a name="pattern"></a>Padrão

10 dígitos:

- seis dígitos no formato DDMMYY, que são a data de nascimento
- um hífen
- quatro dígitos onde o dígito final é um dígito de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_denmark_id"></a>Palavra-chave \_ da Dinamarca \_ id

```
centrale person register
civilt registreringssystem
cpr
cpr#
gesundheitskarte nummer
gesundheitsversicherungkarte nummer
health card
health insurance card number
health insurance number
identification number
identifikationsnummer
identifikationsnummer#
identity number
krankenkassennummer
national ID#
national number#
national number
personalidnumber#
personalidentityno#
personal ID number
personnummer
personnummer#
reisekrankenversicherungskartenummer
rejsesygesikringskort
ssn
ssn#
skat ID
skat kode
skat nummer
skattenummer
social security number
sundhedsforsikringskort
sundhedsforsikringsnummer
sundhedskort
sundhedskortnummer
sygesikring
sygesikringkortnummer
tax code
travel health insurance card
uniqueidentityno#
tax number
tax registration number
tax ID
tax identification number
tax ID#
tax number#
tax no
tax no#
tax number
tax identification no
tin#
tax ID no#
tax ID number#
tax no#
tin ID
tin no
cpr.nr
cprnr
cprnummer
personnr
person register
sygesikringsbevis
sygesikringsbevisnr
sygesikringsbevisnummer
sygesikringskort
sygesikringskortnr
sygesikringskortnummer
sygesikringsnr
sygesikringsnummer
```

## <a name="eu-debit-card-number"></a>Número do cartão de débito da UE

### <a name="format"></a>Formato

16 dígitos

### <a name="pattern"></a>Padrão

Padrão complexo e robusto

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_eu_debit_card"></a>\_Cartão de débito da UE \_ de \_ palavras-chave

```
account number
card number
card no.
security number
cc#
```

#### <a name="keyword_card_terms_dict"></a>Termos \_ de cartão de \_ palavra-chave \_ dict

```
acct nbr
acct num
acct no
Americans express
Americans express
americano espresso
amex
atm card
atm cards
atm kaart
atmcard
atmcards
atmkaart
atmkaarten
ban contact
bank card
bankkaart
card holder
card holders
card num
card number
card numbers
card type
cardano numerico
cardholder
cardholders
card number
card numbers
carta bianca
carta credito
carta di credito
cartao de credito
cartao de crédito
cartao de debito
cartao de débito
carte bancaire
carte blanche
carte bleue
carte de credit
carte de crédit
carte di credito
carteblanche
cartão de credito
cartão de crédito
cartão de debito
cartão de débito
cb
ccn
check card
check cards
check card
check cards
chequekaart
cirrus
cirrus-edc-maestro
controlekaart
controlekaarten
credit card
credit cards
credit card
credit cards
debetkaart
debetkaarten
debit card
debit cards
debit card
debit cards
debito automatico
diners club
diners club
discover
discover card
discover cards
discover card
discover cards
débito automático
edc
eigentümername
european debit card
hoofdkaart
hoofdkaarten
in viaggio
japanese card bureau
japanse kaartdienst
jcb
kaart
kaart num
kaartaantal
kaartaantallen
kaarthouder
kaarthouders
karte
karteninhaber
karteninhabers
kartennr
kartennummer
kreditkarte
kreditkarten-nummer
kreditkarteninhaber
kreditkarteninstitut
kreditkartennummer
kreditkartentyp
maestro
master card
master cards
Mastercard
master cards
mc
mister cash
n carta
carta
no de tarjeta
no do cartao
no do cartão
no. de tarjeta
no. do cartao
no. do cartão
nr carta
nr. carta
numeri di scheda
numero carta
numero de cartao
numero de carte
numero de cartão
numero de tarjeta
numero della carta
numero di carta
numero di scheda
numero do cartao
numero do cartão
numéro de carte
nº carta
nº de carte
nº de la carte
nº de tarjeta
nº do cartao
nº do cartão
nº. do cartão
número de cartao
número de cartão
número de tarjeta
número do cartao
scheda dell'assegno
scheda dell'atmosfera
scheda dell'atmosfera
scheda della banca
scheda di controllo
scheda di debito
scheda matrice
schede dell'atmosfera
schede di controllo
schede di debito
schede matrici
scoprono la scheda
scoprono le schede
solo
supporti di scheda
supporto di scheda
switch
tarjeta atm
tarjeta credito
tarjeta de atm
tarjeta de credito
tarjeta de debito
tarjeta debito
tarjeta no
tarjetahabiente
tipo della scheda
ufficio giapponese della
scheda
v pay
v-pay
visa
visa plus
visa electron
visto
visum
vpay
```

#### <a name="keyword_card_security_terms_dict"></a>Termos de \_ segurança do cartão \_ de \_ palavra-chave \_ dict

```
card identification number
card verification
cardi la verifica
cid
cod seg
cod seguranca
cod segurança
cod sicurezza
cod. seg
cod. seguranca
cod. segurança
cod. sicurezza
codice di sicurezza
codice di verifica
codigo
codigo de seguranca
codigo de segurança
crittogramma
cryptogram
cryptogramme
cv2
cvc
cvc2
cvn
cvv
cvv2
cód seguranca
cód segurança
cód. seguranca
cód. segurança
código
código de seguranca
código de segurança
de kaart controle
geeft nr uit
issue no
issue number
kaartidentificatienummer
kreditkartenprufnummer
kreditkartenprüfnummer
kwestieaantal
no. dell'edizione
no. di sicurezza
numero de securite
numero de verificacao
numero dell'edizione
numero di identificazione della
scheda
numero di sicurezza
numero van veiligheid
numéro de sécurité
nº autorizzazione
número de verificação
perno il blocco
pin block
prufziffer
prüfziffer
security code
security no
security number
sicherheits kode
sicherheitscode
sicherheitsnummer
speldblok
veiligheid nr
veiligheidsaantal
veiligheidscode
veiligheidsnummer
verfalldatum
```

#### <a name="keyword_card_expiration_terms_dict"></a>Termos de \_ expiração do cartão de \_ \_ palavra-chave \_ dict

```
ablauf
data de expiracao
data de expiração
data del exp
data di exp
data di scadenza
data em que expira
data scad
data scadenza
date de validité
datum afloop
datum van exp
de afloop
espira
espira
exp date
exp datum
expiration
expire
expires
expiry
fecha de expiracion
fecha de venc
gultig bis
gultigkeitsdatum
gültig bis
gültigkeitsdatum
la scadenza
scadenza
valable
validade
valido hasta
valor
venc
vencimento
vencimiento
verloopt
vervaldag
vervaldatum
vto
válido hasta
```

## <a name="eu-drivers-license-number"></a>Número da carta de condução da UE

Estas são as entidades do tipo de informação sensível ao número de carta de condução da UE.

- Áustria
- Bélgica
- Bulgária
- Croácia
- Chipre
- Checo
- Dinamarca
- Estónia
- Finlândia
- França
- Alemanha
- Grécia
- Hungria
- Irlanda
- Itália
- Letónia
- Lituânia
- Luxemburgo
- Malta
- Países Baixos
- Polónia
- Portugal
- Roménia
- Eslováquia
- Eslovénia
- Espanha
- Suécia
- Reino Unido

## <a name="eu-national-identification-number"></a>Número de identificação nacional da UE

Estas são as entidades do tipo de informação sensível ao número de identificação nacional da UE.

- Áustria
- Bélgica
- Bulgária
- Croácia
- Chipre
- Checo
- Dinamarca
- Estónia
- Finlândia
- França
- Alemanha
- Grécia
- Hungria
- Irlanda
- Itália
- Letónia
- Lituânia
- Luxemburgo
- Malta
- Países Baixos
- Polónia
- Portugal
- Roménia
- Eslováquia
- Eslovénia
- Espanha
- Reino Unido

## <a name="eu-passport-number"></a>Número de passaporte da UE

Estas são as entidades do número de passaportes da UE tipo informação sensível Estes são as entidades do pacote de números de passaporte da UE.

- Áustria
- Bélgica
- Bulgária
- Croácia
- Chipre
- Checo
- Dinamarca
- Estónia
- Finlândia
- França
- Alemanha
- Grécia
- Hungria
- Irlanda
- Itália
- Letónia
- Lituânia
- Luxemburgo
- Malta
- Países Baixos
- Polónia
- Portugal
- Roménia
- Eslováquia
- Eslovénia
- Espanha
- Suécia
- Reino Unido

## <a name="eu-social-security-number-or-equivalent-identification"></a>Número de segurança social da UE ou identificação equivalente

Estas são as entidades que estão no Número de Segurança Social da UE ou tipo de informação sensível à identificação equivalente.

- Áustria
- Bélgica
- Croácia
- Checo
- Dinamarca
- Finlândia
- França
- Alemanha
- Grécia
- Hungria
- Portugal
- Espanha
- Suécia

## <a name="eu-tax-identification-number"></a>Número de identificação fiscal da UE

Estas entidades estão no tipo de informação sensível ao número de identificação fiscal da UE.

- Áustria
- Bélgica
- Bulgária
- Croácia
- Chipre
- Checo
- Dinamarca
- Estónia
- Finlândia
- França
- Alemanha
- Grécia
- Hungria
- Irlanda
- Itália
- Letónia
- Lituânia
- Luxemburgo
- Malta
- Países Baixos
- Polónia
- Portugal
- Roménia
- Eslováquia
- Eslovénia
- Espanha
- Suécia
- Reino Unido



## <a name="finland-national-id"></a>ID nacional da Finlândia

### <a name="format"></a>Formato

seis dígitos mais um personagem indicando um século mais três dígitos mais um dígito de verificação

### <a name="pattern"></a>Padrão

O padrão deve incluir todos os seguintes:

- seis dígitos no formato DDMMYY, que são uma data de nascimento
- marcador do século (ou '-', '+' ou 'a')
- número de identificação pessoal de três dígitos
- um dígito ou letra (caso insensível) que é um dígito de verificação

### <a name="keywords"></a>Palavras-chave

```
ainutlaatuinen henkilökohtainen tunnus
henkilökohtainen tunnus
henkilötunnus
henkilötunnusnumero#
henkilötunnusnumero
hetu
ID no
ID number
identification number
identiteetti numero
identity number
ID number
kansallinen henkilötunnus
kansallisen henkilökortin
national ID card
national ID no.
personal ID
personal identity code
personalidnumber#
personbeteckning
personnummer
social security number
sosiaaliturvatunnus
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
tunnistenumero
tunnus numero
tunnusluku
tunnusnumero
verokortti
veronumero
verotunniste
verotunnus
```

## <a name="finland-passport-number"></a>Número do passaporte da Finlândia

Esta entidade sensível do tipo informação está disponível no tipo de informação sensível ao número de passaportes da UE e está disponível como entidade de tipo informação sensível autónoma.

### <a name="format"></a>Formato

combinação de nove letras e dígitos

### <a name="pattern"></a>Padrão

combinação de nove letras e dígitos:

- duas letras (não sensíveis a casos)
- sete dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keywords_eu_passport_number_common"></a>Palavras-chave \_ número de passaporte da UE \_ \_ \_ comum

```
passport#
passport #
passport ID
passports
passport no
passport no
passport number
passport number
passport numbers
passport numbers
```

#### <a name="keyword_finland_passport_number"></a>Número de \_ passaporte da Finlândia \_ \_

```
suomalainen passi
passin numero
passin numero.#
passin numero#
passin numero.
passi#
passi number
```

## <a name="france-drivers-license-number"></a>Número da carta de condução da França

Esta entidade sensível do tipo informação está disponível no tipo de informação sensível ao número de carta de condução da UE e está disponível como entidade de tipo informação sensível autónoma.

### <a name="format"></a>Formato

12 dígitos

### <a name="pattern"></a>Padrão

12 dígitos com validação para descontar padrões semelhantes, tais como números de telefone franceses

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_french_drivers_license"></a>Carta-chave \_ carta de \_ \_ condução francesa

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
permis de conduire
license number
license number
license numbers
license numbers
numéros de license
```

## <a name="france-national-id-card-cni"></a>Cartão de identidade nacional da França (CNI)

### <a name="format"></a>Formato

12 dígitos

### <a name="pattern"></a>Padrão

12 dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keywords_france_eu_national_id_card"></a>Palavras-chave \_ frança \_ \_ \_ cartão de identidade nacional da UE \_

```
card number
carte nationale d'identité
carte nationale d'idenite no
cni#
cni
compte bancaire
national identification number
national identity
nationalidno#
numéro d'assurance maladie
numéro de carte vitale
```

## <a name="france-passport-number"></a>Número de passaporte da França

Esta entidade sensível do tipo informação está disponível no tipo de informação sensível ao número de passaportes da UE e está disponível como entidade de tipo informação sensível autónoma.

### <a name="format"></a>Formato

nove dígitos e letras

### <a name="pattern"></a>Padrão

nove dígitos e letras:

- dois dígitos
- duas letras (não sensíveis a casos)
- cinco dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_passport"></a>Passaporte de palavra-chave \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="france-social-security-number-insee-or-equivalent-identification"></a>Número de segurança social da França (INSEE) ou identificação equivalente

Esta entidade sensível do tipo informação está incluída no número de segurança social da UE e no tipo de informação sensível ao ID equivalente e está disponível como entidade de tipo informação sensível autónoma.

### <a name="format"></a>Formato

15 dígitos

### <a name="pattern"></a>Padrão

Deve coincidir com um de dois padrões:

- 13 dígitos seguidos de um espaço seguido de dois dígitos ou
- 15 dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_fr_insee"></a>Palavra-chave \_ fr \_ insee

```
insee
securité sociale
securite sociale
national ID
national identification
numéro d'identité
no d'identité
no. d'identité
numero d'identite
no d'identite
no. d'identite
social security number
social security code
social insurance number
le numéro d'identification nationale
d'identité nationale
numéro de sécurité sociale
le code de la sécurité sociale
numéro d'assurance sociale
numéro de sécu
code sécu
```

## <a name="germany-drivers-license-number"></a>Número da carta de condução da Alemanha

Esta entidade sensível do tipo informação está incluída no tipo de informação sensível ao número de carta de condução da UE e está disponível como entidade de tipo informação sensível autónoma.

### <a name="format"></a>Formato

combinação de 11 dígitos e letras

### <a name="pattern"></a>Padrão

11 dígitos e letras (não sensíveis a casos):

- um dígito ou letra
- dois dígitos
- seis dígitos ou letras
- um dígito
- um dígito ou letra

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_german_drivers_license_number"></a>Número da \_ carta de \_ condução alemã \_ \_ de palavra-chave

```
ausstellungsdatum
ausstellungsort
ausstellende behöde
ausstellende behorde
ausstellende behoerde
führerschein
fuhrerschein
fuehrerschein
führerscheinnummer
fuhrerscheinnummer
fuehrerscheinnummer
führerschein-
fuhrerschein-
fuehrerschein-
führerscheinnummernr
fuhrerscheinnummernr
fuehrerscheinnummernr
führerscheinnummerklasse
fuhrerscheinnummerklasse
fuehrerscheinnummerklasse
nr-führerschein
nr-fuhrerschein
nr-fuehrerschein
no-führerschein
no-fuhrerschein
no-fuehrerschein
n-führerschein
n-fuhrerschein
n-fuehrerschein
permis de conduire
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dlno
```

## <a name="germany-identity-card-number"></a>Número do bilhete de identidade da Alemanha

### <a name="format"></a>Formato

desde 1 de novembro de 2010: Nove letras e dígitos

de 1 de abril de 1987 até 31 de outubro de 2010: 10 dígitos

### <a name="pattern"></a>Padrão

desde 1 de novembro de 2010:

- uma letra (não sensível a casos)
- oito dígitos

de 1 de abril de 1987 a 31 de outubro de 2010:

- 10 dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_germany_id_card"></a>Cartão de id da \_ palavra-chave \_ \_ alemanha

```
ausweis
gpid
identification
identifikation
identifizierungsnummer
identity card
identity number
id-nummer
personal ID
personalausweis
persönliche ID nummer
persönliche identifikationsnummer
persönliche-id-nummer
```

## <a name="germany-passport-number"></a>Número de passaporte da Alemanha

Esta entidade sensível do tipo informação está incluída no tipo de informação sensível ao número de passaportes da UE e está disponível como entidade de tipo informação sensível autónoma.

### <a name="format"></a>Formato

10 dígitos ou letras

### <a name="pattern"></a>Padrão

O padrão deve incluir todos os seguintes:

- primeiro caráter é um dígito ou uma letra deste conjunto (C, F, G, H, J, K)
- três dígitos
- cinco dígitos ou letras deste conjunto (C, -H, J-N, P, R, T, V-Z)
- um dígito

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_german_passport"></a>Palavra-chave \_ passaporte alemão \_

```
reisepasse
reisepassnummer
No-Reisepass
Nr-Reisepass
Reisepass-Nr
Passnummer
reisepässe
passeport no.
passeport no
```

## <a name="greece-national-id-card"></a>Cartão de identidade nacional da Grécia

### <a name="format"></a>Formato

Combinação de 7-8 letras e números mais um traço

### <a name="pattern"></a>Padrão

Sete letras e números (formato antigo):

- Uma letra (qualquer letra do alfabeto grego)
- Um traço
- Seis dígitos

Oito letras e números (novo formato):

- Duas letras cujo carácter maiúscula ocorre nos alfabetos grego e latino (ABEZHIKMNOPTYX)
- Um traço
- Seis dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_greece_id_card"></a>Cartão \_ de id da grécia \_ palavra-chave \_

```
greek ID
greek national ID
greek personal ID card
greek police ID
identity card
tautotita
ταυτότητα
ταυτότητας
```

## <a name="hong-kong-identity-card-hkid-number"></a>Número do bilhete de identidade de Hong Kong (HKID)

### <a name="format"></a>Formato

Combinação de 8-9 letras e números mais parênteses opcionais em torno do caráter final

### <a name="pattern"></a>Padrão

Combinação de 8-9 letras:

- 1-2 letras (não sensíveis a casos)
- Seis dígitos
- O carácter final (qualquer dígito ou a letra A), que é o dígito de verificação e é opcionalmente incluído em parênteses.

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_hong_kong_id_card"></a>Cartão de identidade \_ de hong \_ kong \_ \_

```
hkid
Hong Kong identity card
HKIDC
ID card
identity card
hk identity card
Hong Kong ID
香港身份證
香港永久性居民身份證
身份證
身份証
身分證
身分証
香港身份証
香港身分證
香港身分証
香港身份證
香港居民身份證
香港居民身份証
香港居民身分證
香港居民身分証
香港永久性居民身份証
香港永久性居民身分證
香港永久性居民身分証
香港永久性居民身份證
香港非永久性居民身份證
香港非永久性居民身份証
香港非永久性居民身分證
香港非永久性居民身分証
香港特別行政區永久性居民身份證
香港特別行政區永久性居民身份証
香港特別行政區永久性居民身分證
香港特別行政區永久性居民身分証
香港特別行政區非永久性居民身份證
香港特別行政區非永久性居民身份証
香港特別行政區非永久性居民身分證
香港特別行政區非永久性居民身分証
```

## <a name="ip-address"></a>Endereço IP

### <a name="format"></a>Formato

#### <a name="ipv4"></a>IPv4:

Padrão complexo, que explica versões formatadas (períodos) e não formatadas (sem períodos) dos endereços IPv4

#### <a name="ipv6"></a>IPv6:

Padrão complexo que explica os números de IPv6 formatados (que incluem cólons)

### <a name="pattern"></a>Padrão

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_ipaddress"></a>\_Ipaddress de palavra-chave

```
IP (this keyword is case-sensitive)
ip address
ip addresses
internet protocol
IP-כתובת ה
```

## <a name="india-permanent-account-number-pan"></a>Número de conta permanente da Índia (PAN)

### <a name="format"></a>Formato

10 letras ou dígitos

### <a name="pattern"></a>Padrão

10 letras ou dígitos:

- Três letras (não sensíveis a casos)
- Uma carta em C, P, H, F, A, T, B, L, J, G (não sensível a casos)
- Uma carta
- Quatro dígitos
- Uma carta (não sensível a casos)

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_india_permanent_account_number"></a>Palavra-chave \_ número de conta permanente \_ \_ \_ índia

```
Permanent Account Number
PAN
```

## <a name="india-unique-identification-aadhaar-number"></a>Número de identificação única da Índia (Aadhaar)

### <a name="format"></a>Formato

12 dígitos contendo espaços ou traços opcionais

### <a name="pattern"></a>Padrão

12 dígitos:

- Um dígito, que não é 0 ou 1
- Três dígitos
- Um espaço opcional ou traço
- Quatro dígitos
- Um espaço opcional ou traço
- O dígito final, que é o dígito de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_india_aadhar"></a>Palavra-chave \_ india \_ aadhar

```
aadhaar
aadhar
aadhar#
uid
आधार
uidai
```

## <a name="indonesia-identity-card-ktp-number"></a>Número do bilhete de identidade da Indonésia (KTP)

### <a name="format"></a>Formato

16 dígitos contendo períodos opcionais

### <a name="pattern"></a>Padrão

16 dígitos:

- Código de província de dois dígitos
- Um período (opcional)
- Regência de dois dígitos ou código da cidade
- Código subdistricado de dois dígitos
- Um período (opcional)
- Seis dígitos no formato DDMMYY, que são a data de nascimento
- Um período (opcional)
- Quatro dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_indonesia_id_card"></a>\_ \_ Cartão de id da palavra-chave da Indonésia \_

```
KTP
Kartu Tanda Penduduk
Nomor Induk Kependudukan
```

## <a name="international-banking-account-number-iban"></a>Número de conta bancária internacional (IBAN)

### <a name="format"></a>Formato

Código de país (duas letras) mais dígitos de verificação (dois dígitos) mais :::no-loc text="bban"::: número (até 30 caracteres)

### <a name="pattern"></a>Padrão

O padrão deve incluir todos os seguintes:

- Código de país de duas letras
- Dois dígitos de verificação (seguido de um espaço opcional)
- 1-7 grupos de quatro letras ou dígitos (podem ser separados por espaços)
- 1-3 letras ou dígitos

O formato para cada país é ligeiramente diferente. O tipo de informação sensível IBAN abrange estes 60 países:

```
ad, ae, al, at, az, ba, be, bg, bh, ch, cr, cy, cz, de, dk, do, ee, es, fi, fo, fr, gb, ge, gi, gl, gr, hr, hu, that is, il, is, it, kw, kz, lb, li, lt, lu, lv, mc, md, me, mk, mr, mt, mu, nl, no, pl, pt, ro, rs, sa, se, si, sk, sm, tn, tr, vg
```

### <a name="keywords"></a>Palavras-chave

Nenhum

## <a name="ireland-personal-public-service-pps-number"></a>Número do serviço público pessoal da Irlanda (PPS)

### <a name="format"></a>Formato

Formato antigo (até 31 de dezembro de 2012):

- sete dígitos seguidos por 1-2 letras

Novo formato (1 de janeiro de 2013 e depois):

- sete dígitos seguidos por duas letras

### <a name="pattern"></a>Padrão

Formato antigo (até 31 de dezembro de 2012):

- sete dígitos
- uma a duas letras (não sensível a casos)

Novo formato (1 de janeiro de 2013 e depois):

- sete dígitos
- uma letra (não sensível a casos) que é um dígito de verificação alfabética
- Uma letra opcional no alcance A-I, ou &quot; W&quot;

### <a name="keywords"></a>Palavras-chave

#### <a name="keywords_ireland_eu_national_id_card"></a>Palavras-chave \_ irlanda \_ \_ \_ cartão de identidade nacional da UE \_

```
client identity service
identification number
personal ID number
personal public service number
personal service no
phearsanta seirbhíse poiblí
pps no
pps number
pps num
pps service no
ppsn
ppsno#
ppsno
psp
public service no
publicserviceno#
publicserviceno
revenue and social insurance number
rsi no
rsi number
rsin
seirbhís aitheantais cliant
uimh
uimhir aitheantais chánach
uimhir aitheantais phearsanta
uimhir phearsanta seirbhíse poiblí
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="israel-bank-account-number"></a>Número da conta bancária de Israel

### <a name="format"></a>Formato

13 dígitos

### <a name="pattern"></a>Padrão

Formatado:

- dois dígitos
- um traço
- três dígitos
- um traço
- oito dígitos

Não formatado:

- 13 dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_israel_bank_account_number"></a>Palavra-chave \_ número da conta bancária de Israel \_ \_ \_

```
Bank Account Number
Bank Account
Account Number
מספר חשבון בנק
```

## <a name="israel-national-identification-number"></a>Número de identificação nacional de Israel

### <a name="format"></a>Formato

nove dígitos

### <a name="pattern"></a>Padrão

nove dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_israel_national_id"></a>Palavra-chave \_ Israel \_ \_ ID Nacional

```
מספר זהות
מספר זיה וי
מספר זיהוי ישר אלי
זהותישר אלית
هو ية اسرائيل ية عدد
هوية إسرائ يلية
رقم الهوية
عدد هوية فريدة من نوعها
ID number#
ID number
identity no
identity number#
identity number
israeliidentitynumber
personal ID
unique ID
```

## <a name="italy-drivers-license-number"></a>Número da carta de condução da Itália

Esta entidade sensível do tipo informação está incluída no tipo de informação sensível ao número de carta de condução da UE e está disponível como entidade de tipo informação sensível autónoma.

### <a name="format"></a>Formato

uma combinação de 10 letras e dígitos

### <a name="pattern"></a>Padrão

uma combinação de 10 letras e dígitos:

- uma letra (não sensível a casos)
- a letra &quot; A &quot; ou V &quot; &quot; (não sensível a casos)
- sete dígitos
- uma letra (não sensível a casos)

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_italy_drivers_license_number"></a>Palavra-chave \_ itália número de carta de \_ \_ condução \_

```
numero di patente
patente di guida
patente guida
patenti di guida
patenti guida
```

## <a name="japan-bank-account-number"></a>Número da conta bancária do Japão

### <a name="format"></a>Formato

sete ou oito dígitos

### <a name="pattern"></a>Padrão

número da conta bancária:

- sete ou oito dígitos
- Código da sucursal de conta bancária:
- quatro dígitos
- um espaço ou traço (opcional)
- três dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_jp_bank_account"></a>Conta \_ bancária jp palavra-chave \_ \_

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
口座番号
銀行口座
銀行口座番号
総合口座
普通預金口座
普通口座
当座預金口座
当座口座
預金口座
振替口座
銀行
バンク
```

#### <a name="keyword_jp_bank_branch_code"></a>Código da \_ \_ \_ sucursal do banco JP palavra-chave \_

```
支店番号
支店コード
店番号
```

## <a name="japan-drivers-license-number"></a>Número da carta de condução do Japão

### <a name="format"></a>Formato

12 dígitos

### <a name="pattern"></a>Padrão

12 dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_jp_drivers_license_number"></a>Número de \_ carta de \_ \_ condução jp palavra-chave \_

```
driver license
drivers license
driver'license
drivers licenses
driver'licenses
driver licenses
dl#
dls#
lic#
lics#
運転免許証
運転免許
免許証
免許
運転免許証番号
運転免許番号
免許証番号
免許番号
運転免許証ナンバー
運転免許ナンバー
免許証ナンバー
運転免許証no
運転免許no
免許証no
免許no
運転経歴証明書番号
運転経歴証明書
運転免許証No.
運転免許No.
免許証No.
免許No.
運転免許証#
運転免許#
免許証#
免許#
```

## <a name="japan-passport-number"></a>Número de passaporte do Japão

### <a name="format"></a>Formato

duas letras seguidas de sete dígitos

### <a name="pattern"></a>Padrão

duas letras (não sensíveis a casos) seguidas de sete dígitos

#### <a name="keyword_jp_passport"></a>Passaporte jp palavra-chave \_ \_

```
Passport
Passport Number
Passport No.
Passport #
パスポート
パスポート番号
パスポートナンバー
パスポート＃
パスポート#
パスポートNo.
旅券番号
旅券番号＃
旅券番号♯
旅券ナンバー
```

## <a name="japan-residence-card-number"></a>Número do cartão de residência do Japão

### <a name="format"></a>Formato

12 letras e dígitos

### <a name="pattern"></a>Padrão

12 letras e dígitos:

- duas letras (não sensíveis a casos)
- oito dígitos
- duas letras (não sensíveis a casos)

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_jp_residence_card_number"></a>Número do \_ \_ \_ cartão de residência jp palavra-chave \_

```
Residence card number
Residence card no
Residence card #
在留カード番号
在留カード
在留番号
```

## <a name="japan-resident-registration-number"></a>Número de registo residente no Japão

### <a name="format"></a>Formato

11 dígitos

### <a name="pattern"></a>Padrão

11 dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_jp_resident_registration_number"></a>Número \_ de registo de residente jp palavra-chave \_ \_ \_

```
Resident Registration Number
Residents Basic Registry Number
Resident Registration No.
Resident Register No.
Residents Basic Registry No.
Basic Resident Register No.
外国人登録証明書番号
証明書番号
登録番号
外国人登録証
```

## <a name="japan-social-insurance-number-sin"></a>Número do seguro social do Japão (SIN)

### <a name="format"></a>Formato

7-12 dígitos

### <a name="pattern"></a>Padrão

7-12 dígitos:

- quatro dígitos
- um hífen (opcional)
- seis dígitos OU
- 7-12 dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_jp_sin"></a>Palavra-chave \_ jp \_ pecado

```
Social Insurance No.
Social Insurance Num
Social Insurance Number
健康保険被保険者番号
健保番号
基礎年金番号
雇用保険被保険者番号
雇用保険番号
保険証番号
社会保険番号
社会保険No.
社会保険
介護保険
介護保険被保険者番号
健康保険被保険者整理番号
雇用保険被保険者整理番号
厚生年金
厚生年金被保険者整理番号
```

## <a name="malaysia-identification-card-number"></a>Número do cartão de identificação da Malásia

### <a name="format"></a>Formato

12 dígitos contendo hífenes opcionais

### <a name="pattern"></a>Padrão

12 dígitos:

- seis dígitos no formato YYMMDD, que são a data de nascimento
- um traço (opcional)
- código de local de nascimento de duas letras
- um traço (opcional)
- três dígitos aleatórios
- código de género de um dígito

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_malaysia_id_card_number"></a>Número de \_ \_ cartão de id da malaysia \_ palavra-chave \_

```
digital application card
i/c
i/c no
ic
ic no
ID card
identification Card
identity card
k/p
k/p no
kad akuan diri
kad aplikasi digital
kad pengenalan malaysia
kp
kp no
mykad
mykas
mykid
mypr
mytentera
malaysia identity card
malaysian identity card
nric
personal identification card
```

## <a name="netherlands-citizens-service-bsn-number"></a>Número do serviço de cidadão holandês (BSN)

### <a name="format"></a>Formato

oito a nove dígitos contendo espaços opcionais

### <a name="pattern"></a>Padrão

oito e nove dígitos:

- três dígitos
- um espaço (opcional)
- três dígitos
- um espaço (opcional)
- dois e três dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keywords_netherlands_eu_national_id_card"></a>Palavras-chave \_ Holanda \_ \_ \_ Cartão nacional de identidade da UE \_

```
bsn#
bsn
burgerservicenummer
citizen service number
person number
personal number
personal numeric code
person-related number
persoonlijk nummer
persoonlijke numerieke code
persoonsgebonden
persoonsnummer
sociaal-fiscaal nummer
social-fiscal number
sofi
sofinummer
uniek identificatienummer
uniek identiteitsnummer
unique identification number
unique identity number
uniqueidentityno#
```

## <a name="new-zealand-ministry-of-health-number"></a>Número do Ministério da Saúde da Nova Zelândia

### <a name="format"></a>Formato

três letras, um espaço (opcional) e quatro dígitos

### <a name="pattern"></a>Padrão

- três letras (não sensíveis a casos), exceto 'I' e 'O'
- um espaço (opcional)
- quatro dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_nz_terms"></a>Termos nz palavra-chave \_ \_

```
NHI
New Zealand
Health
treatment
National Health Index Number
nhi number
nhi no.
NHI#
National Health Index No.
National Health Index Id
National Health Index #
```

## <a name="norway-identification-number"></a>Número de identificação da Noruega

### <a name="format"></a>Formato

11 dígitos

### <a name="pattern"></a>Padrão

11 dígitos:

- seis dígitos no formato DDMMYY, que são a data de nascimento
- número individual de três dígitos
- dois dígitos de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_norway_id_number"></a>Número de \_ id da \_ palavra-chave da Noruega \_

```
Personal identification number
Norwegian ID Number
ID Number
Identification
Personnummer
Fødselsnummer
```

## <a name="philippines-unified-multi-purpose-identification-number"></a>Número de identificação multiusos unificado das Filipinas

### <a name="format"></a>Formato

12 dígitos separados por hífenes

### <a name="pattern"></a>Padrão

12 dígitos:

- quatro dígitos
- um hífen
- sete dígitos
- um hífen
- um dígito

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_philippines_id"></a>Palavra-chave \_ filipinas \_ id

```
Unified Multi-Purpose ID
UMID
Identity Card
Pinag-isang Multi-Layunin ID
```

## <a name="poland-identity-card"></a>Bilhete de identidade da Polónia

### <a name="format"></a>Formato

três letras e seis dígitos

### <a name="pattern"></a>Padrão

três letras (não sensíveis a casos) seguidas de seis dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_poland_national_id_passport_number"></a>Palavra-chave \_ número do \_ passaporte nacional \_ da \_ Polónia \_

```
Dowód osobisty
Numer dowodu osobistego
Nazwa i numer dowodu osobistego
Nazwa i nr dowodu osobistego
Nazwa i nr dowodu tożsamości
Dowód Tożsamości
dow. os.
```

## <a name="poland-national-id-pesel"></a>ID nacional da Polónia (PESEL)

### <a name="format"></a>Formato

11 dígitos

### <a name="pattern"></a>Padrão

- 6 dígitos que representam a data de nascimento no formato YYMMDD
- 4 dígitos
- 1 dígito de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_pesel_identification_number"></a>Número \_ de identificação de pesel de palavra-chave \_ \_

```
dowód osobisty
dowódosobisty
niepowtarzalny numer
niepowtarzalnynumer
nr.-pesel
nr-pesel
numer identyfikacyjny
pesel
tożsamości narodowej
```

## <a name="poland-passport-number"></a>Número de passaporte da Polónia

Esta entidade sensível do tipo informação está incluída no tipo de informação sensível ao número de passaportes da UE e está disponível como entidade de tipo informação sensível autónoma.

### <a name="format"></a>Formato

duas letras e sete dígitos

### <a name="pattern"></a>Padrão

Duas letras (não sensíveis a casos) seguidas de sete dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_poland_national_id_passport_number"></a>Palavra-chave \_ número do \_ passaporte nacional \_ da \_ Polónia \_

```
Numer paszportu
Nr. Paszportu
Paszport
```

## <a name="portugal-citizen-card-number"></a>Número do cartão de cidadão de Portugal

### <a name="format"></a>Formato

oito dígitos

### <a name="pattern"></a>Padrão

oito dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_portugal_citizen_card"></a>Palavra-chave \_ \_ cartão de cidadão de Portugal \_

```
bilhete de identidade
cartão de cidadão
citizen card
document number
documento de identificação
ID number
identification no
identification number
identity card no
identity card number
national ID card
nic
número bi de portugal
número de identificação civil
número de identificação fiscal
número do documento
portugal bi number
```

## <a name="portugal-drivers-license-number"></a>Número da carta de condução de Portugal

Esta entidade sensível do tipo informação só está disponível no tipo de informação sensível ao número de carta de condução da UE.

### <a name="format"></a>Formato

dois padrões - duas letras seguidas de 5-8 dígitos com caracteres especiais

### <a name="pattern"></a>Padrão

Padrão 1: Duas letras seguidas de 5/6 com caracteres especiais:

- Duas letras (não sensíveis a casos)
- Um hífen
- Cinco ou seis dígitos
- Um espaço
- Um dígito

Padrão 2: Uma letra seguida de 6/8 dígitos com caracteres especiais:

- Uma letra (não sensível a casos)
- Um hífen
- Seis ou oito dígitos
- Um espaço
- Um dígito

### <a name="keywords"></a>Palavras-chave

#### <a name="keywords_eu_drivers_license_number"></a>Palavras-chave \_ número da carta de \_ \_ condução da UE \_

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_portugal_eu_drivers_license_number"></a>Palavras-chave \_ número da carta de \_ \_ \_ condução da UE \_

```
carteira de motorista
carteira motorista
carteira de habilitação
carteira habilitação
número de licença
número licença
permissão de condução
permissão condução
Licença condução Portugal
carta de condução
```

## <a name="saudi-arabia-national-id"></a>ID Nacional da Arábia Saudita

### <a name="format"></a>Formato

10 dígitos

### <a name="pattern"></a>Padrão

10 dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_saudi_arabia_national_id"></a>Palavra-chave \_ da Arábia \_ Saudita \_ \_ id

```
Identification Card
I card number
ID number
الوطنية الهوية بطاقة رقم
```

## <a name="singapore-national-registration-identity-card-nric-number"></a>Número do bilhete de identidade nacional de Singapura (NRIC)

### <a name="format"></a>Formato

nove letras e dígitos

### <a name="pattern"></a>Padrão

- nove letras e dígitos:
- a letra &quot; F &quot; , &quot; &quot; &quot; &quot; G, S, ou &quot; T &quot; (não sensíveis a casos)
- sete dígitos
- um dígito de verificação alfabética

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_singapore_nric"></a>Palavra-chave \_ \_ nric de Singapura

```
National Registration Identity Card
Identity Card Number
NRIC
IC
Foreign Identification Number
FIN
身份证
身份證
```

## <a name="south-africa-identification-number"></a>Número de identificação da África do Sul

### <a name="format"></a>Formato

13 dígitos que podem conter espaços

### <a name="pattern"></a>Padrão

13 dígitos:

- seis dígitos no formato YYMMDD, que são a data de nascimento
- quatro dígitos
- um indicador de cidadania de um dígito
- o dígito &quot; 8 &quot; ou &quot; 9&quot;
- um dígito, que é um dígito de checkum

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_south_africa_identification_number"></a>\_ \_ Palavra-chave \_ número de identificação na África do Sul \_

```
Identity card
ID
Identification
```

## <a name="south-korea-resident-registration-number"></a>Número de registo residente na Coreia do Sul

### <a name="format"></a>Formato

13 dígitos contendo um hífen

### <a name="pattern"></a>Padrão

13 dígitos:

- seis dígitos no formato YYMMDD, que são a data de nascimento
- um hífen
- um dígito determinado pelo século e género
- código de região de nascimento de quatro dígitos
- um dígito usado para diferenciar as pessoas para quem os números anteriores são idênticos
- um dígito de verificação.

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_south_korea_resident_number"></a>\_Palavra-chave número residente na Coreia do Sul \_ \_ \_

```
National ID card
Citizen's Registration Number
Jumin deungnok beonho
RRN
주민등록번호
```

## <a name="spain-social-security-number-ssn"></a>Número de segurança social de Espanha (SSN)

Esta entidade sensível do tipo informação está incluída no número de segurança social da UE ou no tipo de informação sensível ao ID equivalente e está disponível como entidade de tipo informação sensível autónoma.

### <a name="format"></a>Formato

11-12 dígitos

### <a name="pattern"></a>Padrão

11-12 dígitos:

- dois dígitos
- um corte para a frente (opcional)
- sete a oito dígitos
- um corte para a frente (opcional)
- dois dígitos

### <a name="keywords"></a>Palavras-chave

Nenhum

## <a name="sweden-national-id"></a>ID nacional da Suécia

### <a name="format"></a>Formato

10 ou 12 dígitos e um delimiter opcional

### <a name="pattern"></a>Padrão

10 ou 12 dígitos e um delimiter opcional:

- dois dígitos (opcional)
- Seis dígitos no formato de data YYMMDD
- limítrodão de &quot; - &quot; ou &quot; + &quot; (opcional)
- quatro dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keywords_swedish_national_identifier"></a>Palavras-chave \_ \_ identificador nacional sueco \_

```
ID no
ID number
ID#
identification no
identification number
identifikationsnumret#
identifikationsnumret
identitetshandling
identity document
identity no
identity number
id-nummer
personal ID
personnummer#
personnummer
skatteidentifikationsnummer
```

## <a name="sweden-passport-number"></a>Número de passaporte da Suécia

Esta entidade sensível do tipo informação está incluída no tipo de informação sensível ao número de passaportes da UE e está disponível como entidade de tipo informação sensível autónoma.

### <a name="format"></a>Formato

oito dígitos

### <a name="pattern"></a>Padrão

oito dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_sweden_passport"></a>Palavra-chave \_ passaporte da Suécia \_

```
visa requirements
Alien Registration Card
Schengen visas
Schengen visa
Visa Processing
Visa Type
Single Entry
Multiple Entries
G3 Processing Fees
```

#### <a name="keyword_passport"></a>Passaporte de palavra-chave \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="swift-code"></a>Código SWIFT

### <a name="format"></a>Formato

quatro letras seguidas de 5-31 letras ou dígitos

### <a name="pattern"></a>Padrão

quatro letras seguidas de 5-31 letras ou dígitos:

- código bancário de quatro letras (não sensível a casos)
- um espaço opcional
- 4-28 letras ou dígitos (número de conta bancária básica (BBAN))
- um espaço opcional
- uma a três letras ou dígitos (restante do BBAN)

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_swift"></a>Palavra-chave \_ rápida

```
international organization for standardization 9362
iso 9362
iso9362
swift#
swift code
swift number
swiftroutingnumber
swift code
swift number #
swift routing number
bic number
bic code
bic #
bic#
bank identifier code
Organization internationale de normalization 9362
rapide #
code SWIFT
le numéro de swift
swift numéro d'acheminement
le numéro BIC
#BIC
code identificateur de banque
SWIFTコード
SWIFT番号
BIC番号
BICコード
SWIFT コード
SWIFT 番号
BIC 番号
BIC コード
金融機関識別コード
金融機関コード
銀行コード
```

## <a name="taiwan-national-identification-number"></a>Número de identificação nacional de Taiwan

### <a name="format"></a>Formato

uma letra (em inglês) seguida de nove dígitos

### <a name="pattern"></a>Padrão

uma letra (em inglês) seguida de nove dígitos:

- uma letra (em inglês, não sensível a casos)
- o dígito &quot; 1 &quot; ou &quot; 2&quot;
- oito dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_taiwan_national_id"></a>Palavra-chave \_ de Taiwan \_ \_ id nacional

```
身份證字號
身份證
身份證號碼
身份證號
身分證字號
身分證
身分證號碼
身份證號
身分證統一編號
國民身分證統一編號
簽名
蓋章
簽名或蓋章
簽章
```

## <a name="taiwan-passport-number"></a>Número do passaporte de Taiwan

### <a name="format"></a>Formato

- número de passaporte biométrico: nove dígitos
- número de passaporte não biométrico: nove dígitos

### <a name="pattern"></a>Padrão

Número do passaporte biométrico:

- o personagem &quot; 3&quot;
- oito dígitos

Número de passaporte não biométrico:

- nove dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_taiwan_passport"></a>Palavra-chave \_ passaporte de Taiwan \_

```
ROC passport number
Passport number
Passport no
Passport Num
Passport #
护照
中華民國護照
Zhōnghuá Mínguó hùzhào
```

## <a name="taiwan-resident-certificate-arctarc-number"></a>Número do certificado residente de Taiwan (ARC/TARC)

### <a name="format"></a>Formato

10 letras e dígitos

### <a name="pattern"></a>Padrão

10 letras e dígitos:

- duas letras (não sensíveis a casos)
- oito dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_taiwan_resident_certificate"></a>Certificado de residente de \_ Taiwan \_ de \_ palavra-chave

```
Resident Certificate
Resident Cert
Resident Cert.
Identification card
Alien Resident Certificate
ARC
Taiwan Area Resident Certificate
TARC
居留證
外僑居留證
台灣地區居留證
```

## <a name="uk-drivers-license-number"></a>Reino Unido número da carta de condução

Esta entidade sensível do tipo informação está incluída no tipo de informação sensível ao número de carta de condução da UE e está disponível como entidade de tipo informação sensível autónoma.

### <a name="format"></a>Formato

Combinação de 18 letras e dígitos no formato especificado

### <a name="pattern"></a>Padrão

18 letras e dígitos:

- cinco letras (não sensíveis a casos) ou o dígito &quot; 9 &quot; em vez de uma letra
- um dígito
- cinco dígitos no formato de data MMDDY para data de nascimento (o 7º personagem é aumentado por 50 se o condutor for feminino, ou seja, 51 a 62 em vez de 01 a 12)
- duas letras (não sensíveis a casos) ou o dígito &quot; 9 &quot; em vez de uma letra
- cinco dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_uk_drivers_license"></a>Carta-chave \_ da carta de \_ condução do Reino Unido \_

```
DVLA
light vans
quad bikes
motor cars
125cc
sidecar
tricycles
motorcycles
photo card license
learner drivers
license holder
license holders
driving licenses
driving license
dual control car
```

## <a name="uk-electoral-roll-number"></a>Reino Unido número de cadernos eleitorais

### <a name="format"></a>Formato

duas letras seguidas de 1-4 dígitos

### <a name="pattern"></a>Padrão

duas letras (não sensíveis a casos) seguidas de números de 1-4

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_uk_electoral"></a>Palavra-chave \_ eleitoral do Reino Unido \_

- nomeação do conselho
- formulário de nomeação
- registo eleitoral
- caderno eleitoral

## <a name="uk-national-health-service-number"></a>Reino Unido número do serviço nacional de saúde

### <a name="format"></a>Formato

10-17 dígitos separados por espaços

### <a name="pattern"></a>Padrão

10-17 dígitos:

- ou três ou 10 dígitos
- um espaço
- três dígitos
- um espaço
- quatro dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_uk_nhs_number"></a>Palavra-chave \_ número do SNS no Reino Unido \_ \_

```
national health service
nhs
health services authority
health authority
```

#### <a name="keyword_uk_nhs_number1"></a>Palavra-chave \_ no sns no Reino Unido \_ \_ número1

```
patient ID
patient identification
patient no
patient number
```

#### <a name="keyword_uk_nhs_number_dob"></a>Palavra-chave \_ \_ uk nhs number \_ \_ dob

```
GP
DOB
D.O.B
Date of Birth
Birth Date
```

## <a name="uk-national-insurance-number-nino"></a>Reino Unido número do seguro nacional (NINO)

Esta entidade sensível do tipo informação está incluída no tipo de informação sensível ao número de identificação nacional da UE e está disponível como entidade de tipo informação sensível autónoma.

### <a name="format"></a>Formato

sete caracteres ou nove caracteres separados por espaços ou traços

### <a name="pattern"></a>Padrão

dois padrões possíveis:

- duas letras (os INI válidos utilizam apenas certos caracteres neste prefixo, que este padrão valida; não são sensíveis a casos)
- seis dígitos
- ou 'A', 'B', 'C' ou 'D' (como o prefixo, apenas certos caracteres são permitidos no sufixo; não são sensíveis a casos)

OR

- duas letras
- um espaço ou traço
- dois dígitos
- um espaço ou traço
- dois dígitos
- um espaço ou traço
- dois dígitos
- um espaço ou traço
- ou 'A', 'B', 'C', ou 'D'


### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_uk_nino"></a>Palavra-chave \_ \_ uk nino

```
national insurance number
national insurance contributions
protection act
insurance
social security number
insurance application
medical application
social insurance
medical attention
social security
Great Britain
NI Number
NI No.
NI #
NI#
insurance#
insurance number
national insurance#
nationalinsurancenumber
```

## <a name="uk-unique-taxpayer-reference-number"></a>Reino Unido Número único de referência do contribuinte

Este tipo de informação sensível só está disponível para utilização em:

- políticas de prevenção de perda de dados
- políticas de conformidade com a comunicação
- governação da informação
- gestão de registos
- Segurança de aplicativos na nuvem da Microsoft

### <a name="format"></a>Formato

10 dígitos sem espaços e delimiters

### <a name="pattern"></a>Padrão

10 dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keywords_uk_eu_tax_file_number"></a>Palavras-chave \_ número de \_ \_ \_ ficheiros de impostos da UE no Reino Unido \_

```
tax number
tax file
tax ID
tax identification no
tax identification number
tax no#
tax no
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="us-bank-account-number"></a>Número da conta bancária dos EUA

### <a name="format"></a>Formato

6-17 dígitos

### <a name="pattern"></a>Padrão

6-17 dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_usa_bank_account"></a>\_Palavra-chave conta bancária dos EUA \_ \_

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account.
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
```

## <a name="us-drivers-license-number"></a>Número da carta de condução dos EUA

### <a name="format"></a>Formato

Depende do estado

### <a name="pattern"></a>Padrão

depende do estado- por exemplo, Nova Iorque:

- nove dígitos formatados como ddd vai coincidir.
- nove dígitos como dddddd não vai corresponder.

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_us_drivers_license_abbreviations"></a>Palavra-chave \_ nos \_ motoristas \_ de licença \_ abreviaturas

```
DL
DLS
CDL
CDLS
ID
IDs
DL#
DLS#
CDL#
CDLS#
ID#
IDs#
ID number
ID numbers
LIC
LIC#
```

#### <a name="keyword_us_drivers_license"></a>Palavra-chave \_ nos \_ \_ motoristas licença

```
DriverLic
DriverLics
DriverLicense
DriverLicenses
Driver Lic
Driver Lics
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
identification number
identification numbers
identification #
ID card
ID cards
identification card
identification cards
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
ID card#
ID cards#
identification card#
identification cards#
```

#### <a name="keyword_state_name_drivers_license_name"></a>Palavra-chave \_ [nome do \_ estado] \_ nome da carta \_ de condução \_

- abreviatura do estado (por exemplo, &quot; &quot; NY)
- nome do estado (por exemplo, &quot; Nova &quot; Iorque)

## <a name="us-individual-taxpayer-identification-number-itin"></a>Número de identificação individual dos contribuintes dos EUA (ITIN)

### <a name="format"></a>Formato

nove dígitos que começam com um &quot; 9 &quot; e contêm um &quot; 7 ou &quot; &quot; 8 como o quarto &quot; dígito, opcionalmente formatado com espaços ou traços

### <a name="pattern"></a>Padrão

formatado:

- o dígito &quot; 9&quot;
- dois dígitos
- um espaço ou traço
- um &quot; 7 &quot; ou &quot; 8&quot;
- um dígito
- um espaço, ou traço
- quatro dígitos

não testado:

- o dígito &quot; 9&quot;
- dois dígitos
- um &quot; 7 &quot; ou &quot; 8&quot;
- cinco dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_itin"></a>\_Palavra-chave itin

```
taxpayer
tax ID
tax identification
itin
i.t.i.n.
ssn
tin
social security
tax payer
itins
tax ID
individual taxpayer
```

## <a name="us-social-security-number-ssn"></a>Número de segurança social dos EUA (SSN)

### <a name="format"></a>Formato

nove dígitos, que podem estar em um padrão formatado ou não equipado

>[!Note]
> Se for emitida antes de meados de 2011, um SSN tem uma forte formatação onde certas partes do número devem estar dentro de determinados intervalos para serem válidas (mas não há uma função de verificação).
>

### <a name="pattern"></a>Padrão

quatro funções procuram SSNs em quatro padrões diferentes:

- Func \_ ssn encontra SSNs com formatação forte pré-2011 que são formatadas com traços ou espaços (ddd-ddd-dddd OR ddd ddd dddd)
- Func \_ ssn não formado \_ encontra SSNs com formatação forte pré-2011 que não são formatadas como nove dígitos consecutivos (ddddddd)
- Func \_ \_ formatado \_ ssn encontra SSNs pós-2011 que são formatados com traços ou espaços (ddd-dd-dddd OR ddd ddd dddd)
- Func \_ randomized \_ unformatted \_ ssn encontra SSNs pós-2011 que não são formatadas como nove dígitos consecutivos (dddddddd)

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_ssn"></a>\_Ssn palavra-chave

```
SSA Number
social security number
social security #
social security#
social security no
Social Security#
Soc Sec
SSN
SSNS
SSN#
SS#
SSID
```

## <a name="us--uk-passport-number"></a>Reino Unido. número de passaporte

O Reino Unido. A entidade do tipo informação sensível ao número de passaportes está disponível no tipo de informação sensível ao número de passaportes da UE e está disponível como entidade de tipo informação sensível autónoma.

### <a name="format"></a>Formato

nove dígitos

### <a name="pattern"></a>Padrão

nove dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_passport"></a>Passaporte de palavra-chave \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```
