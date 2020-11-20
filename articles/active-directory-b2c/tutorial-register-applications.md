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
ms.date: 04/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 84a3ef7b41b17e85c594213246211d45911ac56a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953054"
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

Para uma aplicação web, você precisa criar um segredo de aplicação. Este segredo será usado pela sua aplicação para trocar um código de autorização para um token de acesso.

#### <a name="app-registrations"></a>[Registos de aplicações](#tab/app-reg-ga/)

1. Na página **Azure AD B2C - Registos de aplicações,** selecione a aplicação que criou, por exemplo *webapp1*.
1. No menu esquerdo, em **Manage,** selecione **Certificates & secrets**.
1. Selecione **Novo segredo do cliente**.
1. Insira uma descrição para o segredo do cliente na caixa **Descrição.** Por exemplo, *o segredo de clientes1*.
1. Em **Expira**, selecione uma duração para a qual o segredo é válido e, em seguida, selecione **Adicionar**.
1. Registar o **valor** do segredo. Utiliza este valor como segredo de aplicação no código da sua aplicação.

#### <a name="applications-legacy"></a>[Candidaturas (Legado)](#tab/applications-legacy/)

1. Na página **Azure AD B2C - Aplicações,** selecione a aplicação que criou, por exemplo *webapp1*.
1. Selecione **Teclas** e, em seguida, **selecione Gerar tecla**.
1. **Selecione Guardar** para ver a chave. Anote o valor da **Chave da aplicação**. Utiliza este valor como segredo de aplicação no código da sua aplicação.

* * *

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a:

> [!div class="checklist"]
> * Registar uma aplicação Web
> * Criar um segredo de cliente

Em seguida, aprenda a criar fluxos de utilizador para permitir que os seus utilizadores se inscrevam, inscrevam-se e gerem os seus perfis.

> [!div class="nextstepaction"]
> [Criar fluxos de utilizadores em Azure Ative Directory B2C >](tutorial-create-user-flows.md)