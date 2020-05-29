---
title: Entidades de identificação
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: f07b71bf8996612798b87d32a21a15ec72db0b32
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140947"
---
Esta categoria de entidade inclui informação financeira e formas oficiais de identificação. Disponível a partir da versão `2019-10-01` modelo. Os subtipos estão listados abaixo. 

### <a name="financial-account-identification"></a>Identificação da Conta Financeira

| Nome do subtipo               | Descrição                                                                |
|----------------------------|----------------------------------------------------------------------------|
| Número de Encaminhamento ABA        | Números de encaminhamento da American Banker Association (ABA).                  |
| Código SWIFT                 | Códigos SWIFT para informações de instruções de pagamento.                           |
| Cartão de Crédito                | Números de cartão de crédito.                                                       |
| Número de Conta Bancária Internacional (IBAN)                  | Códigos IBAN para informações de instruções de pagamento.                            |


### <a name="government-and-countryregion-specific-identification"></a>Identificação específica do governo e do país/região

> [!NOTE]
> As seguintes entidades financeiras e específicas de país não são devolvidas com o `domain=phi` parâmetro:
> * Números de passaporte
> * IDs de impostos

As entidades abaixo estão agrupadas e listadas por país:

Argentina
* Número de Identidade Nacional argentina (DNI)

Austrália
* Número do passaporte da Austrália
* Número de ficheiros fiscais da Austrália
* Número da carta de condução da Austrália
* Número da conta médica da Austrália
* Número da conta bancária da Austrália

Bélgica
* Número nacional da Bélgica

Brasil 
* Número da Entidade Jurídica do Brasil (CNPJ)
* Número do CPF do Brasil
* Cartão Nacional de Identidade do Brasil (RG)

Canadá
* Número do Seguro Social do Canadá
* Número da carta de condução do Canadá
* Número da conta bancária do Canadá
* Número do passaporte do Canadá
* Número de identificação pessoal da saúde do Canadá (PHIN)
* Número do Serviço de Saúde do Canadá

Chile
* Número do bilhete de identidade 

China
* Número do Bilhete de Identidade Residente da China (PRC)

Croácia
* Número do bilhete de identidade da Croácia
* Número de Identificação Pessoal da Croácia (OIB)

República Checa
* Número de identidade pessoal checa

Dinamarca
* Número de identificação pessoal da Dinamarca

União Europeia (UE)
* Número de identificação nacional da UE
* Número de passaporte da UE
* Número da carta de condução da UE
* Número de Segurança Social da UE (SSN) ou ID equivalente
* Número de identificação fiscal da UE (TIN)
* Número do cartão de débito da UE

Finlândia
* ID Nacional da Finlândia
* Número do passaporte da Finlândia

França
* Cartão de identidade nacional da França (CNI)
* Número de Segurança Social da França (INSEE)
* Número do passaporte da França
* Número da carta de condução da França

Alemanha
* Número do bilhete de identidade da Alemanha
* Número do passaporte alemão
* Número da carta de condução alemã

Grécia 
* Número do cartão de identidade nacional da Grécia

RAE de Hong Kong
* Número do Bilhete de Identidade de Hong Kong (HKID)

Índia
* Número de Conta Permanente da Índia (PAN)
* Número de identificação única da Índia (Aadhaar)

Indonésia
* Número do Bilhete de Identidade da Indonésia (KTP)

Irlanda
* Número do Serviço Público Pessoal da Irlanda (PPS)

Israel
* ID Nacional de Israel
* Número da conta bancária de Israel

Itália
* ID da carta de condução de Itália

Japão
* Número de registo de residentes no Japão
* Número do cartão de residência japonês
* Número da carta de condução do Japão
* Número do Seguro Social (SIN)
* Número do passaporte do Japão
* Número de conta bancária do Japão

Malásia
* Número do Bilhete de Identidade da Malásia

Países Baixos
* Número do Serviço de Cidadãos Holandeses (BSN)

Nova Zelândia
* Número do Ministério da Saúde da Nova Zelândia

Noruega
* Número de identidade da Noruega

Filipinas
* Número de ID unificado das Filipinas

Polónia
* Cartão de Identidade da Polónia
* ID Nacional da Polónia (PESEL)
* Passaporte da Polónia

Portugal 
* Número do Cartão de Cidadão de Portugal

Arábia Saudita
* ID Nacional da Arábia Saudita

Singapura
* Número do Cartão De Identidade de Registo Nacional de Singapura (NRIC)

África do Sul
* Número de identificação da África do Sul

Coreia do Sul
* Número de registo residente na Coreia do Sul

Espanha 
* Número da Segurança Social de Espanha (SSN)

Suécia
* ID Nacional da Suécia
* Número do passaporte da Suécia

Taiwan 
* ID Nacional de Taiwan
* Certificado de Residente de Taiwan (ARC/TARC)
* Número do passaporte de Taiwan

Tailândia
* Código de Identificação da População Tailandesa

Reino Unido
* ID do passaporte
* Reino Unido Número da carta de condução
* Reino Unido Número do Seguro Nacional (NINO)
* Reino Unido Número do Serviço Nacional de Saúde
* Reino Unido Número do Caderno Eleitoral
* Reino Unido. Número do passaporte

Estados Unidos da América
* Número de Segurança Social dos EUA (SSN)
* Número da carta de condução dos EUA
* Reino Unido. Número do passaporte
* Número de identificação individual dos contribuintes dos EUA (ITIN)
* Número da Drug Enforcement Agency (DEA)
* Número da conta bancária dos EUA
