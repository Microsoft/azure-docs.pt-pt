---
title: Como obter appSource certificado para Diretório Ativo Azure Microsoft Docs
description: Detalhes sobre como obter a sua aplicação AppSource certificada para O Diretório Ativo Azure.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 3ad4efa3b8126a9b9c6557822f61e3bfff3fe120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154887"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Como obter AppSource Certified for Azure Ative Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

O [Microsoft AppSource](https://appsource.microsoft.com/) é um destino para os utilizadores empresariais descobrirem, experimentarem e gerirem aplicações SaaS de linha de negócio (SaaS autónomas e add-on aos produtos Microsoft SaaS existentes).

Para listar uma aplicação Autónoma SaaS no AppSource, a sua aplicação deve aceitar um único sign-on a partir de contas de trabalho de qualquer empresa ou organização que tenha o Azure Ative Directory (Azure AD). O processo de início de sessão deve utilizar os protocolos [OpenID Connect](v1-protocols-openid-connect-code.md) ou [OAuth 2.0.](v1-protocols-oauth-code.md) A integração da SAML não é aceite para a certificação AppSource.

## <a name="guides-and-code-samples"></a>Guias e amostras de código

Se quiser saber como integrar a sua aplicação com o Azure AD utilizando o Open ID connect, siga os nossos guias e amostras de código no [guia do desenvolvedor do Diretório Ativo Azure](v1-overview.md#get-started "Começar com a AD Azure para programadores").

## <a name="multi-tenant-applications"></a>Aplicações multi-inquilino

Uma *aplicação multi-arrendatária* é uma aplicação que aceita inscrições de utilizadores de qualquer empresa ou organização que tenham AD Azure sem exigir uma instância, configuração ou implementação separada. O AppSource recomenda que as aplicações implementem vários arrendamentos para permitir a experiência de experimentação gratuita *de um clique.*

Para permitir o arrendamento multi-arrendamento na sua aplicação, siga estes passos:
1. `Multi-Tenanted` Detete `Yes` imóvel para informações sobre o registo de aplicação no [portal Azure.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) Por padrão, as aplicações criadas no portal Azure são configuradas como *[inquilino único.](#single-tenant-applications)*
1. Atualize o seu código `common` para enviar pedidos para o ponto final. Para tal, atualize o `https://login.microsoftonline.com/{yourtenant}` `https://login.microsoftonline.com/common*`ponto final de .
1. Para algumas plataformas, como asASP .NET, também precisa de atualizar o seu código para aceitar vários emitentes.

Para obter mais informações sobre o multi-arrendamento, consulte Como assinar em qualquer utilizador do [Azure Ative Directory (Azure AD) utilizando o padrão de aplicação multi-inquilino](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="single-tenant-applications"></a>Pedidos de inquilino único

Uma *aplicação de inquilino único* é uma aplicação que apenas aceita inscrições de utilizadores de uma instância Azure AD definida. Os utilizadores externos (incluindo contas de trabalho ou escola de outras organizações, ou contas pessoais) podem inscrever-se numa aplicação de um único inquilino depois de adicionarem cada utilizador como conta de hóspedes à instância azure AD de que a aplicação está registada. 

Pode adicionar utilizadores como contas de hóspedes ao Azure AD através da [colaboração Azure AD B2B](../b2b/what-is-b2b.md) e pode fazê-lo [programaticamente](../../active-directory-b2c/code-samples.md). Ao utilizar o B2B, os utilizadores podem criar um portal de self-service que não requer um convite para iniciar sessão. Para mais informações, consulte o portal self-service para a inscrição de [colaboração Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal).

As aplicações de um único inquilino podem ativar a experiência *Contact Me,* mas se quiser ativar a experiência de experimentação de um clique/livre que o AppSource recomenda, ative o arrendamento multi-arrendamento na sua aplicação.

## <a name="appsource-trial-experiences"></a>Experiências experimentais AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Teste gratuito (experiência experimental liderada pelo cliente)

O teste liderado pelo cliente é a experiência que o AppSource recomenda, uma vez que oferece um acesso de um clique à sua aplicação. O exemplo que se segue mostra como é esta experiência:

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt-text="Shows Free trial for customer-led trial experience"/><ul><li>Utilizador encontra a sua aplicação no Web Site da AppSource</li><li>Seleciona opção 'Teste gratuito'</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt-text="Shows how user is redirected to a URL in your web site"/><ul><li>AppSource redireciona o utilizador para um URL no seu site</li><li>O seu site inicia automaticamente o processo <i>de início de inscrição individual</i> (na carga da página)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt-text="Shows the Microsoft sign-in page"/><ul><li>O utilizador é redirecionado para a página de acesso à Microsoft</li><li>Utilizador fornece credenciais para iniciar sessão</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt-text="Example: Consent page for an application"/><ul><li>Utilizador dá consentimento para a sua aplicação</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>Os completes de sessão e o utilizador são redirecionados para o seu site</li><li>Utilizador inicia o teste gratuito</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Contacte-me (experiência de experimentação liderada por parceiros)

Pode utilizar a experiência experimental do parceiro quando uma operação manual ou a longo prazo tem de acontecer para fornecer ao utilizador/empresa -- por exemplo, a sua aplicação precisa de fornecer máquinas virtuais, instâncias de base de dados ou operações que demoram muito tempo a ser concluídas. Neste caso, depois de o utilizador selecionar o botão **'Teste de Pedido'** e preencher um formulário, o AppSource envia-lhe as informações de contacto do utilizador. Ao receber esta informação, forneça o ambiente e envie as instruções ao utilizador sobre como aceder à experiência experimental:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt-text="Shows Contact me for partner-led trial experience"/><ul><li>Utilizador encontra a sua aplicação no site appSource</li><li>Seleciona a opção 'Contacte-me'</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt-text="Shows an example form with contact info"/><ul><li>Preenche um formulário com informações de contacto</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/usercontact.png" width="55%" alt-text="Shows placeholder for user information"/></td>
            <td>Recebe informações do utilizador</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/setupenv.png" width="55%" alt-text="Shows placeholder for setup environment info"/></td>
            <td>Ambiente de configuração</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/contactcustomer.png" width="55%" alt-text="Shows placeholder for trial info"/></td>
            <td>Contato com o utilizador com informações de experimentação</td>
        </tr>
        </table><br/><br/>
        <ul><li>Recebe as informações do utilizador e configura a instância experimental</li><li>Envia a hiperligação para aceder à sua aplicação ao utilizador</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt-text="Shows the application sign-in screen"/><ul><li>O utilizador acede à sua aplicação e completa o processo de inscrição única</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt-text="Shows an example consent page for an application"/><ul><li>Utilizador dá consentimento para a sua aplicação</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>Os completes de sessão e o utilizador são redirecionados para o seu site</li><li>Utilizador inicia o teste gratuito</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Mais informações

Para mais informações sobre a experiência de teste AppSource, consulte [este vídeo](https://aka.ms/trialexperienceforwebapps). 

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre aplicações de construção que suportem os sign-ins Azure AD, consulte cenários de [autenticação para AD Azure](https://docs.microsoft.com/azure/active-directory/develop/v1-authentication-scenarios).
- Para obter informações sobre como listar a sua aplicação SaaS no AppSource, consulte as [Informações do Parceiro AppSource](https://appsource.microsoft.com/partners)

## <a name="get-support"></a>Obter suporte

Para a integração da AD Azure, usamos [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) com a comunidade para fornecer apoio.

Recomendamos vivamente que faça as suas perguntas sobre stack overflow primeiro e navegue nos problemas existentes para ver se alguém já fez a sua pergunta antes. Certifique-se de que as suas perguntas ou comentários estão marcados [ `[azure-active-directory]` e `[appsource]` ](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Use a secção de comentários seguintes para fornecer feedback e ajudar-nos a refinar e moldar o nosso conteúdo.

<!--Reference style links -->
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:v1-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->
