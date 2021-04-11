---
title: Configure a autenticação da Microsoft
description: Saiba como configurar a autenticação da Conta microsoft como fornecedor de identidade para o seu Serviço de Aplicações ou aplicação Azure Functions.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 5e7b4c7ed1950b2fa585639a3097cc1f5688d739
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077955"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>Configure o seu Serviço de Aplicações ou app Azure Functions para utilizar o login da Conta Microsoft

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra-lhe como configurar o Azure App Service ou Azure Functions para usar o AAD para suportar logins pessoais de contas da Microsoft.

> [!IMPORTANT]
> Embora o fornecedor de Conta Microsoft ainda esteja suportado, recomenda-se que as aplicações utilizem o fornecedor da Plataforma de Identidade da [Microsoft (Azure AD)](./configure-authentication-provider-aad.md). A Plataforma de Identidade da Microsoft oferece suporte tanto para contas organizacionais como para contas pessoais da Microsoft.

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>Registe a sua aplicação na Conta Microsoft

1. Aceda às [**inscrições**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) da App no portal Azure. Se necessário, inscreva-se na sua conta Microsoft.
1. Selecione **Novo registo** e, em seguida, introduza um nome de inscrição.
1. Sob **tipos de contas suportadas**, selecione Contas em qualquer **diretório organizacional (qualquer diretório AD AZure - Multitenant) e contas pessoais da Microsoft (por exemplo, Skype, Xbox)**
1. Em **Redirecionar URIs**, selecione **Web** e, em seguida, insira `https://<app-domain-name>/.auth/login/aad/callback` . *\<app-domain-name>* Substitua-o pelo nome de domínio da sua aplicação.  Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Certifique-se de que utiliza o esquema HTTPS na URL.

1. Selecione **Registar**.
1. Copiar o **ID do Pedido (Cliente).** Precisará dela mais tarde.
1. Do painel esquerdo, selecione **Certificados & segredos**  >  **Novo segredo do cliente.** Introduza uma descrição, selecione a duração da validade e **selecione Adicionar**.
1. Copie o valor que aparece na página **Certificados & segredos.** Depois de deixar a página, não voltará a ser exibida.

    > [!IMPORTANT]
    > O valor secreto do cliente (palavra-passe) é uma importante credencial de segurança. Não partilhe a palavra-passe com ninguém nem a distribua dentro de uma aplicação ao cliente.

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>Adicione informações da Conta Microsoft à sua aplicação de Serviço de Aplicações

1. Aceda à sua candidatura no [portal Azure].
1. Selecione **Definições**  >  **Autenticação / Autorização**, e certifique-se de que a **autenticação do serviço de aplicações** está **acesa**.
1. No âmbito **dos Fornecedores de Autenticação**, selecione **Azure Ative Directory**. Selecione **Advanced** no **modo Gestão**. Cole na Identificação de Aplicação (cliente) e no segredo do cliente que obteve anteriormente. Utilização **`https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0`** para o campo url **emitente.**
1. Selecione **OK**.

   O Serviço de Aplicações fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do seu site e APIs. Tem de autorizar os utilizadores no seu código de aplicação.

1. (Opcional) Para restringir o acesso aos utilizadores da conta microsoft, detena **a ação a tomar quando o pedido não for autenticado** para **iniciar sessão com o Azure Ative Directory**. Ao definir esta funcionalidade, a sua aplicação requer que todos os pedidos sejam autenticados. Também redireciona todos os pedidos não autenticados para usar a AAD para autenticação. Note que, uma vez que configuraste o url **do emitente** para usar o inquilino da Conta Microsoft, apenas as contas pessoais autenticarão com sucesso.

   > [!CAUTION]
   > Restringir o acesso desta forma aplica-se a todas as chamadas para a sua app, o que pode não ser desejável para aplicações que tenham uma página inicial disponível ao público, como em muitas aplicações de página única. Para tais aplicações, **permitir pedidos anónimos (nenhuma ação)** pode ser preferido para que a aplicação comece manualmente a autenticação em si. Para mais informações, consulte [o fluxo de autenticação.](overview-authentication-authorization.md#authentication-flow)

1. Selecione **Guardar**.

Está agora pronto para utilizar a Conta microsoft para autenticação na sua aplicação.

## <a name="next-steps"></a><a name="related-content"> </a>Passos seguintes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Portal do Azure]: https://portal.azure.com/
