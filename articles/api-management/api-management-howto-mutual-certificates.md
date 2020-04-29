---
title: Serviços de back-end seguros usando autenticação de certificado de cliente
titleSuffix: Azure API Management
description: Saiba como garantir serviços back-end utilizando a autenticação de certificado de cliente na Azure API Management.
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
ms.openlocfilehash: b0ddf6dda99ee666e3052b5a70e51c7e4208a374
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80347095"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Como proteger serviços de back-end com a autenticação de certificado de cliente na Gestão de API do Azure

A API Management permite-lhe garantir o acesso ao serviço back-end de uma API utilizando certificados de cliente. Este guia mostra como gerir certificados na instância de serviço de Gestão API Azure no portal Azure. Também explica como configurar uma API para usar um certificado para aceder a um serviço de back-end.

Para obter informações sobre a gestão de certificados utilizando a API Management REST API, consulte a entidade de certificado sarna <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">REST API</a>management Azure Management .

## <a name="prerequisites"></a><a name="prerequisites"> </a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este guia mostra-lhe como configurar a sua instância de serviço de Gestão API para utilizar a autenticação do certificado de cliente para aceder ao serviço back-end para uma API. Antes de seguir os passos deste artigo, deverá ter o seu serviço back-end configurado para autenticação de certificado de cliente (para configurar a autenticação de certificado no Serviço de[Aplicações Azure consulte este artigo).][to configure certificate authentication in Azure WebSites refer to this article] Precisa de acesso ao certificado e à palavra-passe para o enviar para o serviço de Gestão API.

## <a name="upload-a-certificate"></a><a name="step1"> </a>Faça upload de um Certificado

> [!NOTE]
> Em vez de um certificado carregado, pode utilizar um certificado armazenado no serviço [Azure Key Vault,](https://azure.microsoft.com/services/key-vault/) como mostra este [exemplo](https://github.com/galiniliev/api-management-policy-snippets/blob/galin/AkvCert/examples/Look%20up%20Key%20Vault%20certificate%20using%20Managed%20Service%20Identity%20and%20call%20backend.policy.xml).

![Adicionar certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-new.png)

Siga os passos abaixo para fazer o upload de um novo certificado de cliente. Se ainda não criou uma instância de serviço de Gestão API, consulte o tutorial Criar uma instância de serviço de [Gestão API][Create an API Management service instance].

1. Navegue para a sua instância de serviço azure API Management no portal Azure.
2. Selecione **Certificados** do menu.
3. Clique no botão **+ Adicionar**.
    ![Adicionar certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)
4. Procure o certificado, forneça o seu ID e senha.
5. Clique em **Criar**.

> [!NOTE]
> O certificado deve estar em formato **.pfx.** São permitidos certificados auto-assinados.

Uma vez que o certificado é carregado, mostra nos **Certificados**.  Se tiver muitos certificados, tome nota da impressão digital do certificado pretendido para [configurar uma API para utilizar um certificado][Configure an API to use a client certificate for gateway authentication]de cliente para autenticação gateway .

> [!NOTE]
> Para desativar a validação da cadeia de certificados ao utilizar, por exemplo, um certificado auto-assinado, siga os passos descritos neste [item](api-management-faq.md#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)de FAQ .

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Eliminar um certificado de cliente

Para eliminar um certificado, clique no menu de **contexto...** e **selecione Excluir** ao lado do certificado.

![Eliminar certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png)

Se o certificado estiver a ser utilizado por uma API, então é apresentado um ecrã de aviso. Para eliminar o certificado, tem primeiro de remover o certificado de quaisquer APIs que estejam configuradas para o utilizar.

![Eliminar falha nos certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="configure-an-api-to-use-a-client-certificate-for-gateway-authentication"></a><a name="step2"> </a>Configure uma API para usar um certificado de cliente para autenticação gateway

1. Clique em **APIs** a partir do menu **de Gestão API** à esquerda e navegue para a API.
    ![Ativar certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. No separador **Design,** clique num ícone de lápis da secção **Backend.**
3. Altere as **credenciais gateway** para **Cliente cert** e selecione o seu certificado a partir do dropdown.
    ![Ativar certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Clique em **Guardar**.

> [!WARNING]
> Esta alteração entra em vigor imediatamente e as chamadas para operações dessa API usarão o certificado para autenticar no servidor de back-end.


> [!TIP]
> Quando um certificado é especificado para autenticação gateway para o serviço back-end de uma API, torna-se parte da política para essa API, e pode ser visto no editor de política.

## <a name="self-signed-certificates"></a>Certificados auto-assinados

Se estiver a utilizar certificados auto-assinados, terá de desativar a validação da cadeia de certificados para que a API Management comunique com o sistema backend. Caso contrário, devolverá um código de erro de 500. Para configurar isto, pode [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) utilizar os cmdlets PowerShell (para nova extremidade traseira) [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) `-SkipCertificateChainValidation` ou (para a extremidade traseira existente) e definir o parâmetro para `True`.

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
