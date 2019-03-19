---
title: Configurar uma aplicação para aceder a APIs Web | Azure
description: Saiba como configurar uma aplicação registada na plataforma de identidade da Microsoft para incluir URI(s) de redirecionamento, credenciais ou permissões para aceder a APIs Web.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ef499e49fc4d1a0dc79dfc4efb818f7330b57b6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57995205"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis-preview"></a>Início rápido: Configurar uma aplicação de cliente para aceder a APIs (pré-visualização) para a web

Para que uma aplicação cliente Web/confidencial possa participar num fluxo de concessão de autorização que precise de autenticação (e obter um token de acesso), essa aplicação tem de estabelecer credenciais seguras. O método de autenticação predefinido que o portal do Azure suporta é ID de cliente + chave secreta.

Além disso, antes de um cliente poder aceder a uma API Web exposta por uma aplicação de recurso (como a Microsoft Graph API), a arquitetura de consentimento garante que o cliente obtém a concessão de permissão necessária, com base nas permissões pedidas. Por predefinição, todas as aplicações podem escolher permissões da Microsoft Graph API. A [permissão “Iniciar e ler perfil de utilizador” da Graph API](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) está selecionada por predefinição. Pode escolher de entre [dois tipos de permissões](developer-glossary.md#permissions) para cada API Web pretendida:

* **Permissões da aplicação** - a aplicação cliente tem de aceder à API Web diretamente como a própria (sem contexto de utilizador). Este tipo de permissão requer consentimento do administrador e também não está disponível para aplicações cliente públicas (de ambiente de trabalho e móveis).
* **Permissões delegadas** - a aplicação cliente tem de aceder à API Web como o utilizador com sessão iniciada, mas com acesso limitado pela permissão selecionada. Este tipo de permissão pode ser concedido por um utilizador, a não ser que exija consentimento do administrador.

  > [!NOTE]
  > Adicionar uma permissão delegada a uma aplicação não concede automaticamente consentimento aos utilizadores no inquilino. Os utilizadores continuam a ter de consentir manualmente as permissões delegadas adicionadas no runtime, a não ser que o administrador conceda acesso em nome de todos eles.

Neste início rápido, vamos mostrar-lhe como configurar a sua app para:

* [Adicionar URIs de redirecionamento à aplicação](#add-redirect-uris-to-your-application)
* [Adicionar credenciais à aplicação Web](#add-credentials-to-your-web-application)
* [Adicionar permissões para aceder a APIs Web](#add-permissions-to-access-web-apis)

## <a name="prerequisites"></a>Pré-requisitos

Para começar, certifique-se de que cumpre estes pré-requisitos:

* Saiba mais sobre as [permissões e consentimentos](v2-permissions-and-consent.md) suportados, que é importante compreender se estiver a criar aplicações que têm de ser utilizadas por outros utilizadores ou aplicações.
* Ter um inquilino com aplicações registadas.
  * Se não tiver aplicações registadas, [saiba como registar aplicações na plataforma de identidade da Microsoft](quickstart-register-app.md).
* Inscreva-na experiência de Pré-visualização para registos de aplicações no portal do Azure. Os passos neste início rápido correspondem à nova IU e apenas funcionam se tiver optado pela experiência de Pré-visualização.

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Iniciar sessão no portal do Azure e selecionar a aplicação

Antes de poder configurar a aplicação, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
1. No painel de navegação do lado esquerdo, selecione o serviço **Azure Active Directory** e, em seguida, selecione **Registos de aplicações (Pré-visualização)**.
1. Encontre e selecione a aplicação que quer configurar. Depois de selecionar a aplicação, verá a página **Descrição Geral** da aplicação ou a página de registo principal.
1. Siga os passos para configurar a aplicação para aceder às APIs Web: 
    * [Adicionar URIs de redirecionamento à aplicação](#add-redirect-uris-to-your-application)
    * [Adicionar credenciais à aplicação Web](#add-credentials-to-your-web-application)
    * [Adicionar permissões para aceder a APIs Web](#add-permissions-to-access-web-apis)

## <a name="add-redirect-uris-to-your-application"></a>Adicionar URI(s) de redirecionamento à aplicação

[![Adicionar URIs de redirecionamento personalizados para aplicações cliente da Web e públicas](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png)](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png#lightbox)

Para adicionar um URI de redirecionamento à aplicação:

1. Na página **Descrição Geral** da aplicação, selecione a secção **Autenticação**.

1. Para adicionar um URI de redirecionamento personalizado para aplicações cliente da Web e públicas, siga estes passos:

   1. Localize a secção **URI de redirecionamento**.
   1. Selecione o tipo de aplicação que está a compilar, **Web** ou **Cliente público (móvel e ambiente de trabalho)**.
   1. Introduza o URI de redirecionamento da aplicação,
      * Para aplicações Web, indique o URL base da sua aplicação. Por exemplo, `http://localhost:31544` pode ser o URL de uma aplicação Web em execução no seu computador local. Os utilizadores utilizariam este URL para iniciar sessão numa aplicação cliente Web.
      * Para aplicações públicas, indique o URI utilizado pelo Azure AD para devolver respostas de token. Introduza um valor específico da aplicação, como, por exemplo, https://MyFirstApp.

1. Para escolher de entre os URIs de Redirecionamento sugeridos para clientes públicos (móveis, ambiente de trabalho), siga estes passos:

    1. Localize a secção **URIs de Redirecionamento sugeridos para clientes públicos (móveis, ambiente de trabalho)**.
    1. Utilize as caixas de verificação para selecionar o URI ou URIs de Redirecionamento adequados para a sua aplicação.

## <a name="add-credentials-to-your-web-application"></a>Adicionar credenciais à aplicação Web

[![Adicionar certificados e segredos do cliente](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png#lightbox)

Para adicionar uma credencial à aplicação Web:

1. Na página **Descrição Geral** da aplicação, selecione a secção **Certificados e segredos**.

1. Para adicionar um certificado, siga estes passos:

    1. Selecione **Carregar o certificado**.
    1. Selecione o ficheiro que pretende carregar. Tem de ser do tipo .cer, .pem ou .crt.
    1. Selecione **Adicionar**.

1. Para adicionar um segredo do cliente, siga estes passos:

    1. Selecione **Novo segredo do cliente**.
    1. Adicione uma descrição do segredo do cliente.
    1. Selecione uma duração.
    1. Selecione **Adicionar**.

> [!NOTE]
> Depois de guardar as alterações à configuração, a coluna mais a direita incluirá o valor do segredo do cliente. **Certifique-se de que copia o valor** para utilizar no código da sua aplicação cliente, pois aquele deixa de estar acessível quando sair desta página.

## <a name="add-permissions-to-access-web-apis"></a>Adicionar permissões para aceder a APIs Web

[![Adicionar permissões de APIs](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png)](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png#lightbox)

Para adicionar uma ou mais permissões para aceder às APIs de recursos a partir do seu cliente:

1. Na página **Descrição Geral** da aplicação, selecione **Permissões de API**.
1. Selecione o botão **Adicionar uma permissão**.
1. Por predefinição, a vista permite-lhe selecionar de entre **APIs da Microsoft**. Selecione a secção de APIs em que está interessado:
    * **APIs da Microsoft** - permite-lhe selecionar permissões para APIs da Microsoft, como a Microsoft Graph.
    * **APIs que a minha organização utiliza** - permite-lhe selecionar permissões para as APIs que a sua organização expôs ou APIs com que a organização se integrou.
    * **As minhas APIs** - permite-lhe selecionar permissões para as APIs expostas por si.
1. Depois de selecionar as APIs, verá a página **Pedir Permissões de API**. Se a API expuser as permissões delegadas e as permissões da aplicação, selecione o tipo de permissão de que a sua aplicação precisa.
1. Quando tiver concluído, selecione **Adicionar permissões**. É reencaminhado para a página **Permissões da API** na qual as permissões foram guardadas e adicionadas à tabela.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre outros inícios rápidos de gestão de aplicações relacionados:

* [Registar uma aplicação na plataforma de identidade da Microsoft](quickstart-register-app.md)
* [Configurar uma aplicação para expor APIs Web](quickstart-configure-app-expose-web-apis.md)
* [Modificar as contas suportadas por uma aplicação](quickstart-modify-supported-accounts.md)
* [Remover uma aplicação registada na plataforma de identidade da Microsoft](quickstart-remove-app.md)

Para saber mais sobre os dois objetos do Azure AD que representam uma aplicação registada e o relacionamento entre os mesmos, veja [Application objects and service principal objects](app-objects-and-service-principals.md) (Objetos da aplicação e objetos do principal de serviço).

Para saber mais sobre as diretrizes de imagem corporativa que deve seguir quando desenvolver aplicações com o Azure Active Directory, veja [Diretrizes de imagem corporativa para aplicações](howto-add-branding-in-azure-ad-apps.md).
