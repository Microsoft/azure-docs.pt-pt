---
title: Procuração de aplicação da AD Azure frequentemente fez perguntas / Microsoft Docs
description: Saiba respostas a perguntas frequentes (FAQ) sobre a utilização de Procuração de Aplicações AD Azure para publicar aplicações internas no local a utilizadores remotos.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: ec9eeb0c35d96ee777771260686178faa536e909
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877308"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Ative Directory (Azure AD) Application Proxy frequentemente feito perguntas

Esta página responde frequentemente a perguntas sobre o Procurador de Aplicação de Aplicação azure Ative Directory (Azure AD).

## <a name="enabling-azure-ad-application-proxy"></a>Habilitar a procuração de aplicação ad azure

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Que licença é necessária para usar o Proxy de Aplicação AD Azure?

Para utilizar o Proxy de Aplicação AD Azure, deve ter uma licença Azure AD Premium P1 ou P2. Para mais informações sobre licenciamento, consulte [O Preço do Diretório Ativo do Azure](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Porque é que o "Botão de Procuração de Aplicação ativa está acinzentado?

Certifique-se de que tem pelo menos uma licença Azure AD Premium P1 ou P2 e um Conector proxy de aplicação AD Azure instalado. Depois de instalar com sucesso o seu primeiro conector, o serviço de proxy de aplicação AD Azure será ativado automaticamente.

## <a name="connector-configuration"></a>Configuração do conector

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Os serviços de Conector proxy de aplicação podem funcionar num contexto de utilizador diferente do predefinido?

Não, este cenário não é apoiado. As definições predefinidas são:

- Conector proxy de aplicação da Microsoft AAD - WAPCSvc - Serviço de Rede
- Microsoft AAD Application Proxy Updateor - WAPCUpdaterSvc - NT Authority\System

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>A minha aplicação back-end está hospedada em vários servidores web e requer persistência de sessão do utilizador (stickiness). Como posso alcançar a persistência da sessão? 

Para recomendações, consulte [Alta disponibilidade e equilíbrio de carga dos seus conectores e aplicações do Application Proxy](application-proxy-high-availability-load-balancing.md).

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>A terminação tLS (inspeção ou aceleração TLS/HTTPS) está no tráfego dos servidores do conector para o Azure suportado?

O Conector proxy de aplicação executa a autenticação baseada em certificados para o Azure. TLS Termination (inspeção ou aceleração TLS/HTTPS) quebra este método de autenticação e não é suportado. O tráfego do conector para o Azure deve contornar todos os dispositivos que estejam a executar a Rescisão de TLS.  

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Devo criar uma conta dedicada para registar o conector com o Procurador de Aplicação AD Azure?

Não há razão para isso. Qualquer conta global de administrador ou administrador de aplicações funcionará. As credenciais introduzidas durante a instalação não são utilizadas após o processo de registo. Em vez disso, é emitido um certificado para o conector, que é utilizado para autenticação a partir desse ponto.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Como posso monitorizar o desempenho do conector de procuração de aplicação AD Azure?

Existem contadores de Monitor de Desempenho que são instalados juntamente com o conector. Para os visualizar:  

1. Selecione **Iniciar**, escreva "Perfmon", e prima ENTER.
2. Selecione **Performance** Monitor **+** e clique no ícone verde.
3. Adicione os contadores proxy de proxy de aplicação da **Microsoft AAD** que pretende monitorizar.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>O conector Proxy de Aplicação AD Azure tem de estar na mesma sub-rede que o recurso?

O conector não é obrigado a estar na mesma sub-rede. No entanto, necessita de resolução de nomes (DNS, ficheiro anfitrião) para o recurso e a conectividade necessária da rede (encaminhamento para o recurso, portas abertas sobre o recurso, etc.). Para recomendações, consulte considerações de topologia da rede ao utilizar o Proxy de Aplicação de [Diretório Ativo Azure](application-proxy-network-topology.md).

## <a name="application-configuration"></a>Configuração da aplicação

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Qual é a duração do tempo de paragem padrão e "longo"? O tempo pode ser prolongado?

O comprimento padrão é de 85 segundos. A regulação "longa" é de 180 segundos. O prazo limite não pode ser prorrogado.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Como mudo a página de aterragem das minhas cargas de aplicação?

A partir da página de Registos de Aplicação, pode alterar o URL da página inicial para o URL externo desejado da página de aterragem. A página especificada irá carregar quando a aplicação for lançada a partir das Minhas Apps ou do Portal do Office 365. Para os passos de configuração, consulte [Definir uma página inicial personalizada para aplicações publicadas utilizando o Proxy de Aplicação AD Do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Só podem ser publicadas aplicações baseadas no IIS? E as aplicações web que estão a funcionar em servidores web não Windows? O conector tem de ser instalado num servidor com o IIS instalado?

Não, não há requisito sido para candidaturas que sejam publicadas. Pode publicar aplicações web em servidores que não o Windows Server. No entanto, poderá não ser possível utilizar a pré-autenticação com um Servidor não Windows, dependendo se o servidor web suporta Negociar (autenticação Kerberos). O IIS não é necessário no servidor onde o conector está instalado.

## <a name="integrated-windows-authentication"></a>Autenticação Integrada do Windows.

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Quando devo utilizar o método DirectorallowedToDelegateToAccount ao criar a Delegação Limitada kerberos (KCD)?

O método PrincipalAllowedToDelegateToAccount é utilizado quando os servidores de conector estão num domínio diferente da conta de serviço de aplicação web. Requer a utilização de uma delegação restrita baseada em recursos.
Se os servidores do conector e a conta de serviço de aplicação web estiverem no mesmo domínio, pode utilizar utilizadores e computadores de diretório ativo para configurar as definições da delegação em cada uma das contas da máquina do conector, permitindo-lhes delegar no SPN alvo.

Se os servidores do conector e a conta de serviço de aplicação web estiverem em diferentes domínios, a delegação baseada em Recursos é utilizada. As permissões da delegação estão configuradas no servidor web-alvo e na conta de serviço de aplicação web. Este método de delegação constrangida é relativamente novo. O método foi introduzido no Windows Server 2012, que suporta a delegação de domínio seletiva, permitindo ao proprietário de recursos (serviço web) controlar quais as contas de máquinas e serviços que podem delegar no mesmo. Não há UI para ajudar nesta configuração, por isso terá de usar o PowerShell.
Para mais informações, consulte a delegação de entendimento de [branco Kerberos constrangida com procuração](https://aka.ms/kcdpaper)de aplicação .

## <a name="pass-through-authentication"></a>Autenticação pass-through

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Posso usar políticas de acesso condicional para aplicações publicadas com autenticação pass-through?

As Políticas de Acesso Condicional só são aplicadas para utilizadores pré-autenticados com sucesso em AD Azure. A autenticação pass-through não desencadeia a autenticação da AD Azure, pelo que as Políticas de Acesso Condicional não podem ser aplicadas. Com a autenticação pass-through, as políticas de MFA devem ser implementadas no servidor no local, se possível, ou permitindo a pré-autenticação com o Proxy de Aplicação AD Azure.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>Posso publicar um pedido web com requisito de autenticação de certificado de cliente?

Não, este cenário não é suportado porque o Proxy de Aplicação vai acabar com o tráfego de TLS.  

## <a name="remote-desktop-gateway-publishing"></a>Publicação remota de Gateway de Desktop

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Como posso publicar Remote Desktop Gateway sobre o Procurador de Aplicação AD Azure?

Consulte a publicação de Ambiente de Trabalho Remoto com procuração de [aplicação AD Azure](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>Posso usar a Delegação Limitada kerberos (Single Sign-On - Windows Integrated Authentication) no cenário de publicação remote Desktop Gateway?

Não, este cenário não é apoiado.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Os meus utilizadores não usam o Internet Explorer 11 e o cenário de pré-autenticação não funciona para eles. Isto é esperado?

Sim, é esperado. O cenário de pré-autenticação requer um controlo ActiveX, que não é suportado em navegadores de terceiros.

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>O Cliente Web do Ambiente de Trabalho Remoto (HTML5) é suportado?

Não, este cenário não é apoiado atualmente. Siga o nosso fórum de feedback [userVoice](https://aka.ms/aadapuservoice) para obter atualizações sobre esta funcionalidade.

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>Depois de configurar o cenário de pré-autenticação, percebi que o utilizador tem de autenticar duas vezes: primeiro no formulário de entrada de adad Azure e depois no formulário de entrada de entrada RDWeb. Isto é esperado? Como posso reduzir isto a um inscreveu-se?

Sim, é esperado. Se o computador do utilizador for Azure AD, o utilizador entra automaticamente no Azure AD. O utilizador precisa de fornecer as suas credenciais apenas no formulário de acesso ao RDWeb.

## <a name="sharepoint-publishing"></a>Publicação SharePoint

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Como posso publicar sharePoint sobre procuração de aplicação ad azure?

Consulte o [Enable acesso remoto ao SharePoint com procuração de aplicação AD Azure](application-proxy-integrate-with-sharepoint-server.md).

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>Posso usar a aplicação móvel SharePoint (iOS/ Android) para aceder a um servidor sharePoint publicado?

A [aplicação móvel SharePoint](https://docs.microsoft.com/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises) não suporta a pré-autenticação do Azure Ative Directory atualmente.

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Publicação de Serviços da Federação de Diretórios Ativos (AD FS) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Posso usar o Proxy de Aplicação AD Azure como proxy AD FS (como procuração de aplicação web)?

Não. A Procuração de Aplicações Azure AD foi concebida para trabalhar com a AD Azure e não preenche os requisitos para atuar como um proxy AD FS.

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>O suporte webSocket funciona para aplicações que não o QlikSense?

Atualmente, o suporte ao protocolo WebSocket ainda se encontra em pré-visualização pública e pode não funcionar para outras aplicações. Alguns clientes tiveram sucesso misto usando o protocolo WebSocket com outras aplicações. Se testar tais cenários, adoraríamos ouvir os seus resultados. Por favor envie-nos o seu feedback em aadapfeedback@microsoft.com.

As funcionalidades (Eventlogs, PowerShell e Remote Desktop Services) no Windows Admin Center (WAC) ou no Remote Desktop Web Client (HTML5) não funcionam através do Proxy de Aplicação AD Azure atualmente.

## <a name="link-translation"></a>Tradução de ligação

### <a name="does-using-link-translation-affect-performance"></a>A utilização da tradução link afeta o desempenho?

Sim. A tradução de ligação afeta o desempenho. O serviço Application Proxy digitaliza a aplicação de links codificados e substitui-as pelos respetivos URLs externos publicados antes de os apresentar ao utilizador. 

Para um melhor desempenho, recomendamos a utilização de URLs internos e externos idênticos configurando [domínios personalizados](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain). Se utilizar domínios personalizados não for possível, pode melhorar o desempenho da tradução de ligações utilizando o My Apps Secure Sign em Extensão ou o Microsoft Edge Browser no telemóvel. Consulte [Redirecionamento de links codificados para aplicações publicadas com procuração de aplicação ad do Azure AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Carateres universais

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Como uso wildcards para publicar duas aplicações com o mesmo nome de domínio personalizado, mas com protocolos diferentes, uma para HTTP e outra para HTTPS?

Este cenário não é suportado diretamente. As suas opções para este cenário são:

1. Publique os URLs HTTP e HTTPS como aplicações separadas com um wildcard, mas dê a cada um deles um domínio personalizado diferente. Esta configuração funcionará uma vez que têm URLS externos diferentes.

2. Publique o URL HTTPS através de uma aplicação wildcard. Publique as aplicações HTTP separadamente utilizando estes cmdlets proxy powerShell de aplicação:
   - [Gestão de aplicações proxy de aplicação](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [Gestão de conector proxy de aplicação](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
