---
title: 'Azure AD Connect: Considerações de identidade híbrida para governo Azure'
description: Considerações especiais para implantar o Azure AD Connect com a nuvem governamental.
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
ms.openlocfilehash: 2ad28beb68afb5207e7b36c5d76c4dac808c5114
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81378928"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Considerações sobre identidade híbrida para o Azure Government 
O seguinte documento descreve as considerações para a implementação de um ambiente híbrido com a nuvem do Governo Azure.  Esta informação é fornecida como referência para administradores e arquitetos que estão a trabalhar com a nuvem do Governo Azure.  
> [!NOTE] 
> Para integrar um ambiente ad no local com a nuvem Azure Governemnt, você precisa atualizar para o mais recente lançamento do [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594). 

> [!NOTE] 
> Para obter uma lista completa de pontos finais do Governo dos EUA, consulte a [documentação](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) 

## <a name="pass-through-authentication"></a>Autenticação pass-through 
São fornecidas as seguintes informações para a implementação da autenticação pass-through (PTA) e da nuvem do Governo azure.

### <a name="allow-access-to-urls"></a>Permitir o acesso aos URLs  
Antes de implementar o agente de autenticação pass-through, verifique se existe uma firewall entre os seus servidores e a AD Azure. Se a sua firewall ou proxy permitir a listagem de dNS, adicione as seguintes ligações: 
> [!NOTE]
> As seguintes orientações aplicam-se igualmente à instalação do [conector Proxy](https://aka.ms/whyappproxy) de Aplicação para ambientes do Governo Azure.

|do IdP |Como é usado|
|-----|-----| 
|*.msappproxy.us *.servicebus.usgovcloudapi.net|Comunicação entre o agente e o serviço de nuvem Azure AD |
|mscrl.microsoft.us:80 crl.microsoft.us:80 </br>ocsp.msocsp.us:80 www.microsoft.us:80| O agente usa estes URLs para verificar certificados.| 
|login.windows.us secure.aadcdn.microsoftonline-p.com * </br>.microsoftonline.us .microsoftonline-p.us * </br>*.msauth.net </br> *.msauthimages.net </br>*.msecnd.net</br>*.msftauth.net </br>*.msftauthimages.net</br>*.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| O agente utiliza estes URLs durante o processo de registo.| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Instale o agente para a nuvem do Governo Azure 
Para instalar o agente para a nuvem do Governo Azure, deve seguir estes passos específicos: No terminal da linha de comando, navegar para pasta onde esteja localizado o executável para instalar o agente. Executar o seguinte comando que especifica que a instalação é para o Governo Azure. 

Para autenticação passthrough: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

Para procuração de aplicação:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>Sinal único ligado 
Configurar o servidor Azure AD Connect: Se utilizar a Autenticação Pass-through como método de entrada, não é necessária uma verificação prévia adicional. Se utilizar a sincronização de hash de palavra-passe como método de entrada e se houver uma firewall entre o Azure AD Connect e o Azure AD, certifique-se de que:
- Utiliza a versão 1.1.644.0 ou mais tarde do Azure AD Connect. 
- Se a sua firewall ou proxy permitir a listagem de dNS, adicione as ligações aos URLs *.msapproxy.us sobre a porta 443. Caso contrário, permita o acesso às gamas IP do Centro de Dados Azure, que são atualizadas semanalmente. Este pré-requisito só é aplicável quando ativa a funcionalidade. Não é necessário para os insinsos reais do utilizador. 

### <a name="rolling-out-seamless-sso"></a>Lançando SSO sem emenda 
Pode lançar gradualmente SSO sem emenda aos seus utilizadores utilizando as instruções fornecidas abaixo. Começa por adicionar o seguinte URL Azure AD a todas ou selecionadas definições de zona intranet dos utilizadores utilizando a Política de Grupo no Diretório Ativo:https://autologon.microsoft.us 

Além disso, você precisa ativar uma definição de política de zona Intranet chamada Permitir atualizações para barra de estado através de script através de Política de Grupo. Considerações de navegador Mozilla Firefox (todas as plataformas) Mozilla Firefox não utiliza automaticamente a autenticação Kerberos. Cada utilizador deve adicionar manualmente o URL Azure AD às suas definições firefox utilizando os seguintes passos: 
1. Executar Firefox e insira cerca de:config na barra de endereços. Rejeite qualquer notificação que veja. 
2. Procure a preferência da rede.negotiate-auth.trusted-uris. Esta preferência lista os sites fidedignos do Firefox para a autenticação Kerberos. 
3. Clique à direita e selecione Modificar. 
4. Entra https://autologon.microsoft.us no campo.
5. Selecione OK e, em seguida, reabra o navegador. 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge com base no Crómio (todas as plataformas) 
Se tiver ultrapassado as `AuthNegotiateDelegateAllowlist` definições de política ou no `AuthServerAllowlist` seu ambiente, certifique-sehttps://autologon.microsoft.us) de que adiciona o URL da Azure AD ( também a eles. 

### <a name="google-chrome-all-platforms"></a>Google Chrome (todas as plataformas) 
Se tiver ultrapassado as `AuthNegotiateDelegateWhitelist` definições de política ou no `AuthServerWhitelist` seu ambiente, certifique-sehttps://autologon.microsoft.us) de que adiciona o URL da Azure AD ( também a eles. 

## <a name="next-steps"></a>Passos seguintes
[Sinal](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[único](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) de autenticação pass-through 
