---
title: Pré-requisitos de API | Azure Marketplace
description: Pré-requisitos partir usando as APIs de Portal do Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: d3c2d89d3c3561e86047529e5b284e4481fc1652
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819692"
---
<a name="api-prerequisites"></a>Pré-requisitos de API
================

Há dois ativos programáticos exigidos que você precisa para usar as APIs de Portal do Cloud Partner: uma entidade de serviço e um token de acesso do Azure Active Directory (Azure AD).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Criar uma entidade de serviço em seu locatário de Azure Active Directory
----------------------------------------------------------------

Primeiro, você precisa criar uma entidade de serviço em seu locatário do Azure AD. Esse locatário será atribuído a seu próprio conjunto de permissões no Portal do Cloud Partner. Seu código chamará APIs usando como esse locatário em vez de usar suas credenciais pessoais.  Para obter uma explicação completa da criação de uma entidade de serviço, consulte [usar o portal para criar um aplicativo Azure Active Directory e uma entidade de serviço que possa acessar recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).


<a name="add-the-service-principal-to-your-account"></a>Adicionar a entidade de serviço à sua conta
-----------------------------------------

Agora que você criou a entidade de serviço em seu locatário, você pode adicioná-la como um usuário à sua conta de Portal do Cloud Partner. Assim como um usuário, a entidade de serviço pode ser um proprietário ou um colaborador para o Portal.

Use as etapas a seguir para adicionar a entidade de serviço:

1. Faça logon no Portal do Cloud Partner. 
2. Clique em **usuários** na barra de menus à esquerda e escolha **Adicionar usuário**.

   ![Adicionar um usuário ao portal](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. Na lista suspensa **tipo** , selecione **entidade de serviço** e adicione os seguintes detalhes:

-   Um **nome amigável** para a entidade de serviço, por exemplo `spAccount`.
-   A **ID do aplicativo**. Para localizar esse identificador, vá para o [portal do Azure](https://portal.azure.com), clique em **Azure Active Directory**, escolha **registros de aplicativo**e clique em seu aplicativo.
-   A **ID do locatário**, também conhecida como a **ID do diretório**, para seu locatário do Azure AD. Você pode encontrar esse identificador na página Azure Active Directory no [portal do Azure](https://portal.azure.com), em **Propriedades**.
-   A **ID de objeto** para o objeto de entidade de serviço. Você pode obter esse identificador no portal do Azure. Vá para **Azure Active Directory**, escolha **registros de aplicativo**, clique em seu aplicativo e clique no nome do aplicativo em **aplicativo gerenciado no diretório local**. Em seguida, vá para a página **Propriedades** para localizar a ID do objeto. Verifique se você não está pegando a ID de objeto inicial que está em seu aplicativo, mas em vez disso, a ID de objeto no aplicativo gerenciado.
-   A **função** associada à conta, que será usada para o RBAC.

     ![Adicionar um aplicativo gerenciado ao portal](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Clique em **Adicionar** para adicionar a entidade de serviço à sua conta.

   ![Adicionar uma entidade de serviço](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Obter um token de acesso do AD do Azure
----------------------------

As APIs de Portal do Cloud Partner usam os seguintes ativos e protocolos durante a autenticação:

- Um token de portador JWT (token Web JSON) para solicitar acesso aos recursos
- O protocolo [OpenID Connect](https://openid.net/connect/) (OIDC) para verificar a identidade
- [Azure Active Directory (AD do Azure)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) como a autoridade de identidade

Há duas abordagens principais para adquirir programaticamente um token JWT:

- Use a[MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)(biblioteca de autenticação da Microsoft para .net).  Essa abordagem de nível superior é recomendada para desenvolvedores do .NET. 
- Faça uma solicitação **http post** para o ponto de extremidade do **token** OAuth do Azure AD, que assume o formato:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Agora, você pode passar esse token como parte do cabeçalho de autorização para solicitações de API.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Para todas as APIs nessa referência, o cabeçalho de autorização sempre é passado, portanto, não mencionado explicitamente.

Se você encontrar erros de autenticação em sua solicitação, consulte [Solucionando problemas de erros de autenticação](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
