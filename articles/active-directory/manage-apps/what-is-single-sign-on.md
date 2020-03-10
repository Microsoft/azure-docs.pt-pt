---
title: Início de sessão único para aplicações - Azure Active Directory | Documentos da Microsoft
description: Saiba como escolher um método de início de sessão único, quando configurar as aplicações no Azure Active Directory (Azure AD). Utilize o início de sessão único para que os usuários não precisam se lembrar de senhas para cada aplicativo e para simplificar a administração de gestão de contas.
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
ms.openlocfilehash: e24a4209869d4c47f8ac73e250699ec55d006296
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375301"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Início de sessão único para aplicações no Azure Active Directory

O único sinal de inscrição (SSO) adiciona segurança e comodidade quando os utilizadores assinam aplicações no Azure Ative Directory (Azure AD). Este artigo descreve os métodos de inscrição únicos e ajuda-o a escolher o método SSO mais adequado ao configurar as suas aplicações.

- **Com um único registo,** os utilizadores assinam uma vez com uma conta para aceder a dispositivos ligados ao domínio, recursos da empresa, software como aplicações de serviço (SaaS) e aplicações web. Depois de iniciar sessão, o utilizador pode iniciar as aplicações do portal do Office 365 ou o painel de acesso do Azure AD MyApps. Os administradores podem centralizar a gestão de contas de utilizador e automaticamente adicionar ou remover o acesso de utilizador para aplicações com base na associação de grupo.

- **Sem um único sinal,** os utilizadores devem lembrar-se de senhas específicas da aplicação e iniciar sessão em cada aplicação. Necessidades de pessoal de TI para criar e atualizar as contas de utilizador para cada aplicativo, como o Office 365, a caixa e o Salesforce. Os utilizadores têm de se lembrar de suas senhas, além de perder tempo a iniciar sessão em cada aplicação.

## <a name="choosing-a-single-sign-on-method"></a>Escolher um método de início de sessão único

Existem várias formas de configurar uma aplicação para início de sessão único. A escolha de um único método de inscrição depende de como a aplicação está configurada para autenticação.

- As aplicações em nuvem podem utilizar métodos OpenID Connect, OAuth, SAML, baseados em palavras-passe, ligados ou desativados para um único sinal. 
- Aplicações no local podem utilizar a autenticação de Windows integrada, com base em palavra-passe, métodos com base no cabeçalho, ligados ou desativadas para início de sessão único. As opções de locais de trabalho quando aplicativos são configurados para Proxy de aplicações.

Este fluxograma ajuda-o a decidir que método de início de início de sessão único é melhor para sua situação.

![Fluxograma de decisão para método único de sinalização](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

A tabela seguinte resume os métodos de início de sessão únicos e ligações para obter mais detalhes.

| Método de início de sessão único | Tipos de aplicação | Quando utilizar |
| :------ | :------- | :----- |
| [OpenID Connect e OAuth](#openid-connect-and-oauth) | nuvem apenas | Utilize o OpenID Connect e o OAuth quando desenvolver uma nova aplicação. Este protocolo simplifica a configuração da aplicação, tem SDKs fáceis de usar e permite que a sua aplicação utilize o MS Graph.
| [SAML](#saml-sso) | na cloud e no local | Escolha o SAML sempre que possível para aplicações existentes que não utilizem OpenID Connect ou OAuth. A SAML trabalha para aplicações que autenticam utilizando um dos protocolos SAML.|
| [Baseado em palavra-passe](#password-based-sso) | na cloud e no local | Escolha com base em palavra-passe quando a aplicação autentica com nome de utilizador e palavra-passe. Com base em palavra-passe de início de sessão único permite o armazenamento de palavra-passe de aplicação segura e de repetição com uma extensão de browser ou aplicação móvel. Este método utiliza o início de sessão no processo existente fornecido pela aplicação, mas permite que um administrador gerir as palavras-passe. |
| [Ligado](#linked-sign-on) | na cloud e no local | Escolha o sign-on ligado quando a aplicação estiver configurada para um único sinal num outro serviço de fornecedor de identidade. Esta opção não adiciona o início de sessão único para a aplicação. No entanto, a aplicação já pode ter início de sessão único implementado através de outro serviço, como serviços de Federação do Active Directory.|
| [Desativado](#disabled-sso) | na cloud e no local | Escolha um único sinal de saque desativado quando a aplicação não estiver pronta para ser configurada para um único sinal. Os utilizadores têm de introduzir o respetivo nome de utilizador e a palavra-passe sempre que iniciarem esta aplicação.|
| [Autenticação Integrada do Windows (IWA)](#integrated-windows-authentication-iwa-sso) | apenas no local | Escolha o iWA de um único sinal para aplicações que utilizem autenticação integrada do [Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)ou aplicações que tenham conhecimento de sinistros. Para a IWA, os conectores proxy de aplicação utilizam a Delegação Limitada kerberos (KCD) para autenticar os utilizadores na aplicação. |
| [Baseado em cabeçalho](#header-based-sso) | apenas no local | Utilize com base no cabeçalho de início de sessão único quando o aplicativo usa cabeçalhos para autenticação. O único sinal baseado no cabeçalho requer o PingAccess para a AD Azure. Proxy de aplicações utiliza o Azure AD para autenticar o utilizador e, em seguida, passa o tráfego através do serviço de conector.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect e OAuth

Ao desenvolver novas aplicações, utilize protocolos modernos como OpenID Connect e OAuth para obter a melhor experiência de inscrição única para a sua aplicação em várias plataformas de dispositivos. A OAuth permite que utilizadores ou administradores [concedam consentimento](configure-user-consent.md) para recursos protegidos como o [Microsoft Graph](/graph/overview). Fornecemos [SDKs](../develop/reference-v2-libraries.md) fáceis de adotar para a sua aplicação e, além disso, a sua aplicação estará pronta para usar o [Microsoft Graph](/graph/overview).

Para obter mais informações, consulte:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Guia de desenvolvedor de plataformas de identidade da Microsoft.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

## <a name="saml-sso"></a>SAML SSO

Com um único sinal de **entrada saml,** a Azure AD autentica a aplicação utilizando a conta Azure AD do utilizador. O Azure AD comunica as informações de início de sessão para a aplicação através de um protocolo de ligação. Com um único sign-on baseado em SAML, pode mapear os utilizadores para funções específicas de aplicação com base em regras que define nas suas reivindicações SAML.

Escolha um único sinal baseado em SAML quando a aplicação o suporta.

Baseado em SAML início de sessão único é suportada para aplicações que utilizam estes protocolos de:

- SAML 2.0
- WS-Federation

Para configurar uma aplicação SaaS para um único sign-on baseado em SAML, consulte configurar um [único sign-on baseado em SAML](configure-single-sign-on-non-gallery-applications.md). Além disso, muitas aplicações de Software como Serviço (SaaS) têm um [tutorial específico para aplicações](../saas-apps/tutorial-list.md) que o pisam na configuração para um único sign-on baseado em SAML.

Para configurar um pedido para a WS-Federation, siga as mesmas orientações para configurar a aplicação para um único sign-on baseado em SAML, consulte o [signo único baseado em Configuração SAML](configure-single-sign-on-non-gallery-applications.md). Na etapa para configurar a aplicação para utilizar o Azure AD, terá de substituir o URL de login Azure AD para o ponto final da WS-Federação `https://login.microsoftonline.com/<tenant-ID>/wsfed`.

Para configurar um pedido no local para um único sign-on baseado em SAML, consulte o [saml single-sign-on para aplicações no local com procuração](application-proxy-configure-single-sign-on-on-premises-apps.md)de aplicação .

Para obter mais informações sobre o protocolo SAML, consulte o [protocolo SAML de inscrição única](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>SSO baseado em palavra-passe

Com o registo baseado em palavra-passe, os utilizadores acedem à aplicação com um nome de utilizador e uma senha na primeira vez que acedem à sua aplicação. Após o primeiro início de inscrição, a Azure AD fornece o nome de utilizador e a palavra-passe para a aplicação.

Com base em palavra-passe de início de sessão único usa o processo de autenticação existentes fornecido pelo aplicativo. Quando ativa a palavra-passe início de sessão único para uma aplicação, o AD do Azure recolhe e armazena em segurança os nomes de utilizador e palavras-passe para a aplicação. As credenciais do usuário são armazenadas num estado encriptado no diretório.

Escolha um único sign-on baseado em palavra-passe quando:

- Uma aplicação não suporta o protocolo de inscrição individual da SAML.
- Uma aplicação autentica-se com um nome de utilizador e palavra-passe em vez de tokens de acesso e cabeçalhos.

Com base em palavra-passe de início de sessão único é suportado para qualquer aplicativo baseado na nuvem que tem uma baseada em HTML início de sessão na página. O utilizador pode utilizar qualquer um dos seguintes browsers:

- No Windows 7 ou posterior do Internet Explorer 11
   > [!NOTE]
   > O Internet Explorer está em suporte limitado e já não recebe novas atualizações de software. O Microsoft Edge é o navegador recomendado.

- Microsoft Edge na Edição de Aniversário do Windows 10 ou mais tarde
- Microsoft Edge para iOS e Android
- Navegador Gerido intune
- No Windows 7 ou posterior e no MacOS X ou posterior do Chrome
- Firefox 26.0 ou posterior no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior

Para configurar uma aplicação em nuvem para um único sinal baseado em palavra-passe, consulte configurar o [acesso individual da palavra-passe Configurar](configure-password-single-sign-on-non-gallery-applications.md).

Para configurar um pedido no local para um único sinal através do Proxy de Aplicação, consulte a [abóbada de palavra-passe para um único sinal com procuração](application-proxy-configure-single-sign-on-password-vaulting.md) de aplicação

### <a name="how-authentication-works-for-password-based-sso"></a>Como funciona a autenticação para SSO baseado em palavra-passe

Para autenticar um utilizador numa aplicação, a Azure AD recupera as credenciais do utilizador do diretório e introduz-as na página de inscrição da aplicação.  O Azure AD passa em segurança as credenciais de utilizador através de uma extensão de browser ou aplicação móvel. Este processo permite que um administrador gerir as credenciais de utilizador e não exige que os usuários não se esqueça da palavra-passe.

> [!IMPORTANT]
> As credenciais são obfuscadas do utilizador durante o processo de início automático. No entanto, as credenciais são Detetáveis usando as ferramentas de depuração de web. Os utilizadores e administradores tem de seguir as mesmas políticas de segurança, como se as credenciais foram introduzidas diretamente pelo utilizador.

### <a name="managing-credentials-for-password-based-sso"></a>Gerir credenciais para SSO baseado em palavra-passe

Palavras-passe para cada aplicativo também podem ser geridas pelo administrador do Azure AD ou pelos utilizadores.

Quando o administrador do Azure AD gere as credenciais:  

- O utilizador não tem de repor ou não se esqueça de que o nome de utilizador e palavra-passe. O utilizador pode aceder à aplicação ao clicar no mesmo no seu painel de acesso ou através de uma ligação fornecida.
- O administrador pode efetuar tarefas de gestão nas credenciais. Por exemplo, o administrador pode atualizar o acesso a aplicações, de acordo com as associações de grupo do utilizador e o estado de funcionários.
- O administrador pode utilizar as credenciais administrativas para fornecer acesso a aplicativos compartilhados entre vários usuários. Por exemplo, o administrador pode permitir que todos os utilizadores podem aceder a uma aplicação para ter acesso a uma de redes sociais ou a aplicação de partilha de documentos.

Quando o utilizador final gere as credenciais:

- Os utilizadores podem gerir as palavras-passe por atualizar ou excluí-los conforme necessário.
- Os administradores são ainda pode definir novas credenciais para a aplicação.

## <a name="linked-sign-on"></a>Inscrição ligada
O início de sessão ligado permite ao Azure AD fornecer início de sessão único para uma aplicação que já está configurada para início de sessão único em outro serviço. O aplicativo vinculado pode aparecer aos utilizadores finais no portal do Office 365 ou portal do Azure AD MyApps. Por exemplo, um utilizador pode iniciar uma aplicação que está configurada para início de sessão único no Active Directory Federation Services 2.0 (AD FS) no portal do Office 365. Relatórios adicionais também estão disponíveis para aplicações ligadas que são iniciadas a partir do portal do Office 365 ou do portal do Azure AD MyApps. Para configurar uma aplicação para o sign-on ligado, consulte o ['sign-on' ligado](configure-linked-sign-on.md)à Configuração .

### <a name="linked-sign-on-for-application-migration"></a>Inscrição ligada para migração de candidaturas

O sign-on linked pode proporcionar uma experiência consistente ao utilizador enquanto migra aplicações durante um período de tempo. Se estiver a migrar aplicações para o Azure Ative Directory, pode utilizar o signon ligado para publicar rapidamente links para todas as aplicações que pretende migrar.  Os utilizadores podem encontrar todos os links no [portal MyApps](../user-help/active-directory-saas-access-panel-introduction.md) ou no lançador de [aplicações do Office 365.](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) Usuários não saberia que estão a aceder a um aplicativo vinculado ou aplicações migradas.  

Assim que um usuário foi autenticado com um aplicativo vinculado, um registo de conta tem de ser criado antes do utilizador final é fornecido o acesso de início de sessão único. Este registo de conta de aprovisionamento pode um ocorrem automaticamente ou podem ocorrer manualmente por um administrador.

## <a name="disabled-sso"></a>SSO desativado

Modo desativado significa o início de sessão único não é utilizado para a aplicação. Quando o início de sessão único está desativada, os utilizadores poderão ter de se autenticar duas vezes. Em primeiro lugar, os usuários são autenticados para o Azure AD e, em seguida, iniciam sessão na aplicação.

Utilização desativada modo de início de sessão único:

- Se não estiver pronto para integrar esta aplicação com o Azure AD início de sessão único, ou
- Se estiver a testar outros aspectos do aplicativo, ou
- Como uma camada de segurança para uma aplicação no local que não requer que os utilizadores autenticar. Com desativado, o utilizador tem de autenticar.

## <a name="integrated-windows-authentication-iwa-sso"></a>Autenticação do Windows (IWA) integrada SSO

[Application Proxy](application-proxy.md) fornece um único sinal on (SSO) a aplicações que utilizam [autenticação integrada do Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)ou aplicações conscientes de sinistros. Se a sua aplicação utilizar o IWA, autentica o Proxy de aplicações para a aplicação utilizando o Kerberos Constrained Delegation (KCD). Para um aplicativo compatível com declarações que confianças de entidades do Azure Active Directory, início de sessão único funciona porque o utilizador já foi autenticado através do Azure AD.

Escolha o modo de inscrição individual integrado do Windows Authentication para fornecer um único sinal de acesso a uma aplicação no local que autentica com o IWA.

Para configurar uma aplicação no local para iWA, consulte [a Delegação Limitada kerberos para obter um único sessão nas suas aplicações com procuração](application-proxy-configure-single-sign-on-with-kcd.md)de aplicação .

### <a name="how-single-sign-on-with-kcd-works"></a>Como início de sessão único com KCD funciona
Este diagrama explica o fluxo quando um utilizador acede a uma aplicação no local que utiliza o IWA.

![Diagrama de fluxo de fluxo de autenticação aditiva do Microsoft Azure](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. O utilizador introduz o URL para aceder à aplicação no local através do Proxy de Aplicação.
1. Proxy da aplicação redireciona o pedido para serviços de autenticação do Azure AD para preauthenticate. Neste momento, aplica-se do Azure AD qualquer autenticação aplicável e políticas de autorização, como a autenticação multifator. Se o utilizador for validado, o Azure AD cria um token e envia-os para o utilizador.
1. O utilizador passa o token para o Proxy de aplicações.
1. Proxy da aplicação valida o token e recupera o nome Principal de utilizador (UPN) do token. -Lo, em seguida, envia o pedido, o UPN e o nome Principal do serviço (SPN) para o conector através de um canal seguro dually autenticado.
1. O conector utiliza a negociação da Delegação Limitada kerberos (KCD) com a AD no local, fazendo-se passar pelo utilizador para obter um símbolo kerberos para a aplicação.
1. Do Active Directory envia o token de Kerberos para a aplicação para o conector.
1. O conector envia a solicitação original para o servidor de aplicações, com o token Kerberos que recebeu do AD.
1. A aplicação envia a resposta para o conector, que, em seguida, é devolvido para o serviço de Proxy da aplicação e, finalmente, para o usuário.

## <a name="header-based-sso"></a>SSO baseado em cabeçalho

Com base no cabeçalho de início de sessão único funciona para as aplicações que utilizam cabeçalhos HTTP para autenticação. Este método de início de sessão utiliza um serviço de autenticação de terceiros chamado PingAccess. Um utilizador só tem de autenticar para o Azure AD.

Escolha um único sinal baseado em cabeçalho quando o Proxy de Aplicação e o PingAccess estiverem configurados para a aplicação.

Para configurar a autenticação baseada em cabeçalhos, consulte a [autenticação baseada em cabeçalho para um único sinal com procuração](application-proxy-configure-single-sign-on-with-ping-access.md)de aplicação .

### <a name="what-is-pingaccess-for-azure-ad"></a>O que é o PingAccess para o Azure AD?

Usando o PingAccess para o Azure AD, os utilizadores podem acesso e o único início de sessão em aplicações que utilizam cabeçalhos para autenticação. Proxy de aplicações trata esses aplicativos, como qualquer outro, utilizar o Azure AD para autenticar o acesso e, em seguida, passar o tráfego através do serviço de conector. Após a ocorrência da autenticação, o serviço do PingAccess traduz-se o token de acesso do Azure AD para um formato de cabeçalho que é enviado para a aplicação.

Os usuários não notará nada diferente quando iniciam sessão para utilizar as suas aplicações empresariais. Pode ainda trabalham de qualquer lugar em qualquer dispositivo. Os conectores de Proxy de aplicações direcionar o tráfego remoto a todas as aplicações, e eles continuarão automaticamente o balanceamento de carga.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Como posso obter uma licença para o PingAccess?

Uma vez que este cenário é oferecido através de uma parceria entre a Azure AD e o PingAccess, precisa de licenças para ambos os serviços. No entanto, as subscrições do Azure AD Premium incluem uma licença básica do PingAccess que cobre até 20 aplicações. Se precisar de mais de 20 aplicativos baseados no cabeçalho de publicar, pode adquirir uma licença adicional do PingAccess.

Para obter mais informações, consulte [Edições do Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="related-articles"></a>Artigos relacionados
* [Tutorials for integrating SaaS applications with Azure Active Directory](../saas-apps/tutorial-list.md) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)
* [Configurar um único sign-on baseado em SAML](configure-single-sign-on-non-gallery-applications.md)
* [Configurar um único sinal baseado em palavra-passe](configure-password-single-sign-on-non-gallery-applications.md)
* [Configuração de inscrição ligada](configure-linked-sign-on.md)
* [Introdução à Gestão do Acesso às Aplicações](what-is-access-management.md)
* Link de descarregamento: [Plano de implementação de um único sinal.](https://aka.ms/SSODeploymentPlan)
