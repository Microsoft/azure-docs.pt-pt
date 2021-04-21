---
title: Backend Secure API Management usando a autenticação de certificado de cliente
titleSuffix: Azure API Management
description: Saiba como gerir certificados de cliente e garantir serviços de backend utilizando a autenticação de certificado de cliente na Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9d198f34abe826af2dce0c1ed9950600d4c7bbd3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812305"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>Serviços de backend seguros utilizando a autenticação de certificado de cliente na Azure API Management

A API Management permite-lhe garantir o acesso ao serviço de backend de uma API utilizando certificados de cliente. Este guia mostra como gerir certificados numa instância de serviço da Azure API Management utilizando o portal Azure. Também explica como configurar uma API para usar um certificado para aceder a um serviço de backend.

Também pode gerir certificados de Gestão de API utilizando a [API Management REST.](/rest/api/apimanagement/2020-06-01-preview/certificate)

## <a name="certificate-options"></a>Opções de certificado

A API Management oferece duas opções para gerir certificados utilizados para garantir o acesso aos serviços de backend:

* Referência de um certificado gerido no [Cofre chave Azure](../key-vault/general/overview.md) 
* Adicione um arquivo de certificado diretamente na Gestão da API

A utilização de certificados de cofre chave é recomendada porque ajuda a melhorar a segurança da API Management:

* Os certificados armazenados em cofres-chave podem ser reutilizados em todos os serviços
* As [políticas](../key-vault/general/security-features.md#privileged-access) de acesso granular podem ser aplicadas a certificados armazenados em cofres-chave
* Os certificados atualizados no cofre chave são automaticamente rodados na Gestão da API. Após atualização no cofre chave, um certificado na API Management é atualizado dentro de 4 horas. Também pode atualizar manualmente o certificado utilizando o portal Azure ou através da gestão REST API.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Se ainda não criou uma instância de serviço de Gestão API, consulte [Criar uma instância de serviço de Gestão API.][Create an API Management service instance]
* Deverá ter o seu serviço de backend configurado para autenticação de certificado de cliente. Para configurar a autenticação do certificado no Serviço de Aplicações Azure, consulte [este artigo][to configure certificate authentication in Azure WebSites refer to this article]. 
* Precisa de ter acesso ao certificado e à senha de gestão num cofre de chaves Azure ou fazer upload para o serviço de Gestão da API. O certificado deve estar no formato **PFX.** Certificados auto-assinados são permitidos.

### <a name="prerequisites-for-key-vault-integration"></a>Pré-requisitos para a integração do cofre-chave

1. Para obter passos para criar um cofre de chaves, consulte [Quickstart: Crie um cofre de chaves utilizando o portal Azure](../key-vault/general/quick-create-portal.md).
1. Ativar uma [identidade gerida](api-management-howto-use-managed-service-identity.md) atribuída pelo sistema ou atribuída ao utilizador no caso de Gestão da API.
1. Atribua uma [política de acesso](../key-vault/general/assign-access-policy-portal.md) ao cofre chave à identidade gerida com permissões para obter e listar certificados do cofre. Para adicionar a política:
    1. No portal, navegue para o cofre da chave.
    1. Selecione **Definições > Políticas de acesso > + Adicionar Política de Acesso**.
    1. Selecione **permissões de Certificado** e, em seguida, selecione **Get** and **List**.
    1. Em **Select principal,** selecione o nome do recurso da sua identidade gerida. Se estiver a usar uma identidade atribuída ao sistema, o principal é o nome da sua instância de Gestão de API.
1. Criar ou importar um certificado para o cofre da chave. Consulte [Quickstart: set and reconfesso de um certificado do Azure Key Vault utilizando o portal Azure](../key-vault/certificates/quick-create-portal.md).

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>Adicione um certificado de cofre chave

Consulte [os pré-requisitos para a integração do cofre de chaves](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> Ao utilizar um certificado de cofre chave na API Management, tenha cuidado para não apagar o certificado, o cofre ou a identidade gerida utilizada para aceder ao cofre da chave.

Para adicionar um certificado de cofre chave à API Management:

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Gestão API.
1. Em **Segurança**, selecione **Certificados.**
1. Selecione **Certificados**  >  **+ Adicionar**.
1. Em **Id,** insira um nome à sua escolha.
1. No **Certificado**, selecione **Key vault**.
1. Introduza o identificador de um certificado de cofre de chave ou escolha **Selecione** para selecionar um certificado de um cofre de chaves.
    > [!IMPORTANT]
    > Se introduzir um identificador de certificado de cofre de chave, certifique-se de que não tem informações sobre a versão. Caso contrário, o certificado não rodará automaticamente na Gestão da API após uma atualização no cofre da chave.
1. Na **identidade do Cliente,** selecione uma identidade gerida atribuída pelo sistema ou uma identidade gerida atribuída pelo utilizador. Saiba como [adicionar ou modificar identidades geridas no seu serviço de Gestão API.](api-management-howto-use-managed-service-identity.md)
    > [!NOTE]
    > A identidade precisa de permissões para obter e listar o certificado do cofre da chave. Se ainda não configurar o acesso ao cofre da chave, a API Management solicita-lhe que possa configurar automaticamente a identidade com as permissões necessárias.
1. Selecione **Adicionar**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="Adicionar certificado de cofre chave":::

## <a name="upload-a-certificate"></a>Faça upload de um certificado

Para fazer o upload de um certificado de cliente para a API Management: 

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Gestão API.
1. Em **Segurança**, selecione **Certificados.**
1. Selecione **Certificados**  >  **+ Adicionar**.
1. Em **Id,** insira um nome à sua escolha.
1. No **Certificado,** selecione **Custom**.
1. Navegue para selecionar o ficheiro certificado .pfx e introduza a sua palavra-passe.
1. Selecione **Adicionar**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="Faça upload do certificado de cliente":::

Depois de o certificado ser carregado, aparece na janela **certificados.** Se tiver muitos certificados, tome nota da impressão digital do certificado pretendido de forma a configurar uma API para utilizar um certificado de cliente para [autenticação gateway](#configure-an-api-to-use-client-certificate-for-gateway-authentication).

> [!NOTE]
> Para desativar a validação da cadeia de certificados quando utilizar, por exemplo, um certificado auto-assinado, siga os passos descritos nos [certificados auto-assinados](#self-signed-certificates), mais tarde neste artigo.

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>Configurar uma API para utilizar o certificado de cliente para a autenticação gateway

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Gestão API.
1. Em **APIs**, selecione **APIs**.
1. Selecione uma API da lista. 
2. No **separador Design,** selecione o ícone do editor na secção **Backend.**
3. Nas **credenciais Gateway,** selecione **o Certificado do Cliente** e selecione o seu certificado a partir do dropdown.
1. Selecione **Guardar**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="Use o certificado de cliente para autenticação gateway":::

> [!CAUTION]
> Esta alteração tem efeito imediato e as chamadas para operações dessa API utilizarão o certificado para autenticar no servidor backend.

> [!TIP]
> Quando um certificado é especificado para autenticação gateway para o serviço de backend de uma API, torna-se parte da política para essa API, e pode ser visto no editor de política.

## <a name="self-signed-certificates"></a>Certificados auto-assinados

Se estiver a utilizar certificados auto-assinados, terá de desativar a validação da cadeia de certificados para a API Management para comunicar com o sistema de backend. Caso contrário, devolverá um código de erro de 500. Para configurar isto, pode utilizar os [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) cmdlets PowerShell (para novo backend) ou [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (para backend existente) e definir o `-SkipCertificateChainValidation` parâmetro para `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>Excluir um certificado de cliente

Para eliminar um certificado, selecione-o e, em seguida, **selecione Eliminar** do menu de contexto **(...**).

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="Apagar um certificado":::

> [!IMPORTANT]
> Se o certificado for referenciado por quaisquer políticas, é visualizado um ecrã de aviso. Para eliminar o certificado, deve primeiro retirar o certificado de quaisquer políticas configuradas para a sua utilização.

## <a name="next-steps"></a>Passos seguintes

* [Como proteger APIs com a autenticação de certificado de cliente na Gestão de API](api-management-howto-mutual-certificates-for-clients.md)
* Conheça as [políticas em Gestão de API](api-management-howto-policies.md)


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

