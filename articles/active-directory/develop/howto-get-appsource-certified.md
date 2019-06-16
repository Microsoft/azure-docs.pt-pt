---
title: Como obter certificação para o Azure Active Directory AppSource | Documentos da Microsoft
description: Detalhes sobre como a sua aplicação AppSource certificada para Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b820068f7bd59941346c8d2a3e8ede15252d58b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540274"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Como obter o AppSource certificada para Azure Active Directory

[Microsoft AppSource](https://appsource.microsoft.com/) é um destino para usuários empresariais para detetar, experimentar e gerir aplicações de SaaS de linha de negócio (autónomo SaaS e suplementos para produtos de SaaS da Microsoft já existentes).

Para listar uma aplicação autónoma de SaaS no AppSource, a aplicação tem de aceitar início de sessão único de contas de trabalho a partir de qualquer empresa ou organização que tenha o Azure Active Directory (Azure AD). O processo de início de sessão tem de utilizar o [OpenID Connect](v1-protocols-openid-connect-code.md) ou [OAuth 2.0](v1-protocols-oauth-code.md) protocolos. Integração de SAML não é aceite para obter uma certificação do AppSource.

## <a name="guides-and-code-samples"></a>Exemplos de código e guias

Se quiser saber mais sobre como integrar o seu aplicativo com o Azure AD com o ID de Open ligar, siga os nossos guias e exemplos de código a [Guia do programador do Azure Active Directory](v1-overview.md#get-started "introdução ao Azure AD para os desenvolvedores").

## <a name="multi-tenant-applications"></a>Aplicações multi-inquilino

R *aplicação multi-inquilino* é uma aplicação que aceite inícios de sessão dos utilizadores de qualquer empresa ou organização que tem o Azure AD sem a necessidade de uma instância separada, a configuração ou a implementação. AppSource recomenda que as aplicações implementar vários inquilinos para ativar a *clique único* gratuitos a experiência de avaliação.

Para permitir que vários inquilinos na sua aplicação, siga estes passos:
1. Definir `Multi-Tenanted` propriedade `Yes` nas informações do registo de aplicação a [portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Por predefinição, as aplicações criadas no portal do Azure são configuradas como  *[inquilino único](#single-tenant-applications)* .
1. Atualizar seu código para enviar pedidos para o `common` ponto final. Para tal, atualizar o ponto final da `https://login.microsoftonline.com/{yourtenant}` para `https://login.microsoftonline.com/common*`.
1. Em algumas plataformas, como no ASP .NET, terá também de atualizar o seu código aceite vários emissores.

Para obter mais informações sobre vários inquilinos, consulte [como iniciar sessão a qualquer utilizador do Azure Active Directory (Azure AD) utilizando o padrão de aplicação multi-inquilino](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Aplicações de inquilino único

R *aplicação de inquilino único* é uma aplicação que aceite apenas inícios de sessão dos utilizadores de um Azure definido instância do AD. Utilizadores externos (incluindo contas pessoais ou contas escolares de outras organizações ou trabalho) podem iniciar sessão a uma aplicação de inquilino único depois de adicionar cada usuário como uma conta de convidado para a instância do Azure AD que a aplicação fica registada. 

Pode adicionar utilizadores como contas de convidado para o Azure AD através da [colaboração B2B do Azure AD](../b2b/what-is-b2b.md) e pode fazê-lo [programaticamente](../../active-directory-b2c/code-samples.md). Quando utilizar B2B, os utilizadores podem criar um portal self-service que não necessita de um convite para iniciar sessão. Para mais informações, veja [portal self-service para inscrição de colaboração B2B do Azure AD](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal).

Aplicações de inquilino único podem ativar a *contactar-Me* experiência, mas se pretender ativar a experiência de avaliação de único clique/gratuitos que recomenda o AppSource, ativar vários inquilinos na sua aplicação em vez disso.

## <a name="appsource-trial-experiences"></a>Experiências de avaliação do AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Versão de avaliação gratuita (por um instrutor cliente experiência de avaliação) 

A avaliação liderada por cliente é a experiência que AppSource recomenda que oferece um acesso de clique único à sua aplicação. Abaixo como obter uma ilustração desta experiência é semelhante a:<br/><br/>

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%"/><ul><li>Utilizador encontrar seu aplicativo no Web Site do AppSource</li><li>Seleciona a opção "Avaliação gratuita"</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" /><ul><li>AppSource redireciona o utilizador para um URL no seu web site</li><li>Inicia o web site do <i>single-sign-on</i> processos automaticamente (no carregamento de página)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%"/><ul><li>Utilizador é redirecionado à página de início de sessão do Microsoft</li><li>Utilizador fornece credenciais para iniciar sessão</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%"/><ul><li>Utilizador dá consentimento para a sua aplicação</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Início de sessão é concluído e o utilizador é redirecionado para o seu web site</li><li>Utilizador começa a avaliação gratuita</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Contactar-me (liderada por parceiros experiência de avaliação)

Pode utilizar a experiência de avaliação do parceiro quando um manual ou uma operação de longa duração precisa acontecer ao aprovisionar que a utilizador/da empresa – por exemplo, a aplicação tem de aprovisionar máquinas virtuais, as instâncias de base de dados ou operações que demoram muito tempo a concluir. Neste caso, depois do utilizador selecionar o **pedir avaliação** botão e preenche um formulário, AppSource envia-lhe as informações de contacto do utilizador. Quando receber estas informações, em seguida, provisionar o ambiente e enviar as instruções para o usuário sobre como aceder a experiência de avaliação:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%"/><ul><li>Utilizador encontrar seu aplicativo no web site do AppSource</li><li>Seleciona a opção "Contactar-Me"</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%"/><ul><li>Preenche um formulário com informações de contacto</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%"/></td>
            <td>Receber informações de utilizador</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%"/></td>
            <td>Configurar o ambiente</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%"/></td>
            <td>Utilizador de contacto com informações de avaliação</td>
        </tr>
        </table><br/><br/>
        <ul><li>Recebe informações do utilizador e a instância de avaliação de configuração</li><li>Envia a hiperligação para aceder à sua aplicação ao utilizador</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%"/><ul><li>Utilizador aceder à sua aplicação e concluir o processo de início de sessão único</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%"/><ul><li>Utilizador dá consentimento para a sua aplicação</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Início de sessão é concluído e o utilizador é redirecionado para o seu web site</li><li>Utilizador começa a avaliação gratuita</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Mais informações

Para obter mais informações sobre a experiência de avaliação do AppSource, consulte [este vídeo](https://aka.ms/trialexperienceforwebapps). 
 
## <a name="next-steps"></a>Próximos Passos

- Para obter mais informações sobre a criação de aplicações que suportam a inícios de sessão do Azure AD, consulte [cenários de autenticação do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).
- Para informações sobre como listar a sua aplicação SaaS no AppSource, aceda a ver [informações do parceiro de AppSource](https://appsource.microsoft.com/partners)


## <a name="get-support"></a>Obter suporte

Para a integração do Azure AD, utilizamos [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) com a Comunidade para fornecer suporte. 

É altamente recomendável colocar as suas questões no Stack Overflow pela primeira vez e procurar problemas existentes para ver se alguém recebe sua pergunta antes de o pedido. Certifique-se de que suas perguntas ou comentários são marcados com [ `[azure-active-directory]` e `[appsource]` ](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Utilize a seguinte secção de comentários para fornecer comentários e ajude-na refinar e moldar o nosso conteúdo.

<!--Reference style links -->
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started


<!--Image references-->
