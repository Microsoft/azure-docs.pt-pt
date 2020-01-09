---
title: Serviços de back-end seguros usando a autenticação de certificado do cliente
titleSuffix: Azure API Management
description: Saiba como proteger serviços de back-end usando a autenticação de certificado de cliente no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 70c1e22fc7f1fb1cda3fd4af1c2d3aa2cd257201
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442612"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Como proteger serviços de back-end usando a autenticação de certificado de cliente no gerenciamento de API do Azure

O gerenciamento de API permite que você proteja o acesso ao serviço de back-end de uma API usando certificados de cliente. Este guia mostra como gerenciar certificados na instância do serviço de gerenciamento de API do Azure no portal do Azure. Ele também explica como configurar uma API para usar um certificado para acessar um serviço de back-end.

Para obter informações sobre como gerenciar certificados usando a API REST de gerenciamento de API, consulte <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">entidade de certificado da API REST do gerenciamento de API do Azure</a>.

## <a name="prerequisites"> </a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este guia mostra como configurar sua instância de serviço de gerenciamento de API para usar a autenticação de certificado de cliente para acessar o serviço de back-end para uma API. Antes de seguir as etapas neste artigo, você deve ter seu serviço de back-end configurado para autenticação de certificado de cliente ([para configurar a autenticação de certificado nos sites do Azure, consulte este artigo][to configure certificate authentication in Azure WebSites refer to this article]). Você precisa ter acesso ao certificado e à senha para carregá-lo no serviço de gerenciamento de API.

## <a name="step1"> </a>Carregar um certificado

![Adicionar certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-new.png)

Siga as etapas abaixo para carregar um novo certificado de cliente. Se você ainda não criou uma instância de serviço de gerenciamento de API, consulte o tutorial [criar uma instância de serviço de gerenciamento de API][Create an API Management service instance].

1. Navegue até a instância do serviço de gerenciamento de API do Azure no portal do Azure.
2. Selecione **certificados** no menu.
3. Clique no botão **+ Adicionar**.  
    ![adicionar certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)  
4. Procure o certificado, forneça sua ID e senha.  
5. Clique em **Criar**.

> [!NOTE]
> O certificado deve estar no formato **. pfx** . Certificados autoassinados são permitidos.

Depois que o certificado for carregado, ele será exibido nos **certificados**.  Se você tiver muitos certificados, anote a impressão digital do certificado desejado para [Configurar uma API para usar um certificado de cliente para autenticação de gateway][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Para desativar a validação da cadeia de certificados ao usar, por exemplo, um certificado autoassinado, siga as etapas descritas neste [Item](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)de perguntas frequentes.

## <a name="step1a"> </a>Excluir um certificado de cliente

Para excluir um certificado, clique em menu de contexto **...** e selecione **excluir** ao lado do certificado.

![Excluir certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png)

Se o certificado estiver em uso por uma API, uma tela de aviso será exibida. Para excluir o certificado, primeiro você deve remover o certificado de todas as APIs que estão configuradas para usá-lo.

![Falha ao excluir certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="step2"> </a>Configurar uma API para usar um certificado de cliente para autenticação de gateway

1. Clique em **APIs** no menu **Gerenciamento de API** à esquerda e navegue até a API.  
    ![habilitar certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. Na guia **design** , clique em um ícone de lápis da seção de **back-end** . 
3. Altere as **credenciais de gateway** para **certificado de cliente** e selecione o certificado na lista suspensa.  
    ![habilitar certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Clique em **Guardar**. 

> [!WARNING]
> Essa alteração entra em vigor imediatamente e as chamadas para operações dessa API usarão o certificado para autenticar no servidor back-end.


> [!TIP]
> Quando um certificado é especificado para autenticação de gateway para o serviço de back-end de uma API, ele se torna parte da política para essa API e pode ser exibido no editor de políticas.

## <a name="self-signed-certificates"></a>Certificados autoassinados

Se você estiver usando certificados autoassinados, será necessário desabilitar a validação da cadeia de certificados para que o gerenciamento de API se comunique com o sistema de back-end. Caso contrário, ela retornará um código de erro 500. Para configurar isso, você pode usar os cmdlets do PowerShell [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) (para novo back-end) ou [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (para back-end existente) e definir o parâmetro `-SkipCertificateChainValidation` como `True`.

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: https://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
