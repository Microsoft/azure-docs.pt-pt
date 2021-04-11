---
title: Configure a autenticação do Google
description: Saiba como configurar a autenticação do Google como fornecedor de identidade para o seu Serviço de Aplicações ou aplicação Azure Functions.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: f6bec32fa928e840569ed95c35a056db91ea9737
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077997"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>Configure o seu Serviço de Aplicações ou app Azure Functions para usar o login do Google

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra-lhe como configurar o Azure App Service ou Azure Functions para usar o Google como um fornecedor de autenticação.

Para completar o procedimento neste tópico, tem de ter uma conta google que tenha um endereço de e-mail verificado. Para criar uma nova conta do Google, aceda a [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register-your-application-with-google"></a><a name="register"> </a>Registe a sua aplicação com o Google

1. Siga a documentação do Google no [Google Sign-In para aplicações do lado do servidor](https://developers.google.com/identity/sign-in/web/server-side-flow) para criar um ID de cliente e segredo de cliente. Não há necessidade de fazer alterações de código. Basta utilizar as seguintes informações:
    - Para **Origens JavaScript Autorizadas,** utilize `https://<app-name>.azurewebsites.net` com o nome da sua aplicação em *\<app-name>* .
    - Para **Redirecionar autorizado URI,** utilize `https://<app-name>.azurewebsites.net/.auth/login/google/callback` .
1. Copie o ID da aplicação e os valores secretos da App.

    > [!IMPORTANT]
    > O segredo da App é uma importante credencial de segurança. Não partilhe este segredo com ninguém nem o distribua dentro de uma aplicação ao cliente.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Adicione informações do Google à sua aplicação

1. Inscreva-se no [portal Azure] e navegue para a sua aplicação.
1. Selecione **Autenticação** no menu à esquerda. Clique **em Adicionar Fornecedor de identidade**.
1. Selecione o **Google** no dropdown do fornecedor de identidade. Cole nos valores de ID e App Secret que obteve anteriormente.

    O segredo será armazenado como uma [configuração](./configure-common.md#configure-app-settings) de aplicação adesivo chamado `GOOGLE_PROVIDER_AUTHENTICATION_SECRET` . Pode atualizar essa definição mais tarde para utilizar [referências do Key Vault](./app-service-key-vault-references.md) se desejar gerir o segredo no Cofre de Chaves Azure.

1. Se este for o primeiro fornecedor de identidade configurado para a aplicação, também será solicitado com uma secção de **configurações de autenticação do Serviço de Aplicações.** Caso contrário, pode passar para o próximo passo.
    
    Estas opções determinam como a sua aplicação responde a pedidos não autenticados, e as seleções predefinidos redirecionarão todos os pedidos para iniciar sessão com este novo fornecedor. Pode alterar o comportamento agora ou ajustar estas definições mais tarde a partir do ecrã principal de **Autenticação,** escolhendo **Editar** ao lado **das definições de Autenticação**. Para saber mais sobre estas opções, consulte [o fluxo de autenticação.](overview-authentication-authorization.md#authentication-flow)

1. (Opcional) Clique **em seguida: Scopes** e adicione quaisquer âmbitos necessários pela aplicação. Estes serão solicitados no momento de início de sessão para fluxos baseados no navegador.
1. Clique em **Adicionar**.

Está agora pronto para usar o Google para autenticação na sua aplicação. O fornecedor será listado no ecrã de **Autenticação.** A partir daí, pode editar ou eliminar esta configuração do fornecedor.

## <a name="next-steps"></a><a name="related-content"> </a>Passos seguintes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Portal do Azure]: https://portal.azure.com/

