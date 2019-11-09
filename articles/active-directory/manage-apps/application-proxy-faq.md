---
title: Perguntas frequentes sobre o Azure Proxy de Aplicativo do AD | Microsoft Docs
description: Aprenda as respostas para perguntas frequentes sobre como usar o Proxy de Aplicativo do AD do Azure para publicar aplicativos internos e locais para usuários remotos.
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
ms.openlocfilehash: 612b6caf47ec4764aa2bbef162592100198ed0c4
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832203"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Perguntas frequentes sobre o proxy de aplicativo Active Directory (Azure AD)

Esta página responde a perguntas frequentes sobre o proxy de aplicativo Azure Active Directory (Azure AD).

## <a name="enabling-azure-ad-application-proxy"></a>Habilitando o Azure Proxy de Aplicativo do AD

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Qual licença é necessária para usar o Azure Proxy de Aplicativo do AD?

Para usar o Azure Proxy de Aplicativo do AD, você deve ter uma licença Azure AD Premium P1 ou P2. Para obter mais informações sobre licenciamento, consulte [preços de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Por que o botão "Habilitar proxy de aplicativo está esmaecido?"

Verifique se você tem pelo menos uma licença Azure AD Premium P1 ou P2 e um conector de Proxy de Aplicativo do AD do Azure instalado. Depois de instalar com êxito seu primeiro conector, o serviço de Proxy de Aplicativo do AD do Azure será habilitado automaticamente.

## <a name="connector-configuration"></a>Configuração do conector

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Os serviços do conector de proxy de aplicativo podem ser executados em um contexto de usuário diferente do padrão?

Não, esse cenário não tem suporte. As configurações padrão são:

- Conector de proxy de aplicativo do Microsoft AAD-WAPCSvc-serviço de rede
- Atualizador do Conector de Proxy de Aplicativo do Microsoft AAD-WAPCUpdaterSvc-NT Authority\System

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>Meu aplicativo de back-end é hospedado em vários servidores Web e requer persistência de sessão de usuário (adesão). Como posso obter persistência de sessão? 

Para obter recomendações, consulte [alta disponibilidade e balanceamento de carga dos seus aplicativos e conectores de proxy de aplicativo](application-proxy-high-availability-load-balancing.md).

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>Posso fazer um dispositivo proxy de encaminhamento entre os servidores de conector e o servidor de aplicativos back-end?

Não, esse cenário não tem suporte. Somente o conector e os serviços de atualização podem ser configurados para usar um proxy de encaminhamento para o tráfego de saída para o Azure. Consulte [trabalhar com servidores proxy locais existentes](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)

### <a name="is-ssl-termination-sslhhtps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>É terminação SSL (inspeção ou aceleração SSL/HHTPS) no tráfego dos servidores de conector para o Azure com suporte?

O conector de proxy de aplicativo executa a autenticação baseada em certificado no Azure. A terminação SSL (inspeção ou aceleração de SSL/HHTPS) interrompe esse método de autenticação e não tem suporte. O tráfego do conector para o Azure deve ignorar todos os dispositivos que estão executando a terminação SSL.  

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Devo criar uma conta dedicada para registrar o conector com o Proxy de Aplicativo do AD do Azure?

Não há motivo para isso. Qualquer conta de administrador global ou de administração de aplicativos funcionará. As credenciais inseridas durante a instalação não são usadas após o processo de registro. Em vez disso, um certificado é emitido para o conector, que é usado para autenticação desse ponto em diante.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Como posso monitorar o desempenho do conector de Proxy de Aplicativo do AD do Azure?

Há contadores do monitor de desempenho que são instalados junto com o conector do. Para exibi-los:  

1. Selecione **Iniciar**, digite "Perfmon" e pressione Enter.
2. Selecione **Monitor de desempenho** e clique no ícone de **+** verde.
3. Adicione os contadores do **conector de proxy de aplicativo do Microsoft AAD** que você deseja monitorar.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>O conector do Proxy de Aplicativo do AD do Azure precisa estar na mesma sub-rede que o recurso?

O conector não precisa estar na mesma sub-rede. No entanto, ele precisa de resolução de nomes (DNS, arquivo de hosts) para o recurso e a conectividade de rede necessária (roteamento para o recurso, portas abertas no recurso, etc.). Para obter recomendações, consulte [considerações de topologia de rede ao usar proxy de aplicativo do Azure Active Directory](application-proxy-network-topology.md).

## <a name="application-configuration"></a>Configuração da aplicação

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Qual é o comprimento do tempo limite de back-end padrão e "longo"? O tempo limite pode ser estendido?

O comprimento padrão é de 85 segundos. A configuração "longa" é de 180 segundos. O limite de tempo limite não pode ser estendido.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Como fazer alterar a página de aterrissagem que meu aplicativo carrega?

Na página registros do aplicativo, você pode alterar a URL da Home Page para a URL externa desejada da página de aterrissagem. A página especificada será carregada quando o aplicativo for iniciado em meus aplicativos ou no portal do Office 365. Para obter as etapas de configuração, consulte [definir um Home Page personalizado para aplicativos publicados usando o Azure proxy de aplicativo do AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Somente aplicativos baseados no IIS podem ser publicados? E quanto aos aplicativos Web em execução em servidores Web que não são do Windows? O conector precisa ser instalado em um servidor com o IIS instalado?

Não, não há nenhum requisito do IIS para aplicativos publicados. Você pode publicar aplicativos Web em execução em servidores diferentes do Windows Server. No entanto, talvez você não consiga usar a pré-autenticação com um servidor não Windows, dependendo de se o servidor Web dá suporte a Negotiate (autenticação Kerberos). O IIS não é necessário no servidor onde o conector está instalado.

## <a name="integrated-windows-authentication"></a>Autenticação integrada do Windows

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Quando devo usar o método PrincipalsAllowedToDelegateToAccount ao configurar a delegação restrita de Kerberos (KCD)?

O método PrincipalsAllowedToDelegateToAccount é usado quando servidores de conector estão em um domínio diferente da conta de serviço de aplicativo Web. Ele requer o uso da delegação restrita baseada em recursos.
Se os servidores do conector e a conta de serviço do aplicativo Web estiverem no mesmo domínio, você poderá usar Active Directory usuários e computadores para definir as configurações de delegação em cada uma das contas do computador do conector, permitindo que elas deleguem ao SPN de destino.

Se os servidores do conector e a conta de serviço de aplicativo Web estiverem em domínios diferentes, a delegação baseada em recursos será usada. As permissões de delegação são configuradas no servidor Web de destino e na conta de serviço de aplicativo Web. Esse método de delegação restrita é relativamente novo. O método foi introduzido no Windows Server 2012, que dá suporte à delegação entre domínios, permitindo que o proprietário do recurso (serviço Web) controle quais contas de computador e serviço podem delegar a ele. Não há nenhuma interface do usuário para ajudar nessa configuração, portanto, você precisará usar o PowerShell.
Para obter mais informações, consulte o White Paper [noções básicas sobre a delegação restrita de Kerberos com o proxy de aplicativo](https://aka.ms/kcdpaper).

## <a name="pass-through-authentication"></a>Autenticação pass-through

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Posso usar políticas de acesso condicional para aplicativos publicados com autenticação de passagem?

As políticas de acesso condicional são impostas somente para usuários previamente autenticados com êxito no Azure AD. A autenticação de passagem não dispara a autenticação do Azure AD, portanto, as políticas de acesso condicional não podem ser impostas. Com a autenticação de passagem, as políticas de MFA devem ser implementadas no servidor local, se possível, ou habilitando a pré-autenticação com o Azure Proxy de Aplicativo do AD.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>Posso publicar um aplicativo Web com requisito de autenticação de certificado de cliente?

Não, esse cenário não tem suporte porque o proxy de aplicativo encerrará o tráfego TLS.  

## <a name="remote-desktop-gateway-publishing"></a>Publicação Área de Trabalho Remota gateway

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Como posso publicar Área de Trabalho Remota gateway sobre o Proxy de Aplicativo do AD do Azure?

Consulte [publicar área de trabalho remota com o proxy de aplicativo do AD do Azure](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="can-i-use-kerberos-constrained-delegation-in-the-remote-desktop-gateway-publishing-scenario"></a>Posso usar a delegação restrita de Kerberos no cenário de publicação de gateway Área de Trabalho Remota?

Não, esse cenário não tem suporte.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Meus usuários não usam o Internet Explorer 11 e o cenário de pré-autenticação não funciona para eles. Isso é esperado?

Sim, é esperado. O cenário de pré-autenticação requer um controle ActiveX, que não tem suporte em navegadores de terceiros.

### <a name="is-the-remote-desktop-web-client-supported"></a>Há suporte para o cliente Web Área de Trabalho Remota?

Não, atualmente não há suporte para esse cenário. Siga nosso fórum de comentários do [UserVoice](https://aka.ms/aadapuservoice) para obter atualizações sobre esse recurso.

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>Depois de configurar o cenário de pré-autenticação, percebi que o usuário precisa autenticar duas vezes: primeiro no formulário de entrada do Azure AD e, em seguida, no formulário de entrada do RDWeb. Isso é esperado? Como posso reduzir isso para uma entrada?

Sim, é esperado. Se o computador do usuário for ingressado no Azure AD, o usuário entrará no Azure AD automaticamente. O usuário precisa fornecer suas credenciais somente no formulário de entrada do RDWeb.

## <a name="sharepoint-publishing"></a>Publicação do SharePoint

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Como posso publicar o SharePoint no Azure Proxy de Aplicativo do AD?

Consulte [habilitar o acesso remoto ao SharePoint com o Azure proxy de aplicativo do AD](application-proxy-integrate-with-sharepoint-server.md).

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Publicação de Serviços de Federação do Active Directory (AD FS) (AD FS) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Posso usar o Azure Proxy de Aplicativo do AD como AD FS proxy (como o proxy de aplicativo Web)?

Não. O Proxy de Aplicativo do AD do Azure foi projetado para funcionar com o Azure AD e não atende aos requisitos para atuar como um proxy de AD FS.

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>O suporte ao WebSocket funciona para aplicativos diferentes do QlikSense?

Atualmente, o suporte ao protocolo WebSocket ainda está em visualização pública e pode não funcionar para outros aplicativos. Alguns clientes tiveram sucesso misto usando o protocolo WebSocket com outros aplicativos. Se você testar esses cenários, adoraríamos ouvir seus resultados. Envie-nos seus comentários em aadapfeedback@microsoft.com.

## <a name="link-translation"></a>Conversão de link

### <a name="does-using-link-translation-affect-performance"></a>O uso da conversão de links afeta o desempenho?

Sim. A conversão de link afeta o desempenho. O serviço de proxy de aplicativo examina o aplicativo em busca de links codificados e os substitui por suas respectivas URLs externas publicadas antes de apresentá-los ao usuário. 

Para obter o melhor desempenho, é recomendável usar URLs internas e externas idênticas Configurando [domínios personalizados](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain). Se o uso de domínios personalizados não for possível, você poderá melhorar o desempenho da tradução de links usando a extensão de entrada segura de meus aplicativos ou o navegador Microsoft Edge em dispositivos móveis. Consulte [redirecionar links codificados para aplicativos publicados com o Azure proxy de aplicativo do AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Carateres universais

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Como fazer usar curingas para publicar dois aplicativos com o mesmo nome de domínio personalizado, mas com protocolos diferentes, um para HTTP e outro para HTTPS?

Esse cenário não tem suporte diretamente. As opções para esse cenário são:

1. Publique as URLs HTTP e HTTPS como aplicativos separados com um curinga, mas forneça a cada um um domínio personalizado diferente. Essa configuração funcionará, pois elas têm diferentes URLS externas.

2. Publique a URL HTTPS por meio de um aplicativo curinga. Publicar os aplicativos HTTP separadamente usando estes cmdlets do PowerShell do proxy de aplicativo:
   - [Gerenciamento de aplicativos de proxy de aplicativo](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [Gerenciamento de conector de proxy de aplicativo](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
