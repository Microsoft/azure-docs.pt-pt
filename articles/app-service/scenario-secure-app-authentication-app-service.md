---
title: Tutorial - adicione auth a uma aplicação web no Azure App Service / Rio Azure
description: Neste tutorial aprende-se a ativar a autenticação e autorização para uma aplicação web em execução no Azure App Service.  Limite o acesso à aplicação web aos utilizadores da minha organização.
services: active-directory, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: a1b2809371a01b1c6fd822e5c4aaa197d0de3c1b
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428914"
---
# <a name="tutorial-add-authentication-to-your-web-app-running-on-azure-app-service"></a>Tutorial: adicione autenticação à sua aplicação web em execução no Azure App Service

Saiba como ativar o auth para a sua aplicação web em execução no Azure App Service e limite o acesso aos utilizadores na sua organização.

:::image type="content" source="./media/scenario-secure-app-authentication-app-service/web-app-sign-in.svg" alt-text="Inscreva-se no utilizador" border="false":::

O Azure App Service fornece suporte de autenticação e autorização incorporado, para que possa iniciar súm em utilizadores e aceder aos dados escrevendo o mínimo ou nenhum código na sua aplicação web.  A utilização do módulo de autenticação/autorização do Serviço de Aplicações não é necessária, mas ajuda a simplificar a autenticação e a autorização para a sua aplicação. Este artigo mostra como proteger a sua aplicação web com o módulo de autenticação/autorização do Serviço de Aplicações, utilizando o Azure Ative Directory como fornecedor de identidade.

O módulo de autenticação/autorização está ativado e configurado através do portal Azure e das definições de aplicações. Não são necessários SDKs, linguagens específicas ou alterações ao código de aplicação. Uma variedade de fornecedores de identidade são suportados, incluindo: Azure AD, Microsoft Account, Facebook, Google e Twitter. Quando o módulo de autenticação/autorização está ativado, todos os pedidos HTTP de entrada passam por ele antes de serem tratados pelo código da aplicação.  Para saber mais, leia [autenticação e autorização no Serviço de Aplicações Azure.](overview-authentication-authorization.md)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Configurar a autenticação para a aplicação web
> * Limite o acesso à aplicação web aos utilizadores da sua organização

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-publish-a-web-app-on-app-service"></a>Criar e publicar uma aplicação web no Serviço de Aplicações

Para este tutorial, precisa de uma aplicação web implantada no Azure App Service.  Pode utilizar uma aplicação web existente, ou pode seguir o [ASP.NET Core quickstart](quickstart-dotnetcore.md) para criar e publicar uma nova aplicação web para o App Service.

Quer utilize uma aplicação web existente ou crie uma nova, tome nota do nome da aplicação web e do nome do grupo de recursos para o qual a aplicação web está implantada. Precisa destes nomes durante este tutorial. Ao longo deste tutorial, os nomes de exemplo em procedimentos e imagens contêm *SecureWebApp*.

## <a name="configure-authentication-and-authorization"></a>Configurar a autenticação e a autorização

Tem agora uma aplicação web em execução no Serviço de Aplicações.  Em seguida, ativa a autenticação e autorização para a aplicação web. Vai utilizar o Azure Active Directory como o fornecedor de identidade. Para obter mais informações, veja [Configure Azure Active Directory authentication for your App Services application](configure-authentication-provider-aad.md) (Configurar a autenticação do Azure Active Directory na sua aplicação dos Serviços de Aplicação).

No menu do [portal Azure,](https://portal.azure.com) selecione **grupos de Recursos** ou procure e selecione *grupos* de Recursos a partir de qualquer página.

Nos **grupos de recursos,** encontre e selecione o seu grupo de recursos. Em **Visão Geral,** selecione a página de gestão da sua aplicação.

:::image type="content" alt-text="Selecione Serviço de Aplicações" source="./media/scenario-secure-app-authentication-app-service/select-app-service.png":::

No menu esquerdo da sua aplicação, selecione **Autenticação /Autorização,** em seguida, ative a Autenticação do Serviço de Aplicações selecionando **On**.

Em **Ação a tomar quando o pedido não é autenticado** , selecione **Iniciar sessão com o Azure Active Directory**.

No âmbito **dos Fornecedores de Autenticação** , selecione **Azure Ative Directory**. Selecione **Express** e, em seguida, aceite as definições predefinidos para criar uma nova aplicação AD e selecione **OK**.

:::image type="content" alt-text="Autenticação expressa" source="./media/scenario-secure-app-authentication-app-service/configure-authentication.png":::

Na página **Autenticação/Autorização,** **selecione Guardar.**

Assim que vir a notificação com a `Successfully saved the Auth Settings for <app-name> App` mensagem, refresque a página do portal.

Tem agora uma aplicação que é protegida pela autenticação e autorização do Serviço de Aplicações.

## <a name="verify-limited-access-to-the-web-app"></a>Verificar acesso limitado à aplicação web

Quando ativou o módulo de autenticação e autorização do Serviço de Aplicações, foi criado um registo de aplicações no seu inquilino AZure AD.  O registo da aplicação tem o mesmo nome de exibição que a sua aplicação web. Para verificar as definições, selecione **O Diretório Ativo Azure** a partir do menu do portal e selecione **registos de Aplicações**.  Selecione o registo de aplicações que foi criado.  Na visão geral, verifique se **os tipos de conta suportados** são **definidos apenas** para a Minha organização .

:::image type="content" alt-text="Verificar acesso" source="./media/scenario-secure-app-authentication-app-service/verify-access.png":::

Para verificar se o acesso à sua aplicação está limitado a utilizadores da sua organização, inicie um navegador em modo incógnito ou privado e navegue para `https://<app-name>.azurewebsites.net` .  Deverá ser direcionado para uma página de sing-in segura, verificando se os utilizadores não autenticados não têm acesso ao site.  Inscreva-se como utilizador na sua organização para ter acesso ao site.  Também pode iniciar um novo navegador e tentar iniciar sôms usando uma conta pessoal para verificar se os utilizadores fora da organização não têm acesso.  

## <a name="clean-up-resources"></a>Limpar recursos

Se terminar este tutorial e já não precisar da aplicação web ou dos recursos associados, [limpe os recursos que criou.](scenario-secure-app-clean-up-resources.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
>
> * Configurar a autenticação para a aplicação web
> * Limite o acesso à aplicação web aos utilizadores da sua organização

> [!div class="nextstepaction"]
> [Serviço de aplicações acessa armazenamento](scenario-secure-app-access-storage.md)
