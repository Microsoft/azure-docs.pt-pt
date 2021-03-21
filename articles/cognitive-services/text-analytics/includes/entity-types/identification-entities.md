---
title: Entidades de identificação
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 352b81bf2dfeca1d7413e7cac131264d06c7b92e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599309"
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

        Para obter esta categoria de entidade, adicione `ABARoutingNumber` ao `pii-categories` parâmetro. `ABARoutingNumber` também serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="2":::
      **Línguas documentais suportadas**

      `en`
      
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

        Para obter esta categoria de entidade, adicione `SWIFTCode` ao `pii-categories` parâmetro. `SWIFTCode` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="2":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`
      
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

        Para obter esta categoria de entidade, adicione `CreditCardNumber` ao `pii-categories` parâmetro. `CreditCardNumber` serão devolvidos na resposta da API se for detetado.

    :::column-end:::
    :::column span="2":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`, `zh-hans`, `ja`, `ko`
      
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

        Para obter esta categoria de entidade, adicione `InternationlBankingAccountNumber` ao `pii-categories` parâmetro. `InternationlBankingAccountNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="2":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`
      
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
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `ARNationalIdentityNumber` ao `pii-categories` parâmetro. `ARNationalIdentityNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`, `es`
      
   :::column-end:::
:::row-end:::


#### <a name="austria"></a>Áustria

:::row:::
    :::column span="":::
        **Entidade**

        Bilhete de identidade da Áustria

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `ATIdentityCard` ao `pii-categories` parâmetro. `ATIdentityCard` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação fiscal da Áustria

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `ATTaxIdentificationNumber` ao `pii-categories` parâmetro. `ATTaxIdentificationNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do Imposto sobre o Valor Acrescentado (IVA) da Áustria

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `ATValueAddedTaxNumber` ao `pii-categories` parâmetro. `ATValueAddedTaxNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::



#### <a name="australia"></a>Austrália

:::row:::
    :::column span="":::
        **Entidade**

        Número de conta bancária na Austrália

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `AUDriversLicenseNumber` ao `pii-categories` parâmetro. `AUDriversLicenseNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de negócios australiano

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `AUBusinessNumber` ao `pii-categories` parâmetro. `AUBusinessNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da empresa australiana

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `AUCompanyNumber` ao `pii-categories` parâmetro. `AUCompanyNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Carta de condução da Austrália  

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `AUDriversLicense` ao `pii-categories` parâmetro. `AUDriversLicense` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da conta médica da Austrália

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `AUMedicalAccountNumber` ao `pii-categories` parâmetro. `AUMedicalAccountNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de passaporte da Austrália

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `ATPassportNumber` ao `pii-categories` parâmetro. `ATPassportNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de ficheiros fiscais da Austrália

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `ATTaxIdentificationNumber` ao `pii-categories` parâmetro. `ATTaxIdentificationNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="belgium"></a>Bélgica

:::row:::
    :::column span="":::
        **Entidade**

        Número nacional da Bélgica

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `BENationalNumber` ao `pii-categories` parâmetro. `BENationalNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do Imposto sobre o Valor Acrescentado da Bélgica (IVA)

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `BEValueAddedTaxNumber` ao `pii-categories` parâmetro. `BEValueAddedTaxNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::


#### <a name="brazil"></a>Brasil 

:::row:::
    :::column span="":::
        **Entidade**

        Número de entidade jurídica do Brasil (CNPJ)

        

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `BRLegalEntityNumber` ao `pii-categories` parâmetro. `BRLegalEntityNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do CPF do Brasil

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `BRCPFNumber` ao `pii-categories` parâmetro. `BRCPFNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Cartão Nacional de Identidade do Brasil (RG)

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `BRNationalIDRG` ao `pii-categories` parâmetro. `BRNationalIDRG` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="canada"></a>Canadá

:::row:::
    :::column span="":::
        **Entidade**

        Número da conta bancária do Canadá

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `CABankAccountNumber` ao `pii-categories` parâmetro. `CABankAccountNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da carta de condução do Canadá

    :::column-end:::

    :::column span="2":::

        Para obter esta categoria de entidade, adicione `CADriversLicenseNumber` ao `pii-categories` parâmetro. `CADriversLicenseNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do serviço de saúde do Canadá

        
    :::column-end:::

    :::column span="2":::

        Para obter esta categoria de entidade, adicione `CAHealthServiceNumber` ao `pii-categories` parâmetro. `CAHealthServiceNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do passaporte do Canadá

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `CAPassportNumber` ao `pii-categories` parâmetro. `CAPassportNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação pessoal da saúde do Canadá (PHIN)

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `CAPersonalHealthIdentification` ao `pii-categories` parâmetro. `CAPersonalHealthIdentification` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do seguro social do Canadá

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `CASocialInsuranceNumber` ao `pii-categories` parâmetro. `CASocialInsuranceNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::

#### <a name="chile"></a>Chile 

:::row:::
    :::column span="":::
        **Entidade**

        Número do bilhete de identidade do Chile

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `CLIdentityCardNumber` ao `pii-categories` parâmetro. `CLIdentityCardNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `es`
      
   :::column-end:::
:::row-end:::

#### <a name="china"></a>China

:::row:::
    :::column span="":::
        **Entidade**

        Número do Bilhete de Identidade Residente da China (PRC)

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `CNResidentIdentityCardNumber` ao `pii-categories` parâmetro. `CNResidentIdentityCardNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>União Europeia (UE)

:::row:::
    :::column span="":::
        **Entidade**

        Número do cartão de débito da UE

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `EUDebitCardNumber` ao `pii-categories` parâmetro. `EUDebitCardNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da carta de condução da UE

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `EUDriversLicenseNumber` ao `pii-categories` parâmetro. `EUDriversLicenseNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Coordenadas da UE GPU

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `EUGPSCoordinates` ao `pii-categories` parâmetro. `EUGPSCoordinates` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação nacional da UE

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `EUNationalIdentificationNumber` ao `pii-categories` parâmetro. `EUNationalIdentificationNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de passaporte da UE

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `EUPassportNumber` ao `pii-categories` parâmetro. `EUPassportNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de Segurança Social da UE (SSN) ou ID equivalente

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `EUSocialSecurityNumber` ao `pii-categories` parâmetro. `EUSocialSecurityNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação fiscal da UE (TIN)

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `EUTaxIdentificationNumber` ao `pii-categories` parâmetro. `EUTaxIdentificationNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::

#### <a name="france"></a>França

:::row:::
    :::column span="":::
        **Entidade**

        Número da carta de condução da França

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `FRDriversLicenseNumber` ao `pii-categories` parâmetro. `FRDriversLicenseNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do seguro de saúde francês

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `FRHealthInsuranceNumber` ao `pii-categories` parâmetro. `FRHealthInsuranceNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Cartão de identidade nacional da França (CNI)

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `FRNationalID` ao `pii-categories` parâmetro. `FRNationalID` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de passaporte da França

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `FRPassportNumber` ao `pii-categories` parâmetro. `FRPassportNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de Segurança Social da França (INSEE)

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `FRSocialSecurityNumber` ao `pii-categories` parâmetro. `FRSocialSecurityNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação fiscal da França (Numéro SPI)

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `FRTaxIdentificationNumber` ao `pii-categories` parâmetro. `FRTaxIdentificationNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do Imposto sobre o Valor Acrescentado (IVA) da França

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `FRValueAddedTaxNumber` ao `pii-categories` parâmetro. `FRValueAddedTaxNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::

#### <a name="germany"></a>Alemanha

:::row:::
    :::column span="":::
        **Entidade**

        Número da carta de condução alemã

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `DEDriversLicenseNumber` ao `pii-categories` parâmetro. `DEDriversLicenseNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do bilhete de identidade da Alemanha

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `DEIdentityCardNumber` ao `pii-categories` parâmetro. `DEIdentityCardNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de passaporte da Alemanha

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `DEPassportNumber` ao `pii-categories` parâmetro. `DEPassportNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação fiscal da Alemanha

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `DETaxIdentificationNumber` ao `pii-categories` parâmetro. `DETaxIdentificationNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do Imposto sobre o Valor Acrescentado da Alemanha

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `DEValueAddedNumber` ao `pii-categories` parâmetro. `DEValueAddedNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>RAE de Hong Kong

:::row:::
    :::column span="":::
        **Entidade**

        Número do Bilhete de Identidade de Hong Kong (HKID)

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `HKIdentityCardNumber` ao `pii-categories` parâmetro. `HKIdentityCardNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Hungria

:::row:::
    :::column span="":::
        **Entidade**

        Número de identificação pessoal da Hungria

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `HUPersonalIdentificationNumber` ao `pii-categories` parâmetro. `HUPersonalIdentificationNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação fiscal da Hungria

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `HUTaxIdentificationNumber` ao `pii-categories` parâmetro. `HUTaxIdentificationNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do Imposto sobre o Valor Acrescentado da Hungria

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `HUValueAddedNumber` ao `pii-categories` parâmetro. `HUValueAddedNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="india"></a>Índia

:::row:::
    :::column span="":::
        **Entidade**

        Número de Conta Permanente da Índia (PAN)

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `INPermanentAccount` ao `pii-categories` parâmetro. `INPermanentAccount` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação única da Índia (Aadhaar)

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `INUniqueIdentificationNumber` ao `pii-categories` parâmetro. `INUniqueIdentificationNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Indonésia

:::row:::
    :::column span="":::
        **Entidade**

        Número do Bilhete de Identidade da Indonésia (KTP)

    :::column-end:::
    :::column span="2":::

        **Detalhes**

        Para obter esta categoria de entidade, adicione `IDIdentityCardNumber` ao `pii-categories` parâmetro. `IDIdentityCardNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="ireland"></a>Irlanda

:::row:::
    :::column span="":::
        **Entidade**

        Número do Serviço Público Pessoal da Irlanda (PPS)

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `IEPersonalPublicServiceNumber` ao `pii-categories` parâmetro. `IEPersonalPublicServiceNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
 
        Serviço Público Pessoal da Irlanda (PPS) Número v2

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `IEPersonalPublicServiceNumberV2` ao `pii-categories` parâmetro. `IEPersonalPublicServiceNumberV2` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="israel"></a>Israel

:::row:::
    :::column span="":::
        **Entidade**

        ID Nacional de Israel

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `ILNationalID` ao `pii-categories` parâmetro. `ILNationalID` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da conta bancária de Israel

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `ILBankAccountNumber` ao `pii-categories` parâmetro. `ILBankAccountNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="italy"></a>Itália

:::row:::
    :::column span="":::
        **Entidade**

        ID da carta de condução de Itália

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `ITDriversLicenseNumber` ao `pii-categories` parâmetro. `ITDriversLicenseNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Código Fiscal de Itália

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `ITFiscalCode` ao `pii-categories` parâmetro. `ITFiscalCode` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Itália Número de Imposto sobre o Valor Acrescentado

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `ITValueAddedTaxNumber` ao `pii-categories` parâmetro. `ITValueAddedTaxNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::


#### <a name="japan"></a>Japão

:::row:::
    :::column span="":::
        **Entidade**

        Número de conta bancária do Japão

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `JPBankAccountNumber` ao `pii-categories` parâmetro. `JPBankAccountNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da carta de condução do Japão

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `JPDriversLicenseNumber` ao `pii-categories` parâmetro. `JPDriversLicenseNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japão "O meu número" (pessoal)

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `JPMyNumberPersonal` ao `pii-categories` parâmetro. `JPMyNumberPersonal` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japão "My Number" (Corporate)

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `JPMyNumberCorporate` ao `pii-categories` parâmetro. `JPMyNumberCorporate` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de registo de residentes no Japão

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `ITValueAddedTaxNumber` ao `pii-categories` parâmetro. `ITValueAddedTaxNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

     `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do cartão de residência do Japão

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `JPResidenceCardNumber` ao `pii-categories` parâmetro. `JPResidenceCardNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da Segurança Social do Japão (SIN)

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `JPSocialInsuranceNumber` ao `pii-categories` parâmetro. `JPSocialInsuranceNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do passaporte do Japão

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `JPPassportNumber` ao `pii-categories` parâmetro. `JPPassportNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Luxemburgo

:::row:::
    :::column span="":::
        **Entidade**

        Número de identificação nacional do Luxemburgo (pessoas singulares)

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `LUNationalIdentificationNumberNatural` ao `pii-categories` parâmetro. `LUNationalIdentificationNumberNatural` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação nacional luxemburguês (pessoas não singulares)

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `LUNationalIdentificationNumberNonNatural` ao `pii-categories` parâmetro. `LUNationalIdentificationNumberNonNatural` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::

#### <a name="malta"></a>Malta

:::row:::
    :::column span="":::
        **Entidade**

        Número do bilhete de identidade de Malta

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `MTIdentityCardNumber` ao `pii-categories` parâmetro. `MTIdentityCardNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação fiscal de Malta

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `MTTaxIDNumber` ao `pii-categories` parâmetro. `MTTaxIDNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Nova Zelândia

:::row:::
    :::column span="":::
        **Entidade**

        Número da conta bancária da Nova Zelândia

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `NZBankAccountNumber` ao `pii-categories` parâmetro. `NZBankAccountNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da carta de condução da Nova Zelândia

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `NZDriversLicenseNumber` ao `pii-categories` parâmetro. `NZDriversLicenseNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de receitas do interior da Nova Zelândia

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `NZInlandRevenueNumber` ao `pii-categories` parâmetro. `NZInlandRevenueNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do Ministério da Saúde da Nova Zelândia

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `NZMinistryOfHealthNumber` ao `pii-categories` parâmetro. `NZMinistryOfHealthNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número do Bem-Estar Social da Nova Zelândia

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `NZSocialWelfareNumber` ao `pii-categories` parâmetro. `NZSocialWelfareNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Filipinas

:::row:::
    :::column span="":::
        **Entidade**

        Número de ID unificado das Filipinas

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `PHUnifiedMultiPurposeIDNumber` ao `pii-categories` parâmetro. `PHUnifiedMultiPurposeIDNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portugal 

:::row:::
    :::column span="":::
        **Entidade**

        Número do Cartão de Cidadão de Portugal

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `PTCitizenCardNumber` ao `pii-categories` parâmetro. `PTCitizenCardNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `pt-pt`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número de Identificação Fiscal de Portugal

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `PTTaxIdentificationNumber` ao `pii-categories` parâmetro. `PTTaxIdentificationNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`
      
   :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Singapura

:::row:::
    :::column span="":::
        **Entidade**

        Número do Cartão de Identidade de Registo Nacional de Singapura (NRIC)

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `PTTaxIdentificationNumber` ao `pii-categories` parâmetro. `PTTaxIdentificationNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`, `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>África do Sul

:::row:::
    :::column span="":::
        **Entidade**

        Número de identificação da África do Sul

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `ZAIdentificationNumber` ao `pii-categories` parâmetro. `ZAIdentificationNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Coreia do Sul

:::row:::
    :::column span="":::
        **Entidade**

        Número de registo residente na Coreia do Sul

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `KRResidentRegistrationNumber` ao `pii-categories` parâmetro. `KRResidentRegistrationNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="spain"></a>Espanha

:::row:::
    :::column span="":::
        **Entidade**

        Espanha DNI

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `ESDNI` ao `pii-categories` parâmetro. `ESDNI` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da Segurança Social de Espanha (SSN)

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `ESSocialSecurityNumber` ao `pii-categories` parâmetro. `ESSocialSecurityNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação fiscal de Espanha

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `ESTaxIdentificationNumber` ao `pii-categories` parâmetro. `ESTaxIdentificationNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>Suíça

:::row:::
    :::column span="":::
        **Entidade**

        Número de Segurança Social Suíça AHV

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `CHSocialSecurityNumber` ao `pii-categories` parâmetro. `CHSocialSecurityNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `fr`, `de`, `it`
      
   :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Taiwan 

:::row:::
    :::column span="":::
        **Entidade**

        ID Nacional de Taiwan

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `TWNationalID` ao `pii-categories` parâmetro. `TWNationalID` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Certificado de Residente de Taiwan (ARC/TARC)

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `TWResidentCertificate` ao `pii-categories` parâmetro. `TWResidentCertificate` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do passaporte de Taiwan

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `TWPassportNumber` ao `pii-categories` parâmetro. `TWPassportNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>Reino Unido

:::row:::
    :::column span="":::
        **Entidade**

        Reino Unido Número da Carta de Condução

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `UKDriversLicenseNumber` ao `pii-categories` parâmetro. `UKDriversLicenseNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`
      
    :::column-end:::
    
:::row-end:::
:::row:::
    :::column span="":::

       Reino Unido Número do Caderno Eleitoral

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `UKNationalInsuranceNumber` ao `pii-categories` parâmetro. `UKNationalInsuranceNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Reino Unido Número do Serviço Nacional de Saúde (SNS)

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `UKNationalHealthNumber` ao `pii-categories` parâmetro. `UKNationalHealthNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Reino Unido Número do Seguro Nacional (NINO)

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `UKNationalInsuranceNumber` ao `pii-categories` parâmetro. `UKNationalInsuranceNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Reino Unido ou número de passaporte dos EUA

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `USUKPassportNumber` ao `pii-categories` parâmetro. `USUKPassportNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Reino Unido Número único de referência do contribuinte

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `UKUniqueTaxpayerNumber` ao `pii-categories` parâmetro. `UKUniqueTaxpayerNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="united-states"></a>Estados Unidos da América

:::row:::
    :::column span="":::
        **Entidade**

        Número de Segurança Social dos EUA (SSN)

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter esta categoria de entidade, adicione `USSocialSecurityNumber` ao `pii-categories` parâmetro. `USSocialSecurityNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::
      **Línguas documentais suportadas**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número da carta de condução dos EUA

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `USDriversLicenseNumber` ao `pii-categories` parâmetro. `USDriversLicenseNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Reino Unido ou Reino Unido. Número do passaporte

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `USUKPassportNumber` ao `pii-categories` parâmetro. `USUKPassportNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número de identificação individual dos contribuintes dos EUA (ITIN)

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `USIndividualTaxpayerIdentification` ao `pii-categories` parâmetro. `USIndividualTaxpayerIdentification` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número da Agência de Combate à Droga dos EUA (DEA)

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `DrugEnforcementAgencyNumber` ao `pii-categories` parâmetro. `DrugEnforcementAgencyNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número da conta bancária dos EUA

    :::column-end:::
    :::column span="2":::

        Para obter esta categoria de entidade, adicione `USBankAccountNumber` ao `pii-categories` parâmetro. `USBankAccountNumber` serão devolvidos na resposta da API se for detetado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
