---
title: Opções únicas de inscrição em Azure AD
description: Saiba mais sobre as opções disponíveis para um único sign-on (SSO) no Azure Ative Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: iangithinji
ms.reviewer: arvindh, japere
ms.openlocfilehash: 202ccf6f540ec78c2bb30e0f0a0173609bba578c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751459"
---
# <a name="single-sign-on-options-in-azure-ad"></a>Opções únicas de inscrição em Azure AD

O sign-on único proporciona muitos benefícios em relação aos métodos tradicionais de inscrição.

- **Com uma única solução,** os utilizadores assinam uma vez com uma conta para aceder a dispositivos ligados ao domínio, recursos da empresa, software como aplicações de serviço (SaaS) e aplicações web. Após a sessão, o utilizador pode lançar aplicações a partir do portal Office 365 ou my Apps. Os administradores podem centralizar a gestão da conta de utilizador e adicionar ou remover automaticamente o acesso do utilizador a aplicações com base na adesão ao grupo.

- **Sem uma única sincê-in,** os utilizadores devem lembrar-se de senhas específicas da aplicação e iniciar sôs-se em cada aplicação. O pessoal de TI precisa de criar e atualizar contas de utilizadores para cada aplicação, como o Microsoft 365, Box e Salesforce. Os utilizadores precisam de se lembrar das suas palavras-passe, além de gastar o tempo para iniciar súm na súmia de cada aplicação.

Para saber mais sobre um único sign-on, veja [o que é um único sign-on?](what-is-single-sign-on.md)

## <a name="choosing-a-single-sign-on-method"></a>Escolher um único método de inscrição

Existem várias formas de configurar um pedido de inscrição única. A escolha de um único método de inscrição depende da configuração da aplicação para autenticação.

- As aplicações em nuvem podem utilizar métodos OpenID Connect, OAuth, SAML, baseados em palavras-passe, ligados ou desativados para um único s-on. 
- As aplicações no local podem utilizar métodos baseados em palavras-passe, autenticação integrada do Windows, baseados em cabeçalhos, ligados ou desativados para uma única sação. As escolhas no local funcionam quando as aplicações são configuradas para Procuração de Aplicação.

Este fluxograma ajuda-o a decidir qual o método de inscrição único melhor para a sua situação.

![Fluxograma de decisão para um único método de inscrição](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

A tabela a seguir resume os métodos de inscrição única e liga-se a mais detalhes.

| Método de inscrição único | Tipos de aplicação | Quando utilizar |
| :------ | :------- | :----- |
| [OpenID Connect e OAuth](#openid-connect-and-oauth) | nuvem e no local | Utilize o OpenID Connect e o OAuth ao desenvolver uma nova aplicação. Este protocolo simplifica a configuração da aplicação, tem SDKs fáceis de usar e permite que a sua aplicação utilize o Ms Graph.
| [SAML](#saml-sso) | nuvem e no local | Escolha SAML sempre que possível para aplicações existentes que não utilizem OpenID Connect ou OAuth. A SAML trabalha para aplicações que autenticam usando um dos protocolos SAML.|
| [Baseada em palavra-passe](#password-based-sso) | nuvem e no local | Escolha a palavra-passe com base quando a aplicação autenticar com nome de utilizador e senha. O sign-on único baseado em palavra-passe permite o armazenamento de senha de aplicação segura e a repetição usando uma extensão do navegador web ou uma aplicação móvel. Este método utiliza o processo de inscrição existente fornecido pela aplicação, mas permite que um administrador gere as palavras-passe. |
| [Ligado](#linked-sign-on) | nuvem e no local | Escolha o sinal de inscrição ligado quando a aplicação estiver configurada para um único s-ins insitado noutro serviço de fornecedor de identidade. Esta opção não adiciona um único sinal de inscrição à aplicação. No entanto, a aplicação pode já ter um único sign-on implementado usando outro serviço, como os Serviços da Federação de Diretórios Ativos.|
| [Desativado](#disabled-sso) | nuvem e no local | Escolha um único sinal de desativado quando a aplicação não estiver pronta para ser configurada para um único s-on. Este modo é o padrão quando cria a aplicação.|
| [Autenticação integrada do Windows (IWA)](#integrated-windows-authentication-iwa-sso) | apenas no local | Escolha o único sign-on da IWA para aplicações que utilizem [a Autenticação Integrada do Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)ou aplicações com conhecimento de sinistros. Para a IWA, os conectores Proxy de aplicação utilizam a Delegação Restrita Kerberos (KCD) para autenticar os utilizadores na aplicação. |
| [Baseado em cabeçalho](#header-based-sso) | apenas no local | Utilize um único sinal de sinalização baseado no cabeçalho quando a aplicação utilizar cabeçalhos para autenticação. O Application Proxy utiliza o Azure AD para autenticar o utilizador e, em seguida, passa o tráfego através do serviço de conector.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect e OAuth

Ao desenvolver novas aplicações, utilize protocolos modernos como OpenID Connect e OAuth para obter a melhor experiência de súp livre para a sua aplicação em várias plataformas de dispositivos. A OAuth permite que utilizadores ou administradores [concedam consentimento](configure-user-consent.md) para recursos protegidos como [o Microsoft Graph](/graph/overview). Fornecemos [SDKs](../develop/reference-v2-libraries.md) fáceis de adotar para a sua aplicação e, além disso, a sua aplicação estará pronta para usar [o Microsoft Graph.](/graph/overview)

Para obter mais informações, consulte:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Guia do desenvolvedor de plataformas de identidade da Microsoft.](../develop/index.yml)

## <a name="saml-sso"></a>SAML SSO

Com **um único sinal de SATURA,** o Azure AD autentica-se na aplicação utilizando a conta Azure AD do utilizador. A Azure AD comunica a informação de inscrição à aplicação através de um protocolo de ligação. Com um único sign-on baseado em SAML, pode mapear os utilizadores para funções de aplicação específicas com base nas regras que define nas suas alegações SAML.

Escolha o sign-on único baseado em SAML quando a aplicação o suporta.

O sign-on único baseado em SAML é suportado para aplicações que usam qualquer um destes protocolos:

- SAML 2.0
- WS-Federation

Para configurar um pedido SaaS para um único sign-on baseado em SAML, consulte um [único sinal baseado em SAML baseado em Configure](configure-saml-single-sign-on.md). Além disso, muitas aplicações de Software como serviço (SaaS) têm um [tutorial específico para aplicações](../saas-apps/tutorial-list.md) que o movem através da configuração para um único sign-on baseado em SAML.

Para configurar um pedido para a WS-Federação, siga as mesmas orientações para configurar o pedido de inscrição única baseada em SAML. Na etapa para configurar a aplicação para utilizar o Azure AD, terá de substituir o URL de login Azure AD para o ponto final WS-Federation `https://login.microsoftonline.com/<tenant-ID>/wsfed` .

Para configurar um pedido no local para um único sign-on baseado em SAML, consulte [o auto-sign-on da SAML para aplicações no local com o Application Proxy](application-proxy-configure-single-sign-on-on-premises-apps.md).

Para obter mais informações sobre o protocolo SAML, consulte [o protocolo SAML de assinatura única](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>SSO baseado em palavra-passe

Com o início de s based password, os utilizadores acedem à aplicação com um nome de utilizador e senha na primeira vez que acedem à sua assinatura. Após o primeiro início de sação, o Azure AD fornece o nome de utilizador e a palavra-passe à aplicação.

O sign-on único baseado em palavra-passe utiliza o processo de autenticação existente fornecido pela aplicação. Quando ativa o sign-on único da palavra-passe para uma aplicação, o Azure AD recolhe e armazena de forma segura os nomes de utilizador e as palavras-passe para a aplicação. As credenciais do utilizador são armazenadas num estado encriptado no diretório.

Além disso, o Administrador pode ativar políticas de acesso condicional AD Azure ou autenticação de vários fatores para SSO baseado em palavras-passe.

Escolha o único sinal de sôm- insi por palavra-passe quando:

- Uma aplicação não suporta um único protocolo de inscrição da SAML.
- Uma aplicação autentica-se com um nome de utilizador e palavra-passe em vez de acesso a tokens e cabeçalhos.

O único sinal baseado em palavra-passe é suportado para qualquer aplicação baseada na nuvem que tenha uma página de surgiusinseção baseada em HTML. O utilizador pode utilizar qualquer um dos seguintes navegadores:

- Internet Explorer 11 no Windows 7 ou mais tarde
   > [!NOTE]
   > O Internet Explorer está em suporte limitado e já não recebe novas atualizações de software. O Microsoft Edge é o navegador recomendado.

- Microsoft Edge na Edição de Aniversário do Windows 10 ou mais tarde
- Microsoft Edge para iOS e Android
- Browser Gerido do Intune
- Chrome no Windows 7 ou mais tarde, e no macOS X ou mais tarde
- Firefox 26.0 ou mais tarde no Windows XP SP2 ou mais tarde, e no macOS X 10.6 ou mais tarde

Para configurar uma aplicação em nuvem para um único sinal de assinatura baseado em palavra-passe, consulte [configurar o sign-in único da palavra-passe](configure-password-single-sign-on-non-gallery-applications.md).

Para configurar um pedido no local para um único sinal de acesso através de Procuração de Aplicação, consulte [abotoação de palavra-passe para um único sinal de acesso com procuração de aplicação](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>Como funciona a autenticação para SSO baseado em palavra-passe

Para autenticar um utilizador numa aplicação, o Azure AD recupera as credenciais do utilizador do diretório e introduz-as na página de inscrição da aplicação.  O Azure AD passa de forma segura as credenciais do utilizador através de uma extensão do navegador web ou de uma aplicação móvel. Este processo permite que um administrador gere as credenciais dos utilizadores e não exige que os utilizadores se lembrem da sua palavra-passe.

> [!IMPORTANT]
> As credenciais são obstinadas do utilizador durante o processo de inscrição automática. No entanto, as credenciais são detetáveis utilizando ferramentas de depuragem web. Os utilizadores e administradores devem seguir as mesmas políticas de segurança como se as credenciais fossem inseridas diretamente pelo utilizador.

### <a name="managing-credentials-for-password-based-sso"></a>Gestão de credenciais para SSO baseado em palavras-passe

As palavras-passe de cada aplicação podem ser geridas pelo administrador AD Azure ou pelos utilizadores.

Quando o administrador da AD Azure gere as credenciais:  

- O utilizador não precisa de reiniciar ou lembrar-se do nome de utilizador e da palavra-passe. O utilizador pode aceder à aplicação clicando nela nas suas Apps Ou através de um link fornecido.
- O administrador pode fazer tarefas de gestão nas credenciais. Por exemplo, o administrador pode atualizar o acesso à aplicação de acordo com os membros do grupo de utilizadores e o estado do funcionário.
- O administrador pode usar credenciais administrativas para fornecer acesso a aplicações partilhadas entre muitos utilizadores. Por exemplo, o administrador pode permitir que todos os que possam aceder a uma aplicação tenham acesso a uma aplicação de partilha de redes sociais ou de partilha de documentos.

Quando o utilizador final gere as credenciais:

- Os utilizadores podem gerir as suas palavras-passe atualizando-as ou eliminando-as conforme necessário.
- Os administradores ainda podem definir novas credenciais para a aplicação.

## <a name="linked-sign-on"></a>Inscrição ligada
O sign-on ligado permite que a Azure AD forneça um único sinal de acesso a uma aplicação que já está configurada para um único sinal de acesso a outro serviço. A aplicação ligada pode parecer ser utilizadores finais no portal Office 365 ou no portal Azure AD MyApps. Por exemplo, um utilizador pode lançar uma aplicação configurada para um único sign-on em Ative Directory Federation Services 2.0 (AD FS) a partir do portal Office 365. Estão também disponíveis relatórios adicionais para aplicações ligadas que são lançadas a partir do portal Office 365 ou do portal Azure AD MyApps. Para configurar um pedido de inscrição ligada, consulte o [sign-on ligado ao Configure](configure-linked-sign-on.md).

### <a name="linked-sign-on-for-application-migration"></a>Inscrição ligada para migração de aplicações

O s-on ligado pode proporcionar uma experiência consistente do utilizador enquanto migra aplicações durante um período de tempo. Se estiver a migrar aplicações para o Azure Ative Directory, pode utilizar o sign-on ligado para publicar rapidamente links para todas as aplicações que pretende migrar.  Os utilizadores podem encontrar todos os links no [portal MyApps](../user-help/my-apps-portal-end-user-access.md) ou no lançador de [aplicações Microsoft 365.](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) Os utilizadores não saberão que estão a aceder a uma aplicação ligada ou a uma aplicação migratória.  

Uma vez que um utilizador tenha autenticado com uma aplicação ligada, é necessário criar um registo de conta antes de o utilizador final ter acesso único de s.a.. O provisionamento deste registo de conta pode ocorrer automaticamente ou pode ocorrer manualmente por um administrador.

>[!NOTE]
>Não é possível aplicar políticas de acesso condicional ou autenticação de vários fatores a uma aplicação ligada. Isto porque uma aplicação ligada não fornece capacidades únicas de inscrição através do Azure AD. Ao configurar uma aplicação ligada, está simplesmente a adicionar um link que aparecerá no lançador de aplicações ou no portal MyApps. 

## <a name="disabled-sso"></a>SSO desativado

Modo desativado significa que o único sinal não é utilizado para a aplicação. Quando uma única sentique é desativada, os utilizadores podem precisar de autenticar duas vezes. Primeiro, os utilizadores autenticam a AZure AD e depois fazem o súmis na aplicação.

Utilize o modo de inscrição único desativado:

- Se você não está pronto para integrar esta aplicação com Azure AD single sign-on, ou
- Se estiver a testar outros aspetos da aplicação, ou
- Como uma camada de segurança para uma aplicação no local que não requer que os utilizadores autentem a autenticação. Com o desativado, o utilizador precisa de autenticar.

Note que se configurar a aplicação para um único sinal de SSL iniciado pelo SP e alterar o modo SSO para desativar, não impedirá os utilizadores de assinarem a aplicação fora do portal MyApps. Para isso, é necessário [desativar a capacidade de os utilizadores se inscreverem](disable-user-sign-in-portal.md)

## <a name="integrated-windows-authentication-iwa-sso"></a>Autenticação integrada do Windows (IWA) SSO

[O Application Proxy](application-proxy.md) fornece um único sign-on (SSO) a aplicações que utilizam [aplicações integradas de autenticação do Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)ou aplicações com conhecimento de sinistros. Se a sua aplicação utilizar o IWA, o Application Proxy autentica-se na aplicação utilizando a Delegação Restrita Kerberos (KCD). Para uma aplicação consciente de reclamações que confie no Azure Ative Directory, a assinatura única funciona porque o utilizador já foi autenticado utilizando a Azure AD.

Escolha o modo integrado de autenticação do Windows single para fornecer um único sinal de acesso a uma aplicação no local que autentica com AAI.

Para configurar uma aplicação no local para a IWA, consulte [a Delegação Restrita Kerberos para um único sinal de inscrição nas suas aplicações com o Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md).

### <a name="how-single-sign-on-with-kcd-works"></a>Como funciona um único sinal com O KCD
Este diagrama explica o fluxo quando um utilizador acede a uma aplicação no local que utiliza a IWA.

![Diagrama de fluxo de autenticação AD do Microsoft Azure](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. O utilizador entra no URL para aceder à aplicação no local através do Application Proxy.
1. Application Proxy redireciona o pedido para serviços de autenticação Azure AD para pré-autorenticato. Neste momento, a Azure AD aplica quaisquer políticas de autenticação e autorização aplicáveis, como a autenticação multifactor. Se o utilizador for validado, o Azure AD cria um símbolo e envia-o para o utilizador.
1. O utilizador passa o token para Application Proxy.
1. Application Proxy valida o token e recupera o Nome Principal do Utilizador (UPN) a partir do token. Em seguida, envia o pedido, a UPN, e o Nome Principal de Serviço (SPN) para o Conector através de um canal seguro duplamente autenticado.
1. O conector utiliza a negociação da Delegação Restrita Kerberos (KCD) com o AD nas instalações, fazendo-se passar pelo utilizador para obter um token Kerberos para a aplicação.
1. O Ative Directory envia o token Kerberos para a aplicação ao conector.
1. O conector envia o pedido original para o servidor de aplicações, utilizando o token Kerberos que recebeu da AD.
1. A aplicação envia a resposta para o conector, que é depois devolvido ao serviço Application Proxy e, finalmente, ao utilizador.

## <a name="header-based-sso"></a>SSO baseado em cabeçalho

Os trabalhos de sinalização único baseados em cabeçalhos para aplicações que utilizam cabeçalhos HTTP para autenticação.

Escolha um único sinal de inscrição baseado no cabeçalho quando o Application Proxy estiver configurado para a aplicação no local.

Para saber mais sobre a autenticação baseada em cabeçalho, consulte [o SSO baseado em Cabeçalho](application-proxy-configure-single-sign-on-with-headers.md).


## <a name="next-steps"></a>Passos seguintes
* [Quickstart Series em Gestão de Aplicações](view-applications-portal.md)
* [Planear uma implementação de início de sessão único](plan-sso-deployment.md)
* [Único sinal com aplicativos no local](application-proxy-config-sso-how-to.md)
