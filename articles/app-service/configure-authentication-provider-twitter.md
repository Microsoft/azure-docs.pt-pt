---
title: Configurar a autenticação do Twitter
description: Saiba como configurar a autenticação do Twitter como fornecedor de identidade para o seu Serviço de Aplicações ou aplicação Azure Functions.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 6ecce954991d9f3901c54a6f87fc803b32469862
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077980"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Configure o seu Serviço de Aplicações ou app Azure Functions para usar o login do Twitter

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra como configurar o Azure App Service ou Azure Functions para usar o Twitter como fornecedor de autenticação.

Para completar o procedimento neste artigo, precisa de uma conta no Twitter que tenha um endereço de e-mail verificado e número de telefone. Para criar uma nova conta no Twitter, vá a [twitter.com].

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Registe a sua candidatura no Twitter

1. Inscreva-se no [portal Azure] e vá à sua candidatura. Copie o seu **URL.** Vai usá-lo para configurar a sua aplicação do Twitter.
1. Vá ao site do [Twitter Developers,] inscreva-se com as credenciais da sua conta de Twitter e selecione **Criar uma aplicação**.
1. Insira o **nome da App** e a descrição da **Aplicação** para a sua nova aplicação. Cole o **URL** da sua aplicação no campo URL do **Site.** Na secção **URLs callback, insira** o URL HTTPS da sua aplicação de Serviço de Aplicações e apendam o caminho `/.auth/login/twitter/callback` . Por exemplo, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. Na parte inferior da página, digite pelo menos 100 caracteres em **Diga-nos como esta aplicação será usada,** em seguida, selecione **Create**. Clique em **Criar** novamente no pop-up. Os detalhes da aplicação são apresentados.
1. Selecione o **separador Chaves e Tokens de acesso.**

   Tome nota destes valores:
   - Chave API
   - Chave secreta da API

   > [!IMPORTANT]
   > A chave secreta da API é uma importante credencial de segurança. Não partilhe este segredo com ninguém nem o distribua com a sua aplicação.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Adicione informações do Twitter à sua aplicação

1. Inscreva-se no [portal Azure] e navegue para a sua aplicação.
1. Selecione **Autenticação** no menu à esquerda. Clique **em Adicionar Fornecedor de identidade**.
1. Selecione o **Twitter** no dropdown do fornecedor de identidade. Cole nos `API key` valores e `API secret key` valores que obteve anteriormente.

    O segredo será armazenado como uma [configuração](./configure-common.md#configure-app-settings) de aplicação adesivo chamado `TWITTER_PROVIDER_AUTHENTICATION_SECRET` . Pode atualizar essa definição mais tarde para utilizar [referências do Key Vault](./app-service-key-vault-references.md) se desejar gerir o segredo no Cofre de Chaves Azure.

1. Se este for o primeiro fornecedor de identidade configurado para a aplicação, também será solicitado com uma secção de **configurações de autenticação do Serviço de Aplicações.** Caso contrário, pode passar para o próximo passo.
    
    Estas opções determinam como a sua aplicação responde a pedidos não autenticados, e as seleções predefinidos redirecionarão todos os pedidos para iniciar sessão com este novo fornecedor. Pode alterar o comportamento agora ou ajustar estas definições mais tarde a partir do ecrã principal de **Autenticação,** escolhendo **Editar** ao lado **das definições de Autenticação**. Para saber mais sobre estas opções, consulte [o fluxo de autenticação.](overview-authentication-authorization.md#authentication-flow)

1. Clique em **Adicionar**.

Está agora pronto para usar o Twitter para autenticação na sua aplicação. O fornecedor será listado no ecrã de **Autenticação.** A partir daí, pode editar ou eliminar esta configuração do fornecedor.

## <a name="next-steps"></a><a name="related-content"> </a>Passos seguintes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Desenvolvedores do Twitter]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Portal do Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
