---
title: Condições na política de acesso condicional - Diretório Ativo Azure
description: Quais são as condições de uma política de acesso condicional AD Azure
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: a74fe2bf6b326dac782ac75418a7f4960e66501a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87275008"
---
# <a name="conditional-access-conditions"></a>Acesso Condicional: Condições

Dentro de uma política de Acesso Condicional, um administrador pode fazer uso de sinais de condições como risco, plataforma do dispositivo ou localização para melhorar as suas decisões políticas. 

![Defina uma política de acesso condicional e especifique as condições](./media/concept-conditional-access-conditions/conditional-access-conditions.png)

Podem ser combinadas múltiplas condições para criar políticas de acesso condicional e de grãos finos e específicas.

Por exemplo, ao aceder a uma aplicação sensível, um administrador pode ter informações de risco de entrada na Proteção de Identidade e localização na sua decisão de acesso, além de outros controlos como a autenticação de vários fatores.

## <a name="sign-in-risk"></a>Risco de início de sessão

Para clientes com acesso à Proteção de [Identidade,](../identity-protection/overview-identity-protection.md)o risco de inscrição pode ser avaliado como parte de uma política de Acesso Condicional. O risco de entrada representa a probabilidade de um dado pedido de autenticação não ser autorizado pelo proprietário da identidade. Mais informações sobre o risco de inscrição podem ser encontradas nos artigos, [O que é risco](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) e [como: Configurar e permitir políticas](../identity-protection/howto-identity-protection-configure-risk-policies.md)de risco .

## <a name="user-risk"></a>Risco de utilizador 

Para clientes com acesso à [Proteção de Identidade,](../identity-protection/overview-identity-protection.md)o risco do utilizador pode ser avaliado como parte de uma política de Acesso Condicional. O risco do utilizador representa a probabilidade de uma determinada identidade ou conta estar comprometida. Mais informações sobre o risco do utilizador podem ser encontradas nos artigos, [O que é risco](../identity-protection/concept-identity-protection-risks.md#user-risk) e [como: Configurar e permitir políticas de risco.](../identity-protection/howto-identity-protection-configure-risk-policies.md)

## <a name="device-platforms"></a>Plataformas de dispositivos

A plataforma do dispositivo caracteriza-se pelo sistema operativo que funciona num dispositivo. O Azure AD identifica a plataforma utilizando informações fornecidas pelo dispositivo, como as cordas do agente do utilizador. Uma vez que as cordas do agente do utilizador podem ser modificadas, esta informação não é verificada. A plataforma do dispositivo deve ser utilizada em conjunto com as políticas de conformidade do dispositivo Microsoft Intune ou como parte de uma declaração de bloqueio. O padrão é aplicar-se a todas as plataformas do dispositivo.

O Azure AD Conditional Access suporta as seguintes plataformas do dispositivo:

- Android
- iOS
- Windows Phone
- Windows
- macOS

Se bloquear a autenticação de legados utilizando a condição **de Outros clientes,** também pode definir a condição da plataforma do dispositivo.

## <a name="locations"></a>Localizações

Ao configurar a localização como uma condição, as organizações podem optar por incluir ou excluir locais. Estes locais nomeados podem incluir informações públicas da rede IPv4, país ou região, ou mesmo áreas desconhecidas que não mapeiam para países ou regiões específicas. Apenas os intervalos IP podem ser marcados como uma localização fidedigna.

Ao incluir **qualquer localização,** esta opção inclui qualquer endereço IP na internet e não apenas locais com nomes configurados. Ao selecionar **qualquer local,** os administradores podem optar por excluir todos os locais **fidedignos** ou **selecionados.**

Por exemplo, algumas organizações podem optar por não exigir a autenticação de vários fatores quando os seus utilizadores estão ligados à rede num local de confiança, como a sua sede física. Os administradores poderiam criar uma política que incluísse qualquer localização, mas excluindo as localizações selecionadas para as suas redes sede.

Mais informações sobre as localizações podem ser encontradas no artigo, [Qual é a condição de localização no Azure Ative Directory Conditional Access](location-condition.md).

## <a name="client-apps-preview"></a>Aplicativos de cliente (pré-visualização)

As políticas de Acesso Condicional por padrão aplicam-se a aplicações e aplicações baseadas no navegador que utilizam protocolos de autenticação modernos. Além destas aplicações, os administradores podem optar por incluir clientes Exchange ActiveSync e outros clientes que utilizam protocolos legados.

> [!NOTE]
> O Configure Sim/Não alternar na condição de aplicações do cliente foi removido para facilitar a mente de ver quais as aplicações do cliente selecionadas. Isto não afeta quais as aplicações de clientes a que uma política existente se aplica.

- Browser
   - Estas incluem aplicações baseadas na Web que usam protocolos como SAML, WS-Federation, OpenID Connect, ou serviços registados como um cliente confidencial da OAuth.
- Aplicativos móveis e clientes de desktop
   - Clientes de autenticação moderna
      - Esta opção inclui aplicações como o desktop do Office e aplicações telefónicas.
   - Troca de clientes ActiveSync
      - Por predefinição, isto inclui toda a utilização do protocolo Exchange ActiveSync (EAS). Escolher **a política de Aplicação apenas para plataformas suportadas** irá limitar-se a plataformas suportadas como iOS, Android e Windows.
      - Quando a política bloqueia a utilização do Exchange ActiveSync, o utilizador afetado receberá um único e-mail de quarentena. Este e-mail fornece informações sobre o porquê de estarem bloqueados e incluir instruções de reparação se possível.
   - Outros clientes
      - Esta opção inclui clientes que utilizam protocolos de autenticação básica/legado que não suportam a autenticação moderna.
         - SMTP autenticado - Usado pelos clientes POP e IMAP para enviar mensagens de correio eletrónico.
         - Autodiscover - Usado pelos clientes Outlook e EAS para encontrar e ligar às caixas de correio em Exchange Online.
         - Exchange Online PowerShell - Usado para ligar-se a Exchange Online com powerShell remoto. Se bloquear a autenticação básica para Exchange Online PowerShell, tem de utilizar o Módulo Exchange Online PowerShell para se ligar. Para obter instruções, consulte [Connect to Exchange Online PowerShell utilizando a autenticação de vários fatores.](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)
         - Exchange Web Services (EWS) - Uma interface de programação que é usada pelo Outlook, Outlook for Mac e aplicações de terceiros.
         - IMAP4 - Usado por clientes de e-mail IMAP.
         - MAPI over HTTP (MAPI/HTTP) - Usado pelo Outlook 2010 e posteriormente.
         - Offline Address Book (OAB) - Uma cópia das coleções de listas de endereços que são descarregadas e usadas pelo Outlook.
         - Outlook Anywhere (RPC over HTTP) - Usado pelo Outlook 2016 e mais cedo.
         - Serviço Outlook - Usado pela aplicação De Correio e Calendário para o Windows 10.
         - POP3 - Usado por clientes de e-mail POP.
         - Reportar Serviços Web - Usado para recuperar dados de relatório em Exchange Online.

Estas condições são normalmente utilizadas quando se necessita de um dispositivo gerido, bloqueando a autenticação de legados e bloqueando aplicações web, mas permitindo aplicações móveis ou de desktop.

### <a name="supported-browsers"></a>Browsers suportados

Esta definição funciona com todos os navegadores. No entanto, para satisfazer uma política do dispositivo, como um requisito de dispositivo conforme, os seguintes sistemas operativos e navegadores são suportados:

| SO | Navegadores |
| :-- | :-- |
| Windows 10 | Microsoft Edge, Internet Explorer, Chrome |
| Windows 8 / 8.1 | Internet Explorer, Chrome |
| Windows 7 | Internet Explorer, Chrome |
| iOS | Microsoft Edge, Intune Managed Browser, Safari |
| Android | Microsoft Edge, Intune Managed Browser, Chrome |
| Windows Phone | Microsoft Edge, Internet Explorer |
| Windows Server 2019 | Microsoft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Por que vejo um certificado no navegador

No Windows 7, iOS, Android e macOS Azure AD identifica o dispositivo usando um certificado de cliente que é a provisionado quando o dispositivo está registado no AZure AD.  Quando um utilizador entra pela primeira vez através do navegador, o utilizador é solicitado a selecionar o certificado. O utilizador deve selecionar este certificado antes de utilizar o navegador.

#### <a name="chrome-support"></a>Suporte cromado

Para suporte ao Chrome na **Atualização de Criadores do Windows 10 (versão 1703)** ou posterior, instale a extensão das [contas do Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Esta extensão é necessária quando uma política de acesso condicional requer detalhes específicos do dispositivo.

Para implementar automaticamente esta extensão para os navegadores Chrome, crie a seguinte chave de registo:

- Caminho HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist
- Nome 1
- Tipo REG_SZ (Corda)
- Dados ppnbnpeolgkicgegkbbbjmhlideopiji;https \: //clients2.google.com/service/update2/crx

Para o suporte do Chrome no **Windows 8.1 e 7,** crie a seguinte chave de registo:

- Caminho HKEY_LOCAL_MACHINE\SOFTWARE\Políticas\Google\Chrome\AutoSelectCertificateForUrls
- Nome 1
- Tipo REG_SZ (Corda)
- Dados {"pattern":" https://device.login.microsoftonline.com "filter":{"ISSUER":{"CN":"MS-Organization-Access}}}}

Estes navegadores suportam a autenticação do dispositivo, permitindo que o dispositivo seja identificado e validado contra uma política. A verificação do dispositivo falha se o navegador estiver em funcionamento em modo privado.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Aplicações móveis suportadas e clientes de desktop

As organizações podem selecionar **aplicações móveis e clientes de desktop** como app de clientes.

Esta definição tem impacto nas tentativas de acesso feitas a partir das seguintes aplicações móveis e clientes de desktop:

| Aplicações do cliente | Serviço-Alvo | Plataforma |
| --- | --- | --- |
| Aplicativo Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS e Android |
| Mail/Calendar/People app, Outlook 2016, Outlook 2013 (com autenticação moderna)| Bolsa de Escritório 365 Online | Windows 10 |
| MFA e política de localização para aplicações. As políticas baseadas em dispositivos não são apoiadas.| Qualquer serviço de aplicativo My Apps | Android e iOS |
| Microsoft Teams Services - isto controla todos os serviços que suportam as Equipas microsoft e todas as suas aplicações de clientes - Windows Desktop, iOS, Android, WP e cliente web | Microsoft Stream | Windows 10, Windows 8.1, Windows 7, iOS, Android e macOS |
| Aplicativos Office 2016, Office 2013 (com autenticação moderna), [cliente sincronizado OneDrive](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Aplicativos office 2016, aplicativos Universal Office, Office 2013 (com autenticação moderna), [cliente sincronizado OneDrive](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (Word, Excel, PowerPoint, OneNote only). | Office 365 SharePoint Online | macOS |
| Escritório 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Aplicativos móveis de escritório | Office 365 SharePoint Online | Android, iOS |
| App Office Yammer | Escritório 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office for macOS) | Bolsa de Escritório 365 Online | macOS |
| Outlook 2016, Outlook 2013 (com autenticação moderna), Skype para Negócios (com autenticação moderna) | Bolsa de Escritório 365 Online | Windows 8.1, Windows 7 |
| App móvel outlook | Bolsa de Escritório 365 Online | Android, iOS |
| Aplicação Power BI | Serviço Power BI | Windows 10, Windows 8.1, Windows 7, Android e iOS |
| Skype para Empresas | Bolsa de Escritório 365 Online| Android, iOS |
| Aplicativo Visual Studio Team Services | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS e Android |

### <a name="exchange-activesync-clients"></a>Troca de clientes ActiveSync

- As organizações só podem selecionar clientes Exchange ActiveSync ao atribuir a política aos utilizadores ou grupos. Selecionando **Todos os utilizadores**, **Todos os utilizadores convidados e externos,** ou **funções de Diretório** farão com que todos os utilizadores fiquem bloqueados.
- Ao criar uma política atribuída aos clientes Exchange ActiveSync, o **Office 365 Exchange Online** deve ser a única aplicação em nuvem atribuída à apólice. 
- As organizações podem reduzir o âmbito desta política a plataformas específicas que utilizam a condição das **plataformas do Dispositivo.**

Se o controlo de acesso atribuído à apólice utilizar **requerer a aplicação de cliente aprovada,** o utilizador é direcionado para instalar e utilizar o cliente móvel do Outlook. No caso de ser necessária **a autenticação multi-factor,** os utilizadores afetados estão bloqueados, porque a autenticação básica não suporta a autenticação de vários fatores.

Para obter mais informações, veja os seguintes artigos:

- [Bloquear autenticação do legado com Acesso Condicional](block-legacy-authentication.md)
- [Exigir aplicações de clientes aprovadas com Acesso Condicional](app-based-conditional-access.md)

### <a name="other-clients"></a>Outros clientes

Ao selecionar **Outros clientes,** pode especificar uma condição que afete as aplicações que utilizam a autenticação básica com protocolos de correio como IMAP, MAPI, POP, SMTP e aplicações antigas do Office que não utilizam a autenticação moderna.

## <a name="device-state-preview"></a>Estado do dispositivo (pré-visualização)

A condição do estado do dispositivo pode ser usada para excluir dispositivos que sejam híbridos Azure AD unidos e/ou dispositivos marcados como conformes com uma política de conformidade microsoft Intune a partir das políticas de acesso condicional de uma organização.

Por exemplo, *todos os utilizadores* que acedam à aplicação cloud *da Microsoft Azure Management,* incluindo **todos os dispositivos,** excluindo a **adada híbrida híbrida** do dispositivo, e dispositivo marcado como **conforme** e para *controlos de acesso*, **Bloco**. 
   - Este exemplo criaria uma política que apenas permite o acesso à Microsoft Azure Management a partir de dispositivos que são híbridos Azure AD unidos e/ou dispositivos marcados como conformes.

## <a name="next-steps"></a>Passos seguintes

- [Acesso Condicional: Concessão](concept-conditional-access-grant.md)

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)
