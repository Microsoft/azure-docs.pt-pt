---
title: Definir um perfil técnico de Insights de Aplicação numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico de Insights de Aplicação numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 77bb53e2605913fcee6999284acb04616efc53af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201417"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Defina um perfil técnico de Insights de Aplicação numa política personalizada Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) suporta o envio de dados de eventos diretamente para [a Application Insights](../azure-monitor/app/app-insights-overview.md) utilizando a chave de instrumentação fornecida ao Azure AD B2C.  Com um perfil técnico do Application Insights, pode obter registos de eventos detalhados e personalizados para as viagens do utilizador para:

* Obtenha informações sobre o comportamento do utilizador.
* Resolva as suas próprias políticas de desenvolvimento ou produção.
* Medir o desempenho.
* Criar notificações a partir de Insights de Aplicação.


## <a name="protocol"></a>Protocolo

O **atributo nome** do elemento **Protocolo** tem de ser definido para `Proprietary` . O atributo **handler** deve conter o nome totalmente qualificado do conjunto de manipuladores de protocolo que é utilizado pela Azure AD B2C para insights de aplicação: `Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

O exemplo a seguir mostra o perfil técnico comum de Insights de Aplicação. Outros perfis técnicos de Insights de Aplicação incluem o AzureInsights-Common para alavancar a sua configuração.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Reclamações de entrada

O elemento **InputClaims** contém uma lista de reclamações a enviar para Insights de Aplicação. Também pode mapear o nome da sua reclamação para um nome que prefere aparecer no Application Insights. O exemplo a seguir mostra como enviar telemetrias para Application Insights. As propriedades de um evento são adicionadas através da `{property:NAME}` sintaxe, onde name é propriedade sendo adicionada ao evento. DefaultValue pode ser um valor estático ou um valor que é resolvido por um dos [resolveres de reclamações suportados](claim-resolver-overview.md).

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

O elemento **InputClaimsTransformations** pode conter uma coleção de **elementos inputClaimsTransformation** que são utilizados para modificar as reclamações de entrada ou gerar novos antes de enviar para Insights de Aplicação.

## <a name="persist-claims"></a>Persistir reivindicações

O elemento PersistedClaims não é utilizado.

## <a name="output-claims"></a>Reclamações de saída

Os elementos OutputClaims e OutputClaimsTransformations não são utilizados.

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento CryptographicKeys não é utilizado.


## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| InstrumentaçãoKey| Sim | A [chave de instrumentação](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)Application Insights , que será utilizada para registar os eventos. | 
| DeveloperMode| Não | Um Boolean que indica se o modo de desenvolvimento está ativado. Valores possíveis: `true` ou `false` (predefinição). Estes metadados controlam a forma como os eventos são tamponados. Num ambiente de desenvolvimento com volume mínimo de eventos, permitir que o modo de desenvolvimento resulte em eventos enviados imediatamente para a Application Insights.|  
|Desativação DeTelemetria |Não |Um Booleano que indique se a telemetria deve ser ativada ou não. Valores possíveis: `true` ou `false` (predefinição).| 


## <a name="next-steps"></a>Passos seguintes

- [Criar um recurso do Application Insights](../azure-monitor/app/create-new-resource.md)
- Saiba como rastrear o [comportamento do utilizador no Azure Ative Directory B2C usando Insights de Aplicação](analytics-with-application-insights.md)
