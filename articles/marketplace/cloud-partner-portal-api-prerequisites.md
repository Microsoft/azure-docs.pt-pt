---
title: Pré-requisitos da API - Azure Marketplace
description: Pré-requisitos para a utilização das APIs do Portal do Parceiro cloud.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: c932db1b86cecae4d90b0d6acffc4a0cde49e369
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86115745"
---
<a name="api-prerequisites"></a>Pré-requisitos da API
================

> [!NOTE]
> As APIs do Portal do Parceiro cloud estão integradas no Partner Center e continuarão a funcionar após as suas ofertas serem migradas para o Partner Center. A integração introduz pequenas mudanças. Reveja as alterações listadas no [Cloud Partner Portal API Reference](./cloud-partner-portal-api-overview.md) para garantir que o seu código continua a funcionar após a migração para o Partner Center.

Existem dois ativos programáticos necessários para utilizar o Portal do Parceiro de Nuvem APIs: um diretor de serviço e um token de acesso Azure Ative (Azure AD).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Crie um diretor de serviço no seu inquilino Azure Ative Directory
----------------------------------------------------------------

Primeiro, você precisa criar um diretor de serviço no seu inquilino AZure AD. Este inquilino será atribuído o seu próprio conjunto de permissões no Portal do Parceiro Cloud. O seu código chamará APIs usando como inquilino em vez de usar as suas credenciais pessoais.  Para obter uma explicação completa da criação de um principal de serviço, consulte [Como: Use o portal para criar uma aplicação AD AD Azure que possa aceder aos recursos.](../active-directory/develop/howto-create-service-principal-portal.md)


<a name="add-the-service-principal-to-your-account"></a>Adicione o principal de serviço à sua conta
-----------------------------------------

Agora que criou o principal de serviço no seu inquilino, pode adicioná-lo como utilizador à sua conta Cloud Partner Portal. Tal como um utilizador, o titular do serviço pode ser proprietário ou colaborador do portal.

Utilize os seguintes passos para adicionar o principal de serviço:

1. Inscreva-se no Portal do Parceiro cloud. 
2. Clique nos **Utilizadores** na barra de menus esquerda e escolha **Adicionar Utilizador**.

   ![Adicionar um utilizador ao portal](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. A partir do **dropdown tipo,** selecione **o principal de serviço** e adicione os seguintes detalhes:

-   Um **nome amigável** para o diretor de serviço, por `spAccount` exemplo.
-   O ID do formulário de **aplicação.** Para encontrar este identificador, aceda ao [portal Azure,](https://portal.azure.com)clique no **Azure Ative Directory,** escolha **as inscrições da App**e clique na sua aplicação.
-   O **ID do Inquilino**, também conhecido como ID do **Diretório,** para o seu inquilino AZure AD. Pode encontrar este identificador na página Azure Ative Directory no [portal Azure](https://portal.azure.com), em **Propriedades**.
-   O **ID do objeto** para o seu objeto principal de serviço. Pode obter este identificador do portal Azure. Vá ao **Azure Ative Directory**, escolha **registos de Aplicações,** clique na sua aplicação e clique no nome da aplicação sob **aplicação Gerida no diretório local.** Em seguida, vá à página **propriedades,** para encontrar o ID do objeto. Certifique-se de que não está a agarrar o ID inicial do Objeto que está na sua aplicação, mas sim o ID do Objeto na aplicação gerida.
-   O **Papel** associado à conta, que será usada para o RBAC.

     ![Adicione uma app gerida ao portal](./media/cloud-partner-portal-api-prerequisites/managed-app.png)

1. Clique **em Adicionar** para adicionar o principal do serviço à sua conta.

   ![Adicione um diretor de serviço](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Obter um token de acesso do Azure AD
----------------------------

As APIs do Portal do Parceiro cloud utilizam os seguintes ativos e protocolos durante a autenticação:

- Um símbolo do portador da JSON Web Token (JWT) para solicitar o acesso aos recursos
- O protocolo [OpenID Connect](https://openid.net/connect/) (OIDC) para verificar a identidade
- [Azure Ative Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) como autoridade de identidade

Existem duas abordagens principais para a aquisição programática de um símbolo JWT:

- Utilize a Biblioteca de Autenticação microsoft para .NET[(MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Esta abordagem de nível superior é recomendada para desenvolvedores .NET. 
- Faça um pedido **HTTP POST** ao ponto final de aauth **token** Azure AD, que toma o formulário:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Agora, você pode passar este token como parte do cabeçalho de autorização para pedidos de API.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Para todas as APIs nesta referência, o cabeçalho de autorização é sempre assumido como aprovado, pelo que não é mencionado explicitamente.

Se encontrar erros de autenticação no seu pedido, consulte [erros de autenticação de resolução de problemas](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
