---
title: Estratégias de autenticação do serviço de medição do mercado Mercado Azure
description: Estratégias de autenticação de serviços de medição suportadas no Mercado Azure.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/13/2020
ms.openlocfilehash: 4b3a2ed71845b8848c9cb0ac5002e0c69a170410
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83642319"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Estratégias de autenticação do serviço de medição do mercado

O serviço de medição do mercado suporta duas estratégias de autenticação:

* [Ficha de segurança da AD Azure](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
* [identidades geridas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 

Explicaremos quando e como usar as diferentes estratégias de autenticação para submeter seguramente contadores personalizados usando o serviço de medição do Marketplace.

## <a name="using-the-azure-ad-security-token"></a>Usando o símbolo de segurança da AD Azure

Os tipos de oferta aplicáveis são aplicações SaaS e Azure com tipo de plano de aplicação gerido.  

Submeta contadores personalizados utilizando um ID de aplicação fixa predefinido para autenticar.

Para ofertas SaaS, o Azure AD é a única opção disponível.

Para aplicações Azure com plano de aplicação gerido, deve considerar a utilização desta estratégia nos seguintes casos:

* Já tem um mecanismo de comunicação com os seus serviços de backend, e pretende alargar este mecanismo para emitir contadores personalizados a partir de um serviço central.
* Tem uma lógica complexa de metros personalizados.  Execute esta lógica num local central, em vez dos recursos de aplicação geridos.

Uma vez registado o seu pedido, pode solicitar programáticamente um sinal de segurança da AD Azure. Espera-se que a editora use este símbolo e faça um pedido para resolvê-lo.

Para obter mais informações sobre estas fichas, consulte os tokens de acesso ao [Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Obtenha um símbolo com base na aplicação Azure AD

#### <a name="http-method"></a>Método HTTP

**Publicar**

#### <a name="request-url"></a>*URL do Pedido*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*Parâmetro URI*

|  **Parameter name** (Nome do parâmetro) |  **Necessário**  |  **Descrição**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   Verdadeiro         | Identificação do inquilino do pedido de Anúncio Azure registado.   |
| | | |

#### <a name="request-header"></a>*Cabeçalho de pedido*

|  **Nome do cabeçalho**    |  **Necessário**  |  **Descrição**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   Verdadeiro         | Tipo de conteúdo associado ao pedido. O valor predefinido é `application/x-www-form-urlencoded`.  |
| | | |

#### <a name="request-body"></a>*Solicitar corpo*

|  **Nome de propriedade**  |  **Necessário**  |  **Descrição**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   Verdadeiro         | Tipo de concessão. O valor predefinido é `client_credentials`. |
|  `Client_id`        |   Verdadeiro         | Identificador cliente/app associado à aplicação Azure AD.|
|  `client_secret`    |   Verdadeiro         | A palavra-passe associada à aplicação Azure AD.  |
|  `Resource`         |   Verdadeiro         | Recurso-alvo para o qual o símbolo é solicitado. O valor predefinido é `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`.  |
| | | |

#### <a name="response"></a>*Resposta*

|  **Nome**    |  **Tipo**  |  **Descrição**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | O pedido foi bem sucedido.  |
| | | |

#### <a name="tokenresponse"></a>*Resposta token*

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

A utilização desta abordagem permitirá que a identidade dos recursos implantados se atentuque para enviar eventos de utilização de contadores personalizados.  Pode incorporar o código que emite o uso dentro dos limites da sua implantação.

>[!Note]
>A editora deve assegurar que os recursos que emitem utilização estão bloqueados, para que não seja adulterado.

A sua aplicação gerida pode conter diferentes tipos de recursos, desde Máquinas Virtuais até Funções Azure.  Para obter mais informações sobre como autenticar usando identidades geridas para diferentes serviços, consulte [como utilizar identidades geridas para recursos Azure.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-can-i-use-managed-identities-for-azure-resources)

Por exemplo, siga os passos abaixo para autenticar usando um VM do Windows,

1. Certifique-se de que a Identidade Gerida está configurada utilizando um dos métodos:
    * [Azure portal UI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)
    * [CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm)
    * [PowerShell](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm)
    * [Modelo de gestor de recursos azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm)
    * [REST](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-rest-vm#system-assigned-managed-identity)
    * [SDKs do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm)

1. Obtenha um sinal de acesso para id de aplicação de serviço de medição do Marketplace ( `20e940b3-4c77-4b0b-9a53-9e16a1b010a7` ) utilizando a identidade do sistema, RDP para o VM, abra a consola PowerShell e execute o comando abaixo

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. Obtenha o ID da aplicação gerida da propriedade 'ManagedBy' dos atuais grupos de recursos

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 
    
    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. O serviço de medição do mercado requer reportar a utilização numa `resourceID` , e se uma `resourceUsageId` aplicação gerida.

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "/providers/Microsoft.Solutions/applications/" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. Utilize o serviço de [medição marketplace API](./marketplace-metering-service-apis.md) para emitir o uso.

## <a name="next-steps"></a>Passos seguintes

* [Criar uma oferta de aplicações do Azure](./create-new-azure-apps-offer.md)