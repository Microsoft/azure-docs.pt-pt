---
title: Início de sessão único para aplicações - Azure Active Directory | Documentos da Microsoft
description: Saiba como escolher um método de início de sessão único, quando configurar as aplicações no Azure Active Directory (Azure AD). Utilize o início de sessão único para que os usuários não precisam se lembrar de senhas para cada aplicativo e para simplificar a administração de gestão de contas.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: celested
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 186bc220778ed669672bfbc689dad6471195f4ce
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448565"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Início de sessão único para aplicações no Azure Active Directory
Início de sessão único (SSO) adiciona segurança e conveniência, quando os utilizadores início de sessão em aplicações no Azure Active Directory (Azure AD). Este artigo descreve os métodos de início de sessão únicos e ajuda a escolher o método mais apropriado de SSO quando configurar as suas aplicações.

- **Com início de sessão único**, os utilizadores iniciam sessão uma vez com uma conta para aceder a dispositivos associados a um domínio, recursos, software como aplicações de serviço (SaaS), da empresa e aplicações web. Depois de iniciar sessão, o utilizador pode iniciar as aplicações do portal do Office 365 ou o painel de acesso do Azure AD MyApps. Os administradores podem centralizar a gestão de contas de utilizador e automaticamente adicionar ou remover o acesso de utilizador para aplicações com base na associação de grupo. 

- **Sem início de sessão único**, os utilizadores tem de se lembrar de senhas específicas da aplicação e iniciar sessão em cada aplicação. Necessidades de pessoal de TI para criar e atualizar as contas de utilizador para cada aplicativo, como o Office 365, a caixa e o Salesforce. Os utilizadores têm de se lembrar de suas senhas, além de perder tempo a iniciar sessão em cada aplicação.

## <a name="choosing-a-single-sign-on-method"></a>Escolher um método de início de sessão único

Existem várias formas de configurar uma aplicação para início de sessão único. Escolher um método de início de sessão único depende da forma como a aplicação está configurada para autenticação. 

- Aplicações na cloud podem utilizar o OpenID Connect, OAuth, SAML, com base em palavra-passe, ligado ou métodos de desativado para início de sessão único. 
- Aplicações no local podem utilizar a autenticação de Windows integrada, com base em palavra-passe, métodos com base no cabeçalho, ligados ou desativadas para início de sessão único. As opções de locais de trabalho quando aplicativos são configurados para Proxy de aplicações.

Este fluxograma ajuda-o a decidir que método de início de início de sessão único é melhor para sua situação. 

![Escolher método de início de sessão único](./media/what-is-single-sign-on/choose-single-sign-on-method.png)

A tabela seguinte resume os métodos de início de sessão únicos e ligações para obter mais detalhes. 

| Método de início de sessão único | Tipos de aplicação | Quando utilizar |
| :------ | :------- | :----- |
| [OpenID Connect e OAuth](#openid-connect-and-oauth) | Apenas na nuvem | Utilize o OpenID Connect e OAuth ao desenvolver um novo aplicativo. Este protocolo simplifica a configuração da aplicação, tem SDKs fácil de usar e permite que a sua aplicação para utilizar o MS Graph.
| [SAML](#saml-sso) | Apenas na nuvem | Escolha SAML sempre que possível para os aplicativos existentes que não usam o OpenID Connect ou do OAuth. SAML funciona para as aplicações que se autenticam utilizando um dos protocolos de SAML.|
| [Com base em palavra-passe](#password-based-sso) | na cloud e no local | Escolha a palavra-passe quando o aplicativo efetua a autenticação com o nome de utilizador e palavra-passe. Com base em palavra-passe de início de sessão único permite o armazenamento de palavra-passe de aplicação segura e de repetição com uma extensão de browser ou aplicação móvel. Este método utiliza o início de sessão no processo existente fornecido pela aplicação, mas permite que um administrador gerir as palavras-passe. |
| [Ligado](#linked-sso) | na cloud e no local | Escolha ligado início de sessão único quando a aplicação está configurada para início de sessão único em outro serviço do fornecedor de identidade. Esta opção não adiciona o início de sessão único para a aplicação. No entanto, a aplicação já pode ter início de sessão único implementado através de outro serviço, como serviços de Federação do Active Directory.|
| [Desativado](#disabled-sso) | na cloud e no local | Escolha desativado início de sessão único quando a aplicação não está pronta para ser configurado para início de sessão único. Os utilizadores têm de introduzir o respetivo nome de utilizador e a palavra-passe sempre que iniciarem esta aplicação.|
| [Autenticação integrada do Windows (IWA)](#integrated-windows-authentication-iwa-sso) | apenas no local | Escolha IWA início de sessão único para aplicações que utilizam [autenticação integrada do Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication), ou aplicações com suporte para afirmações. Para IWA, os conectores de Proxy da aplicação utilizam a delegação restrita de Kerberos (KCD) para autenticar utilizadores para a aplicação. | 
| [Com base no cabeçalho](#header-based-sso) | apenas no local | Utilize com base no cabeçalho de início de sessão único quando o aplicativo usa cabeçalhos para autenticação. Com base no cabeçalho de início de sessão único requer o PingAccess para o Azure AD. Proxy de aplicações utiliza o Azure AD para autenticar o utilizador e, em seguida, passa o tráfego através do serviço de conector.  | 

## <a name="openid-connect-and-oauth"></a>OpenID Connect e OAuth
Ao desenvolver novos aplicativos, utilize protocolos modernos, como o OpenID Connect e OAuth para alcançar a melhor única início de sessão experiência para a sua aplicação em várias plataformas de dispositivo. OAuth permite que os utilizadores ou os administradores [conceder autorização](configure-user-consent.md) como o recursos protegidos [MS Graph](/graph/overview). Fornecemos fácil adotar [SDKs](../develop/reference-v2-libraries.md) para a sua aplicação e, além disso, a aplicação estará pronta a utilizar [MS Graph](/graph/overview).

Para obter mais informações, consulte:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Guia para programadores do Active Directory Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>SAML SSO
Com o **SAML início de sessão único**, o Azure AD autentica para o aplicativo usando a conta de utilizador do Azure AD. O Azure AD comunica as informações de início de sessão para a aplicação através de um protocolo de ligação. Com baseado em SAML início de sessão único, pode mapear os utilizadores a funções de aplicação específica, com base nas regras que definir nas afirmações SAML.

Escolha baseado em SAML início de sessão único quando o aplicativo oferece suporte a ele.


Baseado em SAML início de sessão único é suportada para aplicações que utilizam estes protocolos de:

- SAML 2.0
- WS-Federation

Para configurar uma aplicação para baseado em SAML início de sessão único, consulte [baseado em SAML configurar início de sessão único](configure-single-sign-on-portal.md). Além disso, muitos Software como um aplicativo de serviço (SaaS) tem um [específico do aplicativo tutorial](../saas-apps/tutorial-list.md) que as etapas através da configuração para SAML com base no início de sessão único.

Para configurar uma aplicação para WS-Federation, seguir as mesmas regras para configurar a aplicação para baseado em SAML início de sessão único, consulte [baseado em SAML configurar início de sessão único](configure-single-sign-on-portal.md). No passo para configurar a aplicação para utilizar o Azure AD, terá de substituir o URL de início de sessão do Azure AD para o ponto de extremidade do WS-Federation `https://login.microsoftonline.com/<tenant-ID>/wsfed`.

Para obter mais informações sobre o protocolo SAML, consulte [único início de sessão do protocolo SAML](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>SSO baseado em palavra-passe
Com baseado em palavra-passe de início de sessão, utilizadores iniciam sessão aplicação com um nome de utilizador e palavra-passe na primeira vez que acederem ao mesmo. Após o primeiro início de sessão, o Azure AD fornece o nome de utilizador e palavra-passe para a aplicação. 

Com base em palavra-passe de início de sessão único usa o processo de autenticação existentes fornecido pelo aplicativo. Quando ativa a palavra-passe início de sessão único para uma aplicação, o AD do Azure recolhe e armazena em segurança os nomes de utilizador e palavras-passe para a aplicação. As credenciais do usuário são armazenadas num estado encriptado no diretório. 

Escolher com base em palavra-passe único início de sessão quando:

- Uma aplicação não suporta o protocolo SAML de único início de sessão.
- Uma aplicação autentica-se com um nome de utilizador e palavra-passe em vez de tokens de acesso e cabeçalhos.

Com base em palavra-passe de início de sessão único é suportado para qualquer aplicativo baseado na nuvem que tem uma baseada em HTML início de sessão na página. O utilizador pode utilizar qualquer um dos seguintes browsers:

- No Windows 7 ou posterior do Internet Explorer 11
- Microsoft Edge no Windows 10 Anniversary Edition ou posterior
- No Windows 7 ou posterior e no MacOS X ou posterior do Chrome
- Firefox 26.0 ou posterior no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior

Para configurar uma aplicação na cloud para baseado em palavra-passe de início de sessão único, consulte [configurar a aplicação para a palavra-passe início de sessão único](application-sign-in-problem-password-sso-gallery.md#configure-the-application-for-password-single-sign-on).

Para configurar uma aplicação no local para o início de sessão único através do Proxy de aplicações, consulte [vaulting para início de sessão único com o Proxy da aplicação de palavra-passe](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>Como funciona a autenticação para SSO baseado em palavra-passe

Para autenticar um usuário a uma aplicação, o Azure AD obtém as credenciais do utilizador do diretório e insere em página de início de sessão do aplicativo.  O Azure AD passa em segurança as credenciais de utilizador através de uma extensão de browser ou aplicação móvel. Este processo permite que um administrador gerir as credenciais de utilizador e não exige que os usuários não se esqueça da palavra-passe.

> [!IMPORTANT]
> As credenciais são oculto do usuário durante o processo de início de sessão automatizado. No entanto, as credenciais são Detetáveis usando as ferramentas de depuração de web. Os utilizadores e administradores tem de seguir as mesmas políticas de segurança, como se as credenciais foram introduzidas diretamente pelo utilizador.

### <a name="managing-credentials-for-password-based-sso"></a>Gerir credenciais para SSO baseado em palavra-passe

Palavras-passe para cada aplicativo também podem ser geridas pelo administrador do Azure AD ou pelos utilizadores.

Quando o administrador do Azure AD gere as credenciais:  

- O utilizador não tem de repor ou não se esqueça de que o nome de utilizador e palavra-passe. O utilizador pode aceder à aplicação ao clicar no mesmo no seu painel de acesso ou através de uma ligação fornecida.
- O administrador pode efetuar tarefas de gestão nas credenciais. Por exemplo, o administrador pode atualizar o acesso a aplicações, de acordo com as associações de grupo do utilizador e o estado de funcionários.
- O administrador pode utilizar as credenciais administrativas para fornecer acesso a aplicativos compartilhados entre vários usuários. Por exemplo, o administrador pode permitir que todos os utilizadores podem aceder a uma aplicação para ter acesso a uma de redes sociais ou a aplicação de partilha de documentos.

Quando o utilizador final gere as credenciais:

- Os utilizadores podem gerir as palavras-passe por atualizar ou excluí-los conforme necessário. 
- Os administradores são ainda pode definir novas credenciais para a aplicação.


## <a name="linked-sso"></a>SSO ligado
O início de sessão ligado permite ao Azure AD fornecer início de sessão único para uma aplicação que já está configurada para início de sessão único em outro serviço. O aplicativo vinculado pode aparecer aos utilizadores finais no portal do Office 365 ou portal do Azure AD MyApps. Por exemplo, um utilizador pode iniciar uma aplicação que está configurada para início de sessão único no Active Directory Federation Services 2.0 (AD FS) no portal do Office 365. Relatórios adicionais também estão disponíveis para aplicações ligadas que são iniciadas a partir do portal do Office 365 ou do portal do Azure AD MyApps. 

### <a name="linked-sso-for-application-migration"></a>Ligado SSO para a migração de aplicativo

SSO ligado pode fornecer uma experiência de usuário consistente, durante a migração aplicativos durante um período de tempo. Se estiver a migrar aplicações para o Azure Active Directory, pode utilizar ligado início de sessão único para publicar rapidamente as ligações para todas as aplicações que pretende migrar.  Os usuários podem encontrar todas as ligações na [MyApps portal](../user-help/active-directory-saas-access-panel-introduction.md) ou o [iniciador de aplicações do Office 365](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Usuários não saberia que estão a aceder a um aplicativo vinculado ou aplicações migradas.  

Assim que um usuário foi autenticado com um aplicativo vinculado, um registo de conta tem de ser criado antes do utilizador final é fornecido o acesso de início de sessão único. Este registo de conta de aprovisionamento pode um ocorrem automaticamente ou podem ocorrer manualmente por um administrador.

## <a name="disabled-sso"></a>SSO desativado

Modo desativado significa o início de sessão único não é utilizado para a aplicação. Quando o início de sessão único está desativada, os utilizadores poderão ter de se autenticar duas vezes. Em primeiro lugar, os usuários são autenticados para o Azure AD e, em seguida, iniciam sessão na aplicação. 

Utilização desativada modo de início de sessão único:

- Se não estiver pronto para integrar esta aplicação com o Azure AD início de sessão único, ou
- Se estiver a testar outros aspectos do aplicativo, ou
- Como uma camada de segurança para uma aplicação no local que não requer que os utilizadores autenticar. Com desativado, o utilizador tem de autenticar. 

## <a name="integrated-windows-authentication-iwa-sso"></a>Autenticação do Windows (IWA) integrada SSO

[Proxy de aplicações](application-proxy.md) fornece logon único (SSO) para aplicativos que usam [autenticação integrada do Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication), ou aplicações com suporte para afirmações. Se a sua aplicação utilizar o IWA, autentica o Proxy de aplicações para a aplicação utilizando o Kerberos Constrained Delegation (KCD). Para um aplicativo compatível com declarações que confianças de entidades do Azure Active Directory, início de sessão único funciona porque o utilizador já foi autenticado através do Azure AD.

Escolha a autenticação integrada do Windows única-modo de sessão:

- Para fornecer início de sessão único para uma aplicação no local que autentica com IWA. 

Para configurar uma aplicação no local para IWA, consulte [delegação restrita de Kerberos para início de sessão único para aplicações com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md). 

### <a name="how-single-sign-on-with-kcd-works"></a>Como início de sessão único com KCD funciona
Este diagrama explica o fluxo quando um utilizador acede a uma aplicação no local que utiliza o IWA.

![Diagrama de fluxo de autenticação do Microsoft AAD](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. O utilizador introduz o URL para aceder à aplicação de local no através do Proxy de aplicações.
2. Proxy da aplicação redireciona o pedido para serviços de autenticação do Azure AD para preauthenticate. Neste momento, aplica-se do Azure AD qualquer autenticação aplicável e políticas de autorização, como a autenticação multifator. Se o utilizador for validado, o Azure AD cria um token e envia-os para o utilizador.
3. O utilizador passa o token para o Proxy de aplicações.
4. Proxy da aplicação valida o token e recupera o nome Principal de utilizador (UPN) do token. -Lo, em seguida, envia o pedido, o UPN e o nome Principal do serviço (SPN) para o conector através de um canal seguro dually autenticado.
5. O conector utiliza a negociação de Kerberos Constrained Delegation (KCD) com o AD, representar o utilizador para obter um token de Kerberos para o aplicativo de no local.
6. Do Active Directory envia o token de Kerberos para a aplicação para o conector.
7. O conector envia a solicitação original para o servidor de aplicações, com o token Kerberos que recebeu do AD.
8. A aplicação envia a resposta para o conector, que, em seguida, é devolvido para o serviço de Proxy da aplicação e, finalmente, para o usuário.

## <a name="header-based-sso"></a>SSO baseado em cabeçalho

Com base no cabeçalho de início de sessão único funciona para as aplicações que utilizam cabeçalhos HTTP para autenticação. Este método de início de sessão utiliza um serviço de autenticação de terceiros chamado PingAccess. Um utilizador só tem de autenticar para o Azure AD. 

Escolher com base no cabeçalho único início de sessão quando:

- Proxy de aplicações e o PingAccess estão configurados para a aplicação

Para configurar a autenticação baseada em cabeçalho, consulte [autenticação baseada em cabeçalho para início de sessão único com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-ping-access.md). 

### <a name="what-is-pingaccess-for-azure-ad"></a>O que é o PingAccess para o Azure AD?

Usando o PingAccess para o Azure AD, os utilizadores podem acesso e o único início de sessão em aplicações que utilizam cabeçalhos para autenticação. Proxy de aplicações trata esses aplicativos, como qualquer outro, utilizar o Azure AD para autenticar o acesso e, em seguida, passar o tráfego através do serviço de conector. Após a ocorrência da autenticação, o serviço do PingAccess traduz-se o token de acesso do Azure AD para um formato de cabeçalho que é enviado para a aplicação.

Os usuários não notará nada diferente quando iniciam sessão para utilizar as suas aplicações empresariais. Pode ainda trabalham de qualquer lugar em qualquer dispositivo. Os conectores de Proxy de aplicações direcionar o tráfego remoto a todas as aplicações, e eles continuarão automaticamente o balanceamento de carga.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Como posso obter uma licença para o PingAccess?

Uma vez que este cenário é disponibilizado através de uma parceria entre o Azure AD e o PingAccess, precisa de licenças para ambos os serviços. No entanto, as subscrições do Azure AD Premium incluem uma licença de PingAccess básica, que abrange até 20 aplicações. Se precisar de mais de 20 aplicativos baseados no cabeçalho de publicar, pode adquirir uma licença adicional do PingAccess. 

Para obter mais informações, consulte [Edições do Azure Active Directory](../fundamentals/active-directory-whatis.md).


## <a name="related-articles"></a>Artigos relacionados
* [Tutoriais para integrar aplicações SaaS com o Azure Active Directory](../saas-apps/tutorial-list.md)
* [Tutorial para configurar o início de sessão único](configure-single-sign-on-portal.md)
* [Introdução à gestão do acesso a aplicações](what-is-access-management.md)
* Ligação de transferência: [Plano de implantação de início de sessão único](https://aka.ms/SSODeploymentPlan).


