---
title: Configure a autenticação do Facebook
description: Saiba como configurar a autenticação do Facebook como fornecedor de identidade para o seu Serviço de Aplicações ou aplicação Azure Functions.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: bc639eaea76b3309d6ed047e73c726040da19639
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078014"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Configure o seu Serviço de Aplicações ou app Azure Functions para usar o login do Facebook

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra como configurar o Azure App Service ou Azure Functions para usar o Facebook como fornecedor de autenticação.

Para completar o procedimento neste artigo, precisa de uma conta no Facebook que tenha um endereço de e-mail verificado e um número de telemóvel. Para criar uma nova conta no Facebook, vá a [facebook.com].

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Registe a sua candidatura no Facebook

1. Vá ao site do [Facebook Developers] e inscreva-se com as credenciais da sua conta do Facebook.

   Se não tiver uma conta no Facebook para desenvolvedores, **selecione Get Started** e siga as etapas de registo.
1. Selecione **as minhas**  >  **aplicações Adicionar nova aplicação.**
1. No campo **Display Name:**
   1. Digite um nome único para a sua aplicação.
   1. Forneça o seu **Email de Contacto.**
   1. Selecione **Criar ID de aplicação.**
   1. Complete a verificação de segurança.

   Abre o painel de desenvolvedores da sua nova aplicação do Facebook.
1. Selecione **o Início de**  >  **Sessão do Facebook** do Dashboard  >  **Configurar** a  >  **Web**.
1. Na navegação à esquerda no **Facebook Login**, selecione **Definições**.
1. No campo **DE REorção de IU** válido, insira `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` . Lembre-se de substituir `<app-name>` pelo nome da sua aplicação Azure App Service.
1. Selecione **Guardar alterações**.
1. No painel esquerdo, selecione **Definições**  >  **Básicas**. 
1. No campo **App Secret,** selecione **Show**. Copie os valores do **ID** da App e **da App Secret.** Usa-os mais tarde para configurar a sua aplicação de Serviço de Aplicações em Azure.

   > [!IMPORTANT]
   > O segredo da aplicação é uma importante credencial de segurança. Não partilhe este segredo com ninguém nem o distribua dentro de uma aplicação ao cliente.
   >

1. A conta do Facebook que usou para registar a aplicação é administradora da aplicação. Neste momento, apenas os administradores podem inscrever-se nesta aplicação.

   Para autenticar outras contas do Facebook, selecione **App Review** e permita tornar **\<your-app-name> público** o acesso do público em geral à aplicação através da autenticação do Facebook.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Adicione informações do Facebook à sua aplicação

1. Inscreva-se no [portal Azure] e navegue para a sua aplicação.
1. Selecione **Autenticação** no menu à esquerda. Clique **em Adicionar Fornecedor de identidade**.
1. Selecione o **Facebook** no dropdown do fornecedor de identidade. Cole nos valores de ID e App Secret que obteve anteriormente.

    O segredo será armazenado como uma [configuração](./configure-common.md#configure-app-settings) de aplicação adesivo chamado `FACEBOOK_PROVIDER_AUTHENTICATION_SECRET` . Pode atualizar essa definição mais tarde para utilizar [referências do Key Vault](./app-service-key-vault-references.md) se desejar gerir o segredo no Cofre de Chaves Azure.

1. Se este for o primeiro fornecedor de identidade configurado para a aplicação, também será solicitado com uma secção de **configurações de autenticação do Serviço de Aplicações.** Caso contrário, pode passar para o próximo passo.
    
    Estas opções determinam como a sua aplicação responde a pedidos não autenticados, e as seleções predefinidos redirecionarão todos os pedidos para iniciar sessão com este novo fornecedor. Pode alterar o comportamento agora ou ajustar estas definições mais tarde a partir do ecrã principal de **Autenticação,** escolhendo **Editar** ao lado **das definições de Autenticação**. Para saber mais sobre estas opções, consulte [o fluxo de autenticação.](overview-authentication-authorization.md#authentication-flow)

1. (Opcional) Clique **em seguida: Scopes** e adicione quaisquer âmbitos necessários pela aplicação. Estes serão solicitados no momento de início de sessão para fluxos baseados no navegador.
1. Clique em **Adicionar**.

Está agora pronto para usar o Facebook para autenticação na sua aplicação. O fornecedor será listado no ecrã de **Autenticação.** A partir daí, pode editar ou eliminar esta configuração do fornecedor.

## <a name="next-steps"></a><a name="related-content"> </a>Passos seguintes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->
[Desenvolvedores do Facebook]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portal do Azure]: https://portal.azure.com/
