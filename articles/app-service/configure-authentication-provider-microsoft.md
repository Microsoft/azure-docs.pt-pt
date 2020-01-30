---
title: Configurar a autenticação da Microsoft
description: Saiba como configurar a autenticação da conta da Microsoft como um provedor de identidade para seu aplicativo do serviço de aplicativo.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 95c603d4a10eb0e4d0817e20755c0f9b36baa96f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842338"
---
# <a name="configure-your-app-service-app-to-use-microsoft-account-login"></a>Configurar seu aplicativo do serviço de aplicativo para usar o logon da conta da Microsoft

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra-lhe como configurar o Azure App Service para utilizar a AAD para suportar logins pessoais da conta da Microsoft.

> [!NOTE]
> Tanto as contas pessoais da Microsoft como as contas organizacionais utilizam o fornecedor de identidade AAD. Neste momento, não é possível configurar este fornecedor de identidade para suportar ambos os tipos de login.

## <a name="register-microsoft-account"> </a>Registrar seu aplicativo com a conta da Microsoft

1. Vá para [**registros de aplicativo**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) na portal do Azure. Se necessário, entre com seu conta Microsoft.
1. Selecione **novo registro**e insira um nome de aplicativo.
1. Sob os tipos de **conta suportados**, selecione **Contas em qualquer diretório organizacional (Qualquer diretório AD Azure - Multitenant) e contas pessoais da Microsoft (por exemplo, Skype, Xbox)**
1. Em **Redirecione os URIs,** selecione **Web**e introduza `https://<app-domain-name>/.auth/login/aad/callback`. Substitua *\<app-domain-name >* pelo nome de domínio do seu aplicativo.  Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Certifique-se de usar o esquema HTTPS na URL.

1. Selecione **Registar**.
1. Copie a **ID do aplicativo (cliente)** . Precisará dela mais tarde.
1. No painel esquerdo, selecione **certificados & segredos** > **novo segredo do cliente**. Insira uma descrição, selecione a duração da validade e selecione **Adicionar**.
1. Copie o valor que aparece na página **certificados & segredos** . Depois de sair da página, ela não será exibida novamente.

    > [!IMPORTANT]
    > O valor de segredo do cliente (senha) é uma credencial de segurança importante. Não compartilhe a senha com ninguém nem a distribua em um aplicativo cliente.

## <a name="secrets"> </a>Adicionar informações da conta da Microsoft ao seu aplicativo do serviço de aplicativo

1. Vá para seu aplicativo no [portal do Azure].
1. Selecione **configurações** > **autenticação/autorização**e verifique se a **autenticação do serviço de aplicativo** está **ativada**.
1. Em **provedores de autenticação**, selecione **Azure Active Directory**. Selecione **Advanced** no **modo de gestão**. Cole a ID do aplicativo (cliente) e o segredo do cliente obtidos anteriormente. Utilize **https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0** para o campo Url do **Emitente.**
1. Selecione **OK**.

   O serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo e às APIs do site. Você deve autorizar os usuários no código do aplicativo.

1. (Opcional) Para restringir o acesso aos utilizadores da conta da Microsoft, detete toda a Ação quando o **pedido não for autenticado** para iniciar sessão com o **Diretório Ativo do Azure**. Quando você define essa funcionalidade, seu aplicativo requer que todas as solicitações sejam autenticadas. Também redireciona todos os pedidos não autenticados para a utilização de AAD para autenticação. Note que, uma vez que configurou o seu **Url emitente** para utilizar o inquilino da Conta Microsoft, apenas os acréscimos pessoais irão autenticar com sucesso.

   > [!CAUTION]
   > Restringir o acesso dessa maneira se aplica a todas as chamadas para seu aplicativo, o que pode não ser desejável para aplicativos que têm um home page publicamente disponível, como em muitos aplicativos de página única. Para tais aplicativos, **Permitir solicitações anônimas (nenhuma ação)** pode ser preferível para que o aplicativo inicie manualmente a autenticação em si. Para obter mais informações, consulte [fluxo de autenticação](overview-authentication-authorization.md#authentication-flow).

1. Selecione **Guardar**.

Agora você está pronto para usar a conta da Microsoft para autenticação em seu aplicativo.

## <a name="related-content"> </a>Próximas etapas

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Portal do Azure]: https://portal.azure.com/
