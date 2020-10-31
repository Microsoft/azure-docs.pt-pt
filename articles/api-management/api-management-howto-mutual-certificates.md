---
title: Serviços de back-end seguros usando a autenticação de certificado de cliente
titleSuffix: Azure API Management
description: Saiba como garantir serviços de back-end utilizando a autenticação de certificado de cliente na Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 980d3ca52016c65301ea72e4e669c4bafea4c053
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077205"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Como proteger serviços de back-end com a autenticação de certificados de cliente na Gestão de API do Azure

A API Management permite-lhe garantir o acesso ao serviço back-end de uma API utilizando certificados de cliente. Este guia mostra como gerir certificados na instância de serviço da Azure API Management no portal Azure. Também explica como configurar uma API para usar um certificado para aceder a um serviço de back-end.

Para obter informações sobre a gestão de certificados utilizando a API Management REST API, consulte <a href="/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">a Azure API Management REST API Certificate entidade</a>.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este guia mostra-lhe como configurar a sua instância de serviço de Gestão API para utilizar a autenticação de certificado do cliente para aceder ao serviço back-end para uma API. Antes de seguir os passos deste artigo, deverá ter o seu serviço de back-end configurado para autenticação de certificado de cliente ( para configurar a[autenticação de certificado no Serviço de Aplicações Azure consulte este artigo).][to configure certificate authentication in Azure WebSites refer to this article] Precisa de ter acesso ao certificado e à senha para o enviar para o serviço de Gestão da API.

## <a name="upload-a-certificate"></a><a name="step1"> </a>Faça upload de um certificado

> [!NOTE]
> Em vez de um certificado carregado, pode utilizar um certificado armazenado no serviço [Azure Key Vault,](https://azure.microsoft.com/services/key-vault/) como mostrado neste [exemplo](https://github.com/galiniliev/api-management-policy-snippets/blob/galin/AkvCert/examples/Look%20up%20Key%20Vault%20certificate%20using%20Managed%20Service%20Identity%20and%20call%20backend.policy.xml).

![Adicionar certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-new.png)

Siga os passos abaixo para fazer o upload de um novo certificado de cliente. Se ainda não criou uma instância de serviço de Gestão API, consulte o tutorial [Criar uma instância de serviço de Gestão API.][Create an API Management service instance]

1. Navegue para a sua instância de serviço de Gestão AZure no portal Azure.
2. Selecione **Certificados** do menu.
3. Clique no botão **+ Adicionar** .
    ![Screenshot que realça o botão + Adicionar.](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)
4. Procure o certificado, forneça o seu ID e senha.
5. Clique em **Criar** .

> [!NOTE]
> O certificado deve estar em formato **.pfx.** Certificados auto-assinados são permitidos.

Uma vez que o certificado é carregado, ele aparece nos **Certificados** .  Se tiver muitos certificados, tome nota da impressão digital do certificado pretendido para [configurar uma API para utilizar um certificado de cliente para autenticação gateway][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Para desativar a validação da cadeia de certificados quando utilizar, por exemplo, um certificado auto-assinado, siga os passos descritos neste [artigo](api-management-faq.md#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)de FAQ .

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Excluir um certificado de cliente

Para eliminar um certificado, clique no menu de **contexto...** e **selecione Eliminar** ao lado do certificado.

![Apagar certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png)

Se o certificado for utilizado por uma API, é apresentado um ecrã de aviso. Para eliminar o certificado, deve primeiro retirar o certificado de quaisquer APIs que estejam configurados para o utilizar.

![Eliminar falha nos certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="configure-an-api-to-use-a-client-certificate-for-gateway-authentication"></a><a name="step2"> </a>Configurar uma API para utilizar um certificado de cliente para autenticação gateway

1. Clique **em APIs** a partir do menu **API Management** à esquerda e navegue para a API.
    ![Ativar certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. No **separador Design,** clique num ícone de lápis da secção **Backend.**
3. Altere as **credenciais Gateway** para **o Certificado do Cliente** e selecione o seu certificado a partir do dropdown.
    ![Screenshot que mostra onde alterar as credenciais de gateway e selecione o seu certificado.](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Clique em **Guardar** .

> [!WARNING]
> Esta alteração é eficaz imediatamente, e as chamadas para operações dessa API usarão o certificado para autenticar no servidor back-end.


> [!TIP]
> Quando um certificado é especificado para autenticação gateway para o serviço back-end de uma API, torna-se parte da política para essa API, e pode ser visto no editor de política.

## <a name="self-signed-certificates"></a>Certificados auto-assinados

Se estiver a utilizar certificados auto-assinados, terá de desativar a validação da cadeia de certificados para que a API Management comunique com o sistema de backend. Caso contrário, devolverá um código de erro de 500. Para configurar isto, pode utilizar os [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) cmdletes PowerShell (para a parte de trás) ou [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (para a parte traseira existente) e definir o `-SkipCertificateChainValidation` parâmetro para `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: ./api-management-caching-policies.md
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps