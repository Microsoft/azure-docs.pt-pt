---
title: Tutorial - Adicionar autenticação a uma aplicação web no Azure App Service Rio Azure
description: Neste tutorial, aprende-se a ativar a autenticação e autorização para uma aplicação web em execução no Azure App Service. Limite o acesso à aplicação web aos utilizadores da sua organização.
services: active-directory, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: 297db67b80f9e5ae04e3c10f370ece9b5fdedee3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "96020454"
---
# <a name="tutorial-add-authentication-to-your-web-app-running-on-azure-app-service"></a>Tutorial: Adicione autenticação à sua aplicação web em execução no Azure App Service

Saiba como ativar a autenticação da sua aplicação web em execução no Azure App Service e limite o acesso aos utilizadores na sua organização.

:::image type="content" source="./media/scenario-secure-app-authentication-app-service/web-app-sign-in.svg" alt-text="Diagrama que mostra o ster-in do utilizador." border="false":::

O Serviço de Aplicações fornece suporte de autenticação e autorização incorporado, para que possa iniciar súmis nos utilizadores e aceder aos dados escrevendo o mínimo ou nenhum código na sua aplicação web. A utilização do módulo de autenticação/autorização do Serviço de Aplicações não é necessária, mas ajuda a simplificar a autenticação e a autorização para a sua aplicação. Este artigo mostra como proteger a sua aplicação web com o módulo de autenticação/autorização do Serviço de Aplicações utilizando o Azure Ative Directory (Azure AD) como fornecedor de identidade.

O módulo de autenticação/autorização está ativado e configurado através do portal Azure e das definições de aplicações. Não são necessários SDKs, linguagens específicas ou alterações ao código de aplicação. Uma variedade de fornecedores de identidade são suportados, que inclui Azure AD, Microsoft Account, Facebook, Google e Twitter. Quando o módulo de autenticação/autorização está ativado, todos os pedidos HTTP de entrada passam por ele antes de serem tratados pelo código da aplicação. Para saber mais, consulte [autenticação e autorização no Serviço de Aplicações Azure.](overview-authentication-authorization.md)

Neste tutorial, vai aprender a:

> [!div class="checklist"]
>
> * Configurar a autenticação para a aplicação web.
> * Limite o acesso à aplicação web aos utilizadores da sua organização.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-publish-a-web-app-on-app-service"></a>Criar e publicar uma aplicação web no Serviço de Aplicações

Para este tutorial, você precisa de uma aplicação web implantada no Serviço de Aplicações. Pode utilizar uma aplicação web existente, ou pode seguir o [ASP.NET Core quickstart](quickstart-dotnetcore.md) para criar e publicar uma nova aplicação web para o App Service.

Quer utilize uma aplicação web existente ou crie uma nova, tome nota do nome da aplicação web e do nome do grupo de recursos para o qual a aplicação web está implantada. Precisa destes nomes durante este tutorial. Ao longo deste tutorial, os nomes de exemplo em procedimentos e imagens contêm *SecureWebApp*.

## <a name="configure-authentication-and-authorization"></a>Configurar a autenticação e a autorização

Tem agora uma aplicação web em execução no Serviço de Aplicações. Em seguida, ativa a autenticação e autorização para a aplicação web. Usa a Azure AD como fornecedor de identidade. Para mais informações, consulte [a autenticação Configure Azure AD para a sua aplicação Serviço de Aplicações.](configure-authentication-provider-aad.md)

No menu do [portal Azure,](https://portal.azure.com) selecione **grupos de Recursos,** ou procure e selecione **grupos** de Recursos a partir de qualquer página.

Nos **grupos de recursos,** encontre e selecione o seu grupo de recursos. Em **Visão Geral,** selecione a página de gestão da sua aplicação.

:::image type="content" alt-text="Screenshot que mostra a seleção da página de gestão da sua aplicação." source="./media/scenario-secure-app-authentication-app-service/select-app-service.png":::

No menu esquerdo da sua aplicação, selecione **Autenticação / Autorização**, e, em seguida, ative a Autenticação do Serviço de Aplicações selecionando **On**.

Em **Ação a tomar quando o pedido não é autenticado**, selecione **Iniciar sessão com o Azure Active Directory**.

No âmbito **dos Fornecedores de Autenticação**, selecione **Azure Ative Directory**. Selecione **Express** e, em seguida, aceite as definições predefinidos para criar uma nova aplicação Ative Directory. Selecione **OK**.

:::image type="content" alt-text="Screenshot que mostra a autenticação express." source="./media/scenario-secure-app-authentication-app-service/configure-authentication.png":::

Na página **Autenticação/Autorização,** **selecione Guardar.**

Quando vir a notificação com a `Successfully saved the Auth Settings for <app-name> App` mensagem, refresque a página do portal.

Tem agora uma aplicação que é protegida pela autenticação e autorização do Serviço de Aplicações.

## <a name="verify-limited-access-to-the-web-app"></a>Verificar acesso limitado à aplicação web

Quando ativou o módulo de autenticação/autorização do Serviço de Aplicações, foi criado um registo de aplicações no seu inquilino AZure AD. O registo da aplicação tem o mesmo nome de exibição que a sua aplicação web. Para verificar as definições, selecione **O Diretório Ativo Azure** a partir do menu do portal e selecione **registos de Aplicações**. Selecione o registo de aplicações que foi criado. Na visão geral, verifique se **os tipos de conta suportados** são **definidos apenas** para a Minha organização .

:::image type="content" alt-text="Screenshot que mostra o acesso à verificação." source="./media/scenario-secure-app-authentication-app-service/verify-access.png":::

Para verificar se o acesso à sua aplicação está limitado a utilizadores da sua organização, inicie um navegador em modo incógnito ou privado e vá a `https://<app-name>.azurewebsites.net` . Deve ser direcionado para uma página de sing-in segura, verificando se os utilizadores não autenticados não têm acesso ao site. Inscreva-se como utilizador na sua organização para ter acesso ao site. Também pode iniciar um novo navegador e tentar iniciar sôms, utilizando uma conta pessoal para verificar se os utilizadores fora da organização não têm acesso.

## <a name="clean-up-resources"></a>Limpar os recursos

Se terminou este tutorial e já não precisa da aplicação web ou dos recursos associados, [limpe os recursos que criou.](scenario-secure-app-clean-up-resources.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
>
> * Configurar a autenticação para a aplicação web.
> * Limite o acesso à aplicação web aos utilizadores da sua organização.

> [!div class="nextstepaction"]
> [Serviço de aplicações acessa armazenamento](scenario-secure-app-access-storage.md)
