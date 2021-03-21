---
title: 'Azure AD Connect: Considerações de identidade híbrida para a nuvem do Governo Azure'
description: Considerações especiais para a implantação do Azure AD Connect com a nuvem do Governo Azure.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbb4298d0d9a9d7589c4a2055b4d55a0b852f951
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97504360"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Considerações de identidade híbridas para a nuvem do Governo do Azure

Este artigo descreve considerações para integrar um ambiente híbrido com a nuvem do Governo Azure da Microsoft. Esta informação é fornecida como uma referência para administradores e arquitetos que trabalham com a nuvem do Governo Azure.

> [!NOTE]
> Para integrar um ambiente de Diretório Ativo do Microsoft (seja no local ou hospedado num IaaS que faça parte da mesma nuvem) com a nuvem do Governo Azure, precisa de atualizar para o mais recente lançamento do [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

Para obter uma lista completa dos pontos finais do Departamento de Defesa do Governo dos Estados Unidos, consulte a [documentação](/office365/enterprise/office-365-u-s-government-dod-endpoints).

## <a name="azure-ad-pass-through-authentication"></a>Autenticação pass-through Azure AD

As seguintes informações descrevem a implementação da Autenticação Pass-through e da nuvem do Governo Azure.

### <a name="allow-access-to-urls"></a>Permitir o acesso aos URLs

Antes de implementar o agente de autenticação Pass-through, verifique se existe uma firewall entre os seus servidores e a AD AZure. Se a sua firewall ou proxy permitir programas bloqueados ou seguros do Sistema de Nomes de Domínio (DNS), adicione as seguintes ligações.

> [!NOTE]
> As seguintes orientações também se aplicam à instalação do [conector proxy de aplicação AD Azure](../manage-apps/what-is-application-proxy.md) para ambientes do Governo Azure.

|URL |Como é utilizado|
|-----|-----|
|&#42;.msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|O agente utiliza estes URLs para comunicar com o serviço de nuvem AZure AD. |
|`mscrl.microsoft.us:80` </br>`crl.microsoft.us:80` </br>`ocsp.msocsp.us:80` </br>`www.microsoft.us:80`| O agente usa estes URLs para verificar os certificados.|
|login.windows.us </br>secure.aadcdn.microsoftonline-p.com </br>&#42;.microsoftonline.us </br>&#42;.microsoftonline-p.us </br>&#42;.msauth.net </br>&#42;.msauthimages.net </br>&#42;.msecnd.net</br>&#42;.msftauth.net </br>&#42;.msftauthimages.net</br>&#42;.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctldl.windowsupdate.us:80| O agente utiliza estes URLs durante o processo de registo.

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Instale o agente para a nuvem do Governo Azure

Siga estes passos para instalar o agente para a nuvem do Governo Azure:

1. No terminal da linha de comando, aceda à pasta que contém o ficheiro executável que instala o agente.
1. Executar os seguintes comandos, que especificam que a instalação é para o Governo Azure.

   Para autenticação pass-through:

   ```
   AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
   ```

   Para procuração de aplicação:

   ```
   AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
   ```

## <a name="single-sign-on"></a>Início de sessão único

### <a name="set-up-your-azure-ad-connect-server"></a>Confiem o seu servidor AZure AD Connect

Se utilizar a Autenticação Pass-through como método de entrada, não é necessária qualquer verificação prévia adicional. Se utilizar a sincronização de hash de palavra-passe como método de entrada e existir uma firewall entre a Azure AD Connect e a AZure AD, certifique-se de que:

- Utilize a versão 1.1.644.0 ou mais tarde do Azure AD Connect.
- Se a sua firewall ou proxy permitir programas DNS bloqueados ou seguros, adicione as ligações aos URLs &#42;.msappproxy.us sobre a porta 443.

  Caso contrário, permita o acesso às gamas IP do datacenter Azure, que são atualizadas semanalmente. Este pré-requisito só se aplica quando ativar a funcionalidade. Não é necessário para as inscrições reais do utilizador.

### <a name="roll-out-seamless-single-sign-on"></a>Desenrolar Sign-On single sem emenda

Pode lançar gradualmente o Azure AD Seamless Single Sign-On aos seus utilizadores utilizando as seguintes instruções. Começa por adicionar o URL AD AD do Azure `https://autologon.microsoft.us` às definições da zona intranet de todos ou selecionados dos utilizadores, utilizando a Política de Grupo no Diretório Ativo.

Também é necessário ativar a definição de política de zona **intranet Permitir atualizações para a barra de estado através do script através da Política de Grupo**.

## <a name="browser-considerations"></a>Considerações do navegador

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (todas as plataformas)

O Mozilla Firefox não utiliza automaticamente a autenticação Kerberos. Cada utilizador deve adicionar manualmente o URL AD AD do Azure às suas definições firefox seguindo estes passos:

1. Corra o Firefox e insira **cerca de:config**   na barra de endereços. Dispense quaisquer notificações que possa ver.
1. Procure a **preferência network.negotiate-auth.trust-uris.**   Esta preferência lista os sites fidedignos pelo Firefox para a autenticação Kerberos.
1. Clique com o nome da preferência e, em seguida, **selecione Modifie**.
1. Entra `https://autologon.microsoft.us` na caixa.
1. Selecione **OK**   e, em seguida, reabra o navegador.

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge baseado em Chromium (todas as plataformas)

Se tiver ultrapassado as  `AuthNegotiateDelegateAllowlist`   definições ou `AuthServerAllowlist`   definições de política no seu ambiente, certifique-se de que lhe adiciona o URL AD `https://autologon.microsoft.us` Azure.

### <a name="google-chrome-all-platforms"></a>Google Chrome (todas as plataformas)

Se tiver ultrapassado as  `AuthNegotiateDelegateWhitelist`   definições ou `AuthServerWhitelist`   definições de política no seu ambiente, certifique-se de que lhe adiciona o URL AD `https://autologon.microsoft.us` Azure.

## <a name="next-steps"></a>Passos seguintes

- [Autenticação pass-through](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [Único sign-on](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)
