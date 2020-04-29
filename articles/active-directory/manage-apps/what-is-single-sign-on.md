---
title: Inscrição única para candidaturas - Diretório Ativo Azure / Microsoft Docs
description: Saiba escolher um único método de inscrição ao configurar aplicações no Azure Ative Directory (Azure AD). Utilize um único sinal para que os utilizadores não precisem de se lembrar de senhas para cada aplicação e para simplificar a administração da gestão da conta.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: mimart
ms.reviewer: arvindh, japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: f46bcf412403d8f911e484e12a9d1f421b1666f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79366075"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Inscrição única para candidaturas no Diretório Ativo do Azure

O único sinal de inscrição (SSO) adiciona segurança e comodidade quando os utilizadores assinam aplicações no Azure Ative Directory (Azure AD). Este artigo descreve os métodos de inscrição únicos e ajuda-o a escolher o método SSO mais adequado ao configurar as suas aplicações.

- **Com um único registo,** os utilizadores assinam uma vez com uma conta para aceder a dispositivos ligados ao domínio, recursos da empresa, software como aplicações de serviço (SaaS) e aplicações web. Após a essão, o utilizador pode lançar aplicações a partir do portal Office 365 ou do painel de acesso Azure AD MyApps. Os administradores podem centralizar a gestão da conta do utilizador e adicionar ou remover automaticamente o acesso dos utilizadores a aplicações com base na adesão ao grupo.

- **Sem um único sinal,** os utilizadores devem lembrar-se de senhas específicas da aplicação e iniciar sessão em cada aplicação. O pessoal de TI precisa de criar e atualizar as contas dos utilizadores para cada aplicação, como o Office 365, Box e Salesforce. Os utilizadores precisam de se lembrar das suas palavras-passe, além de passarem o tempo a iniciar sessão em cada aplicação.

## <a name="choosing-a-single-sign-on-method"></a>Escolher um único método de inscrição

Existem várias formas de configurar um pedido de inscrição única. A escolha de um único método de inscrição depende de como a aplicação está configurada para autenticação.

- As aplicações em nuvem podem utilizar métodos OpenID Connect, OAuth, SAML, baseados em palavras-passe, ligados ou desativados para um único sinal. 
- As aplicações no local podem utilizar métodos integrados de autenticação do Windows baseados em palavras-passe, baseados em cabeçalhos, ligados ou desativados para um único sinal. As escolhas no local funcionam quando as aplicações são configuradas para procuração de aplicação.

Este fluxograma ajuda-o a decidir qual o método único de inscrição é o melhor para a sua situação.

![Fluxograma de decisão para método único de sinalização](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

A tabela seguinte resume os métodos de inscrição simples e liga-se a mais detalhes.

| Método único de inscrição | Tipos de aplicação | Quando utilizar |
| :------ | :------- | :----- |
| [OpenID Connect e OAuth](#openid-connect-and-oauth) | nuvem apenas | Utilize o OpenID Connect e o OAuth quando desenvolver uma nova aplicação. Este protocolo simplifica a configuração da aplicação, tem SDKs fáceis de usar e permite que a sua aplicação utilize o MS Graph.
| [SAML](#saml-sso) | nuvem e no local | Escolha o SAML sempre que possível para aplicações existentes que não utilizem OpenID Connect ou OAuth. A SAML trabalha para aplicações que autenticam utilizando um dos protocolos SAML.|
| [Baseado em palavra-passe](#password-based-sso) | nuvem e no local | Escolha com base em palavra-passe quando a aplicação autentica com nome de utilizador e palavra-passe. O único sign-on baseado em palavra-passe permite o armazenamento e repetição de palavras-passe de aplicação segura usando uma extensão do navegador web ou uma aplicação móvel. Este método utiliza o processo de entrada existente fornecido pela aplicação, mas permite a um administrador gerir as palavras-passe. |
| [Ligado](#linked-sign-on) | nuvem e no local | Escolha o sign-on ligado quando a aplicação estiver configurada para um único sinal num outro serviço de fornecedor de identidade. Esta opção não adiciona um único sinal de inscrição à aplicação. No entanto, a aplicação pode já ter um único sign-on implementado utilizando outro serviço, como os Serviços da Federação de DirectórioActivo.|
| [Desativado](#disabled-sso) | nuvem e no local | Escolha um único sinal de saque desativado quando a aplicação não estiver pronta para ser configurada para um único sinal. Este modo é o padrão quando cria a aplicação.|
| [Autenticação Integrada do Windows (IWA)](#integrated-windows-authentication-iwa-sso) | no local apenas | Escolha o iWA de um único sinal para aplicações que utilizem autenticação integrada do [Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)ou aplicações que tenham conhecimento de sinistros. Para a IWA, os conectores proxy de aplicação utilizam a Delegação Limitada kerberos (KCD) para autenticar os utilizadores na aplicação. |
| [Baseado em cabeçalho](#header-based-sso) | no local apenas | Utilize um único sinal baseado no cabeçalho quando a aplicação utilizar cabeçalhos para autenticação. O único sinal baseado no cabeçalho requer o PingAccess para a AD Azure. Aplicação Proxy usa AD Azure para autenticar o utilizador e, em seguida, passa o tráfego através do serviço de conector.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect e OAuth

Ao desenvolver novas aplicações, utilize protocolos modernos como OpenID Connect e OAuth para obter a melhor experiência de inscrição única para a sua aplicação em várias plataformas de dispositivos. A OAuth permite que utilizadores ou administradores [concedam consentimento](configure-user-consent.md) para recursos protegidos como o [Microsoft Graph](/graph/overview). Fornecemos [SDKs](../develop/reference-v2-libraries.md) fáceis de adotar para a sua aplicação e, além disso, a sua aplicação estará pronta para usar o [Microsoft Graph](/graph/overview).

Para obter mais informações, consulte:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Guia de desenvolvedor de plataformas de identidade da Microsoft.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

## <a name="saml-sso"></a>SAML SSO

Com um único sinal de **entrada saml,** a Azure AD autentica a aplicação utilizando a conta Azure AD do utilizador. A Azure AD comunica a informação de início de sessão à aplicação através de um protocolo de ligação. Com um único sign-on baseado em SAML, pode mapear os utilizadores para funções específicas de aplicação com base em regras que define nas suas reivindicações SAML.

Escolha um único sinal baseado em SAML quando a aplicação o suporta.

A inscrição única baseada na SAML é suportada para aplicações que utilizam qualquer um destes protocolos:

- SAML 2.0
- WS-Federation

Para configurar uma aplicação SaaS para um único sign-on baseado em SAML, consulte configurar um [único sign-on baseado em SAML](configure-single-sign-on-non-gallery-applications.md). Além disso, muitas aplicações de Software como Serviço (SaaS) têm um [tutorial específico para aplicações](../saas-apps/tutorial-list.md) que o pisam na configuração para um único sign-on baseado em SAML.

Para configurar um pedido para a WS-Federation, siga as mesmas orientações para configurar a aplicação para um único sign-on baseado em SAML, consulte o [signo único baseado em Configuração SAML](configure-single-sign-on-non-gallery-applications.md). Na etapa para configurar a aplicação para utilizar o Azure AD, terá de substituir o URL `https://login.microsoftonline.com/<tenant-ID>/wsfed`de login Azure AD para o ponto final da WS-Federação.

Para configurar um pedido no local para um único sign-on baseado em SAML, consulte o [saml single-sign-on para aplicações no local com procuração](application-proxy-configure-single-sign-on-on-premises-apps.md)de aplicação .

Para obter mais informações sobre o protocolo SAML, consulte o [protocolo SAML de inscrição única](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>SSO baseado em palavra-passe

Com o registo baseado em palavra-passe, os utilizadores acedem à aplicação com um nome de utilizador e uma senha na primeira vez que acedem à sua aplicação. Após o primeiro início de inscrição, a Azure AD fornece o nome de utilizador e a palavra-passe para a aplicação.

O único sign-on baseado em palavra-passe utiliza o processo de autenticação existente fornecido pela aplicação. Quando ativa o início de um único sinal de senha para uma aplicação, o Azure AD recolhe e armazena de forma segura os nomes e senhas de utilizador para a aplicação. As credenciais dos utilizadores são armazenadas num estado encriptado no diretório.

Escolha um único sign-on baseado em palavra-passe quando:

- Uma aplicação não suporta o protocolo de inscrição individual da SAML.
- Uma aplicação autentica com um nome de utilizador e senha em vez de fichas de acesso e cabeçalhos.

O único sign-on baseado em palavra-passe é suportado para qualquer aplicação baseada na nuvem que tenha uma página de entrada baseada em HTML. O utilizador pode utilizar qualquer um dos seguintes navegadores:

- Internet Explorer 11 no Windows 7 ou mais tarde
   > [!NOTE]
   > O Internet Explorer está em suporte limitado e já não recebe novas atualizações de software. O Microsoft Edge é o navegador recomendado.

- Microsoft Edge na Edição de Aniversário do Windows 10 ou mais tarde
- Microsoft Edge para iOS e Android
- Browser Gerido do Intune
- Chrome no Windows 7 ou mais tarde, e no MacOS X ou mais tarde
- Firefox 26.0 ou mais tarde no Windows XP SP2 ou mais tarde, e em Mac OS X 10.6 ou mais tarde

Para configurar uma aplicação em nuvem para um único sinal baseado em palavra-passe, consulte configurar o [acesso individual da palavra-passe Configurar](configure-password-single-sign-on-non-gallery-applications.md).

Para configurar um pedido no local para um único sinal através do Proxy de Aplicação, consulte a [abóbada de palavra-passe para um único sinal com procuração](application-proxy-configure-single-sign-on-password-vaulting.md) de aplicação

### <a name="how-authentication-works-for-password-based-sso"></a>Como funciona a autenticação para SSO baseado em palavra-passe

Para autenticar um utilizador numa aplicação, a Azure AD recupera as credenciais do utilizador do diretório e introduz-as na página de inscrição da aplicação.  A Azure AD passa de forma segura as credenciais do utilizador através de uma extensão do navegador web ou de uma aplicação móvel. Este processo permite a um administrador gerir as credenciais do utilizador e não exige que os utilizadores se lembrem da sua palavra-passe.

> [!IMPORTANT]
> As credenciais são obfuscadas do utilizador durante o processo de início automático. No entanto, as credenciais são detetáveis usando ferramentas de depuração web. Os utilizadores e administradores devem seguir as mesmas políticas de segurança que se as credenciais fossem introduzidas diretamente pelo utilizador.

### <a name="managing-credentials-for-password-based-sso"></a>Gestão de credenciais para SSO baseado em palavra-passe

As palavras-passe para cada aplicação podem ser geridas pelo administrador da AD Azure ou pelos utilizadores.

Quando o administrador da AD Azure gere as credenciais:  

- O utilizador não necessita de redefinir ou lembrar-se do nome de utilizador e da palavra-passe. O utilizador pode aceder à aplicação clicando nela no seu painel de acesso ou através de um link fornecido.
- O administrador pode fazer tarefas de gestão sobre as credenciais. Por exemplo, o administrador pode atualizar o acesso da aplicação de acordo com os membros do grupo de utilizadores e o estado do empregado.
- O administrador pode usar credenciais administrativas para fornecer acesso a aplicações partilhadas entre muitos utilizadores. Por exemplo, o administrador pode permitir que todos os que podem aceder a uma aplicação tenham acesso a uma aplicação de partilha de redes sociais ou documentos.

Quando o utilizador final gere as credenciais:

- Os utilizadores podem gerir as suas palavras-passe atualizando ou apagando-as conforme necessário.
- Os administradores ainda podem definir novas credenciais para a aplicação.

## <a name="linked-sign-on"></a>Inscrição ligada
O sign-on linked permite que a Azure AD forneça um único sinal a uma aplicação que já está configurada para um único início de sessão noutro serviço. A aplicação ligada pode parecer acabar com os utilizadores no portal Do Office 365 ou no portal Azure AD MyApps. Por exemplo, um utilizador pode lançar uma aplicação configurada para um único sign-on no Ative Directory Federation Services 2.0 (AD FS) do portal Office 365. Estão também disponíveis relatórios adicionais para aplicações ligadas que são lançadas a partir do portal Office 365 ou do portal Azure AD MyApps. Para configurar uma aplicação para o sign-on ligado, consulte o ['sign-on' ligado](configure-linked-sign-on.md)à Configuração .

### <a name="linked-sign-on-for-application-migration"></a>Inscrição ligada para migração de candidaturas

O sign-on linked pode proporcionar uma experiência consistente ao utilizador enquanto migra aplicações durante um período de tempo. Se estiver a migrar aplicações para o Azure Ative Directory, pode utilizar o signon ligado para publicar rapidamente links para todas as aplicações que pretende migrar.  Os utilizadores podem encontrar todos os links no [portal MyApps](../user-help/active-directory-saas-access-panel-introduction.md) ou no lançador de [aplicações do Office 365.](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) Os utilizadores não saberão que estão a aceder a uma aplicação ligada ou a uma aplicação migrada.  

Uma vez que um utilizador tenha autenticado com uma aplicação ligada, é necessário criar um registo de conta antes de o utilizador final ter acesso único ao sinal. O fornecimento deste registo de conta pode ocorrer automaticamente, ou pode ocorrer manualmente por um administrador.

## <a name="disabled-sso"></a>SSO deficiente

Modo desativado significa que o único sinal não é utilizado para a aplicação. Quando o único sinal é desativado, os utilizadores podem precisar de autenticar duas vezes. Primeiro, os utilizadores autenticam a AD Azure e depois assinam a aplicação.

Utilize o modo de sinalização individual desativado:

- Se não estiver pronto para integrar esta aplicação com o Azure AD single sign-on, ou
- Se estiver a testar outros aspetos da aplicação, ou
- Como uma camada de segurança para uma aplicação no local que não requer que os utilizadores autentiquem. Com desativado, o utilizador precisa de autenticar.

Note que se configurar a aplicação para sp-iniciado saml single sign-on e você alterar o modo SSO para desativar, não impedirá os utilizadores de assinar em aplicação fora do portal MyApps. Para isso, é necessário [desativar a capacidade de os utilizadores iniciarem sessão](disable-user-sign-in-portal.md)

## <a name="integrated-windows-authentication-iwa-sso"></a>Autenticação Integrada do Windows (IWA) SSO

[Application Proxy](application-proxy.md) fornece um único sinal on (SSO) a aplicações que utilizam [autenticação integrada do Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)ou aplicações conscientes de sinistros. Se a sua aplicação utilizar o IWA, o Application Proxy autentica a aplicação utilizando a Delegação Limitada kerberos (KCD). Para uma aplicação consciente de sinistros que confia no Azure Ative Directory, as obras de inscrição únicas porque o utilizador já estava autenticado utilizando a Azure AD.

Escolha o modo de inscrição individual integrado do Windows Authentication para fornecer um único sinal de acesso a uma aplicação no local que autentica com o IWA.

Para configurar uma aplicação no local para iWA, consulte [a Delegação Limitada kerberos para obter um único sessão nas suas aplicações com procuração](application-proxy-configure-single-sign-on-with-kcd.md)de aplicação .

### <a name="how-single-sign-on-with-kcd-works"></a>Como funciona um único sinal com a KCD
Este diagrama explica o fluxo quando um utilizador acede a uma aplicação no local que utiliza o IWA.

![Diagrama de fluxo de fluxo de autenticação aditiva do Microsoft Azure](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. O utilizador introduz o URL para aceder à aplicação no local através do Proxy de Aplicação.
1. Aplicação Proxy redireciona o pedido para os serviços de autenticação Da Azure para pré-autenticação. Neste momento, a Azure AD aplica quaisquer políticas de autenticação e autorização aplicáveis, tais como a autenticação multifactor. Se o utilizador for validado, o Azure AD cria um símbolo e envia-o para o utilizador.
1. O utilizador passa o símbolo para o Application Proxy.
1. Aplicação Proxy valida o símbolo e recupera o Nome Principal do Utilizador (UPN) do símbolo. Em seguida, envia o pedido, a UPN e o Nome Principal de Serviço (SPN) para o Conector através de um canal seguro duplamente autenticado.
1. O conector utiliza a negociação da Delegação Limitada kerberos (KCD) com a AD no local, fazendo-se passar pelo utilizador para obter um símbolo kerberos para a aplicação.
1. O Diretório Ativo envia o símbolo Kerberos para a aplicação ao conector.
1. O conector envia o pedido original para o servidor de aplicações, utilizando o símbolo Kerberos que recebeu da AD.
1. A aplicação envia a resposta ao conector, que é depois devolvido ao serviço Proxy de Aplicação e, finalmente, ao utilizador.

## <a name="header-based-sso"></a>SSO baseado em cabeçalho

O único sinal de inscrição baseado em cabeçalho funciona para aplicações que utilizam cabeçalhos HTTP para autenticação. Este método de inscrição utiliza um serviço de autenticação de terceiros chamado PingAccess. Um utilizador só precisa de autenticar a AD Azure.

Escolha um único sinal baseado em cabeçalho quando o Proxy de Aplicação e o PingAccess estiverem configurados para a aplicação.

Para configurar a autenticação baseada em cabeçalhos, consulte a [autenticação baseada em cabeçalho para um único sinal com procuração](application-proxy-configure-single-sign-on-with-ping-access.md)de aplicação .

### <a name="what-is-pingaccess-for-azure-ad"></a>O que é PingAccess para Azure AD?

Utilizando o PingAccess para AD Azure, os utilizadores podem aceder e iniciar um único sessão a aplicações que utilizem cabeçalhos para autenticação. Aplicação Proxy trata estas aplicações como qualquer outra, usando a AD Azure para autenticar o acesso e, em seguida, passar o tráfego através do serviço de conector. Após a autenticação, o serviço PingAccess traduz o token de acesso Azure AD para um formato de cabeçalho que é enviado para a aplicação.

Os seus utilizadores não notarão nada diferente quando iniciarem o seu contrato para utilizarem as suas aplicações corporativas. Ainda podem trabalhar em qualquer lugar em qualquer dispositivo. Os conectores Proxy de aplicação direcionam o tráfego remoto para todas as aplicações, e continuarão a carregar o equilíbrio automaticamente.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Como consigo uma licença para o PingAccess?

Uma vez que este cenário é oferecido através de uma parceria entre a Azure AD e o PingAccess, precisa de licenças para ambos os serviços. No entanto, as subscrições do Azure AD Premium incluem uma licença básica do PingAccess que cobre até 20 aplicações. Se precisar de publicar mais de 20 aplicações baseadas em cabeçalhos, pode adquirir uma licença adicional do PingAccess.

Para mais informações, consulte edições de [Diretório Ativo Azure.](../fundamentals/active-directory-whatis.md)

## <a name="related-articles"></a>Artigos relacionados
* [Tutorials for integrating SaaS applications with Azure Active Directory](../saas-apps/tutorial-list.md) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)
* [Configurar um único sign-on baseado em SAML](configure-single-sign-on-non-gallery-applications.md)
* [Configurar um único sinal baseado em palavra-passe](configure-password-single-sign-on-non-gallery-applications.md)
* [Configuração de inscrição ligada](configure-linked-sign-on.md)
* [Introdução à Gestão do Acesso às Aplicações](what-is-access-management.md)
* Link de descarregamento: [Plano de implementação de um único sinal.](https://aka.ms/SSODeploymentPlan)
