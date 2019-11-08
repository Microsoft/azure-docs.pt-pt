---
title: Entidades nomeadas para informações pessoais
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2019
ms.author: aahi
ms.openlocfilehash: 3aa4da9a9cf3d1d4b664e81f1fd18f2b225d731d
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73800067"
---
## <a name="personal-information-entity-types"></a>Tipos de entidade de informações pessoais:

### <a name="phone-number"></a>Número de Telefone

Números de telefone. 

Idiomas:

* Visualização pública: `English`

| Nome do subtipo           | Descrição                                           |
|------------------------|-------------------------------------------------------|
| N/D                    | Números de telefone, por exemplo `+1 123-123-123`.          |
| Número de telefone da UE        | Números de telefone específicos da União Europeia.         |
| Número de celular da UE | Números de telefone celular específicos à União Europeia. |

### <a name="eu-gps-coordinates"></a>Coordenadas do GPS da UE

 Coordenadas de GPS para locais na União Europeia. 

Idiomas:

* Visualização pública: `English`

| Nome do subtipo | Descrição                               |
|--------------|-------------------------------------------|
| N/D          | Coordenadas de GPS na União Europeia |

### <a name="azure-information"></a>Informações do Azure

Informações identificáveis do Azure, incluindo informações de autenticação e cadeias de conexão. 

Idiomas:

* Visualização pública: `English`

| Nome do subtipo                          | Descrição                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Chave de autenticação do Azure DocumentDB             | Chave de autorização para um servidor do Azure DocumentDB.                           |
| Cadeia de conexão do banco de dados IAAS do Azure | Cadeia de conexão para um banco de dados IaaS (infraestrutura como serviço) do Azure. |
| Cadeia de conexão SQL do Azure           | Cadeia de conexão para um banco de dados SQL do Azure.                                |
| Cadeia de conexão do Azure IoT           | Cadeia de conexão para a IoT (Internet das coisas) do Azure.                        |
| Senha de configuração de publicação do Azure        | Senha para as configurações de publicação do Azure.                                        |
| Cadeia de conexão do cache Redis do Azure   | Cadeia de conexão para um cache do Azure para Redis.                             |
| SAS do Azure                             | Cadeia de conexão para o software como um serviço (SAS) do Azure.                     |
| Cadeia de conexão do barramento de serviço do Azure   | Cadeia de conexão para um barramento de serviço do Azure.                                |
| Chave de conta de armazenamento do Azure             | Chave de conta para uma conta de armazenamento do Azure.                                   |
| Chave da conta de armazenamento do Azure (genérica)   | Chave de conta genérica para uma conta de armazenamento do Azure.                           |
| SQL Server cadeia de conexão          | Cadeia de conexão para um SQL Server.                                         |

### <a name="identification"></a>ID

Idiomas:

* Visualização pública: `English`

#### <a name="financial-account-identification"></a>Identificação da conta financeira

| Nome do subtipo               | Descrição                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABAr números de roteamento        | Números de roteamento de trânsito da ABA (Associação Americana do Banker).                  |
| Código SWIFT                 | Códigos SWIFT para informações de instrução de pagamento.                           |
| Cartão de Crédito                | Números de cartão de crédito.                                                       |
| Código do IBAN                  | Códigos de IBAN para informações de instrução de pagamento.                            |

#### <a name="government-and-country-specific-identification"></a>Identificação específica do governo e do país

As entidades abaixo são agrupadas e listadas por país:

Argentina
* Número de identidade nacional (DNI)

Austrália
* Número do arquivo de imposto 
* ID da licença do driver
* ID do passaporte
* Número da conta médica
* números de conta bancária (por exemplo, contas de verificação, poupança e débito)

Bélgica
* Número nacional

Brasil
* Número da entidade legal (CNPJ)
* Número do CPF
* Cartão de ID nacional (RG)

Canadá
* ID do passaporte
* ID da licença do driver
* Número de seguro de integridade
* Número de ID de integridade pessoal (PHIN)
* Número do seguro social
* números de conta bancária (por exemplo, contas de verificação, poupança e débito)

Chile
* Número do cartão de identidade 

China
* Número do cartão de identidade
* Número de placa de ID residente (PRC)

Croácia
* Número do cartão de ID
* Número da ID pessoal (OIB)

República Checa
* Número do cartão da ID nacional

Dinamarca
* Número da ID pessoal

União Europeia (UE)
* Número de ID nacional
* ID do passaporte
* ID da licença do driver
* Número do seguro social (SSN) ou ID equivalente
* Número de identificação do imposto da UE (TIN)
* Número do cartão de débito da UE

Finlândia
* Número de ID nacional
* ID do passaporte

França
* Placa de ID nacional (CNI)
* Número do seguro social (INSEE)
* ID do passaporte
* ID da licença do driver

Alemanha
* Número do cartão de ID
* ID do passaporte
* ID da licença do driver

Grécia 
* Número do cartão da ID nacional

RAE de Hong Kong
* Número do cartão de ID (HKID)

Índia
* Número da conta permanente (PAN)
* Número de ID exclusiva (Aadhaar)

Indonésia
* Número do cartão de ID (KTP)

Irlanda
* Número do PPS (serviço público pessoal)

Israel
* ID nacional
* números de conta bancária (por exemplo, contas de verificação, poupança e débito)

Itália
* ID da licença do driver

Japão
* Número de registro residente
* Número da placa de residência
* ID da licença do driver
* Número de seguro social (SIN)
* ID do passaporte
* números de conta bancária (por exemplo, contas de verificação, poupança e débito)

Malásia
* Número do cartão de ID

Países Baixos
* Número de serviço do cidadão (BSN)

Nova Zelândia
* Ministério do número de integridade

Noruega
* Número do cartão de ID

Filipinas
* Número de ID de multipropósito unificado

Polónia
* Número do cartão de ID
* ID nacional (PESEL)
* ID do passaporte

Portugal 
* Número do cartão do cidadão

Arábia Saudita
* ID nacional

Singapura
* Número do NRIC (ID de Registro Nacional)

África do Sul
* Número da ID
* Número de registro residente

Coreia do Sul
* Número de registro residente

Espanha 
* Número do seguro social (SSN)

Suécia
* ID nacional
* ID do passaporte

Taiwan 
* ID nacional
* Número do certificado residente (arco/TARC)
* ID do passaporte

Tailândia
* Código de identificação da população

Reino Unido
* ID do passaporte
* ID da licença do driver
* Número de seguro nacional (NINO)
* Número de Serviço de Integridade nacional (NHS)

Estados Unidos
* Número do seguro social (SSN)
* ID da licença do driver
* ID do passaporte
* Número de reversão do eleitoral
* Número de ID de imposto individual (certa)
* Número da Agência de aplicação de drogas (DEA)
* números de conta bancária (por exemplo, contas de verificação, poupança e débito)
