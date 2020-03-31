---
title: Debug SAML com base em um único sign-on - Azure Ative Diretório / Microsoft Docs
description: Debug SAML com base em inscrição única para aplicações no Diretório Ativo Azure.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, paulgarn
ROBOTS: NOINDEX
ms.openlocfilehash: 5d92b43b47a20a75d2c8b6becb69cfee5829e80f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154853"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Debug SAML com base em assinaturaúnica para aplicações no Diretório Ativo Azure

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Saiba como encontrar e corrigir problemas [de inscrição simples](../manage-apps/what-is-single-sign-on.md) para aplicações no Azure Ative Directory (Azure AD) que suportam a Linguagem de Marcação de Afirmação de [Segurança (SAML) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Antes de começar

Recomendamos a instalação da [extensão de sessão de sinalização segura das Minhas Aplicações.](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension) Esta extensão do navegador facilita a recolha do pedido SAML e da informação de resposta SAML que necessita para resolver problemas com um único sinal. Caso não possa instalar a extensão, este artigo mostra-lhe como resolver problemas com e sem a extensão instalada.

Para descarregar e instalar a Extensão de Sinal seguro das Minhas Aplicações, utilize um dos seguintes links.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Teste de assinatura única baseada em SAML

Para testar um único signo baseado em SAML entre a AD Azure e uma aplicação-alvo:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global ou outro administrador autorizado a gerir aplicações.
1. Na lâmina esquerda, selecione **Azure Ative Directory**, e, em seguida, selecione **aplicações Enterprise**. 
1. A partir da lista de aplicações da empresa, selecione o pedido para o qual pretende testar um único sinal e, em seguida, a partir das opções à esquerda selecione **Single sign-on**.
1. Para abrir a experiência de teste de inscrição única baseada no SAML, vá ao Teste de **um único sinal (passo** 5). Se o botão **Teste** estiver acinzentado, tem de preencher e guardar os atributos necessários primeiro na secção **de Configuração SAML Básica.**
1. No teste de uma **lâmina de inscrição única,** utilize as suas credenciais corporativas para iniciar sessão na aplicação-alvo. Pode iniciar sessão como utilizador atual ou como utilizador diferente. Se iniciar sessão como um utilizador diferente, uma solicitação pedir-lhe-á que autentique.

    ![Screenshot mostrando a página SAML SSO do teste](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)

Se tiver sucesso, o teste já passou. Neste caso, a Azure AD emitiu um sinal de resposta SAML ao pedido. A aplicação usou o símbolo SAML para o inscrever com sucesso.

Se tiver um erro na página de sessão de sessão da empresa ou na página da aplicação, utilize uma das seguintes secções para resolver o erro.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Resolva um erro de inscrição na página de sessão da sua empresa

Quando tentar iniciar sessão, poderá ver um erro na página de sessão da sua empresa semelhante ao seguinte exemplo.

![Exemplo mostrando um erro na página de inscrição da empresa](./media/howto-v1-debug-saml-sso-issues/error.png)

Para desinserir este erro, precisa da mensagem de erro e do pedido SAML. A Extensão de Acesso Seguro das Minhas Aplicações reúne automaticamente esta informação e exibe orientações de resolução sobre o Azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Para resolver o erro de inscrição com a extensão de sessão de sinalização segura das minhas aplicações instalada

1. Quando ocorre um erro, a extensão redireciona-o de volta para a lâmina **de sinalização única** do Azure AD Test.
1. Na lâmina **de sinalização single do Teste,** selecione **Descarregue o pedido SAML**.
1. Deve consultar orientações específicas de resolução com base no erro e nos valores do pedido SAML.
1. Verá um botão **Fix-lo** para atualizar automaticamente a configuração em Azure AD para resolver o problema. Se não vir este botão, então a questão do início de sessão não se deve a uma configuração errada no Azure AD.

Se não for prevista qualquer resolução para o erro de entrada, sugerimos que utilize a caixa de texto de feedback para nos informar.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Para resolver o erro sem instalar a extensão de 'Sign-in' "Secure Sign-in" das Minhas Aplicações

1. Copie a mensagem de erro no canto inferior direito da página. A mensagem de erro inclui:
    - Um CorrelationID e carimbo de tempo. Estes valores são importantes quando se cria um caso de suporte com a Microsoft porque ajudam os engenheiros a identificar o seu problema e a fornecer uma resolução precisa ao seu problema.
    - Uma declaração identificando a causa principal do problema.
1. Volte para o Azure AD e encontre a lâmina **de inscrição única** do Teste.
1. Na caixa de texto acima Obtenha orientação de **resolução,** cola a mensagem de erro.
1. Clique em **obter orientação** de resolução para mostrar passos para resolver o problema. A orientação pode requerer informações do pedido SAML ou da resposta SAML. Se não estiver a utilizar a extensão de acesso seguro das Minhas Aplicações, poderá necessitar de uma ferramenta como o [Fiddler](https://www.telerik.com/fiddler) para recuperar o pedido e resposta do SAML.
1. Verifique se o destino no pedido SAML corresponde ao URL de Serviço de Assinatura Única SAML obtido a partir de Azure AD.
1. Verifique se o emitente no pedido SAML é o mesmo identificador configurado para a aplicação em Azure AD. A Azure AD usa o emitente para encontrar uma aplicação no seu diretório.
1. Verifique o AfirmaçãoConsumerServiceURL é onde a aplicação espera receber o token SAML da Azure AD. Pode configurar este valor em Azure AD, mas não é obrigatório se fizer parte do pedido da SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Resolver um erro de inscrição na página de aplicação

Pode iniciar sessão com sucesso e depois ver um erro na página da aplicação. Isto ocorre quando a Azure AD emitiu um sinal para o pedido, mas o pedido não aceita a resposta.

Para resolver o erro, siga estes passos:

1. Se a aplicação estiver na Galeria AD Azure, verifique se seguiu todas as etapas para integrar a aplicação com a Azure AD. Para encontrar as instruções de integração da sua candidatura, consulte a [lista de tutoriais de integração de aplicações SaaS.](../saas-apps/tutorial-list.md)
1. Recupere a resposta SAML.
    - Se a extensão de sinal de segurança das Minhas Aplicações for instalada, a partir da lâmina **de sinalização single do Teste,** clique em **baixar a resposta SAML**.
    - Se a extensão não estiver instalada, utilize uma ferramenta como o [Fiddler](https://www.telerik.com/fiddler) para recuperar a resposta SAML.
1. Note estes elementos no símbolo de resposta SAML:
   - Identificador único do utilizador do valor e formato do NameID
   - Reclamações emitidas no símbolo
   - Certificado usado para assinar o símbolo.

     Para obter mais informações sobre a resposta SAML, consulte o [protocolo SAML de sign-on único](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

1. Agora que reviu a resposta do SAML, consulte [error na página de uma aplicação depois de iniciar sessão](../manage-apps/application-sign-in-problem-application-error.md) para obter orientações sobre como resolver o problema. 
1. Se ainda não conseguir assinar com sucesso, pode perguntar ao fornecedor de aplicações o que falta na resposta SAML.

## <a name="next-steps"></a>Passos seguintes

Agora que o único sinal está a funcionar para a sua aplicação, pode [automatizar o fornecimento e desprovisionamento de utilizadores para aplicações SaaS](../manage-apps/user-provisioning.md) ou [começar com](../conditional-access/app-based-conditional-access.md)o Acesso Condicional .
