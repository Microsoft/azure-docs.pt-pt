---
title: Pré-requisitos da API Mercado Azure
description: Pré-requisitos para cá utilizando as APIs do Portal do Parceiro cloud.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 2a1022c6d041bf645b43dfed391a489de30b2fce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288568"
---
<a name="api-prerequisites"></a>Pré-requisitos da API
================

Existem dois ativos programáticos necessários que você precisa para usar o Portal de Parceiros cloud APIs: um diretor de serviço e um diretório Ativo Azure (Azure AD) de acesso.


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Crie um diretor de serviço no seu inquilino de Diretório Ativo Azure
----------------------------------------------------------------

Primeiro, você precisa criar um diretor de serviço no seu inquilino Azure AD. Este inquilino será designado o seu próprio conjunto de permissões no Portal do Parceiro cloud. O seu código vai chamar APIs usando como este inquilino em vez de usar as suas credenciais pessoais.  Para uma explicação completa da criação de um diretor de serviço, consulte [o portal Use para criar uma aplicação e diretor de serviço Sonárito Ativo Azure que possa aceder a recursos.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)


<a name="add-the-service-principal-to-your-account"></a>Adicione o principal de serviço à sua conta
-----------------------------------------

Agora que criou o diretor de serviço no seu inquilino, pode adicioná-lo como utilizador à sua conta Cloud Partner Portal. Tal como um utilizador, o diretor de serviço pode ser proprietário ou colaborador do portal.

Utilize os seguintes passos para adicionar o diretor de serviço:

1. Inscreva-se no Portal do Parceiro da Nuvem. 
2. Clique nos **Utilizadores** na barra de menus esquerda e escolha **Adicionar utilizador**.

   ![Adicione um utilizador ao portal](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. A partir do **dropdown do Tipo,** selecione **Diretor de Serviço** e adicione os seguintes detalhes:

-   Um **Nome Amigável** para o `spAccount`diretor de serviço, por exemplo.
-   O ID da **aplicação**. Para encontrar este identificador, vá ao [Portal Azure,](https://portal.azure.com)clique no **Diretório Ativo do Azure,** escolha **as inscrições**da App e clique na sua aplicação.
-   O **ID do Inquilino**, também conhecido como Id do **Diretório,** para o seu inquilino Azure AD. Pode encontrar este identificador na página de Diretório Ativo Azure no [portal Azure,](https://portal.azure.com)no âmbito **do Properties**.
-   O **ID do objeto** para o seu objeto principal de serviço. Pode obter este identificador do portal Azure. Vá ao **Azure Ative Directory**, escolha **as inscrições**da App, clique na sua aplicação e clique no nome da aplicação sob **aplicação gerida no diretório local**. Em seguida, vá à página **Propriedades,** para encontrar o ID do Objeto. Certifique-se de que não está a agarrar o ID inicial do Objeto que está na sua aplicação, mas sim o ID do Objeto na aplicação gerida.
-   O **Papel** associado à conta, que será utilizado para o RBAC.

     ![Adicione uma aplicação gerida ao portal](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Clique em **Adicionar** o principal de serviço à sua conta.

   ![Adicione um diretor de serviço](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Obter um token de acesso do Azure AD
----------------------------

O Portal do Parceiro cloud APIs utiliza os seguintes ativos e protocolos durante a autenticação:

- Um token portador da JSON Web Token (JWT) para solicitar acesso aos recursos
- O protocolo [OpenID Connect](https://openid.net/connect/) (OIDC) para verificar a identidade
- [Azure Ative Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) como autoridade identitária

Existem duas abordagens fundamentais para adquirir programáticamente um símbolo JWT:

- Utilize a Biblioteca de Autenticação da Microsoft para .NET[(MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Esta abordagem de nível mais elevado é recomendada para os desenvolvedores .NET. 
- Faça um pedido **HTTP POST** ao ponto final do adauth **aauth** Aauth, que assume o formulário:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Agora, pode passar este símbolo como parte do cabeçalho de autorização para pedidos de API.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Para todas as APIs nesta referência, o cabeçalho de autorização é sempre assumido passado, pelo que não é explicitamente mencionado.

Se tiver erros de autenticação no seu pedido, consulte erros de autenticação de [resolução de problemas](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
