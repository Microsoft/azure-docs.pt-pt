---
title: 'Tutorial: Registar uma candidatura'
titleSuffix: Azure AD B2C
description: Siga este tutorial para saber como registar uma aplicação web no Azure Ative Directory B2C utilizando o portal Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2ec67669edeb52af1044c97c984eb6ba36fd1a0b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579642"
---
# <a name="tutorial-register-a-web-application-in-azure-active-directory-b2c"></a>Tutorial: Registar uma aplicação web no Azure Ative Directory B2C

Antes de as suas [candidaturas](application-types.md) poderem interagir com o Azure Ative Directory B2C (Azure AD B2C), devem estar registadas num inquilino que gere. Este tutorial mostra-lhe como registar uma aplicação web utilizando o portal Azure. 

Uma "aplicação web" refere-se a uma aplicação web tradicional que executa a maior parte da lógica de aplicação no servidor. Podem ser construídos com estruturas como ASP.NET Core, Maven (Java), Flask (Python) e Express (Node.js).

> [!IMPORTANT]
> Se estiver a utilizar uma aplicação de uma página ("SPA") em vez disso (por exemplo, utilizando angular, vue ou React), aprenda [a registar uma aplicação de uma página](tutorial-register-spa.md).
> 
> Se estiver a utilizar uma aplicação nativa (por exemplo, iOS, Android, mobile & desktop), aprenda [a registar uma aplicação de cliente nativo](add-native-application.md).

## <a name="prerequisites"></a>Pré-requisitos
Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se ainda não criou o seu próprio [Inquilino Azure AD B2C,](tutorial-create-tenant.md)crie agora um. Você pode usar um inquilino Azure AD B2C existente.

## <a name="register-a-web-application"></a>Registar uma aplicação Web

Para registar uma aplicação web no seu inquilino Azure AD B2C, pode utilizar a nossa nova experiência de registos de **Aplicações unificadas** ou a nossa experiência de **Aplicações (Legado).** [Saiba mais sobre a nova experiência.](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[Registos de aplicações](#tab/app-reg-ga/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Selecione **as inscrições da App** e, em seguida, selecione Novo **registo**.
1. Insira um **Nome** para a inscrição. Por exemplo, *webapp1*.
1. Nos **tipos de conta suportado**, selecione Contas em qualquer fornecedor de identidade ou **diretório organizacional (para autenticar utilizadores com fluxos de utilizador)**.
1. Em **URI de redirecionamento,** selecione **Web** e, em seguida, `https://jwt.ms` introduza na caixa de texto URL.

    O redirect URI é o ponto final para o qual o utilizador é enviado pelo servidor de autorização (Azure AD B2C, neste caso) após completar a sua interação com o utilizador, e para o qual é enviado um token de acesso ou código de autorização mediante autorização bem sucedida. Numa aplicação de produção, é tipicamente um ponto final acessível ao público onde a sua aplicação está a funcionar, como `https://contoso.com/auth-response` . Para testes como este tutorial, pode `https://jwt.ms` defini-lo para , uma aplicação web de propriedade da Microsoft que exibe o conteúdo descodificado de um símbolo (o conteúdo do token nunca sai do seu navegador). Durante o desenvolvimento da aplicação, pode adicionar o ponto final onde a sua aplicação ouve localmente, como `https://localhost:5000` . Pode adicionar e modificar URIs redirecionando as suas aplicações registadas a qualquer momento.

    Aplicam-se as seguintes restrições ao redirecionamento de IUR:

    * A URL de resposta deve começar pelo regime `https` .
    * A URL de resposta é sensível a casos. O seu caso deve coincidir com o caso do caminho URL da sua aplicação de execução. Por exemplo, se a sua aplicação incluir como parte do seu `.../abc/response-oidc` caminho, não especifique `.../ABC/response-oidc` no URL de resposta. Como o navegador da Web trata os caminhos como sensíveis a casos, os cookies associados `.../abc/response-oidc` podem ser excluídos se forem redirecionados para o URL desajustado do `.../ABC/response-oidc` caso.

1. Em **Permissões**, selecione o *consentimento de administração grant para abrir e offline_access caixa de verificação de permissões.*
1. Selecione **Registar**.

#### <a name="applications-legacy"></a>[Candidaturas (Legado)](#tab/applications-legacy/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Selecione **Aplicações (Legado)** e, em seguida, **selecione Adicionar**.
1. Introduza um nome para a aplicação. Por exemplo, *webapp1*.
1. Para **incluir aplicativo web/web API,** selecione **Sim**.
1. Para **URL de resposta,** insira um ponto final onde a Azure AD B2C deve devolver quaisquer fichas que o seu pedido de candidatura. Por exemplo, pode defini-lo para ouvir localmente em `http://localhost:5000` . Pode adicionar e modificar URIs redirecionando as suas aplicações registadas a qualquer momento.

    Aplicam-se as seguintes restrições ao redirecionamento de IUR:

    * A URL de resposta deve começar pelo `https` regime, a menos que utilize `localhost` .
    * A URL de resposta é sensível a casos. O seu caso deve coincidir com o caso do caminho URL da sua aplicação de execução. Por exemplo, se a sua aplicação incluir como parte do seu `.../abc/response-oidc` caminho, não especifique `.../ABC/response-oidc` no URL de resposta. Como o navegador da Web trata os caminhos como sensíveis a casos, os cookies associados `.../abc/response-oidc` podem ser excluídos se forem redirecionados para o URL desajustado do `.../ABC/response-oidc` caso.

1. Selecione **Criar** para completar o registo de inscrição.

* * *

## <a name="create-a-client-secret"></a>Criar um segredo de cliente

Para uma aplicação web, você precisa criar um segredo de aplicação. O segredo do cliente também é conhecido como *uma senha de aplicação.* O segredo será usado pela sua aplicação para trocar um código de autorização para um token de acesso.

#### <a name="app-registrations"></a>[Registos de aplicações](#tab/app-reg-ga/)

1. Na página **Azure AD B2C - Registos de aplicações,** selecione a aplicação que criou, por exemplo *webapp1*.
1. No menu esquerdo, em **Manage,** selecione **Certificates & secrets**.
1. Selecione **Novo segredo do cliente**.
1. Insira uma descrição para o segredo do cliente na caixa **Descrição.** Por exemplo, *o segredo de clientes1*.
1. Em **Expira**, selecione uma duração para a qual o segredo é válido e, em seguida, selecione **Adicionar**.
1. Grave o **valor** secreto para uso no código de aplicação do seu cliente. Este valor secreto nunca mais é exibido depois de deixar esta página. Utiliza este valor como segredo de aplicação no código da sua aplicação.

#### <a name="applications-legacy"></a>[Candidaturas (Legado)](#tab/applications-legacy/)

1. Na página **Azure AD B2C - Aplicações,** selecione a aplicação que criou, por exemplo *webapp1*.
1. Selecione **Teclas** e, em seguida, **selecione Gerar tecla**.
1. **Selecione Guardar** para ver a chave. Anote o valor da **Chave da aplicação**. Utiliza este valor como segredo de aplicação no código da sua aplicação.

* * *

> [!NOTE]
> Por razões de segurança, pode revirar o segredo da aplicação periodicamente, ou imediatamente em caso de emergência. Qualquer aplicação que se integre com o Azure AD B2C deve estar preparada para lidar com um evento secreto de capotamento, não importa a frequência com que possa ocorrer. Pode definir dois segredos de aplicação, permitindo que a sua aplicação continue a usar o antigo segredo durante um evento de rotação secreta de aplicações. Para adicionar outro segredo de cliente, repita os passos nesta secção. 

## <a name="enable-id-token-implicit-grant"></a>Permitir concessão implícita de id simbólico

A característica determinante da subvenção implícita é que os tokens, como o ID e os tokens de acesso, são devolvidos diretamente do Azure AD B2C à aplicação. Para aplicações web, como ASP.NET aplicações web Core [https://jwt.ms](https://jwt.ms) e, que solicitam um token de ID diretamente do ponto final de autorização, ativar o fluxo de subvenção implícita no registo da aplicação.

1. No menu esquerdo, em **Manage,** **selecione Autenticação.**
1. Sob a concessão implícita, selecione as caixas de verificação **de fichas de acesso** e **id.**
1. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a:

> [!div class="checklist"]
> * Registar uma aplicação Web
> * Criar um segredo de cliente

Em seguida, aprenda a criar fluxos de utilizador para permitir que os seus utilizadores se inscrevam, inscrevam-se e gerem os seus perfis.

> [!div class="nextstepaction"]
> [Criar fluxos de utilizadores em Azure Ative Directory B2C >](tutorial-create-user-flows.md)
