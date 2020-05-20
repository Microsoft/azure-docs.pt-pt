---
title: 'Tutorial: Registe uma candidatura'
titleSuffix: Azure AD B2C
description: Saiba como registar uma aplicação web no Azure Ative Directory B2C utilizando o portal Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6e78a6b422f592caa252cb5856fc590a4bed35a7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83637590"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Tutorial: Registe uma candidatura no Diretório Ativo Azure B2C

Antes de as suas [candidaturas](application-types.md) poderem interagir com o Azure Ative Directory B2C (Azure AD B2C), devem estar registados num inquilino que gere. Este tutorial mostra-lhe como registar uma aplicação web utilizando o portal Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Registar uma aplicação Web
> * Criar um segredo de cliente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não criou o seu próprio [Inquilino B2C Azure AD,](tutorial-create-tenant.md)crie agora um. Você pode usar um inquilino Azure AD B2C existente.

## <a name="register-a-web-application"></a>Registar uma aplicação Web

Para registar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a experiência atual de **Aplicações,** ou a nossa nova experiência unificada de registos de **Aplicações (Pré-visualização).** [Saiba mais sobre a nova experiência.](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Diretório + Subscrição** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Selecione **Aplicações,** e, em seguida, **selecione Adicionar**.
1. Introduza um nome para a aplicação. Por exemplo, *webapp1*.
1. Para **incluir web app/ web API** e **permitir fluxo implícito,** selecione **Sim**.
1. Para **o URL de resposta,** insira um ponto final onde o Azure AD B2C deve devolver quaisquer fichas que a sua aplicação solicite. Por exemplo, pode defini-lo para ouvir localmente `https://localhost:44316` em . Se ainda não sabe o número da porta, pode introduzir um valor de espaço reservado e troque-o mais tarde.

    Para fins de teste como este tutorial, pode defini-lo no `https://jwt.ms` qual exibe o conteúdo de um símbolo para inspeção. Para este tutorial, delineie o **URL de Resposta** para `https://jwt.ms` .

    As seguintes restrições aplicam-se à resposta dos URLs:

    * O URL de resposta deve começar com o esquema `https` .
    * O URL de resposta é sensível a casos. O seu caso deve coincidir com o caso do percurso url da sua aplicação de execução. Por exemplo, se a sua aplicação incluir como parte do seu `.../abc/response-oidc` caminho, não especifique `.../ABC/response-oidc` no URL de resposta. Como o navegador da Web trata os caminhos como sensíveis a casos, os cookies associados `.../abc/response-oidc` podem ser excluídos se redirecionados para o URL desajustado do `.../ABC/response-oidc` caso.

1. Selecione **Criar** para completar o registo de candidatura.

#### <a name="app-registrations-preview"></a>[Registos de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Diretório + Subscrição** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Selecione **registos de aplicativos (Pré-visualização)** e, em seguida, selecione **Nova inscrição**.
1. Insira um **Nome** para a aplicação. Por exemplo, *webapp1*.
1. No âmbito dos tipos de **conta suportados,** selecione **Contas em qualquer diretório organizacional (Qualquer diretório AD Azure - Multitenant) e contas pessoais da Microsoft (por exemplo, Skype, Xbox)**.
1. Em **Redirecione o URI,** selecione **Web**e, em seguida, introduza `https://jwt.ms` na caixa de texto URL.

    O URI redirecionado é o ponto final para o qual o utilizador é enviado pelo servidor de autorização (Azure AD B2C, neste caso) após completar a sua interação com o utilizador, e para o qual um token de acesso ou código de autorização é enviado mediante autorização bem sucedida. Numa aplicação de produção, é tipicamente um ponto final acessível ao público onde a sua aplicação está em `https://contoso.com/auth-response` execução, como . Para fins de teste como este tutorial, pode defini-lo para `https://jwt.ms` , uma aplicação web propriedade da Microsoft que exibe os conteúdos descodificados de um token (o conteúdo do token nunca sai do seu navegador). Durante o desenvolvimento da aplicação, pode adicionar o ponto final onde a sua aplicação ouve localmente, como `https://localhost:5000` . Pode adicionar e modificar URIs redirecionados nas suas aplicações registadas a qualquer momento.

    As seguintes restrições aplicam-se à redirecionamento de URIs:

    * O URL de resposta deve começar com o esquema `https` .
    * O URL de resposta é sensível a casos. O seu caso deve coincidir com o caso do percurso url da sua aplicação de execução. Por exemplo, se a sua aplicação incluir como parte do seu `.../abc/response-oidc` caminho, não especifique `.../ABC/response-oidc` no URL de resposta. Como o navegador da Web trata os caminhos como sensíveis a casos, os cookies associados `.../abc/response-oidc` podem ser excluídos se redirecionados para o URL desajustado do `.../ABC/response-oidc` caso.

1. Em **Permissões**, selecione o consentimento do *administrador grant para abrir e offline_access permissões* verifiquem a caixa.
1. Selecione **Registar**.

Uma vez concluído o registo de candidatura, ative o fluxo implícito de subvenção:

1. Em **'Gerir',** **selecione Autenticação**.
1. Selecione **Experimente a nova experiência** (se mostrada).
1. Sob **a subvenção Implícita,** selecione tanto as **fichas** de acesso como as **fichas de identificação** das caixas de verificação.
1. Selecione **Guardar**.

* * *

## <a name="create-a-client-secret"></a>Criar um segredo de cliente

Se o seu pedido trocar um código de autorização para um sinal de acesso, precisa de criar um segredo de aplicação.

#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Na página **Azure AD B2C - Aplicações,** selecione a aplicação que criou, por exemplo *webapp1*.
1. Selecione **Teclas** e, em seguida, **selecione A tecla Generate**.
1. Selecione **Guardar** para visualizar a tecla. Anote o valor da **Chave da aplicação**. Usa este valor como segredo de aplicação no código da sua aplicação.

#### <a name="app-registrations-preview"></a>[Registos de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Na página **Azure AD B2C - Registos de aplicações (Pré-visualização),** selecione a aplicação que criou, por exemplo *webapp1*.
1. Under **Manage**, selecione **Certificados & segredos.**
1. Selecione **Novo segredo do cliente**.
1. Insira uma descrição para o segredo do cliente na caixa **descrição.** Por exemplo, *clientsecret1*.
1. Em **Expirações,** selecione uma duração para a qual o segredo é válido e, em seguida, selecione **Adicionar**.
1. Grave o **valor**do segredo. Usa este valor como segredo de aplicação no código da sua aplicação.

* * *

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a:

> [!div class="checklist"]
> * Registar uma aplicação Web
> * Criar um segredo de cliente

Em seguida, aprenda a criar fluxos de utilizador para permitir que os seus utilizadores se inscrevam, inscrevam-se e gerem os seus perfis.

> [!div class="nextstepaction"]
> [Criar fluxos de utilizadores no Diretório Ativo Azure B2C >](tutorial-create-user-flows.md)
