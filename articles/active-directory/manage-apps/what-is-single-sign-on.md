---
title: Logon único para aplicativos-Azure Active Directory | Microsoft Docs
description: Saiba como escolher um método de logon único ao configurar aplicativos no Azure Active Directory (AD do Azure). Use o logon único para que os usuários não precisem se lembrar de senhas para cada aplicativo e para simplificar a administração do gerenciamento de conta.
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
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786401"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Logon único para aplicativos no Azure Active Directory

O SSO (logon único) adiciona segurança e conveniência quando os usuários entram em aplicativos no Azure Active Directory (Azure AD). Este artigo descreve os métodos de logon único e ajuda a escolher o método de SSO mais apropriado ao configurar seus aplicativos.

- **Com o logon único**, os usuários entram uma vez com uma conta para acessar dispositivos ingressados no domínio, recursos da empresa, aplicativos SaaS (software como serviço) e aplicativos Web. Depois de entrar, o usuário pode iniciar aplicativos no portal do Office 365 ou no painel de acesso do Azure AD myapps. Os administradores podem centralizar o gerenciamento de contas de usuário e adicionar ou remover automaticamente o acesso do usuário a aplicativos com base na associação de grupo.

- **Sem o logon único**, os usuários devem se lembrar de senhas específicas do aplicativo e entrar em cada aplicativo. A equipe de ti precisa criar e atualizar contas de usuário para cada aplicativo, como Office 365, Box e Salesforce. Os usuários precisam se lembrar de suas senhas, além de gastar tempo para entrar em cada aplicativo.

## <a name="choosing-a-single-sign-on-method"></a>Escolhendo um método de logon único

Há várias maneiras de configurar um aplicativo para logon único. A escolha de um método de logon único depende de como o aplicativo está configurado para autenticação.

- Os aplicativos de nuvem podem usar os métodos OpenID Connect, OAuth, SAML, com base em senha, vinculados ou desabilitados para logon único. 
- Os aplicativos locais podem usar os métodos com base em senha, autenticação integrada do Windows, com base em cabeçalho, vinculado ou desabilitado para logon único. As opções locais funcionam quando os aplicativos são configurados para o proxy de aplicativo.

Este fluxograma ajuda você a decidir qual método de logon único é melhor para sua situação.

![Fluxograma de decisão para o método de logon único](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

A tabela a seguir resume os métodos de logon único e links para mais detalhes.

| Método de logon único | Tipos de aplicação | Quando utilizar |
| :------ | :------- | :----- |
| [OpenID Connect e OAuth](#openid-connect-and-oauth) | somente na nuvem | Use o OpenID Connect e o OAuth ao desenvolver um novo aplicativo. Esse protocolo simplifica a configuração do aplicativo, tem SDKs fáceis de usar e permite que seu aplicativo use o MS Graph.
| [Authority](#saml-sso) | na nuvem e no local | Escolha o SAML sempre que possível para aplicativos existentes que não usam o OpenID Connect ou o OAuth. O SAML funciona para aplicativos que se autenticam usando um dos protocolos SAML.|
| [Baseado em senha](#password-based-sso) | na nuvem e no local | Escolha com base em senha quando o aplicativo for autenticado com nome de usuário e senha. O logon único baseado em senha permite proteger o armazenamento de senha do aplicativo e repetir usando uma extensão de navegador da Web ou aplicativo móvel. Esse método usa o processo de entrada existente fornecido pelo aplicativo, mas permite que um administrador gerencie as senhas. |
| [Vinculado](#linked-sign-on) | na nuvem e no local | Escolha logon vinculado quando o aplicativo estiver configurado para logon único em outro serviço de provedor de identidade. Essa opção não adiciona o logon único ao aplicativo. No entanto, o aplicativo pode já ter o logon único implementado usando outro serviço, como Serviços de Federação do Active Directory (AD FS).|
| [Desativado](#disabled-sso) | na nuvem e no local | Escolha logon único desabilitado quando o aplicativo não estiver pronto para ser configurado para logon único. Os usuários precisam inserir seu nome de usuário e senha toda vez que iniciarem esse aplicativo.|
| [Autenticação integrada do Windows (IWA)](#integrated-windows-authentication-iwa-sso) | somente local | Escolha logon único do IWA para aplicativos que usam [IWA (autenticação integrada do Windows)](/aspnet/web-api/overview/security/integrated-windows-authentication)ou aplicativos com reconhecimento de declaração. Para IWA, os conectores de proxy de aplicativo usam a KCD (delegação restrita de Kerberos) para autenticar usuários no aplicativo. |
| [Baseado em cabeçalho](#header-based-sso) | somente local | Use o logon único baseado em cabeçalho quando o aplicativo usar cabeçalhos para autenticação. O logon único baseado em cabeçalho requer o PingAccess para o Azure AD. O proxy de aplicativo usa o Azure AD para autenticar o usuário e, em seguida, passa o tráfego pelo serviço do conector.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect e OAuth

Ao desenvolver novos aplicativos, use protocolos modernos como o OpenID Connect e o OAuth para obter a melhor experiência de logon único para seu aplicativo em várias plataformas de dispositivo. O OAuth permite que usuários ou administradores [concedam consentimento](configure-user-consent.md) para recursos protegidos, como [Microsoft Graph](/graph/overview). Fornecemos [SDKs](../develop/reference-v2-libraries.md) fáceis de adotar para seu aplicativo e, além disso, seu aplicativo estará pronto para usar [Microsoft Graph](/graph/overview).

Para obter mais informações, veja:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Guia do desenvolvedor da plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>SAML SSO

Com o **logon único do SAML**, o Azure AD é autenticado no aplicativo usando a conta do Azure AD do usuário. O Azure AD comunica as informações de logon para o aplicativo por meio de um protocolo de conexão. Com o logon único baseado em SAML, você pode mapear usuários para funções de aplicativo específicas com base nas regras que você define em suas declarações SAML.

Escolha logon único baseado em SAML quando o aplicativo oferecer suporte a ele.

O logon único baseado em SAML tem suporte para aplicativos que usam qualquer um destes protocolos:

- SAML 2.0
- WS-Federation

Para configurar um aplicativo SaaS para logon único baseado em SAML, consulte [Configurar logon único baseado em SAML](configure-single-sign-on-non-gallery-applications.md). Além disso, muitos aplicativos SaaS (software como serviço) têm um [tutorial específico do aplicativo](../saas-apps/tutorial-list.md) que o orienta pela configuração do logon único baseado em SAML.

Para configurar um aplicativo para o WS-Federation, siga as mesmas diretrizes para configurar o aplicativo para logon único baseado em SAML, consulte [Configurar logon único baseado em SAML](configure-single-sign-on-non-gallery-applications.md). Na etapa de configurar o aplicativo para usar o Azure AD, você precisará substituir a URL de logon do Azure AD para o ponto de extremidade do WS-Federation `https://login.microsoftonline.com/<tenant-ID>/wsfed`.

Para configurar um aplicativo local para logon único baseado em SAML, consulte [logon único do SAML para aplicativos locais com o proxy de aplicativo](application-proxy-configure-single-sign-on-on-premises-apps.md).

Para obter mais informações sobre o protocolo SAML, consulte [protocolo SAML de logon único](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>SSO baseado em senha

Com o logon baseado em senha, os usuários entram no aplicativo com um nome de usuário e senha na primeira vez que o acessam. Após o primeiro logon, o Azure AD fornece o nome de usuário e a senha para o aplicativo.

O logon único baseado em senha usa o processo de autenticação existente fornecido pelo aplicativo. Quando você habilita o logon único com senha para um aplicativo, o Azure AD coleta e armazena com segurança nomes de usuário e senhas para o aplicativo. As credenciais do usuário são armazenadas em um estado criptografado no diretório.

Escolha logon único baseado em senha quando:

- Um aplicativo não dá suporte ao protocolo de logon único SAML.
- Um aplicativo é autenticado com um nome de usuário e senha em vez de tokens de acesso e cabeçalhos.

O logon único baseado em senha tem suporte para qualquer aplicativo baseado em nuvem que tenha uma página de entrada baseada em HTML. O usuário pode usar qualquer um dos seguintes navegadores:

- Internet Explorer 11 no Windows 7 ou posterior
   > [!NOTE]
   > O Internet Explorer está em suporte limitado e não recebe mais novas atualizações de software. O Microsoft Edge é o navegador recomendado.

- Microsoft Edge no Windows 10 aniversário Edition ou posterior
- Microsoft Edge para iOS e Android
- Intune Managed Browser
- Chrome no Windows 7 ou posterior e no MacOS X ou posterior
- Firefox 26,0 ou posterior no Windows XP SP2 ou posterior e no Mac OS X 10,6 ou posterior

Para configurar um aplicativo de nuvem para logon único baseado em senha, consulte [Configurar logon único com senha](configure-password-single-sign-on-non-gallery-applications.md).

Para configurar um aplicativo local para logon único por meio do proxy de aplicativo, consulte [compartimentação de senha para logon único com o proxy de aplicativo](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>Como funciona a autenticação para SSO baseado em senha

Para autenticar um usuário em um aplicativo, o Azure AD recupera as credenciais do usuário do diretório e as insere na página de logon do aplicativo.  O Azure AD passa com segurança as credenciais do usuário por meio de uma extensão de navegador da Web ou aplicativo móvel. Esse processo permite que um administrador gerencie as credenciais do usuário e não exige que os usuários se comentem com sua senha.

> [!IMPORTANT]
> As credenciais são ofuscadas do usuário durante o processo de logon automatizado. No entanto, as credenciais são detectáveis usando ferramentas de depuração da Web. Os usuários e administradores precisam seguir as mesmas políticas de segurança como se as credenciais fossem inseridas diretamente pelo usuário.

### <a name="managing-credentials-for-password-based-sso"></a>Gerenciando credenciais para SSO baseado em senha

As senhas para cada aplicativo podem ser gerenciadas pelo administrador do Azure AD ou pelos usuários.

Quando o administrador do Azure AD gerencia as credenciais:  

- O usuário não precisa redefinir ou lembrar o nome de usuário e a senha. O usuário pode acessar o aplicativo clicando nele no painel de acesso ou por meio de um link fornecido.
- O administrador pode realizar tarefas de gerenciamento nas credenciais. Por exemplo, o administrador pode atualizar o acesso ao aplicativo de acordo com as associações de grupo de usuários e o status do funcionário.
- O administrador pode usar credenciais administrativas para fornecer acesso a aplicativos compartilhados entre vários usuários. Por exemplo, o administrador pode permitir que todos que possam acessar um aplicativo tenham acesso a uma mídia social ou a um aplicativo de compartilhamento de documentos.

Quando o usuário final gerencia as credenciais:

- Os usuários podem gerenciar suas senhas atualizando-as ou excluindo-as conforme necessário.
- Os administradores ainda podem definir novas credenciais para o aplicativo.

## <a name="linked-sign-on"></a>Logon vinculado
O logon vinculado permite que o Azure AD forneça logon único a um aplicativo que já está configurado para logon único em outro serviço. O aplicativo vinculado pode aparecer para usuários finais no portal do Office 365 ou no portal do Azure AD myapps. Por exemplo, um usuário pode iniciar um aplicativo configurado para logon único no Serviços de Federação do Active Directory (AD FS) 2,0 (AD FS) no portal do Office 365. Relatórios adicionais também estão disponíveis para aplicativos vinculados que são iniciados no portal do Office 365 ou no portal do Azure AD myapps. Para configurar um aplicativo para logon vinculado, consulte [Configurar logon vinculado](configure-linked-sign-on.md).

### <a name="linked-sign-on-for-application-migration"></a>Logon vinculado para migração de aplicativo

O logon vinculado pode fornecer uma experiência de usuário consistente enquanto você migra aplicativos durante um período de tempo. Se estiver migrando aplicativos para Azure Active Directory, você poderá usar o logon vinculado para publicar links rapidamente para todos os aplicativos que pretende migrar.  Os usuários podem encontrar todos os links no [portal do myapps](../user-help/active-directory-saas-access-panel-introduction.md) ou no [iniciador de aplicativos do Office 365](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Os usuários não sabem que estão acessando um aplicativo vinculado ou um aplicativo migrado.  

Depois que um usuário for autenticado com um aplicativo vinculado, um registro de conta precisará ser criado antes de o usuário final receber acesso de logon único. O provisionamento desse registro de conta pode ocorrer automaticamente ou pode ocorrer manualmente por um administrador.

## <a name="disabled-sso"></a>SSO desabilitado

Modo desabilitado significa que o logon único não é usado para o aplicativo. Quando o logon único está desabilitado, os usuários podem precisar autenticar duas vezes. Primeiro, os usuários se autenticam no Azure AD e, em seguida, entram no aplicativo.

Usar o modo de logon único desabilitado:

- Se você não estiver pronto para integrar esse aplicativo com o logon único do Azure AD ou
- Se você estiver testando outros aspectos do aplicativo ou
- Como uma camada de segurança para um aplicativo local que não exige que os usuários se autentiquem. Com o desabilitado, o usuário precisa se autenticar.

## <a name="integrated-windows-authentication-iwa-sso"></a>SSO de autenticação integrada do Windows (IWA)

O [proxy de aplicativo](application-proxy.md) fornece SSO (logon único) para aplicativos que usam [IWA (autenticação integrada do Windows)](/aspnet/web-api/overview/security/integrated-windows-authentication)ou aplicativos com reconhecimento de declaração. Se seu aplicativo usar IWA, o proxy de aplicativo autenticará o aplicativo usando a KCD (delegação restrita de Kerberos). Para um aplicativo com reconhecimento de declaração que confia Azure Active Directory, o logon único funciona porque o usuário já foi autenticado usando o Azure AD.

Escolha o modo de logon único da autenticação integrada do Windows para fornecer logon único a um aplicativo local que é autenticado com o IWA.

Para configurar um aplicativo local para IWA, consulte [delegação restrita de Kerberos para logon único em seus aplicativos com o proxy de aplicativo](application-proxy-configure-single-sign-on-with-kcd.md).

### <a name="how-single-sign-on-with-kcd-works"></a>Como o logon único com o KCD funciona
Este diagrama explica o fluxo quando um usuário acessa um aplicativo local que usa IWA.

![Diagrama de fluxo de autenticação Microsoft Azure AD](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. O usuário insere a URL para acessar o aplicativo local por meio do proxy de aplicativo.
1. O proxy de aplicativo redireciona a solicitação aos serviços de autenticação do Azure AD para pré-autenticação. Neste ponto, o Azure AD aplica quaisquer políticas de autenticação e autorização aplicáveis, como a autenticação multifator. Se o usuário for validado, o Azure AD criará um token e o enviará para o usuário.
1. O usuário passa o token para o proxy de aplicativo.
1. O proxy de aplicativo valida o token e recupera o UPN (nome principal do usuário) do token. Em seguida, ele envia a solicitação, o UPN e o nome da entidade de serviço (SPN) para o conector por meio de um canal seguro autenticado dualmente.
1. O conector usa a negociação de KCD (delegação restrita de Kerberos) com o AD local, representando o usuário para obter um token Kerberos para o aplicativo.
1. Active Directory envia o token Kerberos para o aplicativo para o conector.
1. O conector envia a solicitação original para o servidor de aplicativos, usando o token Kerberos recebido do AD.
1. O aplicativo envia a resposta ao conector, que é retornada ao serviço de proxy de aplicativo e, finalmente, ao usuário.

## <a name="header-based-sso"></a>SSO baseado em cabeçalho

O logon único baseado em cabeçalho funciona para aplicativos que usam cabeçalhos HTTP para autenticação. Esse método de logon usa um serviço de autenticação de terceiros chamado PingAccess. Um usuário só precisa se autenticar no Azure AD.

Escolha logon único baseado em cabeçalho quando o proxy de aplicativo e o PingAccess estiverem configurados para o aplicativo.

Para configurar a autenticação baseada em cabeçalho, consulte [autenticação baseada em cabeçalho para logon único com o proxy de aplicativo](application-proxy-configure-single-sign-on-with-ping-access.md).

### <a name="what-is-pingaccess-for-azure-ad"></a>O que é o PingAccess para o Azure AD?

Usando o PingAccess para o Azure AD, os usuários podem acessar e fazer logon único em aplicativos que usam cabeçalhos para autenticação. O proxy de aplicativo trata esses aplicativos como qualquer outro, usando o Azure AD para autenticar o acesso e, em seguida, passando o tráfego pelo serviço do conector. Depois que a autenticação ocorre, o serviço PingAccess converte o token de acesso do Azure AD em um formato de cabeçalho que é enviado para o aplicativo.

Os usuários não notarão nada diferente quando entrarem para usar seus aplicativos corporativos. Eles ainda podem trabalhar de qualquer lugar em qualquer dispositivo. Os conectores de proxy de aplicativo direcionam o tráfego remoto para todos os aplicativos e eles continuarão a balancear a carga automaticamente.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Como fazer obter uma licença para PingAccess?

Como esse cenário é oferecido por meio de uma parceria entre o Azure AD e o PingAccess, você precisa de licenças para ambos os serviços. No entanto, as assinaturas do Azure AD Premium incluem uma licença básica do PingAccess que abrange até 20 aplicativos. Se você precisar publicar mais de 20 aplicativos baseados em cabeçalho, poderá adquirir uma licença adicional do PingAccess.

Para obter mais informações, consulte [Edições do Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="related-articles"></a>Artigos relacionados
* [Tutorials for integrating SaaS applications with Azure Active Directory](../saas-apps/tutorial-list.md) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)
* [Configurando o logon único baseado em SAML](configure-single-sign-on-non-gallery-applications.md)
* [Configurando o logon único baseado em senha](configure-password-single-sign-on-non-gallery-applications.md)
* [Configurando o logon vinculado](configure-linked-sign-on.md)
* [Introdução ao gerenciamento de acesso a aplicativos](what-is-access-management.md)
* Link de download: [plano de implantação de logon único](https://aka.ms/SSODeploymentPlan).
