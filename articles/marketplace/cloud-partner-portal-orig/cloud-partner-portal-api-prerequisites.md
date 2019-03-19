---
title: Pré-requisitos de API | Documentos da Microsoft
description: Pré-requisitos para utilizar as APIs de Portal de parceiro de Cloud.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 0d743d89c344ce9cb6eec116ed9c3e0cbe56f86e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57878652"
---
<a name="api-prerequisites"></a>Pré-requisitos de API
================

Existem dois recursos programáticos necessários que tem de utilizar as APIs de Portal de parceiro de Cloud: token de acesso de um principal de serviço e um Azure Active Directory (Azure AD).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Criar um principal de serviço no seu inquilino do Azure Active Directory
----------------------------------------------------------------

Em primeiro lugar, terá de criar um principal de serviço no inquilino do Azure AD. Este inquilino será atribuído a seu próprio conjunto de permissões no Portal de parceiros de nuvem. Seu código chamará o uso de APIs como este inquilino em vez de utilizar as suas credenciais pessoais.  Para obter uma explicação completa de criação de um principal de serviço, consulte [utilize o portal para criar um Azure Active Directory principal de aplicações e serviço que pode aceder a recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).


<a name="add-the-service-principal-to-your-account"></a>Adicionar o principal de serviço à sua conta
-----------------------------------------

Agora que criou o serviço principal no seu inquilino, pode adicioná-lo como um utilizador à sua conta do Portal de parceiros da Cloud. Tal como um utilizador, o principal de serviço pode ser um proprietário ou Contribuidor para o portal.

Utilize os seguintes passos para adicionar o principal de serviço:

1. Início de sessão para o Portal de parceiros da Cloud. 
2. Clique em **usuários** na barra de menu à esquerda e escolha **adicionar utilizador**.

   ![Adicionar um utilizador para o portal](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. Partir do **tipo** menu pendente, selecione **Principal de serviço** e adicione os seguintes detalhes:

-   R **nome amigável** principal de serviço, por exemplo `spAccount`.
-   O **ID da aplicação**. Para localizar este identificador, vá para o [Portal do Azure](https://portal.azure.com), clique em **Azure Active Directory**, escolha **registos das aplicações**e clique na sua aplicação.
-   O **ID do inquilino**, também conhecida como a **ID do diretório**, para o seu inquilino do Azure AD. Pode encontrar este identificador na página do Azure Active Directory no [portal do Azure](https://portal.azure.com), em **propriedades**.
-   O **ID de objeto** para o objeto principal de serviço. Pode obter este identificador no portal do Azure. Aceda a **do Azure Active Directory**, escolha **registos das aplicações**, clique na sua aplicação e clique no nome da aplicação em **aplicação gerida no diretório local**. Em seguida, vá para o **propriedades** página, para encontrar o ID de objeto. Certifique-se de que não é Pegando o ID de objeto inicial que está na sua aplicação, mas em vez disso, o ID de objeto do aplicativo gerenciado.
-   O **função** associado à conta, que será utilizada para o AZURE.

     ![Adicionar uma aplicação gerida no portal](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Clique em **adicionar** para adicionar o principal de serviço à sua conta.

   ![Adicionar um principal de serviço](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Obtenha o token de um acesso do Azure AD
----------------------------

As APIs do Cloud Partner Portal utilize os seguintes recursos e protocolos durante a autenticação:

- Um token de portador do JSON Web Token (JWT) para pedir acesso aos recursos
- O [OpenID Connect](https://openid.net/connect/) protocol (OIDC) para verificar a identidade
- [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) como a autoridade de identidade

Existem duas abordagens de princípio para aquisição por meio de programação de um token JWT:

- Utilizar a biblioteca de autenticação da Microsoft para .NET ([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Essa abordagem de nível superior é recomendada para os desenvolvedores do .NET. 
- Efetuar uma **HTTP POST** pedido para o oauth do Azure AD **token** ponto final, que assume a forma:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Agora, pode transmitir este token como parte do cabeçalho de autorização para pedidos de API.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Para todas as APIs nesta referência, o cabeçalho authorization é sempre pressuposto transmitido, para que não tenham sido explicitamente falar dele.

Caso se depare com erros de autenticação no seu pedido, consulte [resolução de problemas de erros de autenticação](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
