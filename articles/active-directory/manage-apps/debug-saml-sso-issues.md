---
title: Debug SAML- Azure Ative Directory
description: Debug SAML com base em um único sign-on para aplicações no Azure Ative Directory.
services: active-directory
ms.author: kenwith
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: troubleshooting
ms.workload: identity
ms.date: 02/18/2019
ms.reviewer: luleon, hirsin, paulgarn
ms.openlocfilehash: f8eb00a2a88c6e26dd5361097f17990469caa8f3
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326065"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Depurar o início de sessão único baseado em SAML para as aplicações no Azure Active Directory

Saiba como encontrar e corrigir problemas [de inscrição única](what-is-single-sign-on.md) para aplicações no Azure Ative Directory (Azure AD) que utilizam um único sinal baseado em SAML. 

## <a name="before-you-begin"></a>Antes de começar

Recomendamos a instalação da [extensão de inscrição segura das minhas apps](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension). Esta extensão do navegador facilita a recolha do pedido SAML e das informações de resposta SAML de que necessita para resolver problemas com um único sing-on. Caso não possa instalar a extensão, este artigo mostra-lhe como resolver problemas com e sem a extensão instalada.

Para descarregar e instalar a extensão de inscrição segura das minhas apps, utilize um dos seguintes links.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Teste de sinscrição única baseada em SAML

Para testar o sign-on único baseado em SAML entre a Azure AD e uma aplicação-alvo:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global ou outro administrador autorizado a gerir aplicações.
1. Na lâmina esquerda, selecione **Azure Ative Directory**e, em seguida, selecione **aplicações Enterprise**. 
1. A partir da lista de aplicações empresariais, selecione a aplicação para a qual pretende testar um único sinal de sôr-in e, em seguida, a partir das opções no **seleto único à**esquerda .
1. Para abrir a experiência de teste de súmis com base em SAML, vá ao **Teste de um único sign-on** (passo 5). Se o botão **Teste** estiver acinzentado, tem de preencher e guardar primeiro os atributos necessários na secção **Configuração Básica SAML.**
1. Na lâmina **de inscrição única** do Teste, utilize as suas credenciais corporativas para iniciar snutil na aplicação-alvo. Pode iniciar sposição como utilizador atual ou como um utilizador diferente. Se iniciar sus como um utilizador diferente, um pedido será solicitado para autenticar.

    ![Screenshot mostrando a página SSO do teste SAML](./media/debug-saml-sso-issues/test-single-sign-on.png)

Se tiver assinado com sucesso, o teste passou. Neste caso, a Azure AD emitiu um sinal de resposta SAML ao pedido. A aplicação usou o símbolo SAML para o iniciar com sucesso.

Se tiver um erro na página de sindução da empresa ou na página da aplicação, utilize uma das secções seguintes para resolver o erro.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Resolva um erro de inscrição na página de sindução da sua empresa

Quando tentar iniciar sôm, poderá ver um erro na página de sindução da sua empresa que é semelhante ao exemplo a seguir.

![Exemplo mostrando um erro na página de insusição da empresa](./media/debug-saml-sso-issues/error.png)

Para depurar este erro, precisa da mensagem de erro e do pedido DEL. A extensão de início de sposição "My Apps Secure" reúne automaticamente estas informações e exibe orientações de resolução sobre a Azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Para resolver o erro de inscrição com a extensão de inscrição segura das minhas apps instalada

1. Quando ocorre um erro, a extensão redireciona-o de volta para a lâmina **de sinalização Ad test** Azure.
1. Na lâmina **de inscrição única** do teste, selecione **Descarregue o pedido SAML**.
1. Deve consultar orientações de resolução específicas com base no erro e nos valores do pedido DA SAML.
1. Verá um botão **Fix it** para atualizar automaticamente a configuração em AD Azure para resolver o problema. Se não vir este botão, então a questão da inscrição não se deve a uma configuração errada no Azure AD.

Se não for fornecida nenhuma resolução para o erro de entrada, sugerimos que utilize a caixa de texto de feedback para nos informar.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Para resolver o erro sem instalar a extensão de inscrição segura das minhas apps

1. Copie a mensagem de erro no canto inferior direito da página. A mensagem de erro inclui:
    - Uma CorrelationID e um timetamp. Estes valores são importantes quando cria um caso de suporte com a Microsoft porque ajudam os engenheiros a identificar o seu problema e fornecem uma resolução precisa para o seu problema.
    - Uma declaração que identifica a causa principal do problema.
1. Volte para a Azure AD e encontre a lâmina **de sinalização única do teste.**
1. Na caixa de texto acima **Obtenha orientação de resolução,** cole a mensagem de erro.
1. Clique **em Obter orientação de resolução** para exibir etapas para resolver o problema. A orientação pode requerer informações do pedido SAML ou da resposta SAML. Se não estiver a utilizar a extensão de entrada segura das minhas apps, poderá necessitar de uma ferramenta como [o Fiddler](https://www.telerik.com/fiddler) para recuperar o pedido e resposta do SAML.
1. Verifique se o destino no pedido SAML corresponde ao URL de Serviço único Sign-On SAML obtido a partir da Azure AD.
1. Verifique se o emitente no pedido DO SAML é o mesmo identificador que configuraste para o pedido em Azure AD. A Azure AD usa o emitente para encontrar uma aplicação no seu diretório.
1. Verifique a AfirmaçãoConsumerServiceURL é onde a aplicação espera receber o token SAML da Azure AD. Você pode configurar este valor em Azure AD, mas não é obrigatório se faz parte do pedido da SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Resolver um erro de inscrição na página de aplicação

Pode iniciar sôm com sucesso e depois ver um erro na página da aplicação. Isto ocorre quando a Azure AD emitiu um sinal para o pedido, mas o pedido não aceita a resposta.

Para resolver o erro, siga estes passos ou veja este [pequeno vídeo sobre como usar a Azure AD para resolver problemas saml SSO](https://www.youtube.com/watch?v=poQCJK0WPUk&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0&index=8):

1. Se a aplicação estiver na Galeria AD Azure, verifique se seguiu todos os passos para integrar a aplicação com a Azure AD. Para encontrar as instruções de integração da sua candidatura, consulte a [lista de tutoriais de integração de aplicações SaaS.](../saas-apps/tutorial-list.md)
1. Recupere a resposta do SAML.
    - Se a extensão de inscrição segura das minhas apps for instalada, a partir da lâmina **de sinal único de teste,** clique em baixar a resposta **SAML**.
    - Se a extensão não for instalada, utilize uma ferramenta como [o Fiddler](https://www.telerik.com/fiddler) para recuperar a resposta SAML.
1. Note estes elementos no token de resposta SAML:
   - Identificador exclusivo do utilizador do valor e formato NameID
   - Reclamações emitidas no token
   - Certificado usado para assinar o token.

     Para obter mais informações sobre a resposta [SAML,](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)consulte o protocolo SAML de assinatura única .

1. Agora que reviu a resposta DO SAML, consulte [Error na página de uma aplicação depois de iniciar sessão](application-sign-in-problem-application-error.md) para obter orientações sobre como resolver o problema. 
1. Se ainda não conseguir assinar com sucesso, pode perguntar ao vendedor de aplicações o que falta na resposta SAML.

## <a name="next-steps"></a>Passos seguintes

Agora que o único sign-on está a funcionar com a sua aplicação, pode [automatizar o fornecimento e desaprovisionamento do utilizador às aplicações saaS](../app-provisioning/user-provisioning.md) ou [começar com o Acesso Condicional.](../conditional-access/app-based-conditional-access.md)
