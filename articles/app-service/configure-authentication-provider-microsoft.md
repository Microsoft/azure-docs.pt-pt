---
title: Configure a autenticação da Microsoft
description: Saiba como configurar a autenticação da Conta Microsoft como fornecedor de identidade para o seu App Service ou aplicação Funções Azure.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 6fe0e39584cbaa001e11f712bb2f60b45dba6914
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203493"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>Configure o seu app service ou app Funções Azure para usar login da Conta Microsoft

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra-lhe como configurar o Azure App Service ou as Funções Azure para utilizar a AAD para suportar logins pessoais da conta da Microsoft.

> [!NOTE]
> Tanto as contas pessoais da Microsoft como as contas organizacionais utilizam o fornecedor de identidade AAD. Neste momento, não é possível configurar este fornecedor de identidade para suportar ambos os tipos de login.

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>Registe a sua aplicação com a Conta Microsoft

1. Vá às [**inscrições**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) da App no portal Azure. Se necessário, inscreva-se na sua conta Microsoft.
1. Selecione **Novo registo**e, em seguida, insira um nome de candidatura.
1. Sob os tipos de **conta suportados**, selecione **Contas em qualquer diretório organizacional (Qualquer diretório AD Azure - Multitenant) e contas pessoais da Microsoft (por exemplo, Skype, Xbox)**
1. Em **Redirecionar URIs,** `https://<app-domain-name>/.auth/login/aad/callback`selecione **Web**, e, em seguida, introduza . Substitua o * \<nome de domínio da aplicação>* pelo nome de domínio da sua aplicação.  Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Certifique-se de utilizar o esquema HTTPS no URL.

1. Selecione **Registar**.
1. Copiar o ID da **Aplicação (Cliente)**. Precisará dela mais tarde.
1. Do painel esquerdo, selecione **Certificados & segredos** > Novo segredo do**cliente.** Introduza uma descrição, selecione a duração da validade e selecione **Adicionar**.
1. Copie o valor que aparece na página de **Certificados & segredos.** Depois de sair da página, não voltará a ser exibido.

    > [!IMPORTANT]
    > O valor secreto do cliente (senha) é uma importante credencial de segurança. Não partilhe a palavra-passe com ninguém nem a distribua dentro de uma aplicação do cliente.

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>Adicione informações da Conta Microsoft à sua aplicação de Serviço de Aplicações

1. Vá à sua aplicação no [portal Azure.]
1. Selecione **Definições** > **Autenticação / Autorização,** e certifique-se de que a autenticação do serviço de aplicações está **a ser ressoada**. **App Service Authentication**
1. No âmbito dos **Fornecedores de Autenticação,** selecione **Diretório Ativo Azure**. Selecione **Advanced** no **modo de gestão**. Colar no ID de Aplicação (cliente) e no segredo do cliente que obteve anteriormente. Utilizar **`https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0`** para o campo **Url emitente.**
1. Selecione **OK**.

   O Serviço de Aplicações fornece a autenticação, mas não restringe o acesso autorizado aos conteúdos e APIs do seu site. Tem de autorizar os utilizadores no seu código de aplicações.

1. (Opcional) Para restringir o acesso aos utilizadores da conta da Microsoft, detete toda a Ação quando o **pedido não for autenticado** para iniciar sessão com o **Diretório Ativo do Azure**. Ao definir esta funcionalidade, a sua aplicação requer que todos os pedidos sejam autenticados. Também redireciona todos os pedidos não autenticados para a utilização de AAD para autenticação. Note que, uma vez que configurou o seu **Url emitente** para utilizar o inquilino da Conta Microsoft, apenas os acréscimos pessoais irão autenticar com sucesso.

   > [!CAUTION]
   > Restringir o acesso desta forma aplica-se a todas as chamadas para a sua app, o que pode não ser desejável para aplicações que tenham uma página inicial disponível ao público, como em muitas aplicações de página única. Para tais aplicações, **permitir pedidos anónimos (nenhuma ação)** pode ser preferido para que a aplicação comece manualmente a autenticação em si. Para mais informações, consulte o [fluxo de autenticação](overview-authentication-authorization.md#authentication-flow).

1. Selecione **Guardar**.

Está agora pronto para utilizar a Conta Microsoft para autenticação na sua aplicação.

## <a name="next-steps"></a><a name="related-content"> </a>Passos seguintes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Portal do Azure]: https://portal.azure.com/
