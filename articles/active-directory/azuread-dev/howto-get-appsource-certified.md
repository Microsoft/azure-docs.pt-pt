---
title: Como obter o AppSource certificado para O Azure Ative Directory| Microsoft Docs
description: Detalhes sobre como obter a sua aplicação AppSource certificada para Azure Ative Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: how-to
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 15a4dd56b509571094ef202fbce781104bda9188
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052249"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Como obter AppSource Certificado para Azure Ative Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[O Microsoft AppSource](https://appsource.microsoft.com/) é um destino para os utilizadores empresariais descobrirem, tentarem e gerirem aplicações SaaS de linha de negócio (SaaS autónomo e add-on aos produtos Microsoft SaaS existentes).

Para listar uma aplicação SaaS autónoma no AppSource, a sua aplicação deve aceitar um único s-on a partir de contas de trabalho de qualquer empresa ou organização que tenha diretório Azure Ative (Azure AD). O processo de início de sposição deve utilizar os protocolos [OpenID Connect](v1-protocols-openid-connect-code.md) ou [OAuth 2.0.](v1-protocols-oauth-code.md) A integração DA SAML não é aceite para a certificação AppSource.

## <a name="guides-and-code-samples"></a>Guias e amostras de código

Se quiser aprender como integrar a sua aplicação com a Azure AD utilizando a ligação Open ID, siga os nossos guias e amostras de código no [guia do programador do Azure Ative Directory](v1-overview.md#get-started "Começar com Azure AD para desenvolvedores").

## <a name="multi-tenant-applications"></a>Aplicações multi-inquilino

Uma *aplicação multi-inquilino* é uma aplicação que aceita inscrições de utilizadores de qualquer empresa ou organização que tenha Azure AD sem exigir uma instância, configuração ou implementação separadas. O AppSource recomenda que as aplicações implementem o multi-arrendamento para permitir a experiência de teste gratuito *de clique* único.

Para ativar o multi-arrendamento na sua aplicação, siga estes passos:
1. Desatado `Multi-Tenanted` a propriedade `Yes` para informação do seu registo de inscrição no [portal Azure.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) Por predefinição, as aplicações criadas no portal Azure são configuradas como *[inquilino único.](#single-tenant-applications)*
1. Atualize o seu código para enviar pedidos para o `common` ponto final. Para isso, atualize o ponto final `https://login.microsoftonline.com/{yourtenant}` de `https://login.microsoftonline.com/common*` .
1. Para algumas plataformas, como o ASP .NET, também é necessário atualizar o seu código para aceitar vários emitentes.

Para obter mais informações sobre o multi-arrendamento, consulte [Como assinar em qualquer utilizador do Azure Ative Directory (Azure AD) utilizando o padrão de aplicação multi-inquilinos](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="single-tenant-applications"></a>Pedidos de inquilino único

Uma *aplicação de inquilino único* é uma aplicação que apenas aceita inscrições de utilizadores de uma instância AD definida. Os utilizadores externos (incluindo contas de trabalho ou escola de outras organizações, ou contas pessoais) podem inscrever-se numa aplicação de um único inquilino depois de adicionar cada utilizador como conta de hóspedes à instância AD do Azure que a aplicação está registada. 

Pode adicionar os utilizadores como contas de hóspedes ao Azure AD através da [colaboração Azure AD B2B](../external-identities/what-is-b2b.md) e pode fazê-lo [programáticamente.](../../active-directory-b2c/code-samples.md) Ao utilizar o B2B, os utilizadores podem criar um portal de self-service que não requer um convite para iniciar sessão. Para mais informações, consulte [o portal self-service para inscrição de colaboração Azure AD B2B](../external-identities/self-service-portal.md).

As aplicações de um único inquilino podem ativar a experiência *Do Contacto Me,* mas se quiser ativar a experiência de teste de um clique/teste gratuito que o AppSource recomenda, em vez disso, ativar o multi-arrendamento na sua aplicação.

## <a name="appsource-trial-experiences"></a>Experiências de teste appSource

### <a name="free-trial-customer-led-trial-experience"></a>Ensaio gratuito (experiência de ensaio conduzida pelo cliente)

O teste liderado pelo cliente é a experiência que o AppSource recomenda, uma vez que oferece um acesso de um clique à sua aplicação. O exemplo a seguir mostra como é esta experiência:

<table >
<tr>    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt="Shows Free trial for customer-led trial experience."/><ul><li>O utilizador encontra a sua aplicação no Site AppSource</li><li>Seleciona opção 'Teste gratuito'</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt="Shows how user is redirected to a URL in your web site."/><ul><li>AppSource redireciona o utilizador para um URL no seu site</li><li>O seu web site inicia automaticamente o processo <i>de início de sação</i> (na carga da página)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt="Shows the Microsoft sign-in page."/><ul><li>O utilizador é redirecionado para a página de iniciar sê-in no Microsoft</li><li>Utilizador fornece credenciais para iniciar sinse</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt="Example: Consent page for an application."/><ul><li>O utilizador dá consentimento para a sua aplicação</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt="Shows the experience the user sees when redirected back to your site."/><ul><li>O sismo completa e o utilizador é redirecionado de volta para o seu site</li><li>Utilizador inicia o teste gratuito</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Contacte-me (experiência de teste liderada por parceiros)

Pode utilizar a experiência experimental do parceiro quando uma operação manual ou de longo prazo precisa de acontecer para o fornecimento do utilizador/empresa -- por exemplo, a sua aplicação precisa de providenciar máquinas virtuais, casos de base de dados ou operações que levem muito tempo a concluir. Neste caso, depois de o utilizador selecionar o botão **'Teste de Pedido'** e preencher um formulário, o AppSource envia-lhe as informações de contacto do utilizador. Quando recebe esta informação, fornece o ambiente e envia as instruções ao utilizador sobre como aceder à experiência experimental:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt="Shows Contact me for partner-led trial experience"/><ul><li>O utilizador encontra a sua aplicação no site appSource</li><li>Seleciona a opção 'Contacte-me'</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt="Shows an example form with contact info"/><ul><li>Preenche um formulário com informações de contacto</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/usercontact.png" width="55%" alt="Shows placeholder for user information"/></td>
            <td>Recebe informações do utilizador</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/setupenv.png" width="55%" alt="Shows placeholder for setup environment info"/></td>
            <td>Ambiente de configuração</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/contactcustomer.png" width="55%" alt="Shows placeholder for trial info"/></td>
            <td>Contacte o utilizador com informações de teste</td>
        </tr>
        </table><br/><br/>
        <ul><li>Você recebe informações do utilizador e configuração de instância de teste</li><li>Envia a hiperligação para aceder à sua aplicação ao utilizador</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt="Shows the application sign-in screen"/><ul><li>O utilizador acede à sua aplicação e completa o processo de assinatura única</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt="Shows an example consent page for an application"/><ul><li>O utilizador dá consentimento para a sua aplicação</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt="Shows the experience the user sees when redirected back to your site"/><ul><li>O sismo completa e o utilizador é redirecionado de volta para o seu site</li><li>Utilizador inicia o teste gratuito</li></ul></td>  
</tr>
</table>

### <a name="more-information"></a>Mais informações

Para obter mais informações sobre a experiência de teste appSource, consulte [este vídeo](https://aka.ms/trialexperienceforwebapps). 

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre aplicações de construção que suportem as entradas AD do Azure, consulte cenários de [autenticação para Azure AD](./v1-authentication-scenarios.md).
- Para obter informações sobre como listar a sua aplicação SaaS no AppSource, vá ver [Informações de Parceiros appSource](https://appsource.microsoft.com/partners)

## <a name="get-support"></a>Obter suporte

Para a integração do AD Azure, usamos [o Microsoft Q&A](https://docs.microsoft.com/answers/products/) com a comunidade para fornecer suporte.

Recomendamos vivamente que faça as suas perguntas sobre o Microsoft Q&A primeiro e navegue nos problemas existentes para ver se alguém já fez a sua pergunta antes. Certifique-se de que as suas perguntas ou comentários estão marcados com [`[azure-active-directory]`](https://docs.microsoft.com/answers/topics/azure-active-directory.html) .

Utilize a secção de comentários a seguir para fornecer feedback e ajude-nos a aperfeiçoar e moldar o nosso conteúdo.

<!--Reference style links -->
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:v1-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->