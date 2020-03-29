---
title: Entidades nomeadas para informações pessoais
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: d678a29de9dea8a5a2f6d0259a452ca4c69feb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77086889"
---
## <a name="personal-information-entity-types"></a>Tipos de entidades de informação pessoal:

### <a name="person"></a>Pessoa
Reconhecer nomes de pessoas em texto.

Idiomas:
* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                               | Disponível a partir da versão modelo |
|--------------|-----------------------------------------------------------|----------------------------------------|
| N/D          | Nomes reconhecidos, por exemplo, `Bill Gates``Marie Curie` | `2020-02-01`                           |

### <a name="organization"></a>Organização  

Reconhecer organizações, corporações, agências, empresas, clubes e outros grupos de pessoas.

Idiomas: 

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                                                                       | Disponível a partir da versão modelo|
|--------------|---------------------------------------------------------------------------------------------------|--------------|
| N/D          | organizações, por `Microsoft` `NASA`exemplo,`National Oceanic and Atmospheric Administration` | `2020-02-01` |

### <a name="phone-number"></a>Número de Telefone

Números de telefone (apenas números de telefone dos EUA). 

Idiomas:

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                    | Disponível a partir da versão modelo |
|--------------|------------------------------------------------|----------------------------------------|
| N/D          | Números de telefone dos EUA, por exemplo`(312) 555-0176` | `2020-02-01`                           |

### <a name="email"></a>Email

Endereço de e-mail. 

Idiomas:

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                      | Disponível a partir da versão modelo |
|--------------|--------------------------------------------------|----------------------------------------|
| N/D          | Endereço de e-mail, por exemplo`support@contoso.com` | `2020-02-01`                           |

### <a name="url"></a>do IdP

URLs de Internet.

Idiomas:

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                          | Disponível a partir da versão modelo |
|--------------|------------------------------------------------------|----------------------------------------|
| N/D          | URLs para websites, por exemplo`https://www.bing.com` | `2020-02-01`                           |

### <a name="ip-address"></a>Endereço IP

Endereço de protocolo de Internet

Idiomas:

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                              | Disponível a partir da versão modelo |
|--------------|------------------------------------------|----------------------------------------|
| N/D          | Endereço de rede, por exemplo,`10.0.0.101` | `2020-02-01`                           |

### <a name="quantity"></a>Quantidade 

Quantidades numéricas

Idiomas:

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                   | Disponível a partir da versão modelo |
|--------------|-------------------------------|----------------------------------------|
| Idade          | `90 days old`, `30 years old` | `2020-02-01`                           |

### <a name="datetime"></a>DateTime

Entidades de data e hora

Idiomas:

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                   | Disponível a partir da versão modelo |
|--------------|-------------------------------|----------------------------------------|
| Date         | `May 2nd, 2017`, `05/02/2017` | `2020-02-01`                           |

### <a name="eu-gps-coordinates"></a>Coordenadas de GPS da UE

 Coordenadas GPS para locais dentro da União Europeia. 

Idiomas:

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                               | Disponível a partir da versão modelo |
|--------------|-------------------------------------------|----------------------------------------|
| N/D          | Coordenadas GPS na União Europeia | `2019-10-01`                           |

### <a name="azure-information"></a>Informação do Azure

Informações identificáveis do Azure, incluindo informações de autenticação e cordas de ligação. 

* Disponível a partir `2019-10-01`da versão modelo.

Idiomas:

* Pré-visualização pública:`English`

| Nome do subtipo                          | Descrição                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Chave Auth Azure DocumentDB             | Chave de autorização para um servidor Azure DocumentDB.                           |
| Fio de conexão de base de dados Azure IAAS | Fio de ligação para uma infraestrutura azure como base de dados de serviço (IaaS). |
| Fio de ligação SQL Azure           | Fio de ligação para uma base de dados Azure SQL.                                |
| Fio de conexão Azure IoT           | Fio de ligação para Azure Internet das coisas (IoT).                        |
| Palavra-passe de definição de publicação azure        | Palavra-passe para definições da Azure Publish.                                        |
| Fio de ligação à cache Azure Redis   | Fio de ligação para um Cache Azure para Redis.                             |
| Azure SAS                             | Fio de ligação para o Software Azure como Serviço (SAS).                     |
| Fio de conexão de ônibus de serviço azure   | Fio de ligação para um ônibus de serviço Azure.                                |
| Chave da conta de armazenamento azure             | Chave de conta para uma conta de armazenamento Azure.                                   |
| Chave da conta de armazenamento azure (genérico)   | Chave de conta genérica para uma conta de armazenamento Azure.                           |
| Cadeia de ligação ao servidor SQL          | Fio de ligação para um servidor SQL.                                         |

### <a name="identification"></a>Identificação

* Disponível a partir `2019-10-01`da versão modelo.

Idiomas:

* Pré-visualização pública:`English`

#### <a name="financial-account-identification"></a>Identificação de Conta Financeira

| Nome do subtipo               | Descrição                                                                |
|----------------------------|----------------------------------------------------------------------------|
| Números de encaminhamento da ABA        | Números de encaminhamento de trânsito da American Banker Association (ABA).                  |
| Código SWIFT                 | Códigos SWIFT para informações de instrução de pagamento.                           |
| Cartão de Crédito                | Números de cartão de crédito.                                                       |
| Código IBAN                  | Códigos IBAN para informações de instrução de pagamento.                            |

#### <a name="government-and-country-specific-identification"></a>Identificação específica do governo e do país

As entidades abaixo são agruparadas e listadas por país:

Argentina
* Número de Identidade Nacional (DNI)

Austrália
* Número de ficheiro fiscal 
* Identificação da carta de condução
* ID do passaporte
* Número da conta médica
* números de contas bancárias (por exemplo, contas de verificação, poupança e débito)

Bélgica
* Número nacional

Brasil
* Número de Entidade Jurídica (CNPJ)
* Número de CPF
* Cartão de identificação nacional (RG)

Canadá
* ID do passaporte
* Identificação da carta de condução
* Número do seguro de saúde
* Número de identificação de saúde pessoal (PHIN)
* Número de Segurança Social
* números de contas bancárias (por exemplo, contas de verificação, poupança e débito)

Chile
* Número do bilhete de identidade 

China
* Número do bilhete de identidade
* Número do cartão de identificação residente (RPC)

Croácia
* Número do cartão de identificação
* Número de ID pessoal (OIB)

República Checa
* Número do cartão de identificação nacional

Dinamarca
* Número de identificação pessoal

União Europeia (UE)
* Número de identificação nacional
* ID do passaporte
* Identificação da carta de condução
* Número de Segurança Social (SSN) ou ID equivalente
* Número de identificação fiscal da UE (TIN)
* Número do cartão de débito da UE

Finlândia
* Número de identificação nacional
* ID do passaporte

França
* Cartão de identificação nacional (CNI)
* Número de Segurança Social (INSEE)
* ID do passaporte
* Identificação da carta de condução

Alemanha
* Número do cartão de identificação
* ID do passaporte
* Identificação da carta de condução

Grécia 
* Número do cartão de identificação nacional

RAE de Hong Kong
* Número do cartão de identificação (HKID)

Índia
* Número da Conta Permanente (PAN)
* Número único de identificação (Aadhaar)

Indonésia
* Número do cartão de identificação (KTP)

Irlanda
* Número de Serviço Público Pessoal (PPS)

Israel
* ID Nacional
* números de contas bancárias (por exemplo, contas de verificação, poupança e débito)

Itália
* Identificação da carta de condução

Japão
* Número de registo de residente
* Número do cartão de residência
* Identificação da carta de condução
* Número de Seguro Social (SIN)
* ID do passaporte
* números de contas bancárias (por exemplo, contas de verificação, poupança e débito)

Malásia
* Número do cartão de identificação

Países Baixos
* Número do Serviço do Cidadão (BSN)

Nova Zelândia
* Número do Ministério da Saúde

Noruega
* Número do cartão de identificação

Filipinas
* Número de identificação unificado multi-uso

Polónia
* Número do cartão de identificação
* ID Nacional (PESEL)
* ID do passaporte

Portugal 
* Número do Cartão de Cidadão

Arábia Saudita
* ID Nacional

Singapura
* Número do Cartão de Identificação de Registo Nacional (NRIC)

África do Sul
* Número de identificação
* Número de registo de residente

Coreia do Sul
* Número de inscrição de residente

Espanha 
* Número de Segurança Social (SSN)

Suécia
* ID Nacional
* ID do passaporte

Taiwan 
* ID Nacional
* Número de Certificado de Residente (ARC/TARC)
* ID do passaporte

Tailândia
* Código de Identificação da População

Reino Unido
* ID do passaporte
* Identificação da carta de condução
* Número do Seguro Nacional (NINO)
* Número do Serviço Nacional de Saúde (SNS)

Estados Unidos
* Número de Segurança Social (SSN)
* Identificação da carta de condução
* ID do passaporte
* Número do caderno eleitoral
* Número de identificação de imposto individual (ITIN)
* Número da Agência de Aplicação de Drogas (DEA)
* números de contas bancárias (por exemplo, contas de verificação, poupança e débito)
