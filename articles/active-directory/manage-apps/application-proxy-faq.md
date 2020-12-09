---
title: Azure Ative Directory Application Proxy frequentemente fez perguntas
description: Aprenda respostas a perguntas frequentes (FAQ) sobre a utilização de Proxy da Aplicação AD Azure para publicar aplicações internas e no local para utilizadores remotos.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 07/23/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperfq2
ms.openlocfilehash: e9a03c82fd4bd79e7a47fa34ff7a69870c52f018
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858387"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Diretório Ativo (Azure AD) Aplicação Proxy frequentemente perguntas

Esta página responde frequentemente a perguntas sobre O Azure Ative Directory (Azure AD) Application Proxy.

## <a name="enabling-azure-ad-application-proxy"></a>Ativar o Proxy de Aplicação Ad Azure

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Que licença é necessária para utilizar o Azure AD Application Proxy?

Para utilizar o Azure AD Application Proxy, tem de ter uma licença Azure AD Premium P1 ou P2. Para obter mais informações sobre o licenciamento, consulte [o Azure Ative Directory Pricing](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="what-happens-to-azure-ad-application-proxy-in-my-tenant-if-my-license-expires"></a>O que acontece ao Azure AD Application Proxy no meu inquilino, se a minha licença expirar?
Se a sua licença expirar, o Application Proxy será automaticamente desativado. As informações da sua candidatura serão guardadas por um período máximo de um ano.

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Porque é que o botão "Enable Application Proxy está acinzentado?

Certifique-se de que tem pelo menos uma licença Azure AD Premium P1 ou P2 e um Conector Proxy Proxy de aplicação AD AZure. Depois de instalar com sucesso o seu primeiro conector, o serviço Azure AD Application Proxy será ativado automaticamente.

## <a name="connector-configuration"></a>Configuração do conector

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Os serviços de Conector Proxy da Aplicação podem funcionar num contexto de utilizador diferente do padrão?

Não, este cenário não é apoiado. As definições predefinidos são:

- Conector Proxy de aplicação da Microsoft AAD - WAPCSvc - Serviço de Rede
- Microsoft AAD Application Proxy Connector Updater - WAPCUpdaterSvc - NT Authority\System

### <a name="can-a-guest-user-with-the-global-administrator-or-the-application-administrator-role-register-the-connector-for-the-guest-tenant"></a>Pode um utilizador convidado com o Administrador Global ou a função de Administrador de Aplicação registar o conector para o inquilino (hóspede)?

Não, neste momento, isto não é possível. A tentativa de registo é sempre feita no arrendatário do utilizador.

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>A minha aplicação back-end está hospedada em vários servidores web e requer persistência da sessão do utilizador (pegajosa). Como posso alcançar a persistência da sessão? 

Para obter recomendações, consulte [alta disponibilidade e equilíbrio de carga dos conectores e aplicações do seu Application Proxy](application-proxy-high-availability-load-balancing.md).

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>A rescisão de TLS (inspeção ou aceleração TLS/HTTPS) está suportada no tráfego dos servidores do conector para o Azure?

O Conector de Procuração de Aplicações realiza a autenticação baseada em certificados para a Azure. A rescisão de TLS (inspeção ou aceleração TLS/HTTPS) quebra este método de autenticação e não é suportada. O tráfego do conector para o Azure deve contornar todos os dispositivos que estejam a realizar a Rescisão TLS.  

### <a name="is-tls-12-required-for-all-connections"></a>O TLS 1.2 é necessário para todas as ligações?
Sim. Para fornecer a melhor encriptação em classe aos nossos clientes, o serviço Application Proxy limita o acesso a apenas protocolos TLS 1.2. Estas alterações foram gradualmente lançadas e eficazes desde 31 de agosto de 2019. Certifique-se de que todas as combinações de servidores de clientes e servidores de navegador são atualizadas para utilizar o TLS 1.2 para manter a ligação ao serviço De procuração de aplicações. Estes incluem clientes que os seus utilizadores estão a usar para aceder a aplicações publicadas através do Application Proxy. Consulte a preparação para [o TLS 1.2 no Office 365](/microsoft-365/compliance/prepare-tls-1.2-in-office-365) para obter referências e recursos úteis.

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>Posso colocar um dispositivo de procuração para a frente entre o(s) servidor(s) do conector e o servidor de aplicação de back-end?
Sim, este cenário é suportado a partir da versão do conector 1.5.1526.0. Consulte [o Trabalho com os servidores proxy existentes no local.](application-proxy-configure-connectors-with-proxy-servers.md)

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Devo criar uma conta dedicada para registar o conector com o Azure AD Application Proxy?

Não há razão para isso. Qualquer conta global de administrador ou administrador de aplicação funcionará. As credenciais registadas durante a instalação não são utilizadas após o processo de registo. Em vez disso, é emitido um certificado ao conector, que é utilizado para a autenticação a partir desse ponto.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Como posso monitorizar o desempenho do conector Proxy de aplicação AD Azure?

Existem contadores do Monitor de Desempenho que são instalados juntamente com o conector. Para os visualizar:  

1. Selecione **Iniciar**, digite "Perfmon", e prima ENTER.
2. Selecione **Monitor de Desempenho** e clique no ícone **+** verde.
3. Adicione os contadores **de conector de aplicação proxy da Aplicação Microsoft AAD** que pretende monitorizar.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>O conector Proxy da aplicação AD Azure tem de estar na mesma sub-rede que o recurso?

O conector não é obrigado a estar na mesma sub-rede. No entanto, precisa de resolução de nomes (DNS, ficheiro de anfitriões) para o recurso e a necessária conectividade de rede (encaminhamento para o recurso, portas abertas no recurso, etc.). Para obter recomendações, consulte [considerações de topologia da rede ao utilizar o Azure Ative Directory Application Proxy](application-proxy-network-topology.md).

### <a name="what-versions-of-windows-server-can-i-install-a-connector-on"></a>Que versões do Windows Server posso instalar um conector?
O Proxy da aplicação requer o Windows Server 2012 R2 ou mais tarde. Existe atualmente uma limitação em HTTP2 para o Windows Server 2019. Para utilizar com sucesso o conector no Windows Server 2019, terá de adicionar a seguinte chave de registo e reiniciar o servidor:
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
    ```

## <a name="application-configuration"></a>Configuração da aplicação

### <a name="i-am-receiving-an-error-about-an-invalid-certificate-or-possible-wrong-password"></a>Estou a receber um erro sobre um certificado inválido ou uma possível senha errada

Depois de ter carregado o certificado SSL, recebe a mensagem "Certificado inválido, possível palavra-passe errada" no portal.

Aqui ficam algumas dicas para resolver problemas com este erro:
- Verifique se há problemas com o certificado. Instale-o no computador local. Se não sentir nenhum problema, então o certificado é bom.
- Certifique-se de que a palavra-passe não contém caracteres especiais. Para testes, a palavra-passe deve conter apenas os caracteres 0-9, A-Z e a-z.
- Se o certificado foi criado com o Microsoft Software Key Storage Provider, o algoritmo RSA deve ser utilizado.

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Qual é o comprimento do tempo limite de final de trás e "longo"? O tempo limite pode ser prolongado?

O comprimento predefinido é de 85 segundos. A definição "longa" é de 180 segundos. O prazo não pode ser prorrogado.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Como posso alterar a página de aterragem que a minha aplicação carrega?

A partir da página 'Registos de Candidaturas', pode alterar o URL da página inicial para o URL externo pretendido da página de aterragem. A página especificada será carregada quando a aplicação for lançada a partir das Minhas Apps ou do Portal do Office 365. Para etapas de configuração, consulte [Definir uma página inicial personalizada para aplicações publicadas utilizando o Azure AD Application Proxy](./application-proxy-configure-custom-home-page.md)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Só podem ser publicadas aplicações baseadas no IIS? E as aplicações web em execução em servidores web não-Windows? O conector tem de ser instalado num servidor com o IIS instalado?

Não, não há requisitos do IIS para aplicações que sejam publicadas. Pode publicar aplicações web em execução em servidores que não o Windows Server. No entanto, poderá não ser capaz de utilizar a pré-autenticação com um Servidor não Windows, dependendo se o servidor web suporta negociar (autenticação Kerberos). O IIS não é necessário no servidor onde o conector está instalado.

### <a name="can-i-configure-application-proxy-to-add-the-hsts-header"></a>Posso configurar o Application Proxy para adicionar o cabeçalho HSTS?
A Application Proxy não adiciona automaticamente o cabeçalho HTTP Strict-Transport-Security às respostas HTTPS, mas manterá o cabeçalho se estiver na resposta original enviada pela aplicação publicada. Provar uma definição para ativar esta funcionalidade encontra-se no roteiro. Se estiver interessado numa pré-visualização que permita adicionar isto às respostas, contacte aadapfeedback@microsoft.com para obter mais detalhes.

## <a name="integrated-windows-authentication"></a>Autenticação Integrada do Windows.

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Quando devo utilizar o método PrincipalSAllowedToDeledtoAccount ao criar a Delegação Restrita kerberos (KCD)?

O método PrincipalAllowedToDeegateAccount é utilizado quando os servidores do conector estão num domínio diferente da conta de serviço de aplicação web. Requer a utilização de uma delegação restrita baseada em recursos.
Se os servidores do conector e a conta de serviço de aplicações web estiverem no mesmo domínio, pode utilizar Utilizadores e Computadores de Diretório Ativo para configurar as definições da delegação em cada uma das contas da máquina do conector, permitindo-lhes delegar no SPN alvo.

Se os servidores do conector e a conta de serviço de aplicação web estiverem em diferentes domínios, a delegação baseada em recursos é utilizada. As permissões da delegação estão configuradas na conta de serviço de servidor web e aplicação web alvo. Este método de delegação constrangida é relativamente novo. O método foi introduzido no Windows Server 2012, que suporta a delegação de domínio cruzado, permitindo ao proprietário de recursos (serviço web) controlar quais as contas de máquina e serviço que lhe podem delegar. Não há UI para ajudar nesta configuração, por isso terás de usar o PowerShell.
Para mais informações, consulte a delegação restrita de papel branco [Kerberos com Proxy de aplicação.](https://aka.ms/kcdpaper)

### <a name="does-ntlm-authentication-work-with-azure-ad-application-proxy"></a>A autenticação da NTLM funciona com o Azure AD Application Proxy?

A autenticação NTLM não pode ser utilizada como um método de pré-autenticação ou de um único sinal. A autenticação NTLM só pode ser utilizada quando pode ser negociada diretamente entre o cliente e a aplicação web publicada. A utilização da autenticação NTLM geralmente faz com que um pedido de inscrição apareça no navegador.

### <a name="can-i-use-the-logon-identity-on-premises-user-principal-name-or-on-premises-sam-account-name-in-a-b2b-iwa-single-sign-on-scenario"></a>Posso utilizar a identidade do logon "Nome principal do utilizador no local" ou "Nome da conta SAM no local" num cenário de inscrição única da IWA B2B?

Não, isto não vai funcionar, porque um utilizador convidado em Azure AD não tem o atributo que é exigido por qualquer uma das identidades de início de súm mencionado acima.

Neste caso, haverá uma recaída para "Nome principal do utilizador". Para mais detalhes sobre o cenário B2B, leia [os utilizadores do Grant B2B no Azure AD acesso às suas aplicações no local.](../external-identities/hybrid-cloud-to-on-premises.md)

## <a name="pass-through-authentication"></a>Autenticação pass-through

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Posso utilizar políticas de acesso condicional para aplicações publicadas com autenticação pass-through?

As Políticas de Acesso Condicional só são aplicadas para utilizadores pré-autenticados com sucesso em Azure AD. A autenticação pass-through não desencadeia a autenticação Azure AD, pelo que as Políticas de Acesso Condicional não podem ser aplicadas. Com a autenticação pass-through, as políticas de MFA devem ser implementadas no servidor no local, se possível, ou permitindo a pré-autenticação com Proxy de aplicação AD Azure.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>Posso publicar um pedido web com requisito de autenticação de certificado de cliente?

Não, este cenário não é suportado porque o Application Proxy vai encerrar o tráfego TLS.  

## <a name="remote-desktop-gateway-publishing"></a>Publicação remota do Desktop Gateway

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Como posso publicar Remote Desktop Gateway sobre Azure AD Application Proxy?

Consulte para [publicar desktop remoto com Proxy de aplicação AD Azure](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>Posso utilizar a Delegação Restrita Kerberos (single Sign-On - Autenticação Integrada do Windows) no cenário de publicação de Gateway remoto?

Não, este cenário não é apoiado.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Os meus utilizadores não usam o Internet Explorer 11 e o cenário de pré-autenticação não funciona para eles. Isto é esperado?

Sim, é esperado. O cenário de pré-autenticação requer um controlo ActiveX, que não é suportado em navegadores de terceiros.

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>O Cliente Web de Desktop Remoto (HTML5) está suportado?

Sim, este cenário está neste momento em pré-visualização pública. Consulte para [publicar desktop remoto com Proxy de aplicação AD Azure](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>Depois de configurar o cenário de pré-autenticação, percebi que o utilizador tem de autenticar duas vezes: primeiro no formulário de inscrição AD Azure e, em seguida, no formulário de inscrição RDWeb. Isto é esperado? Como posso reduzir isto a uma s inscrição?

Sim, é esperado. Se o computador do utilizador for a Azure AD, o utilizador entra automaticamente no AD Azure. O utilizador precisa de fornecer as suas credenciais apenas no formulário de entrada rdWeb.

## <a name="sharepoint-publishing"></a>Publicação sharePoint

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Como posso publicar SharePoint sobre Azure AD Application Proxy?

Consulte para [Ativar o acesso remoto ao SharePoint com o Azure AD Application Proxy](application-proxy-integrate-with-sharepoint-server.md).

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>Posso utilizar a aplicação móvel SharePoint (iOS/ Android) para aceder a um servidor SharePoint publicado?

A [aplicação móvel SharePoint](/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises) não suporta atualmente a pré-autenticação do Azure Ative Directory.

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Publicação de Serviços da Federação de Diretórios Ativos (AD FS) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Posso usar o Azure AD Application Proxy como proxy AD FS (como o Proxy de Aplicações Web)?

N.º O Azure AD Application Proxy foi concebido para trabalhar com a Azure AD e não preenche os requisitos para agir como um representante da AD FS.

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>O suporte da WebSocket funciona para outras aplicações que não o QlikSense?

Atualmente, o suporte ao protocolo WebSocket ainda se encontra em pré-visualização pública e pode não funcionar para outras aplicações. Alguns clientes tiveram sucesso misto usando o protocolo WebSocket com outras aplicações. Se testar tais cenários, adoraríamos ouvir os seus resultados. Por favor envie-nos o seu feedback em aadapfeedback@microsoft.com .

As funcionalidades (Eventlogs, PowerShell e Remote Desktop Services) no Windows Admin Center (WAC) ou no Remote Desktop Web Client (HTML5) não funcionam atualmente através do Azure AD Application Proxy.

## <a name="link-translation"></a>Tradução de ligação

### <a name="does-using-link-translation-affect-performance"></a>A utilização da tradução link afeta o desempenho?

Sim. A tradução de ligação afeta o desempenho. O serviço Application Proxy digitaliza a aplicação de links codificados e substitui-os pelos respetivos URLs externos publicados antes de os apresentar ao utilizador. 

Para um melhor desempenho, recomendamos a utilização de URLs internos e externos idênticos configurando [domínios personalizados](./application-proxy-configure-custom-domain.md). Se não for possível utilizar domínios personalizados, pode melhorar o desempenho da tradução de ligações utilizando o Signo Seguro das Minhas Aplicações em Extensão ou o Navegador Microsoft Edge no telemóvel. Consulte [links codificados para apps publicadas com Proxy de aplicações AD AD Azure.](application-proxy-configure-hard-coded-link-translation.md)

## <a name="wildcards"></a>Carateres universais

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Como uso wildcards para publicar duas aplicações com o mesmo nome de domínio personalizado mas com protocolos diferentes, uma para HTTP e outra para HTTPS?

Este cenário não é apoiado diretamente. As suas opções para este cenário são:

1. Publique os URLs HTTP e HTTPS como aplicações separadas com um wildcard, mas dê a cada um deles um domínio personalizado diferente. Esta configuração funcionará uma vez que têm DIFERENTES URLS externos.

2. Publique o URL HTTPS através de uma aplicação wildcard. Publique as aplicações HTTP utilizando separadamente estes cmdlets Proxy PowerShell de aplicação:
   - [Gestão de aplicações proxy](/powershell/module/azuread/#application_proxy_application_management&preserve-view=true)
   - [Gestão de Conector de Proxy de Aplicação](/powershell/module/azuread/#application_proxy_connector_management&preserve-view=true)
