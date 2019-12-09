---
title: Depurar logon único baseado em SAML-Azure Active Directory | Microsoft Docs
description: Depurar logon único baseado em SAML para aplicativos no Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26c3502567df7776e106ae9301aa7ba315cc12cc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917612"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Depurar logon único baseado em SAML para aplicativos no Azure Active Directory

Saiba como localizar e corrigir problemas de [logon único](../manage-apps/what-is-single-sign-on.md) para aplicativos no Azure Active Directory (AD do Azure) que dão suporte a [Security Assertion Markup Language (SAML) 2,0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Antes de começar

É recomendável instalar a [extensão de entrada segura de meus aplicativos](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension). Essa extensão de navegador facilita a coleta de informações de resposta SAML e de solicitação SAML necessárias para resolver problemas com o logon único. Caso não seja possível instalar a extensão, este artigo mostra como resolver problemas com e sem a extensão instalada.

Para baixar e instalar a extensão de entrada segura de meus aplicativos, use um dos links a seguir.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Testar logon único baseado em SAML

Para testar o logon único baseado em SAML entre o Azure AD e um aplicativo de destino:

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador global ou outro administrador autorizado a gerenciar aplicativos.
1. Na folha à esquerda, selecione **Azure Active Directory**e, em seguida, selecione **aplicativos empresariais**. 
1. Na lista de aplicativos empresariais, selecione o aplicativo para o qual você deseja testar o logon único e, em seguida, nas opções à esquerda, selecione **logon único**.
1. Para abrir a experiência de teste de logon único baseada em SAML, vá para **testar logon único** (etapa 5). Se o botão **testar** estiver esmaecido, você precisará preencher e salvar os atributos necessários primeiro na seção **configuração básica do SAML** .
1. Na folha **testar logon único** , use suas credenciais corporativas para entrar no aplicativo de destino. Você pode entrar como o usuário atual ou como um usuário diferente. Se você entrar como um usuário diferente, um prompt pedirá que você autentique.

    ![Captura de tela mostrando a página testar SSO do SAML](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)

Se você tiver entrado com êxito, o teste passou. Nesse caso, o Azure AD emitiu um token de resposta SAML para o aplicativo. O aplicativo usou o token SAML para conectá-lo com êxito.

Se você tiver um erro na página de entrada da empresa ou na página do aplicativo, use uma das próximas seções para resolver o erro.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Resolver um erro de entrada na página de entrada da sua empresa

Ao tentar entrar, você poderá ver um erro na página de entrada da empresa que é semelhante ao exemplo a seguir.

![Exemplo mostrando um erro na página de entrada da empresa](./media/howto-v1-debug-saml-sso-issues/error.png)

Para depurar esse erro, você precisará da mensagem de erro e da solicitação SAML. A extensão de entrada segura de meus aplicativos reúne automaticamente essas informações e exibe diretrizes de resolução no Azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Para resolver o erro de entrada com a extensão de entrada segura de meus aplicativos instalada

1. Quando ocorre um erro, a extensão redireciona você de volta para a folha de **logon único de teste** do Azure AD.
1. Na folha **testar logon único** , selecione **baixar a solicitação SAML**.
1. Você deve ver diretrizes de resolução específicas com base no erro e nos valores na solicitação SAML.
1. Você verá um botão **corrigir** para atualizar automaticamente a configuração no Azure ad para resolver o problema. Se você não vir esse botão, o problema de entrada não será devido a uma configuração incorreta no Azure AD.

Se nenhuma resolução for fornecida para o erro de entrada, sugerimos que você use a caixa de texto comentários para informar-nos.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Para resolver o erro sem instalar a extensão de entrada segura de meus aplicativos

1. Copie a mensagem de erro no canto inferior direito da página. A mensagem de erro inclui:
    - Uma CorrelationId e um carimbo de data/hora. Esses valores são importantes quando você cria um caso de suporte com a Microsoft porque eles ajudam os engenheiros a identificar o problema e fornecem uma resolução precisa para o seu problema.
    - Uma instrução que identifica a causa raiz do problema.
1. Volte para o Azure AD e encontre a folha **testar logon único** .
1. Na caixa de texto acima, **obtenha diretrizes de resolução**, Cole a mensagem de erro.
1. Clique em **obter diretrizes de resolução** para exibir as etapas para resolver o problema. As diretrizes podem exigir informações da solicitação SAML ou da resposta SAML. Se você não estiver usando a extensão de entrada segura de meus aplicativos, talvez precise de uma ferramenta como o [Fiddler](https://www.telerik.com/fiddler) para recuperar a solicitação e a resposta SAML.
1. Verifique se o destino na solicitação SAML corresponde à URL do serviço de logon único SAML obtida do Azure AD.
1. Verifique se o emissor na solicitação SAML é o mesmo identificador que você configurou para o aplicativo no Azure AD. O Azure AD usa o emissor para encontrar um aplicativo em seu diretório.
1. Verifique se AssertionConsumerServiceURL é onde o aplicativo espera receber o token SAML do Azure AD. Você pode configurar esse valor no Azure AD, mas ele não é obrigatório se fizer parte da solicitação SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Resolver um erro de entrada na página do aplicativo

Você pode entrar com êxito e ver um erro na página do aplicativo. Isso ocorre quando o Azure AD emitiu um token para o aplicativo, mas o aplicativo não aceita a resposta.

Para resolver o problema, siga estes passos:

1. Se o aplicativo estiver na galeria do Azure AD, verifique se você seguiu todas as etapas para integrar o aplicativo com o Azure AD. Para encontrar as instruções de integração para seu aplicativo, consulte a [lista de tutoriais de integração de aplicativos SaaS](../saas-apps/tutorial-list.md).
1. Recupere a resposta SAML.
    - Se a extensão de entrada segura de meus aplicativos estiver instalada, na folha **testar logon único** , clique em **baixar a resposta SAML**.
    - Se a extensão não estiver instalada, use uma ferramenta como o [Fiddler](https://www.telerik.com/fiddler) para recuperar a resposta SAML.
1. Observe esses elementos no token de resposta SAML:
   - Identificador exclusivo de usuário do valor NameID e do formato
   - Declarações emitidas no token
   - Certificado usado para assinar o token.

     Para obter mais informações sobre a resposta SAML, consulte [protocolo SAML de logon único](single-sign-on-saml-protocol.md).

1. Agora que você revisou a resposta SAML, consulte [erro na página de um aplicativo depois de entrar](../manage-apps/application-sign-in-problem-application-error.md) para obter orientação sobre como resolver o problema. 
1. Se ainda não for possível entrar com êxito, você poderá perguntar ao fornecedor do aplicativo o que está faltando na resposta SAML.

## <a name="next-steps"></a>Passos seguintes

Agora que o logon único está funcionando para seu aplicativo, você pode [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS](../manage-apps/user-provisioning.md) ou começar a usar o [acesso condicional](../conditional-access/app-based-conditional-access.md).
