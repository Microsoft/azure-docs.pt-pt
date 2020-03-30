---
title: Defina um perfil técnico de Insights de Aplicação numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico de Insights de Aplicação numa política personalizada no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f50373b0841b7626bc405f121015c15ae1587a97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80108578"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Defina um perfil técnico de Insights de Aplicação numa política personalizada Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) suporta o envio de dados do evento diretamente para [a Application Insights](../azure-monitor/app/app-insights-overview.md) utilizando a chave de instrumentação fornecida ao Azure AD B2C.  Com um perfil técnico da Application Insights, pode obter registos de eventos detalhados e personalizados para as suas viagens de utilizador para:

* Obtenha informações sobre o comportamento do utilizador.
* Problemas de suposição das suas próprias políticas em desenvolvimento ou em produção.
* Medir o desempenho.
* Crie notificações a partir de Insights de Aplicação.


## <a name="protocol"></a>Protocolo

O **atributo** nome do elemento **protocolo** `Proprietary`tem de ser definido para . O atributo **do manipulador** deve conter o nome totalmente qualificado do conjunto de manipulador esprotocolo que é utilizado pelo Azure AD B2C para Insights de Aplicação:`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

O exemplo seguinte mostra o perfil técnico comum de Insights de Aplicação. Outros perfis técnicos de Insights de Aplicação incluem o AzureInsights-Common para alavancar a sua configuração.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Reclamações de entrada

O elemento **InputClaims** contém uma lista de reclamações a enviar para Insights de Aplicação. Também pode mapear o nome da sua reclamação para um nome que prefere aparecer no Application Insights. O exemplo que se segue mostra como enviar telemetrias para Application Insights. As propriedades de um evento são `{property:NAME}`adicionadas através da sintaxe, onde o NOME é adicionado ao evento. O DefaultValue pode ser um valor estático ou um valor resolvido por uma das resoluções de [reclamações](claim-resolver-overview.md)suportadas.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **de transformação inputClaims** que são usados para modificar as reclamações de entrada ou gerar novos antes de enviar para Application Insights.

## <a name="persist-claims"></a>Persistir reivindicações

O elemento PersistedClaims não é utilizado.

## <a name="output-claims"></a>Reclamações de produção

Os elementos OutputClaims e OutputClaimsTransformations não são utilizados.

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento CryptographicKeys não é utilizado.


## <a name="metadata"></a>Metadados

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Chave de Instrumentação| Sim | A chave de [instrumentação](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)Application Insights, que será utilizada para registar os eventos. | 
| Modo de Desenvolvimento| Não | Um Boolean que indica se o modo de desenvolvimento está ativado. Valores `true` possíveis: ou `false` (padrão). Estes metadados controlam a forma como os eventos são tamponados. Num ambiente de desenvolvimento com o mínimo volume de eventos, permitir que o modo de desenvolvimento resulte em eventos enviados imediatamente para Application Insights.|  
|Telemetria desativação |Não |Um Boolean que indica se a telemetria deve ser ativada ou não. Valores `true` possíveis: ou `false` (padrão).| 


## <a name="next-steps"></a>Passos seguintes

- [Criar um recurso Deinsights de Aplicação](../azure-monitor/app/create-new-resource.md)
- Saiba como acompanhar o [comportamento do utilizador no Diretório Ativo Azure B2C usando insights](analytics-with-application-insights.md) de aplicação
