---
title: Referência de configurações de acesso condicional Azure Active Directory | Microsoft Docs
description: Obtenha uma visão geral das configurações com suporte em uma política de acesso condicional Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 07/10/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d56b1f800c71a5bbef7ffb1155d05e096113e2c
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162424"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Referência de configurações de acesso condicional Azure Active Directory

Você pode usar o [acesso condicional do Azure Active Directory (AD do Azure)](../active-directory-conditional-access-azure-portal.md) para controlar como os usuários autorizados podem acessar seus recursos.

Este artigo fornece informações de suporte para as seguintes opções de configuração em uma política de acesso condicional:

- Atribuições de aplicativos de nuvem
- Condição de plataforma de dispositivo
- Condição de aplicativos cliente
- Requisito de aplicativo cliente aprovado

Se essas não forem as informações que você está procurando, deixe um comentário no final deste artigo.

## <a name="cloud-apps-assignments"></a>Atribuições de aplicativos de nuvem

Com as políticas de acesso condicional, você controla como os usuários acessam seus [aplicativos de nuvem](conditions.md#cloud-apps-and-actions). Ao configurar uma política de acesso condicional, você precisa selecionar pelo menos um aplicativo de nuvem. 

![Selecione os aplicativos de nuvem para sua política](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Aplicativos em nuvem da Microsoft

Você pode atribuir uma política de acesso condicional aos seguintes aplicativos de nuvem da Microsoft:

- Azure Analysis Services
- DevOps do Azure
- Banco de dados SQL do Azure e data warehouse- [saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Análise do Microsoft Application Insights
- Proteção de Informações do Microsoft Azure- [saiba mais](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Gerenciamento de Microsoft Azure- [saiba mais](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Gerenciamento de assinatura Microsoft Azure
- Microsoft Cloud App Security
- Portal de controle de acesso de ferramentas do Microsoft Commerce
- Serviço de autenticação de ferramentas do Microsoft Commerce
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Inscrição do Microsoft Intune
- Microsoft Planner
- Microsoft PowerApps
- Pesquisa da Microsoft no Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Grupos do Outlook
- Serviço de Power BI
- Project Online
- Skype para Empresas Online
- VPN (rede virtual privada)
- Visual Studio App Center
- Windows Defender ATP

### <a name="other-applications"></a>Outros aplicativos

Além dos aplicativos de nuvem da Microsoft, você pode atribuir uma política de acesso condicional aos seguintes tipos de aplicativos de nuvem:

- Aplicativos conectados ao Azure AD
- Aplicativo de SaaS (software como serviço) federado integrado
- Aplicativos que usam SSO (logon único) de senha
- Aplicações de linha de negócio
- Aplicativos que usam o Azure Proxy de Aplicativo do AD

## <a name="device-platform-condition"></a>Condição de plataforma de dispositivo

Em uma política de acesso condicional, você pode configurar a condição de plataforma do dispositivo para vincular a política ao sistema operacional em um cliente. O acesso condicional do Azure AD dá suporte às seguintes plataformas de dispositivo:

- Android
- iOS
- Windows Phone
- Windows
- macOS

![Vincular a política de acesso ao sistema operacional do cliente](./media/technical-reference/41.png)

Se você bloquear a autenticação herdada usando a condição **outros clientes** , também poderá definir a condição de plataforma do dispositivo.

## <a name="client-apps-condition"></a>Condição de aplicativos cliente

Na política de acesso condicional, você pode configurar a condição de [aplicativos cliente](conditions.md#client-apps) para vincular a política ao aplicativo cliente que iniciou uma tentativa de acesso. Defina a condição aplicativos cliente para conceder ou bloquear o acesso quando uma tentativa de acesso for feita dos seguintes tipos de aplicativos cliente:

- Browser
- Aplicativos móveis e aplicativos da área de trabalho

![Controlar o acesso para aplicativos cliente](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Browsers suportados

Em sua política de acesso condicional, você pode selecionar **navegadores** como aplicativo cliente.

![Controlar o acesso para navegadores com suporte](./media/technical-reference/05.png)

Essa configuração funciona com todos os navegadores. No entanto, para atender a uma política de dispositivo, como um requisito de dispositivo compatível, os seguintes sistemas operacionais e navegadores têm suporte:

| OS                     | Browsers                                        |
| :--                    | :--                                             |
| Windows 10             | Microsoft Edge, Internet Explorer, Chrome       |
| Windows 8 / 8.1        | Internet Explorer, Chrome                       |
| Windows 7              | Internet Explorer, Chrome                       |
| iOS                    | Microsoft Edge, Intune Managed Browser, Safari  |
| Android                | Microsoft Edge, Intune Managed Browsers, Chrome  |
| Windows Phone          | Microsoft Edge, Internet Explorer               |
| Windows Server de 2019    | Microsoft Edge, Internet Explorer, Chrome       |
| Windows Server 2016    | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer                       |
| Windows Server 2008 R2 | Internet Explorer                       |
| macOS                  | Chrome, Safari                                  |


#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Por que vejo um prompt de certificado no navegador

No Windows 7, iOS, Android e macOS, o Azure AD identifica o dispositivo usando um certificado de cliente que é provisionado quando o dispositivo é registrado no Azure AD.  Quando um usuário entra pela primeira vez por meio do navegador, é solicitado que o usuário selecione o certificado. O usuário deve selecionar esse certificado antes de usar o navegador.

#### <a name="chrome-support"></a>Suporte ao Chrome

Para obter suporte ao Chrome na **atualização do Windows 10 para criadores (versão 1703)** ou posterior, instale a [extensão de contas do Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Essa extensão é necessária quando uma política de acesso condicional requer detalhes específicos do dispositivo.

Para implantar automaticamente essa extensão em navegadores Chrome, crie a seguinte chave do registro:

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Name | 1 |
| Type | REG_SZ (String) |
| Data | ppnbnpeolgkicgegkbkbjmhlideopiji; https\://clients2.google.com/Service/Update2/CRX |

Para obter suporte ao Chrome no **Windows 8.1 e no 7**, crie a seguinte chave do registro:

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Name | 1 |
| Type | REG_SZ (String) |
| Data | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

Esses navegadores dão suporte à autenticação de dispositivo, permitindo que o dispositivo seja identificado e validado em relação a uma política. A verificação de dispositivo falhará se o navegador estiver sendo executado em modo privado.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Aplicativos móveis e clientes de área de trabalho com suporte

Em sua política de acesso condicional, você pode selecionar **aplicativos móveis e clientes de área de trabalho** como aplicativo cliente.

![Controlar o acesso para aplicativos móveis ou clientes de área de trabalho com suporte](./media/technical-reference/06.png)

Essa configuração tem um impacto nas tentativas de acesso feitas dos seguintes aplicativos móveis e clientes de desktop:

| Aplicações do cliente | Serviço de destino | Plataforma |
| --- | --- | --- |
| Aplicativo Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS e Android |
| Aplicativo de email/calendário/pessoas, Outlook 2016, Outlook 2013 (com autenticação moderna)| Office 365 Exchange Online | Windows 10 |
| MFA e política de local para aplicativos. Não há suporte para políticas baseadas em dispositivo.| Qualquer serviço de aplicativo meus aplicativos| Android e iOS |
| Microsoft Team Services – controla todos os serviços que dão suporte ao Microsoft Teams e todos os seus aplicativos cliente-Windows desktop, iOS, Android, WP e cliente Web | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android e macOS |
| Aplicativos do Office 2016, Office 2013 (com autenticação moderna), cliente de sincronização do OneDrive (consulte [observações](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Aplicativos do Office 2016, aplicativos do Universal Office, Office 2013 (com autenticação moderna), cliente de sincronização do OneDrive (consulte [observações](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), o suporte a grupos do Office é planejado para o futuro, o suporte a aplicativos do SharePoint é planejado para o futuro | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (somente Word, Excel, PowerPoint, OneNote). Suporte do OneDrive for Business planejado para o futuro| Office 365 SharePoint Online| macOS|
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Aplicativos móveis do Office | Office 365 SharePoint Online | Android, iOS |
| Aplicativo Office Yammer | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office para macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (com autenticação moderna), Skype for Business (com autenticação moderna) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Aplicativo móvel do Outlook | Office 365 Exchange Online | Android, iOS |
| Power BI aplicativo | serviço do Power BI | Windows 10, Windows 8.1, Windows 7, Android e iOS |
| Skype para Empresas | Office 365 Exchange Online| Android, IOS |
| Visual Studio Team Services aplicativo | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS e Android |

## <a name="support-for-legacy-authentication"></a>Suporte para autenticação herdada

Ao selecionar **outros clientes**, você pode especificar uma condição que afeta os aplicativos que usam a autenticação básica com protocolos de email, como IMAP, MAPI, pop, SMTP e aplicativos mais antigos do Office que não usam autenticação moderna.  

![Outros clientes](./media/technical-reference/11.png)

Para obter mais informações, consulte [aplicativos cliente](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Requisito de aplicativo cliente aprovado

Em sua política de acesso condicional, você pode exigir que uma tentativa de acesso aos aplicativos de nuvem selecionados precise ser feita de um aplicativo cliente aprovado. 

![Controlar o acesso para aplicativos cliente aprovados](./media/technical-reference/21.png)

Essa configuração se aplica aos seguintes aplicativos cliente:

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Faturação da Microsoft
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype para Empresas
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer

**Observações**

- Os aplicativos cliente aprovados dão suporte ao recurso de gerenciamento de aplicativos móveis do Intune.
- O requisito **exigir aplicativo cliente aprovado** :
   - O só dá suporte ao iOS e ao Android para a [condição de plataforma de dispositivo](#device-platform-condition).

## <a name="app-protection-policy-requirement"></a>Requisito de política de proteção de aplicativo 

Em sua política de acesso condicional, você pode exigir que uma política de proteção de aplicativo esteja presente no aplicativo cliente antes que o acesso esteja disponível para os aplicativos de nuvem selecionados. 

![Controlar o acesso com a política de proteção de aplicativo](./media/technical-reference/22.png)

Essa configuração se aplica aos seguintes aplicativos cliente:

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Observações**

- Aplicativos para a política de proteção de aplicativo dão suporte ao recurso de gerenciamento de aplicativos móveis do Intune com proteção de política.
- Os requisitos da **política exigir proteção de aplicativo** :
    - O só dá suporte ao iOS e ao Android para a [condição de plataforma de dispositivo](#device-platform-condition).

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral do acesso condicional, consulte [o que é o acesso condicional no Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
- Se você estiver pronto para configurar políticas de acesso condicional em seu ambiente, consulte as [práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md).

<!--Image references-->
[1]: ./media/technical-reference/01.png
