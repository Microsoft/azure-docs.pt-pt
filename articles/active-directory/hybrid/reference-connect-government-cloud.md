---
title: 'Azure AD Connect: Considerações de identidade híbrida para nuvem do Governo Azure'
description: Considerações especiais para a implantação do Azure AD Connect com a nuvem do Governo Azure.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: acdc99ca50255bd9b75828f0a051f364c5218471
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115494"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Considerações de identidade híbrida para a nuvem do Governo de Azure

Este artigo descreve considerações para integrar um ambiente híbrido com a nuvem do Governo Microsoft Azure. Esta informação é fornecida como referência para administradores e arquitetos que trabalham com a nuvem do Governo Azure.

> [!NOTE]
> Para integrar um ambiente no local Microsoft Azure Ative Directory (Azure AD) com a nuvem do Governo Azure, precisa de fazer upgrade para o mais recente lançamento do [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

Para obter uma lista completa dos pontos finais do Departamento de Defesa do Governo dos Estados Unidos, consulte a [documentação](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints).

## <a name="azure-ad-pass-through-authentication"></a>Autenticação pass-through azure AD

As seguintes informações descrevem a implementação da Autenticação Pass-through e da nuvem do Governo Azure.

### <a name="allow-access-to-urls"></a>Permitir o acesso aos URLs

Antes de implementar o agente de autenticação Pass-through, verifique se existe uma firewall entre os seus servidores e a AD Azure. Se a sua firewall ou proxy permitir programas bloqueados ou seguros do Sistema de Nome de Domínio (DNS), adicione as seguintes ligações.

> [!NOTE]
> As seguintes orientações aplicam-se igualmente à instalação do [conector de procuração de aplicação ad-aplicação Azure](https://aka.ms/whyappproxy) para ambientes do Governo Azure.

|do IdP |Como é usado|
|-----|-----|
|&#42;.msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|O agente usa estes URLs para comunicar com o serviço de nuvem Azure AD. |
|mscrl.microsoft.us:80 </br>crl.microsoft.us:80 </br>ocsp.msocsp.us:80 </br>www.microsoft.us:80| O agente usa estes URLs para verificar certificados.|
|login.windows.us </br>secure.aadcdn.microsoftonline-p.com </br>&#42;.microsoftonline.us </br>&#42;.microsoftonline-p.us </br>&#42;.msauth.net </br>&#42;.msauthimages.net </br>&#42;.msecnd.net</br>&#42;.msftauth.net </br>&#42;.msftauthimages.net</br>&#42;.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| O agente utiliza estes URLs durante o processo de registo.

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Instale o agente para a nuvem do Governo Azure

Siga estes passos para instalar o agente para a nuvem do Governo Azure:

1. No terminal da linha de comando, vá para a pasta que contém o ficheiro executável que instala o agente.
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

### <a name="set-up-your-azure-ad-connect-server"></a>Configurar o seu servidor Azure AD Connect

Se utilizar a Autenticação Pass-through como método de inscrição, não é necessária uma verificação prévia adicional. Se utilizar a sincronização de hash de palavra-passe como método de inscrição e existe uma firewall entre o Azure AD Connect e o Azure AD, certifique-se de que:

- Utiliza a versão 1.1.1.644.0 ou mais tarde do Azure AD Connect.
- Se a sua firewall ou proxy permitir programas bloqueados ou seguros de DNS, adicione as ligações ao &#42;.msappproxy.us URLs sobre a porta 443.

  Caso contrário, permita o acesso às gamas IP do Centro de Dados Azure, que são atualizadas semanalmente. Este pré-requisito só se aplica quando ativa a funcionalidade. Não é necessário para os sinais reais do utilizador.

### <a name="roll-out-seamless-single-sign-on"></a>Lançar single sign-on seamless

Pode lançar gradualmente o Azure AD Seamless Single Sign-On para os seus utilizadores utilizando as seguintes instruções. Começa por adicionar o URL Azure AD [https://autologon.microsoft.us](https://autologon.microsoft.us) a todas ou selecionadas definições de zona intranet dos utilizadores utilizando a Política de Grupo em Diretório Ativo.

Também precisa de ativar a definição da política da zona intranet **Permitir atualizações à barra de estado através do script através da Política**de Grupo .

## <a name="browser-considerations"></a>Considerações de navegador

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (todas as plataformas)

Mozilla Firefox não usa automaticamente a autenticação Kerberos. Cada utilizador deve adicionar manualmente o URL Azure AD às suas definições firefox seguindo estes passos:

1. Executar Firefox e **insira cerca de:config**   na barra de endereços. Rejeite quaisquer notificações que possa ver.
1. Procure a preferência da **rede.negotiate-auth.trusted-uris.**   Esta preferência lista os sites confiáveis pelo Firefox para a autenticação Kerberos.
1. Clique no nome da preferência e, em seguida, selecione **Modificar**.
1. Entra  [**https://autologon.microsoft.us**](https://autologon.microsoft.us**)   na caixa.
1. Selecione **OK**   e, em seguida, reabra o navegador.

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge com base no Crómio (todas as plataformas)

Se tiver ultrapassado as  `AuthNegotiateDelegateAllowlist`   definições ou `AuthServerAllowlist`   políticas no seu ambiente, certifique-se de que lhes adiciona o URL Azure [https://autologon.microsoft.us](https://autologon.microsoft.us) AD.

### <a name="google-chrome-all-platforms"></a>Google Chrome (todas as plataformas)

Se tiver ultrapassado as  `AuthNegotiateDelegateWhitelist`   definições ou `AuthServerWhitelist`   políticas no seu ambiente, certifique-se de que lhes adiciona o URL Azure [https://autologon.microsoft.us](https://autologon.microsoft.us) AD.

## <a name="next-steps"></a>Passos seguintes

- [Autenticação pass-through](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [Inscrição única](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)
