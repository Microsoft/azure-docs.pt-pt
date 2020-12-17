---
title: Estratégias de autenticação de serviços de medição de mercado / Mercado Azure
description: Estratégias de autenticação de serviços de medição apoiadas no Mercado Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/21/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: b418a9cae6f6d58dbe82babcfe6fe1e1a5027d43
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657078"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Estratégias de autenticação do serviço de medição do mercado

O serviço de medição de mercado suporta duas estratégias de autenticação:

* [Ficha de segurança da AD AZure](../../active-directory/develop/access-tokens.md)
* [Identidades geridas](../../active-directory/managed-identities-azure-resources/overview.md) 

Explicaremos quando e como usar as diferentes estratégias de autenticação para submeter com segurança contadores personalizados usando o serviço de medição do Marketplace.

## <a name="using-the-azure-ad-security-token"></a>Usando o sinal de segurança Azure AD

Os tipos de oferta aplicáveis são aplicações transacionáveis saaS e Azure com tipo de plano de aplicação gerido.  

Envie contadores personalizados utilizando um ID de aplicação AD fixo predefinido para autenticar.

Para ofertas saas, esta é a única opção disponível. É um passo obrigatório para a publicação de qualquer oferta SaaS, conforme descrito no [registo de uma aplicação SaaS.](./pc-saas-registration.md)

Para aplicações Azure com plano de aplicação gerido, deve considerar a utilização desta estratégia nos seguintes casos:

* Você já tem um mecanismo para comunicar com os seus serviços de backend, e você quer estender este mecanismo para emitir medidores personalizados de um serviço central.
* Tem uma lógica complexa de medidores personalizados.  Executar esta lógica numa localização central, em vez dos recursos de aplicação geridos.

Uma vez registado o seu pedido, pode solicitar programáticamente um sinal de segurança Azure. Espera-se que a editora use este símbolo e faça um pedido para resolvê-lo.

Para obter mais informações sobre estes tokens, consulte [os tokens de acesso do Azure Ative Directory](../../active-directory/develop/access-tokens.md).

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Obtenha um token com base na app AD AZure

#### <a name="http-method"></a>Método HTTP

**Publicar**

#### <a name="request-url"></a>*URL do Pedido*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*Parâmetro URI*

|  **Nome do parâmetro** |  **Obrigatório**  |  **Descrição**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   Verdadeiro         | Identificação do inquilino do pedido registado de Azure AD.   |
| | | |

#### <a name="request-header"></a>*Cabeçalho de pedido*

|  **Nome do cabeçalho**    |  **Obrigatório**  |  **Descrição**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   Verdadeiro         | Tipo de conteúdo associado ao pedido. O valor predefinido é `application/x-www-form-urlencoded`.  |
| | | |

#### <a name="request-body"></a>*Corpo de pedido*

|  **Nome da propriedade**  |  **Obrigatório**  |  **Descrição**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   Verdadeiro         | Tipo grant. Utilize `client_credentials`. |
|  `Client_id`        |   Verdadeiro         | Identificador de cliente/aplicação associado à aplicação Azure AD.|
|  `client_secret`    |   Verdadeiro         | Segredo associado à aplicação AZure AD.  |
|  `Resource`         |   Verdadeiro         | Recurso-alvo para o qual o símbolo é solicitado. Utilize `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`. |
| | | |

#### <a name="response"></a>*Response*

|  **Nome**    |  **Tipo**  |  **Descrição**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | Pedido bem sucedido.  |
| | | |

#### <a name="tokenresponse"></a>*TokenResponse*

Ficha de resposta da amostra:

```JSON
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

## <a name="using-the-azure-managed-identities-token"></a>Usando o símbolo de identidades geridas pelo Azure

O tipo de oferta aplicável é aplicações Azure com tipo de plano de aplicação gerido.

A utilização desta abordagem permitirá que a identidade dos recursos implantados autentica para enviar eventos de utilização de contadores personalizados.  Pode incorporar o código que emite o uso dentro dos limites da sua implantação.

>[!Note]
>O editor deve garantir que os recursos que emitem o uso estão bloqueados, para que não seja adulterado.

A sua aplicação gerida pode conter diferentes tipos de recursos, desde Máquinas Virtuais a Funções Azure.  Para obter mais informações sobre como autenticar usando identidades geridas para diferentes serviços, consulte [como utilizar identidades geridas para recursos Azure).](../../active-directory/managed-identities-azure-resources/overview.md#how-can-i-use-managed-identities-for-azure-resources)

Por exemplo, siga os passos abaixo para autenticar usando um VM do Windows,

1. Certifique-se de que a Identidade Gerida está configurada utilizando um dos métodos:
    * [Azure portal UI](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
    * [CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
    * [PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
    * [Modelo do Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
    * [REST](../../active-directory/managed-identities-azure-resources/qs-configure-rest-vm.md#system-assigned-managed-identity))
    * [SDKs do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

1. Obtenha um token de acesso para o ID da aplicação de medição do Marketplace ( `20e940b3-4c77-4b0b-9a53-9e16a1b010a7` ) usando a identidade do sistema, RDP para o VM, abra a consola PowerShell e execute o comando abaixo

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. Obtenha o ID da aplicação gerida a partir da propriedade 'ManagedBy' dos grupos de recursos atuais

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 

    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. O serviço de medição do mercado requer que se reporte o uso de uma `resourceID` aplicação gerida `resourceUsageId` e se for gerida.

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "/providers/Microsoft.Solutions/applications/" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. Utilize o [serviço de medição marketplace API](./marketplace-metering-service-apis.md) para emitir o uso.

## <a name="next-steps"></a>Passos seguintes

* [Criar uma oferta de aplicações do Azure](../create-new-azure-apps-offer.md)
* [Planear uma oferta SaaS](../plan-saas-offer.md)
