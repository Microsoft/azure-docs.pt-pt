---
title: Entidades de identificação
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 49a2b285d9494dfc5ca0863c021733b4bc874efa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779436"
---
Esta categoria de entidade inclui informação financeira e formas oficiais de identificação. Disponível a partir da versão `2019-10-01` modelo. Os subtipos estão listados abaixo. 

### <a name="financial-account-identification"></a>Identificação de conta financeira

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

As seguintes entidades são agrupadas e listadas por país:

Argentina
* Número de Identidade Nacional argentina (DNI)

Áustria
* Bilhete de Identidade da Áustria
* Número de identificação fiscal da Áustria
* Número do Imposto sobre o Valor Acrescentado (IVA) da Áustria

Austrália
* Número da conta bancária da Austrália
* Número de negócios australiano
* Número da empresa australiana
* Número da carta de condução da Austrália
* Número da conta médica da Austrália
* Número do passaporte da Austrália
* Número de ficheiros fiscais da Austrália

Bélgica
* Número Nacional da Bélgica
* Número do Imposto sobre o Valor Acrescentado da Bélgica

Brasil 
* Número da Entidade Jurídica do Brasil (CNPJ)
* Número do CPF do Brasil
* Cartão Nacional de Identidade do Brasil (RG)

Bulgária
* Número civil uniforme da Bulgária

Canadá
* Número da conta bancária do Canadá
* Número da carta de condução do Canadá
* Número do Serviço de Saúde do Canadá
* Número do passaporte do Canadá
* Número de identificação pessoal da saúde do Canadá (PHIN)
* Número do Seguro Social do Canadá

Chile
* Número do bilhete de identidade 

China
* Número do Bilhete de Identidade Residente da China (PRC)

Croácia
* Número do bilhete de identidade da Croácia
* Número de cartão de identidade nacional da Croácia
* Número de Identificação Pessoal da Croácia (OIB)

Chipre
* Número do bilhete de identidade de Chipre
* Número de identificação fiscal de Chipre

República Checa
* Número de identidade pessoal checa

Dinamarca
* Número de identificação pessoal da Dinamarca

Estónia
* Código de Identificação Pessoal da Estónia

União Europeia (UE)
* Número do cartão de débito da UE
* Número da carta de condução da UE
* Número de identificação nacional da UE
* Número de passaporte da UE
* Número de Segurança Social da UE (SSN) ou ID equivalente
* Número de identificação fiscal da UE (TIN)

Finlândia
* Número do Seguro De Saúde Europeu da Finlândia
* ID Nacional da Finlândia
* Número do passaporte da Finlândia

França
* Número da carta de condução da França
* Número do Seguro de Saúde francês
* Cartão de identidade nacional da França (CNI)
* Número do passaporte da França
* Número de Segurança Social da França (INSEE)
* Número de identificação fiscal da França (Numéro SPI)
* Número de imposto sobre o valor acrescentado da França

Alemanha
* Número da carta de condução alemã
* Número do bilhete de identidade da Alemanha
* Número do passaporte alemão
* Número de identificação fiscal da Alemanha
* Número do Imposto sobre o Valor Acrescentado da Alemanha

Grécia 
* Número de cartão de identidade nacional da Grécia
* Número de identificação fiscal da Grécia

RAE de Hong Kong
* Número do Bilhete de Identidade de Hong Kong (HKID)

Hungria
* Número de identificação nacional da Hungria
* Número de identificação fiscal da Hungria
* Número do Imposto sobre o Valor Acrescentado da Hungria

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
* Código Fiscal de Itália
* Itália Número de Imposto sobre o Valor Acrescentado

Japão
* Número de conta bancária do Japão
* Número da carta de condução do Japão
* Japonês Meu Número Pessoal
* Japonês My Number Corporate
* Número de registo de residentes no Japão
* Número do cartão de residência japonês
* Número da Segurança Social do Japão (SIN)
* Número do passaporte do Japão

Letónia
* Código Pessoal da Letónia

Lituânia
* Código Pessoal da Lituânia

Luxemburgo
* Número de Identificação Nacional do Luxemburgo (Pessoas Singulares)
* Número de Identificação Nacional do Luxemburgo (pessoas não singulares)

Malásia
* Número do bilhete de identidade da Malásia

Malta
* Número do bilhete de identidade de Malta
* Número de identificação fiscal de Malta

Países Baixos
* Número do Serviço de Cidadãos Holandeses (BSN)
* Número de identificação fiscal dos Países Baixos
* Número do Imposto sobre o Valor Acrescentado dos Países Baixos

Nova Zelândia
* Número da conta bancária da Nova Zelândia
* Número da carta de condução da Nova Zelândia
* Número de receitas do interior da Nova Zelândia
* Número do Ministério da Saúde da Nova Zelândia
* Número do Bem-Estar Social da Nova Zelândia

Noruega
* Número de identidade da Noruega

Filipinas
* Número de ID unificado das Filipinas

Polónia
* Cartão de Identidade da Polónia
* ID Nacional da Polónia (PESEL)
* Número do passaporte da Polónia
* Número regon da Polónia
* Número de identificação fiscal da Polónia

Portugal 
* Número do Cartão de Cidadão de Portugal
* Número de Identificação Fiscal de Portugal

Roménia
* Código Numérico Pessoal da Roménia (CNP)

Rússia
* Número do passaporte russo (doméstico)
* Número do passaporte russo (internacional)

Arábia Saudita
* ID Nacional da Arábia Saudita

Singapura
* Número do Cartão de Identidade de Registo Nacional de Singapura (NRIC)

Eslováquia 
* Número pessoal da Eslováquia

Eslovénia
* Número de identificação fiscal da Eslovénia
* Número único de cidadão-mestre da Eslovénia

África do Sul
* Número de identificação da África do Sul

Coreia do Sul
* Número de registo residente na Coreia do Sul

Espanha 
* Espanha DNI
* Número da Segurança Social de Espanha (SSN)
* Número de identificação fiscal de Espanha

Suécia
* ID Nacional da Suécia
* Número do passaporte da Suécia
* Número de identificação fiscal da Suécia

Suíça
* Número de Segurança Social Suíça AHV

Taiwan 
* ID Nacional de Taiwan
* Certificado de Residente de Taiwan (ARC/TARC)
* Número do passaporte de Taiwan

Tailândia
* Código de Identificação da População Tailandesa

Turquia
* Número de identificação nacional turca

Ucrânia
* Número do passaporte da Ucrânia (Doméstico)
* Número do passaporte da Ucrânia (Internacional)

Reino Unido
* Reino Unido Número da Carta de Condução
* Reino Unido Número do Caderno Eleitoral
* Reino Unido Número do Serviço Nacional de Saúde (SNS)
* Reino Unido Número do Seguro Nacional (NINO)
* Reino Unido Número do passaporte
* Reino Unido Número único de referência do contribuinte

Estados Unidos da América
* Número de Segurança Social dos EUA (SSN)
* Número da carta de condução dos EUA
* Número do passaporte dos EUA
* Número de identificação individual dos contribuintes dos EUA (ITIN)
* Número da Agência de Combate à Droga dos EUA (DEA)
* Número da conta bancária dos EUA
