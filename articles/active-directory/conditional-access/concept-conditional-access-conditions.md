---
title: Condições na política de acesso condicional - Diretório Ativo Azure
description: Quais são as condições de uma política de acesso condicional azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 622950c394d59d8ba504901f5bb0eea6bc04707f
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160720"
---
# <a name="conditional-access-conditions"></a>Acesso Condicional: Condições

Dentro de uma política de Acesso Condicional, um administrador pode recorrer a sinais de condições como risco, plataforma de dispositivos ou localização para melhorar as suas decisões políticas. 

![Definir uma política de acesso condicional e especificar condições](./media/concept-conditional-access-conditions/conditional-access-conditions.png)

Várias condições podem ser combinadas para criar políticas de acesso condicional de grãos finos e específicas.

Por exemplo, ao aceder a uma aplicação sensível, um administrador pode considerar informações de risco de entrada na Proteção de Identidade e localização na sua decisão de acesso, além de outros controlos, como a autenticação de vários fatores.

## <a name="sign-in-risk"></a>Risco de inscrição

Para os clientes com acesso à [Proteção de Identidade,](../identity-protection/overview-identity-protection.md)o risco de inscrição pode ser avaliado como parte de uma política de Acesso Condicional. O risco de inscrição representa a probabilidade de um determinado pedido de autenticação não ser autorizado pelo proprietário da identidade. Mais informações sobre o risco de entrada podem ser encontradas nos artigos, [O que é risco](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) e [como: Configurar e ativar políticas](../identity-protection/howto-identity-protection-configure-risk-policies.md)de risco .

## <a name="device-platforms"></a>Plataformas de dispositivos

A plataforma do dispositivo caracteriza-se pelo sistema operativo que funciona num dispositivo. A Azure AD identifica a plataforma utilizando informações fornecidas pelo dispositivo, tais como cordas do agente utilizador. Uma vez que as cordas do agente utilizador podem ser modificadas, esta informação não é verificada. A plataforma do dispositivo deve ser utilizada em conjunto com as políticas de conformidade do dispositivo Microsoft Intune ou como parte de uma declaração de bloco. O padrão é aplicar-se a todas as plataformas do dispositivo.

O Azure AD Conditional Access suporta as seguintes plataformas de dispositivos:

- Android
- iOS
- Windows Phone
- Windows
- macOS

> [!WARNING]
> A Microsoft está ciente de um problema com as políticas de Acesso Condicional e dispositivos baseados em macOS 10.15.4. Mais informações podem ser encontradas no post do blog, [Known Issue: Acesso condicional inesperadamente bloqueando o macOS 10.15.4 cliente de correio nativo/outras aplicações](https://techcommunity.microsoft.com/t5/intune-customer-success/known-issue-conditional-access-unexpectedly-blocking-macos-10-15/ba-p/1322283).

Se bloquear a autenticação do legado utilizando a condição **de Outros clientes,** também pode definir a condição da plataforma do dispositivo.

## <a name="locations"></a>Localizações

Ao configurar a localização como condição, as organizações podem optar por incluir ou excluir locais. Estes locais nomeados podem incluir a informação pública da rede IPv4, país ou região, ou mesmo áreas desconhecidas que não mapeiam para países ou regiões específicas. Apenas as gamas IP podem ser marcadas como um local de confiança.

Ao incluir **qualquer localização,** esta opção inclui qualquer endereço IP na internet e não apenas locais nomeados configurados. Ao selecionar **qualquer local,** os administradores podem optar por excluir todos os locais **confiáveis** ou **selecionados.**

Por exemplo, algumas organizações podem optar por não exigir a autenticação de vários fatores quando os seus utilizadores estão ligados à rede num local de confiança, como a sua sede física. Os administradores poderiam criar uma política que inclua qualquer localização, mas exclui os locais selecionados para as suas redes sedes.

Mais informações sobre os locais podem ser encontradas no artigo, Qual é a condição de localização no Acesso Condicional do [Diretório Ativo Azure.](location-condition.md)

## <a name="client-apps-preview"></a>Aplicativos de clientes (pré-visualização)

As políticas de Acesso Condicional por padrão aplicam-se a aplicações e aplicações baseadas no navegador que utilizam protocolos de autenticação modernos. Além destas aplicações, os administradores podem optar por incluir clientes Exchange ActiveSync e outros clientes que utilizam protocolos legados.

- Browser
   - Estas incluem aplicações baseadas na Web que utilizam protocolos como SAML, WS-Federation, OpenID Connect ou serviços registados como um cliente confidencial da OAuth.
- Aplicativos móveis e clientes de desktop
   - Clientes modernos de autenticação
      - Esta opção inclui aplicações como o desktop do Office e aplicações telefónicas.
   - Clientes ActiveSync de intercâmbio
      - Por predefinição, isto inclui toda a utilização do protocolo Exchange ActiveSync (EAS). A escolha da **política De aplicação apenas a plataformas suportadas** limitará a plataformas suportadas como iOS, Android e Windows.
      - Quando a política bloqueia a utilização do Exchange ActiveSync, o utilizador afetado receberá um único e-mail de quarentena. Este e-mail fornece informações sobre o porquê de estarem bloqueados e incluir instruções de reparação se possível.
   - Outros clientes
      - Esta opção inclui clientes que utilizam protocolos básicos/de autenticação legado que não suportam a autenticação moderna.
         - SMTP autenticado - Usado pelos clientes POP e IMAP para enviar mensagens de correio eletrónico.
         - Autodiscover - Usado pelos clientes Outlook e EAS para encontrar e ligar-se a caixas de correio no Exchange Online.
         - Troca online PowerShell - Usado para ligar a Exchange Online com powerShell remoto. Se bloquear a autenticação Básica para troca de PowerShell online, tem de utilizar o Módulo Exchange Online PowerShell para se ligar. Para obter instruções, consulte Connect to Exchange Online PowerShell utilizando a [autenticação de vários fatores](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
         - Exchange Web Services (EWS) - Uma interface de programação que é usada pelo Outlook, Outlook for Mac e aplicações de terceiros.
         - IMAP4 - Usado por clientes de e-mail IMAP.
         - MAPI sobre HTTP (MAPI/HTTP) - Usado pelo Outlook 2010 e mais tarde.
         - Offline Address Book (OAB) - Uma cópia das coleções da lista de endereços que são descarregadas e utilizadas pelo Outlook.
         - Outlook Anywhere (RPC over HTTP) - Usado pelo Outlook 2016 e mais cedo.
         - Outlook Service - Usado pelo aplicativo Mail and Calendar para windows 10.
         - POP3 - Usado por clientes de e-mail POP.
         - Reporting Web Services - Usado para recuperar dados de relatório sintetizados em Exchange Online.

Estas condições são comumente usadas quando se exige um dispositivo gerido, bloqueando a autenticação do legado e bloqueando aplicações web, mas permitindo aplicações móveis ou de desktop.

### <a name="supported-browsers"></a>Browsers suportados

Esta definição funciona com todos os navegadores. No entanto, para satisfazer uma política de dispositivos, como um requisito de dispositivo compatível, os seguintes sistemas operativos e navegadores são suportados:

| SO | Navegadores |
| :-- | :-- |
| Windows 10 | Microsoft Edge, Internet Explorer, Chrome |
| Windows 8 / 8.1 | Internet Explorer |
| Windows 7 | Internet Explorer |
| iOS | Microsoft Edge, Navegador Gerido intune, Safari |
| Android | Microsoft Edge, Navegador Gerido intune, Chrome |
| Windows Phone | Microsoft Edge, Internet Explorer |
| Windows Server 2019 | Microsoft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Por que vejo um pedido de certificado no navegador

No Windows 7, iOS, Android e macOS Azure AD identifica o dispositivo utilizando um certificado de cliente que é aprovisionado quando o dispositivo está registado na Azure AD.  Quando um utilizador entra pela primeira vez através do navegador, o utilizador é solicitado a selecionar o certificado. O utilizador deve selecionar este certificado antes de utilizar o navegador.

#### <a name="chrome-support"></a>Suporte ao Chrome

Para suporte chrome na **Atualização de Criadores do Windows 10 (versão 1703)** ou posterior, instale a extensão das [Contas Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Esta extensão é necessária quando uma política de acesso condicional requer detalhes específicos do dispositivo.

Para implementar automaticamente esta extensão para os navegadores Chrome, crie a seguinte chave de registo:

|    |    |
| --- | --- |
| Caminho | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Nome | 1 |
| Tipo | REG_SZ (Corda) |
| Dados | ppnbnpeolgkicgegkkkbjmhlideopiji;https\://clients2.google.com/service/update2/crx |

Para suporte chrome no **Windows 8.1 e 7,** crie a seguinte tecla de registo:

|    |    |
| --- | --- |
| Caminho | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Nome | 1 |
| Tipo | REG_SZ (Corda) |
| Dados | {"pattern":"https://device.login.microsoftonline.com"filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

Estes navegadores suportam a autenticação do dispositivo, permitindo que o dispositivo seja identificado e validado contra uma apólice. A verificação do dispositivo falha se o navegador estiver em funcionamento em modo privado.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Aplicações móveis suportadas e clientes de desktop

As organizações podem selecionar **aplicações móveis e clientes** de desktop como aplicação de cliente.

Esta definição tem impacto nas tentativas de acesso feitas a partir das seguintes aplicações móveis e clientes de desktop:

| Aplicações do cliente | Serviço alvo | Plataforma |
| --- | --- | --- |
| App DE CRM dynamics | Dynamics CRM | Windows 10, Windows 8.1, iOS e Android |
| Aplicação Mail/Calendar/People, Outlook 2016, Outlook 2013 (com autenticação moderna)| Escritório 365 Exchange Online | Windows 10 |
| MFA e política de localização para apps. As políticas baseadas em dispositivos não são apoiadas.| Qualquer serviço de aplicativo saqueado das Minhas Aplicações | Android e iOS |
| Microsoft Teams Services - isto controla todos os serviços que suportam as Equipas microsoft e todas as suas Aplicações de Clientes - Windows Desktop, iOS, Android, WP e cliente web | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android e macOS |
| Aplicações do Office 2016, Office 2013 (com autenticação moderna), [cliente de sincronização OneDrive](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Aplicações do Office 2016, aplicações do Universal Office, Office 2013 (com autenticação moderna), [cliente sincronizado OneDrive](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (Palavra, Excel, PowerPoint, OneNote apenas). | Office 365 SharePoint Online | macOS |
| Escritório 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Aplicativos móveis de escritório | Office 365 SharePoint Online | Android, iOS |
| App Office Yammer | Escritório 365 Yammer | Windows 10, iOS, Android |
| Perspetivas 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office for macOS) | Escritório 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (com autenticação moderna), Skype para Negócios (com autenticação moderna) | Escritório 365 Exchange Online | Windows 8.1, Windows 7 |
| Aplicação móvel Outlook | Escritório 365 Exchange Online | Android, iOS |
| Aplicação Power BI | Serviço Power BI | Windows 10, Windows 8.1, Windows 7, Android e iOS |
| Skype para Empresas | Escritório 365 Exchange Online| Android, iOS |
| Aplicativo Visual Studio Team Services | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS e Android |

### <a name="exchange-activesync-clients"></a>Clientes ActiveSync de intercâmbio

- As organizações só podem selecionar clientes Exchange ActiveSync ao atribuir políticas a utilizadores ou grupos. Selecionar **Todos os utilizadores**, **Todos os utilizadores convidados e externos**, ou **funções de Diretório** fará com que todos os utilizadores fiquem bloqueados.
- Ao criar uma política atribuída aos clientes Exchange ActiveSync, o **Office 365 Exchange Online** deve ser a única aplicação em nuvem atribuída à apólice. 
- As organizações podem reduzir o âmbito desta política a plataformas específicas utilizando a condição das **plataformas do Dispositivo.**

Se o controlo de acesso atribuído às utilizações da apólice **exigir aplicação de cliente aprovada,** o utilizador é direcionado para instalar e utilizar o cliente móvel Outlook. No caso de ser necessária **a autenticação multifactor,** os utilizadores afetados são bloqueados, uma vez que a autenticação básica não suporta a autenticação de vários fatores.

Para obter mais informações, veja os artigos seguintes:

- [Autenticação do legado do bloco com Acesso Condicional](block-legacy-authentication.md)
- [Exigir aplicações de clientes aprovadas com Acesso Condicional](app-based-conditional-access.md)

### <a name="other-clients"></a>Outros clientes

Ao selecionar **Outros clientes,** pode especificar uma condição que afeta aplicações que utilizam a autenticação básica com protocolos de correio como iMAP, MAPI, POP, SMTP e aplicações antigas do Office que não usam autenticação moderna.

## <a name="device-state-preview"></a>Estado do dispositivo (pré-visualização)

A condição de estado do dispositivo pode ser usada para excluir dispositivos que sejam híbridos Azure AD unidos e/ou dispositivos marcados como conformes com uma política de conformidade Microsoft Intune a partir das políticas de acesso condicional de uma organização.

Por exemplo, *todos os utilizadores* que acedam à aplicação cloud da Microsoft *Azure Management,* incluindo todo o **estado do dispositivo,** excluindo o Dispositivo Hybrid **Azure AD, aderiram** e **dispositivo marcado como conforme** e para *controlos*de acesso , **Block**. 
   - Este exemplo criaria uma política que apenas permite o acesso à Microsoft Azure Management a partir de dispositivos híbridos Azure AD unidos e/ou dispositivos marcados como conformes.

## <a name="next-steps"></a>Passos seguintes

- [Acesso Condicional: Subvenção](concept-conditional-access-grant.md)

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)
