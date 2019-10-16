---
title: Como obter o AppSource Certified para Azure Active Directory | Microsoft Docs
description: Detalhes sobre como fazer com que seu aplicativo AppSource certificado para Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 139c7c2e6736eeb3e78ac0aab913378ac84160e1
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374085"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Como obter o AppSource Certified para Azure Active Directory

[Microsoft AppSource](https://appsource.microsoft.com/) é um destino para os usuários empresariais descobrirem, experimentarem e gerenciarem aplicativos SaaS de linha de negócios (SaaS autônomo e complemento para produtos SaaS da Microsoft existentes).

Para listar um aplicativo SaaS autônomo no AppSource, seu aplicativo deve aceitar o logon único de contas corporativas de qualquer empresa ou organização que tenha Azure Active Directory (Azure AD). O processo de entrada deve usar o [OpenID Connect](v1-protocols-openid-connect-code.md) ou os protocolos [OAuth 2,0](v1-protocols-oauth-code.md) . A integração SAML não é aceita para a certificação AppSource.

## <a name="guides-and-code-samples"></a>Guias e exemplos de código

Se você quiser saber mais sobre como integrar seu aplicativo com o Azure AD usando o Open ID Connect, siga nossos guias e exemplos de código no [Guia do desenvolvedor de Azure Active Directory](v1-overview.md#get-started "Introdução ao Azure AD para desenvolvedores").

## <a name="multi-tenant-applications"></a>Aplicativos multilocatários

Um *aplicativo multilocatário* é um aplicativo que aceita entradas de usuários de qualquer empresa ou organização que tenha o Azure ad sem a necessidade de uma instância, configuração ou implantação separada. O AppSource recomenda que os aplicativos implementem multilocação para habilitar a experiência de avaliação gratuita de *clique único* .

Para habilitar a multilocação em seu aplicativo, siga estas etapas:
1. Defina a propriedade `Multi-Tenanted` como `Yes` nas informações do registro do aplicativo no [portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Por padrão, os aplicativos criados no portal do Azure são configurados como *[um único locatário](#single-tenant-applications)* .
1. Atualize seu código para enviar solicitações para o ponto de extremidade `common`. Para fazer isso, atualize o ponto de extremidade de `https://login.microsoftonline.com/{yourtenant}` para `https://login.microsoftonline.com/common*`.
1. Para algumas plataformas, como o ASP .NET, você também precisa atualizar seu código para aceitar vários emissores.

Para obter mais informações sobre multilocação, consulte [como entrar em qualquer usuário do Azure Active Directory (AD do Azure) usando o padrão de aplicativo multilocatário](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Aplicativos de locatário único

Um *aplicativo de locatário único* é um aplicativo que só aceita entradas de usuários de uma instância definida do Azure AD. Os usuários externos (incluindo contas corporativas ou de estudante de outras organizações, ou contas pessoais) podem entrar em um aplicativo de locatário único depois de adicionar cada usuário como uma conta de convidado à instância do Azure AD que o aplicativo está registrado. 

Você pode adicionar usuários como contas de convidado ao Azure AD por meio da [colaboração B2B do Azure ad](../b2b/what-is-b2b.md) e pode fazer isso [programaticamente](../../active-directory-b2c/code-samples.md). Ao usar o B2B, os usuários podem criar um portal de autoatendimento que não exige um convite para entrar. Para obter mais informações, consulte [portal de autoatendimento para inscrição de colaboração B2B do Azure ad](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal).

Os aplicativos de locatário único podem habilitar a experiência *entre em contato comigo* , mas se você quiser habilitar a experiência de avaliação gratuita/clique simples que o AppSource recomenda, habilite a multilocação em seu aplicativo.

## <a name="appsource-trial-experiences"></a>Experiências de avaliação do AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Avaliação gratuita (experiência de avaliação orientada pelo cliente)

A avaliação orientada pelo cliente é a experiência que o AppSource recomenda, pois oferece um acesso de clique único ao seu aplicativo. O exemplo a seguir mostra a aparência desta experiência:

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt-text="Shows Free trial for customer-led trial experience"/><ul><li>O usuário localiza seu aplicativo no site do AppSource</li><li>Seleciona a opção ' avaliação gratuita '</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt-text="Shows how user is redirected to a URL in your web site"/><ul><li>O AppSource redireciona o usuário para uma URL no seu site da Web</li><li>Seu site inicia o processo de <i>logon único</i> automaticamente (no carregamento da página)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt-text="Shows the Microsoft sign-in page"/><ul><li>O usuário é redirecionado para a página de entrada da Microsoft</li><li>O usuário fornece credenciais para entrar</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt-text="Example: Consent page for an application"/><ul><li>O usuário dá consentimento para seu aplicativo</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>A entrada é concluída e o usuário é Redirecionado de volta ao site da Web</li><li>O usuário inicia a avaliação gratuita</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Entre em contato comigo (experiência de avaliação orientada por parceiro)

Você pode usar a experiência de avaliação de parceiro quando uma operação manual ou de longo prazo precisa ocorrer para provisionar o usuário/empresa – por exemplo, seu aplicativo precisa provisionar máquinas virtuais, instâncias de banco de dados ou operações que levam muito tempo para serem concluídas. Nesse caso, depois que o usuário selecionar o botão **solicitar avaliação** e preencher um formulário, o AppSource enviará as informações de contato do usuário. Ao receber essas informações, você provisiona o ambiente e envia as instruções para o usuário sobre como acessar a experiência de avaliação:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt-text="Shows Contact me for partner-led trial experience"/><ul><li>O usuário localiza seu aplicativo no site do AppSource</li><li>Seleciona a opção ' entrar em contato comigo '</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt-text="Shows an example form with contact info"/><ul><li>Preenche um formulário com informações de contato</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%" alt-text="Shows placeholder for user information"/></td>
            <td>Você recebe informações do usuário</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%" alt-text="Shows placeholder for setup environment info"/></td>
            <td>Ambiente de instalação</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%" alt-text="Shows placeholder for trial info"/></td>
            <td>Contatar usuário com informações de avaliação</td>
        </tr>
        </table><br/><br/>
        <ul><li>Você recebe as informações do usuário e configura a instância de avaliação</li><li>Você envia o hiperlink para acessar o aplicativo para o usuário</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt-text="Shows the application sign-in screen"/><ul><li>O usuário acessa seu aplicativo e conclui o processo de logon único</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt-text="Shows an example consent page for an application"/><ul><li>O usuário dá consentimento para seu aplicativo</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>A entrada é concluída e o usuário é Redirecionado de volta ao site da Web</li><li>O usuário inicia a avaliação gratuita</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Mais informações

Para obter mais informações sobre a experiência de avaliação do AppSource, consulte [este vídeo](https://aka.ms/trialexperienceforwebapps). 

## <a name="next-steps"></a>Próximos Passos

- Para obter mais informações sobre a criação de aplicativos que dão suporte a entradas do Azure AD, consulte [cenários de autenticação do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/v1-authentication-scenarios).
- Para obter informações sobre como listar seu aplicativo SaaS no AppSource, consulte [informações do parceiro AppSource](https://appsource.microsoft.com/partners)

## <a name="get-support"></a>Obter suporte

Para a integração do Azure AD, usamos [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) com a Comunidade para fornecer suporte.

É altamente recomendável que você faça suas perguntas em Stack Overflow primeiro e procure problemas existentes para ver se alguém fez sua pergunta antes. Verifique se suas perguntas ou comentários estão marcados com [`[azure-active-directory]` e `[appsource]`](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Use a seção de comentários a seguir para fornecer comentários e nos ajudar a refinar e formatar nosso conteúdo.

<!--Reference style links -->
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:v1-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->
