---
title: 'Tutorial: registrar um aplicativo'
titleSuffix: Azure AD B2C
description: Saiba como registrar um aplicativo Web no Azure Active Directory B2C usando o portal do Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2a9e4828428a91c1d1b75fb9d1b9004e52988084
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425474"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Tutorial: registrar um aplicativo no Azure Active Directory B2C

Antes que seus [aplicativos](active-directory-b2c-apps.md) possam interagir com Azure Active Directory B2C (Azure ad B2C), eles devem ser registrados em um locatário que você gerencia. Este tutorial mostra como registrar um aplicativo Web usando o portal do Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Registar uma aplicação Web
> * Criar um segredo do cliente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não criou seu próprio [locatário de Azure ad B2C](tutorial-create-tenant.md), crie um agora. Você pode usar um locatário de Azure AD B2C existente.

## <a name="register-a-web-application"></a>Registar uma aplicação Web

Para registrar um aplicativo em seu locatário Azure AD B2C, você pode usar a experiência de **aplicativos** atual ou nossa nova experiência unificada **de registros de aplicativo (versão prévia)** . [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplicações](#tab/applications/)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione o ícone **diretório + assinatura** na barra de ferramentas do portal e selecione o diretório que contém seu locatário Azure ad B2C.
1. Na portal do Azure, procure e selecione **Azure ad B2C**.
1. Selecione **aplicativos**e, em seguida, selecione **Adicionar**.
1. Introduza um nome para a aplicação. Por exemplo, *webapp1*.
1. Para **incluir aplicativo Web/API Web** e **permitir fluxo implícito**, selecione **Sim**.
1. Para **URL de resposta**, insira um ponto de extremidade onde Azure ad B2C deve retornar quaisquer tokens que seu aplicativo solicitar. Por exemplo, você pode defini-lo para escutar localmente em `https://localhost:44316`. Se você ainda não souber o número da porta, poderá inserir um valor de espaço reservado e alterá-lo mais tarde.

    Para fins de teste como este tutorial, você pode defini-lo como `https://jwt.ms` que exibe o conteúdo de um token para inspeção. Para este tutorial, defina a **URL de resposta** como `https://jwt.ms`.

    As seguintes restrições se aplicam a URLs de resposta:

    * A URL de resposta deve começar com o esquema `https`.
    * A URL de resposta diferencia maiúsculas de minúsculas. Seu caso deve corresponder ao caso do caminho da URL do seu aplicativo em execução. Por exemplo, se seu aplicativo incluir como parte de seu caminho `.../abc/response-oidc`, não especifique `.../ABC/response-oidc` na URL de resposta. Como o navegador da Web trata os caminhos como diferenciando maiúsculas de minúsculas, os cookies associados a `.../abc/response-oidc` podem ser excluídos se forem redirecionados para a URL de `.../ABC/response-oidc` incompatível com maiúsculas e minúsculas.

1. Selecione **criar** para concluir o registro do aplicativo.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione o ícone **diretório + assinatura** na barra de ferramentas do portal e selecione o diretório que contém seu locatário Azure ad B2C.
1. Na portal do Azure, procure e selecione **Azure ad B2C**.
1. Selecione **registros de aplicativo (versão prévia)** e, em seguida, selecione **novo registro**.
1. Insira um **nome** para o aplicativo. Por exemplo, *webapp1*.
1. Selecione **contas em qualquer diretório organizacional ou qualquer provedor de identidade**.
1. Em **URI de redirecionamento**, selecione **Web**e, em seguida, insira `https://jwt.ms` na caixa de texto URL.

    O URI de redirecionamento é o ponto de extremidade para o qual o usuário é enviado pelo servidor de autorização (Azure AD B2C, nesse caso) depois de concluir sua interação com o usuário e para o qual um token de acesso ou código de autorização é enviado após a autorização bem-sucedida. Em um aplicativo de produção, normalmente é um ponto de extremidade publicamente acessível em que seu aplicativo está em execução, como `https://contoso.com/auth-response`. Para fins de teste como este tutorial, você pode defini-lo como `https://jwt.ms`, um aplicativo da Web de propriedade da Microsoft que exibe o conteúdo decodificado de um token (o conteúdo do token nunca deixa o navegador). Durante o desenvolvimento do aplicativo, você pode adicionar o ponto de extremidade onde seu aplicativo escuta localmente, como `https://localhost:5000`. Você pode adicionar e modificar URIs de redirecionamento em seus aplicativos registrados a qualquer momento.

    As seguintes restrições se aplicam a URIs de redirecionamento:

    * A URL de resposta deve começar com o esquema `https`.
    * A URL de resposta diferencia maiúsculas de minúsculas. Seu caso deve corresponder ao caso do caminho da URL do seu aplicativo em execução. Por exemplo, se seu aplicativo incluir como parte de seu caminho `.../abc/response-oidc`, não especifique `.../ABC/response-oidc` na URL de resposta. Como o navegador da Web trata os caminhos como diferenciando maiúsculas de minúsculas, os cookies associados a `.../abc/response-oidc` podem ser excluídos se forem redirecionados para a URL de `.../ABC/response-oidc` incompatível com maiúsculas e minúsculas.

1. Em **permissões**, marque a caixa de seleção *conceder consentimento de administrador às permissões OpenID e offline_access* .
1. Selecione **Registar**.

Depois que o registro do aplicativo for concluído, habilite o fluxo de concessão implícita:

1. Em **gerenciar**, selecione **autenticação**.
1. Selecione **experimentar a nova experiência** (se mostrado).
1. Em **concessão implícita**, marque as caixas de seleção **tokens de acesso** e **tokens de ID** .
1. Selecione **Guardar**.

* * *

## <a name="create-a-client-secret"></a>Criar um segredo do cliente

Se seu aplicativo trocar um código para um token, você precisará criar um segredo do aplicativo.

#### <a name="applicationstabapplications"></a>[Aplicações](#tab/applications/)

1. Na página **Azure ad B2C-aplicativos** , selecione o aplicativo que você criou, por exemplo *webapp1*.
1. Selecione **chaves** e, em seguida, selecione **gerar chave**.
1. Selecione **salvar** para exibir a chave. Anote o valor da **Chave da aplicação**. Você usa esse valor como o segredo do aplicativo no código do aplicativo.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Na página **Azure AD B2C registros de aplicativo (versão prévia)** , selecione o aplicativo que você criou, por exemplo *webapp1*.
1. Em **gerenciar**, selecione **certificados & segredos**.
1. Selecione **Novo segredo do cliente**.
1. Insira uma descrição para o segredo do cliente na caixa **Descrição** . Por exemplo, *clientsecret1*.
1. Em **expirar**, selecione uma duração para a qual o segredo é válido e, em seguida, selecione **Adicionar**.
1. Registre o **valor**do segredo. Você usa esse valor como o segredo do aplicativo no código do aplicativo.

* * *

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Registar uma aplicação Web
> * Criar um segredo do cliente

Em seguida, saiba como criar fluxos de usuário para permitir que os usuários se inscrevam, entrem e gerenciem seus perfis.

> [!div class="nextstepaction"]
> [Criar fluxos de usuário no Azure Active Directory B2C >](tutorial-create-user-flows.md)
