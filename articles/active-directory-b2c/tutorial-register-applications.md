---
title: 'Tutorial: Registe uma candidatura'
titleSuffix: Azure AD B2C
description: Saiba como registar uma aplicação web no Azure Ative Directory B2C utilizando o portal Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dbb689182a45367061ae129304a98a8ee9962051
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840116"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Tutorial: Registe uma candidatura no Diretório Ativo Azure B2C

Antes de as suas [candidaturas](application-types.md) poderem interagir com o Azure Ative Directory B2C (Azure AD B2C), devem estar registados num inquilino que gere. Este tutorial mostra-lhe como registar uma aplicação web utilizando o portal Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Registar uma aplicação Web
> * Criar um segredo do cliente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não criou o seu próprio [Inquilino B2C Azure AD,](tutorial-create-tenant.md)crie agora um. Você pode usar um inquilino Azure AD B2C existente.

## <a name="register-a-web-application"></a>Registar uma aplicação Web

Para registrar um aplicativo em seu locatário Azure AD B2C, você pode usar a experiência de **aplicativos** atual ou nossa nova experiência unificada **de registros de aplicativo (versão prévia)** . [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplicações](#tab/applications/)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione o ícone **diretório + assinatura** na barra de ferramentas do portal e selecione o diretório que contém seu locatário Azure ad B2C.
1. Na portal do Azure, procure e selecione **Azure ad B2C**.
1. Selecione **Aplicações,** e, em seguida, **selecione Adicionar**.
1. Insira um nome para a aplicação. Por exemplo, *webapp1*.
1. Para **incluir web app/ web API** e **permitir fluxo implícito,** selecione **Sim**.
1. Para **o URL de resposta,** insira um ponto final onde o Azure AD B2C deve devolver quaisquer fichas que a sua aplicação solicite. Por exemplo, pode defini-lo para ouvir localmente em `https://localhost:44316`. Se ainda não sabe o número da porta, pode introduzir um valor de espaço reservado e troque-o mais tarde.

    Para fins de teste como este tutorial, pode defini-lo para `https://jwt.ms` que exibe o conteúdo de um símbolo para inspeção. Para este tutorial, delineie o **URL de resposta** para `https://jwt.ms`.

    As seguintes restrições aplicam-se à resposta dos URLs:

    * O URL de resposta deve começar com o esquema `https`.
    * O URL de resposta é sensível a casos. O seu caso deve coincidir com o caso do percurso url da sua aplicação de execução. Por exemplo, se a sua aplicação incluir como parte do seu caminho `.../abc/response-oidc`, não especifique `.../ABC/response-oidc` no URL de resposta. Uma vez que o navegador da Web trata os caminhos como sensíveis a casos, os cookies associados à `.../abc/response-oidc` podem ser excluídos se forem redirecionados para o URL de `.../ABC/response-oidc` desajustado.

1. Selecione **criar** para concluir o registro do aplicativo.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione o ícone **diretório + assinatura** na barra de ferramentas do portal e selecione o diretório que contém seu locatário Azure ad B2C.
1. Na portal do Azure, procure e selecione **Azure ad B2C**.
1. Selecione **registros de aplicativo (versão prévia)** e, em seguida, selecione **novo registro**.
1. Insira um **nome** para o aplicativo. Por exemplo, *webapp1*.
1. Selecione **Contas em qualquer diretório organizacional ou qualquer fornecedor**de identidade.
1. Em **Redirecione o URI,** selecione **Web**e introduza `https://jwt.ms` na caixa de texto URL.

    O URI redirecionado é o ponto final para o qual o utilizador é enviado pelo servidor de autorização (Azure AD B2C, neste caso) após completar a sua interação com o utilizador, e para o qual um token de acesso ou código de autorização é enviado mediante autorização bem sucedida. Numa aplicação de produção, é tipicamente um ponto final acessível ao público onde a sua aplicação está em execução, como `https://contoso.com/auth-response`. Para fins de teste como este tutorial, pode defini-lo para `https://jwt.ms`, uma aplicação web da Microsoft que exibe os conteúdos descodificados de um token (o conteúdo do token nunca sai do seu navegador). Durante o desenvolvimento da aplicação, pode adicionar o ponto final onde a sua aplicação ouve localmente, como `https://localhost:5000`. Pode adicionar e modificar URIs redirecionados nas suas aplicações registadas a qualquer momento.

    As seguintes restrições aplicam-se à redirecionamento de URIs:

    * O URL de resposta deve começar com o esquema `https`.
    * O URL de resposta é sensível a casos. O seu caso deve coincidir com o caso do percurso url da sua aplicação de execução. Por exemplo, se a sua aplicação incluir como parte do seu caminho `.../abc/response-oidc`, não especifique `.../ABC/response-oidc` no URL de resposta. Uma vez que o navegador da Web trata os caminhos como sensíveis a casos, os cookies associados à `.../abc/response-oidc` podem ser excluídos se forem redirecionados para o URL de `.../ABC/response-oidc` desajustado.

1. Em **Permissões**, selecione o consentimento do *administrador grant para abrir e offline_access permissões* verifiquem a caixa.
1. Selecione **Registar**.

Uma vez concluído o registo de candidatura, ative o fluxo implícito de subvenção:

1. Em **gerenciar**, selecione **autenticação**.
1. Selecione **experimentar a nova experiência** (se mostrado).
1. Sob **a subvenção Implícita,** selecione tanto as **fichas** de acesso como as **fichas de identificação** das caixas de verificação.
1. Selecione **Guardar**.

* * *

## <a name="create-a-client-secret"></a>Criar um segredo do cliente

Se o seu pedido trocar um código de autorização para um sinal de acesso, precisa de criar um segredo de aplicação.

#### <a name="applicationstabapplications"></a>[Aplicações](#tab/applications/)

1. Na página **Azure AD B2C - Aplicações,** selecione a aplicação que criou, por exemplo *webapp1*.
1. Selecione **Teclas** e, em seguida, **selecione A tecla Generate**.
1. Selecione **Guardar** para visualizar a tecla. Anote o valor da **Chave da aplicação**. Usa este valor como segredo de aplicação no código da sua aplicação.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Na página **Azure AD B2C - Registos de aplicações (Pré-visualização),** selecione a aplicação que criou, por exemplo *webapp1*.
1. Em **Gerir**, selecione **Certificados e segredos.**
1. Selecione **Novo segredo do cliente**.
1. Insira uma descrição para o segredo do cliente na caixa **descrição.** Por exemplo, *clientsecret1*.
1. Em **Expirações,** selecione uma duração para a qual o segredo é válido e, em seguida, selecione **Adicionar**.
1. Grave o **valor**do segredo. Usa este valor como segredo de aplicação no código da sua aplicação.

* * *

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Registar uma aplicação Web
> * Criar um segredo do cliente

Em seguida, aprenda a criar fluxos de utilizador para permitir que os seus utilizadores se inscrevam, inscrevam-se e gerem os seus perfis.

> [!div class="nextstepaction"]
> [Criar fluxos de utilizadores no Diretório Ativo Azure B2C >](tutorial-create-user-flows.md)
