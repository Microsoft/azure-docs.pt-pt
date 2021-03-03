---
title: Entidades de identificação
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: a376b050d79709885e3542d330bb6b1eea48d046
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750863"
---
### <a name="financial-account-identification"></a>Identificação de conta financeira

Esta categoria de entidade inclui informação financeira e formas oficiais de identificação.

#### <a name="category-aba-routing-number"></a>Categoria: Número de encaminhamento ABA

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Número de encaminhamento ABA

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Números de encaminhamento da American Banker Association (ABA).
      
    :::column-end:::
:::row-end:::

#### <a name="category-swift-code"></a>Categoria: CÓDIGO SWIFT

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Código SWIFT

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Códigos SWIFT para informações de instruções de pagamento.
      
    :::column-end:::
:::row-end:::

#### <a name="category-credit-card"></a>Categoria: Cartão de crédito

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Cartão de crédito

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Números de cartão de crédito. 
      
    :::column-end:::
:::row-end:::

#### <a name="category-international-banking-account-number-iban"></a>Categoria: Número de conta bancária internacional (IBAN) 

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Cartão de crédito

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Códigos IBAN para informações de instruções de pagamento.
      
    :::column-end:::
:::row-end:::

### <a name="government-and-countryregion-specific-identification"></a>Identificação específica do governo e do país/região

> [!NOTE]
> As seguintes entidades financeiras e específicas de país não são devolvidas com o `domain=phi` parâmetro:
> * Números de passaporte
> * IDs de impostos

As seguintes entidades são agrupadas e listadas por país:

#### <a name="argentina"></a>Argentina

:::row:::
    :::column span="":::
        **Entidade**

        Número de Identidade Nacional argentina (DNI)

    :::column-end:::
:::row-end:::


#### <a name="austria"></a>Áustria

:::row:::
    :::column span="":::
        **Entidade**

        Bilhete de identidade da Áustria

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação fiscal da Áustria

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Número do Imposto sobre o Valor Acrescentado (IVA) da Áustria

    :::column-end:::
:::row-end:::



#### <a name="australia"></a>Austrália

:::row:::
    :::column span="":::
        **Entidade**

        Número de conta bancária na Austrália

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Número de negócios australiano

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Número da empresa australiana

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Carta de condução da Austrália  

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da conta médica da Austrália

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de passaporte da Austrália

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Número de passaporte da Austrália

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Número de ficheiros fiscais da Austrália

    :::column-end:::

:::row-end:::


#### <a name="belgium"></a>Bélgica

:::row:::
    :::column span="":::
        **Entidade**

        Número nacional da Bélgica

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do Imposto sobre o Valor Acrescentado da Bélgica (IVA)

    :::column-end:::

:::row-end:::


#### <a name="brazil"></a>Brasil 

:::row:::
    :::column span="":::
        **Entidade**

        Número de entidade jurídica do Brasil (CNPJ)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do CPF do Brasil

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Cartão Nacional de Identidade do Brasil (RG)

    :::column-end:::
:::row-end:::

#### <a name="canada"></a>Canadá

:::row:::
    :::column span="":::
        **Entidade**

        Número da conta bancária do Canadá

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Número da carta de condução do Canadá

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do serviço de saúde do Canadá

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do passaporte do Canadá

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação pessoal da saúde do Canadá (PHIN)

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Número do seguro social do Canadá

    :::column-end:::
:::row-end:::

#### <a name="chile"></a>Chile 

:::row:::
    :::column span="":::
        **Entidade**

        Número do bilhete de identidade do Chile

    :::column-end:::
:::row-end:::

#### <a name="china"></a>China

:::row:::
    :::column span="":::
        **Entidade**

        Número do Bilhete de Identidade Residente da China (PRC)

    :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>União Europeia (UE)

:::row:::
    :::column span="":::
        **Entidade**

        Número do cartão de débito da UE

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da carta de condução da UE

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação nacional da UE

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de passaporte da UE

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de Segurança Social da UE (SSN) ou ID equivalente

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação fiscal da UE (TIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Coordenadas DE GPS da UE

    :::column-end:::
:::row-end:::

#### <a name="france"></a>França

:::row:::
    :::column span="":::
        **Entidade**

        Número da carta de condução da França

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do seguro de saúde francês

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Cartão de identidade nacional da França (CNI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de passaporte da França

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de Segurança Social da França (INSEE)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação fiscal da França (Numéro SPI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do Imposto sobre o Valor Acrescentado (IVA) da França

    :::column-end:::
:::row-end:::

#### <a name="germany"></a>Alemanha

:::row:::
    :::column span="":::
        **Entidade**

        Número da carta de condução alemã

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do bilhete de identidade da Alemanha

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de passaporte da Alemanha

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação fiscal da Alemanha

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do Imposto sobre o Valor Acrescentado da Alemanha

    :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>RAE de Hong Kong

:::row:::
    :::column span="":::
        **Entidade**

        Número do Bilhete de Identidade de Hong Kong (HKID)

    :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Hungria

:::row:::
    :::column span="":::
        **Entidade**

        Número de identificação pessoal da Hungria

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação fiscal da Hungria

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do Imposto sobre o Valor Acrescentado da Hungria

    :::column-end:::
:::row-end:::

#### <a name="india"></a>Índia

:::row:::
    :::column span="":::
        **Entidade**

        Número de Conta Permanente da Índia (PAN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação única da Índia (Aadhaar)

    :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Indonésia

:::row:::
    :::column span="":::
        **Entidade**

        Número do Bilhete de Identidade da Indonésia (KTP)

    :::column-end:::
:::row-end:::

#### <a name="ireland"></a>Irlanda

:::row:::
    :::column span="":::
        **Entidade**

        Número do Serviço Público Pessoal da Irlanda (PPS)

    :::column-end:::
:::row-end:::

#### <a name="israel"></a>Israel

:::row:::
    :::column span="":::
        **Entidade**

        ID Nacional de Israel

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da conta bancária de Israel

    :::column-end:::
:::row-end:::

#### <a name="italy"></a>Itália

:::row:::
    :::column span="":::
        **Entidade**

        ID da carta de condução de Itália

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Código Fiscal de Itália

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Itália Número de Imposto sobre o Valor Acrescentado

    :::column-end:::
:::row-end:::


#### <a name="japan"></a>Japão

:::row:::
    :::column span="":::
        **Entidade**

        Número de conta bancária do Japão

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Número da carta de condução do Japão

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japão "O meu número" (pessoal)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japão "My Number" (Corporate)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de registo de residentes no Japão

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do cartão de residência do Japão

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da Segurança Social do Japão (SIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do passaporte do Japão

    :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Luxemburgo

:::row:::
    :::column span="":::
        **Entidade**

        Número de identificação nacional do Luxemburgo (pessoas singulares)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação nacional luxemburguês (pessoas não singulares)

    :::column-end:::
:::row-end:::

#### <a name="malta"></a>Malta

:::row:::
    :::column span="":::
        **Entidade**

        Número do bilhete de identidade de Malta

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação fiscal de Malta

    :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Nova Zelândia

:::row:::
    :::column span="":::
        **Entidade**

        Número da conta bancária da Nova Zelândia

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da carta de condução da Nova Zelândia

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de receitas do interior da Nova Zelândia

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do Ministério da Saúde da Nova Zelândia

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número do Bem-Estar Social da Nova Zelândia

    :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Filipinas

:::row:::
    :::column span="":::
        **Entidade**

        Número de ID unificado das Filipinas

    :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portugal 

:::row:::
    :::column span="":::
        **Entidade**

        Número do Cartão de Cidadão de Portugal

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número de Identificação Fiscal de Portugal

    :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Singapura

:::row:::
    :::column span="":::
        **Entidade**

        Número do Cartão de Identidade de Registo Nacional de Singapura (NRIC)

    :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>África do Sul

:::row:::
    :::column span="":::
        **Entidade**

        Número de identificação da África do Sul

    :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Coreia do Sul

:::row:::
    :::column span="":::
        **Entidade**

        Número de registo residente na Coreia do Sul

    :::column-end:::
:::row-end:::

#### <a name="spain"></a>Espanha

:::row:::
    :::column span="":::
        **Entidade**

        Espanha DNI

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da Segurança Social de Espanha (SSN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação fiscal de Espanha

    :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>Suíça

:::row:::
    :::column span="":::
        **Entidade**

        Número de Segurança Social Suíça AHV

    :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Taiwan 

:::row:::
    :::column span="":::
        **Entidade**

        ID Nacional de Taiwan

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Certificado de Residente de Taiwan (ARC/TARC)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do passaporte de Taiwan

    :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>Reino Unido

:::row:::
    :::column span="":::
        **Entidade**

        Reino Unido Número da Carta de Condução

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Reino Unido Número do Caderno Eleitoral

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Reino Unido Número do Serviço Nacional de Saúde (SNS)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Reino Unido Número do Seguro Nacional (NINO)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Reino Unido ou número de passaporte dos EUA

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

       Reino Unido Número único de referência do contribuinte

    :::column-end:::

:::row-end:::


#### <a name="united-states"></a>Estados Unidos da América

:::row:::
    :::column span="":::
        **Entidade**

        Número de Segurança Social dos EUA (SSN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número da carta de condução dos EUA

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Reino Unido ou Reino Unido. Número do passaporte

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número de identificação individual dos contribuintes dos EUA (ITIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número da Agência de Combate à Droga dos EUA (DEA)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número da conta bancária dos EUA

    :::column-end:::
:::row-end:::
