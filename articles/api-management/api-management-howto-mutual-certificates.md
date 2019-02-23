---
title: Proteger serviços de back-end com o cliente de autenticação de certificado - gestão de API do Azure | Documentos da Microsoft
description: Saiba como proteger os serviços de back-end com a autenticação de certificado de cliente na gestão de API do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 13a2eb080c6822a8a6786be1952bc588fa8afd80
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729215"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Como proteger os serviços de back-end com o cliente de autenticação de certificado na gestão de API do Azure

Gestão de API permite proteger o acesso para o serviço de back-end de uma API com certificados de cliente. Este guia mostra como gerir certificados na instância de serviço da API Management do Azure no portal do Azure. Também explica como configurar uma API para utilizar um certificado para aceder a um serviço de back-end.

Para obter informações sobre como gerir certificados com a API de REST de gestão de API, consulte <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">entidade do certificado de API do REST de gestão do Azure API</a>.

## <a name="prerequisites"> </a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este guia mostra-lhe como configurar a sua instância do serviço de gestão de API para utilizar a autenticação de certificado de cliente para aceder ao serviço de back-end de uma API. Antes de seguir os passos neste artigo, deve ter o seu serviço de back-end configurado para autenticação de certificados de cliente ([para configurar a autenticação de certificados nos Web sites Azure, consulte a este artigo] [ to configure certificate authentication in Azure WebSites refer to this article]). Precisa de acesso para o certificado e a palavra-passe para carregá-lo para o serviço de gestão de API.

## <a name="step1"> </a>Carregar um certificado de cliente

![Adicionar certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert.png)

Siga os passos abaixo para carregar um novo certificado de cliente. Se ainda não criou uma instância de serviço de gestão de API, veja o tutorial [criar uma instância de serviço de gestão de API][Create an API Management service instance].

1. Navegue até à sua instância do serviço de gestão de API do Azure no portal do Azure.
2. Selecione **certificados de cliente** no menu.
3. Clique nas **+ adicionar** botão.  
    ![Adicionar certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)  
4. Procurar o certificado, forneça o ID e palavra-passe.  
5. Clique em **Criar**.

> [!NOTE]
> O certificado tem de constar **. pfx** formato. Certificados autoassinados são permitidos.

Depois do certificado é carregado, ele mostra os **certificados de cliente**.  Se tiver muitos certificados, anote o thumbprint do certificado pretendido para [configurar uma API para utilizar um certificado de cliente para autenticação de gateway][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Para desativar a validação da cadeia de certificado ao utilizar, por exemplo, um certificado autoassinado, siga os passos descritos nestas perguntas frequentes [item](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end).

## <a name="step1a"> </a>Eliminar um certificado de cliente

Para eliminar um certificado, clique no menu de contexto **...**  e selecione **eliminar** junto ao certificado.

![Eliminar certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-delete.png)

Se o certificado é utilizado por uma API, em seguida, é apresentado um ecrã de aviso. Para eliminar o certificado, primeiro tem de remover o certificado do quaisquer APIs que estão configurados para o utilizar.

![Eliminar a falha de certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="step2"> </a>Configurar uma API para utilizar um certificado de cliente para autenticação de gateway

1. Clique em **APIs** partir a **gestão de API** menu à esquerda e navegue para a API.  
    ![Ativar certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. Na **Design** separador, clique num ícone de lápis do **back-end** secção. 
3. Alteração da **credenciais do Gateway** para **certificado de cliente** e selecione o seu certificado na lista pendente.  
    ![Ativar certificados de cliente](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Clique em **Guardar**. 

> [!WARNING]
> Esta alteração tem efeita imediato e chamadas para operações de que a API utiliza o certificado para autenticar no servidor de back-end.


> [!TIP]
> Quando é especificado um certificado para a autenticação de gateway para o serviço de back-end de uma API, ele se torna parte da política para essa API e pode ser visualizado no editor de políticas.

## <a name="self-signed-certificates"></a>Certificados autoassinados

Se estiver a utilizar certificados autoassinados, terá de desativar a validação da cadeia de certificado para que a gestão de API comunicar com o sistema de back-end. Caso contrário, ela retornará um código de 500 Erro. Para configurar esta opção, pode utilizar o [ `New-AzApiManagementBackend` ](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) (para o novo back-end) ou [ `Set-AzApiManagementBackend` ](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (para o back-end existente) cmdlets do PowerShell e defina o `-SkipCertificateChainValidation` parâmetro `True`.

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
