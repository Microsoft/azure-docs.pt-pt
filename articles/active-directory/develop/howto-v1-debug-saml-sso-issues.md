---
title: Depurar baseado em SAML início de sessão único - Azure Active Directory | Documentos da Microsoft
description: Depure baseado em SAML início de sessão único para aplicações no Azure Active Directory.
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.author: celested
ms.custom: aaddev
ms.reviewer: luleon, hirsin, smalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: b953d979fc5306372c1e751fbbb8942d19fed03f
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648919"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Depurar baseado em SAML início de sessão único para aplicações no Azure Active Directory

Saiba como encontrar e corrigir [início de sessão único](../manage-apps/what-is-single-sign-on.md) problemas para aplicações no Azure Active Directory (Azure AD) que suportam [Security Assertion Markup Language (SAML) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Antes de começar

Recomendamos que instale o [segura de aplicações minha extensão de início de sessão](../user-help/active-directory-saas-access-panel-user-help.md#i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension). Esta extensão de browser torna mais fácil recolher o pedido SAML e as informações de resposta SAML que precisa para a resolução de problemas com o início de sessão único. No caso de não é possível instalar a extensão, este artigo mostra-lhe como resolver problemas com e sem a extensão instalada.

Para transferir e instalar a proteger os meus aplicativos início de sessão extensão, utilize um dos links a seguir.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>Teste baseado em SAML início de sessão único

Para testar baseado em SAML início de sessão único entre o Azure AD e um aplicativo de destino:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como um administrador global ou de outro administrador que está autorizado a gerir aplicações.
1. No painel do lado esquerdo, selecione **do Azure Active Directory**e, em seguida, selecione **aplicações empresariais**. 
1. Na lista de aplicações empresariais, selecione a aplicação para que deseja testar início de sessão único e, em seguida, entre as opções no lado esquerda, selecione **início de sessão único**.
1. Para abrir a baseado em SAML única início de sessão na experiência de teste, aceda a **testar início de sessão único** (etapa 5). Se o **teste** botão está esbatido, terá de preencher e economizar os atributos necessários primeiro a **configuração básica de SAML** secção.
1. Na **testar início de sessão único** painel, utilize as suas credenciais de empresa para iniciar sessão para o aplicativo de destino. Pode iniciar sessão como o utilizador atual, ou como um utilizador diferente. Se iniciar sessão como um utilizador diferente, uma linha de comandos irá pedir-lhe para autenticar.

    ![Página de SAML do teste](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)


Se tiver entrado com êxito nos, o teste foi aprovada. Neste caso, o Azure AD emitido um token de resposta SAML para a aplicação. O aplicativo usado o token SAML para com êxito o início de sessão.

Se tiver um erro na página de início de sessão de empresa ou página da aplicação, utilize uma das secções seguintes para resolver o erro.


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Resolver um erro de início de sessão na sua página de início de sessão da empresa

Quando tentar iniciar sessão, poderá ver um erro na sua empresa início de sessão-página que é semelhante ao seguinte exemplo.

![Erro de início de sessão](./media/howto-v1-debug-saml-sso-issues/error.png)

Para depurar este erro, terá da mensagem de erro e o pedido SAML. Início de sessão extensão My segura de aplicações em automaticamente reúne estas informações e apresenta orientações de resolução no Azure AD. 

### <a name="to-resolve-the-sign-in-error-with-the-myapps-secure-sign-in-extension-installed"></a>Para resolver o erro de início de sessão com o My Apps proteger o início de sessão extensão instalada

1. Quando ocorre um erro, a extensão redireciona-o novamente para o Azure AD **testar início de sessão único** painel. 
1. Sobre o **testar início de sessão único** painel, selecione **transferir o pedido SAML**. 
1. Deverá ver diretrizes com base no erro e os valores no pedido de SAML de resolução específico.
1. Verá uma **corrigi-lo** botão para atualizar automaticamente a configuração no Azure AD para resolver o problema. Se não vir este botão, em seguida, o problema de início de sessão não é devido a uma configuração incorreta no Azure AD.

Se nenhuma resolução é fornecida para o erro de início de sessão, sugerimos que utilize a caixa de texto de comentários em informar-nos.

### <a name="to-resolve-the-error-without-installing-the-myapps-secure-sign-in-extension"></a>Para resolver o erro sem instalar o início de sessão extensão My Apps seguro em

1. Copie a mensagem de erro no canto inferior direito da página. A mensagem de erro inclui:
    - Uma CorrelationID e Timestamp. Estes valores são importantes quando criar um incidente de suporte com a Microsoft, porque eles ajudam os engenheiros de identificar o problema e fornecer uma resolução precisa para o seu problema.
    - Uma declaração de identificar a causa de raiz do problema.
1. Volte para o Azure AD e encontrar os **testar início de sessão único** painel.
1. Na caixa de texto acima **obtenha documentação de orientação de resolução**, cole a mensagem de erro.
1. Clique em **obtenha documentação de orientação de resolução** para apresentar os passos para resolver o problema. As diretrizes poderão necessitar de informações do pedido SAML ou resposta SAML. Se não estiver a utilizar o início de sessão extensão My Apps seguro em, poderá ter uma ferramenta como [Fiddler](https://www.telerik.com/fiddler) para recuperar o pedido SAML e a resposta.
1. Certifique-se de que o destino no pedido de SAML corresponde ao SAML único início de sessão no URL do serviço obtido a partir do Azure AD.
1. Certifique-se de que o emissor no pedido de SAML é o mesmo identificador que configurou para a aplicação no Azure AD. Azure AD utiliza o emissor para localizar uma aplicação no seu diretório.
1. Certifique-se de que AssertionConsumerServiceURL é onde a aplicação espera receber o token SAML do Azure AD. Pode configurar este valor no Azure AD, mas não é obrigatório se faz parte do pedido de SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Resolver um erro de início de sessão na página de aplicativo

Pode iniciar sessão com êxito e, em seguida, ver um erro na página do aplicativo. Isto ocorre quando o Azure AD emitido um token para a aplicação, mas o aplicativo não aceita a resposta.   

Para resolver o problema, siga estes passos:

1. Se a aplicação na galeria do Azure AD, certifique-se de que seguiu todos os passos para integrar a aplicação com o Azure AD. Para obter as instruções de integração para a sua aplicação, consulte a [lista de tutoriais de integração de aplicações de SaaS](../saas-apps/tutorial-list.md).
1. Recuperar a resposta SAML.
    - Se a extensão My segura de aplicações início de sessão estiver instalada, do **testar início de sessão único** painel, clique em **transferir a resposta SAML**.
    - Se a extensão não estiver instalada, utilize uma ferramenta como [Fiddler](https://www.telerik.com/fiddler) para recuperar a resposta SAML. 
1. Observe que esses elementos no token SAML resposta:
    - Identificador exclusivo do utilizador do valor de NameID e de formato
    - Afirmações emitidas no token
    - Certificado utilizado para assinar o token. 

     Para obter mais informações sobre a resposta SAML, consulte [protocolo SAML de início de sessão único](single-sign-on-saml-protocol.md).

1. Agora que viu a resposta SAML, veja [erro na página de uma aplicação depois de iniciar sessão](../manage-apps/application-sign-in-problem-application-error.md) para obter orientações sobre como resolver o problema. 
1. Se ainda não conseguir iniciar sessão com êxito, pode pedir o fornecedor do aplicativo o que está em falta na resposta SAML.


## <a name="next-steps"></a>Passos Seguintes

Agora que o início de sessão único está trabalhando para seu aplicativo, poderia [automatizar o aprovisionamento de utilizador e a aplicações SaaS de desaprovisionamento](../manage-apps/user-provisioning.md) ou [começar com o acesso condicional](../conditional-access/app-based-conditional-access.md).
