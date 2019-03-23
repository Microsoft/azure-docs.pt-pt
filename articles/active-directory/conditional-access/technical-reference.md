---
title: Referência de definições de acesso condicional do Azure Active Directory | Documentos da Microsoft
description: Obtenha uma visão geral das definições suportadas em políticas de acesso condicional do Azure Active Directory.
services: active-directory.
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/22/2019
ms.author: markvi
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a303319a3f87f684b72bcddd8d30cd4a2c60642
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351529"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Referência de definições de acesso condicional do Azure Active Directory

Pode usar [acesso condicional do Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) para controlar os utilizadores autorizados como pode aceder aos seus recursos.   

Este artigo fornece informações de suporte para as seguintes opções de configuração de uma política de acesso condicional: 

- Atribuições de aplicações na cloud

- Condição de plataforma do dispositivo 

- Condição de aplicativos de cliente

- Requisito da aplicação aprovada do cliente


Se não se trata de informações que procura, deixe um comentário no final deste artigo.

## <a name="cloud-apps-assignments"></a>Atribuições de aplicações na cloud

Com as políticas de acesso condicional, controlar a forma como os utilizadores aceder aos seus [aplicações na cloud](conditions.md#cloud-apps). Ao configurar uma política de acesso condicional, tem de selecionar pelo menos uma aplicação da cloud. 

![Selecione as aplicações na cloud para a sua política](./media/technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Aplicações de cloud da Microsoft

Pode atribuir uma política de acesso condicional para as seguintes aplicações de cloud da Microsoft:


- Azure Analysis Services
- DevOps do Azure
- Base de dados SQL do Azure e de armazém de dados - [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- Microsoft Azure Information Protection – [Saiba mais](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Gestão do Microsoft Azure - [Saiba mais](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Microsoft Azure RemoteApp
- Gestão de subscrições do Microsoft Azure
- Microsoft Cloud App Security
- Portal de controlo de acesso de ferramentas do Microsoft Commerce
- Serviço de autenticação de ferramentas do Microsoft Commerce
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Inscrição do Microsoft Intune
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerApps
- Pesquisa da Microsoft no Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams 
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Yammer do Office 365
- Office Delve
- Office Sway 
- Grupos do Outlook
- Project Online
- Skype para Empresas Online
- Rede privada virtual (VPN)
- Visual Studio App Center
- Windows Defender ATP




### <a name="other-applications"></a>Outras aplicações 

Além das aplicações de cloud da Microsoft, pode atribuir uma política de acesso condicional para os seguintes tipos de aplicações na cloud:

- Aplicações do Azure AD-ligado

- Pré-integrado federado software como um aplicativo de serviço (SaaS)

- Aplicações que utilizam a palavra-passe início de sessão único (SSO)

- Aplicações de linha de negócio

- Aplicativos que usam o Proxy de aplicações do Azure AD


## <a name="device-platform-condition"></a>Condição de plataforma do dispositivo

Numa política de acesso condicional, pode configurar a condição de plataforma de dispositivo a associar a política para o sistema operativo num cliente. Acesso condicional do Azure AD suporta as seguintes plataformas de dispositivo:

- Android

- iOS

- Windows Phone

- Windows

- macOS


![Associar a política de acesso para o SO de cliente](./media/technical-reference/41.png)





## <a name="client-apps-condition"></a>Condição de aplicações de cliente 

Na sua política de acesso condicional, pode configurar o [aplicações de cliente](conditions.md#client-apps) condição para associar a política para a aplicação de cliente que iniciou uma tentativa de acesso. Defina o cliente a condição de aplicações para conceder ou bloquear o acesso quando é efetuada uma tentativa de acesso a partir os seguintes tipos de aplicações de cliente:

- Browser
- Aplicações móveis e aplicativos de desktop

![Controlar o acesso para aplicações de cliente](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Browsers suportados 

Na sua política de acesso condicional, pode selecionar **navegadores** como aplicação de cliente.

![Controlar o acesso para browsers suportados](./media/technical-reference/05.png)

Esta definição funciona com todos os navegadores. No entanto, para satisfazer uma política de dispositivo, como um requisito de dispositivo em conformidade, os seguintes sistemas operativos e browsers são suportados:


| SO                     | Browsers                                      |
| :--                    | :--                                           |
| Windows 10             | Internet Explorer, Microsoft Edge, Chrome     |
| Windows 8 / 8.1        | Internet Explorer, Chrome                     |
| Windows 7              | Internet Explorer, Chrome                     |
| iOS                    | Safari, Browser gerido do Intune                |
| Android                | Chrome, o Intune Managed Browser                |
| Windows Phone          | Internet Explorer, Microsoft Edge             |
| Windows Server 2016    | Internet Explorer, Microsoft Edge             |
| Windows Server 2016    | Chrome                                        |
| Windows Server 2012 R2 | Internet Explorer, Chrome                     |
| Windows Server 2008 R2 | Internet Explorer, Chrome                     |
| macOS                  | Chrome, o Safari                                |
 


#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Por que vejo um certificado de linha de comandos no navegador

No Windows 7, iOS, Android e macOS do Azure AD identifica o dispositivo com um certificado de cliente que está aprovisionado quando o dispositivo está registado com o Azure AD.  Quando um utilizador inicia pela primeira vez por meio do navegador é pedido ao utilizador para selecionar o certificado. O utilizador tem de selecionar este certificado antes de utilizar o browser.


#### <a name="chrome-support"></a>Suporte do Chrome

Para o Chrome suporte no **(versão 1703) do Windows 10 Creators Update** ou posterior, instale [esta extensão](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

Para implementar automaticamente esta extensão para navegadores de Chrome, crie a seguinte chave de registo:

|    |    |
|--- | ---|
|Caminho | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
|Name | 1 |
|Type | REG_SZ (String) |
|Dados | ppnbnpeolgkicgegkbkbjmhlideopiji; https://clients2.google.com/service/update2/crx

Para o Chrome suporte no **Windows 8.1 e 7**, crie a seguinte chave de registo:

|    |    |
|--- | ---|
|Caminho | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
|Name | 1 |
|Type | REG_SZ (String) |
|Dados | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}}|

Esses navegadores suportam a autenticação do dispositivo, permitindo ao dispositivo ser identificados e validadas em relação uma política. A verificação de dispositivo falha se o browser está em execução no modo privado. 


### <a name="supported-mobile-applications-and-desktop-clients"></a>Suporte a aplicativos móveis e clientes de ambiente de trabalho

Na sua política de acesso condicional, pode selecionar **aplicações móveis e clientes de ambiente de trabalho** como aplicação de cliente.


![Controlar o acesso para aplicações móveis suportadas ou clientes de ambiente de trabalho](./media/technical-reference/06.png)


Esta definição não tem um impacto em tentativas de acesso do seguintes de aplicações móveis e clientes de ambiente de trabalho: 


| Aplicações do cliente| Serviço de destino| Plataforma |
| --- | --- | --- |
| O Azure RemoteApp| Serviço do Azure RemoteApp| Windows 10, Windows 8.1, Windows 7, iOS, Android e macOS|
| Aplicação do Dynamics CRM| Dynamics CRM| Windows 10, Windows 8.1, iOS e Android|
| Aplicação de correio/calendário/pessoas, 2016 do Outlook, Outlook 2013 (com autenticação moderna)| Office 365 Exchange Online| Windows 10|
| Política de MFA e localização para aplicações. Políticas de dispositivo com base não são suportadas.| Qualquer serviço de aplicações as minhas aplicações| Android e iOS|
| Serviços do Microsoft Teams - esse item controla todos os serviços que suportam o Microsoft Teams e todas as suas aplicações de cliente - área de trabalho do Windows, iOS, Android, WP e cliente web| Microsoft Teams| Windows 10, Windows 8.1, Windows 7, iOS, Android e macOS|
| Cliente de sincronização de aplicações do Office 2016, Office 2013 (com autenticação moderna), o OneDrive (consulte [notas](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))| Office 365 SharePoint Online| Windows 8.1, Windows 7|
| Aplicações do Office 2016, as aplicações do Universal Office, Office 2013 (com autenticação moderna), o cliente de sincronização do OneDrive (consulte [notas](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), o suporte de grupos do Office está previsto para o futuro, o suporte de aplicações do SharePoint está previsto para o futuro| Office 365 SharePoint Online| Windows 10|
| 2016 do Office (Word, apenas o Excel, PowerPoint e OneNote). OneDrive para suporte de negócio planejado para o futuro| Office 365 SharePoint Online| macOS|
| Office 2019| Office 365 SharePoint Online| Windows 10, macOS|
| Aplicações móveis do Office| Office 365 SharePoint Online| Android, iOS|
| Aplicação do Office Yammer| Yammer do Office 365| Windows 10, iOS, Android|
| Outlook 2019| Office 365 SharePoint Online| Windows 10, macOS|
| Outlook 2016 (Office para macOS)| Office 365 Exchange Online| macOS|
| Outlook 2016, Outlook 2013 (com autenticação moderna), o Skype para empresas (com autenticação moderna)| Office 365 Exchange Online| Windows 8.1, Windows 7|
| Aplicação móvel do Outlook| Office 365 Exchange Online| Android, iOS|
| Aplicação do Power BI| Serviço Power BI| Windows 10, Windows 8.1, Windows 7, Android e iOS|
| Skype para Empresas| Office 365 Exchange Online| Android, IOS|
| Aplicação de serviços da Equipe do Visual Studio| Visual Studio Team Services| Windows 10, Windows 8.1, Windows 7, iOS e Android|



## <a name="support-for-legacy-authentication"></a>Suporte para a autenticação de legado

Selecionando **outros clientes**, pode especificar uma condição que afeta as aplicações que utilizam autenticação básica com protocolos de email, como o IMAP, MAPI, POP, SMTP e aplicações do Office mais antigas que não utilizam autenticação moderna.  

![Outros clientes](./media/technical-reference/11.png)

Para obter mais informações, consulte [aplicações de cliente](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Requisito de aplicação aprovada do cliente 

Na sua política de acesso condicional, pode exigir que um acesso tentar as aplicações na cloud selecionada tem de ser feitas a partir de uma aplicação aprovada do cliente. 

![Controlar o acesso para aplicações aprovadas do cliente](./media/technical-reference/21.png)

Esta definição aplica-se para as seguintes aplicações de cliente:



- Microsoft Azure Information Protection
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala 
- Microsoft Launcher
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft PowerBI
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

- As aplicações aprovadas do cliente suportam a funcionalidade de gestão de aplicações móveis do Intune.

- O **requer aplicação aprovada do cliente** requisito:

    - Só suporta o iOS e Android para [condição de plataforma de dispositivo](#device-platform-condition).


## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral do acesso condicional, consulte [o que é o acesso condicional no Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
- Se estiver pronto para configurar políticas de acesso condicional no seu ambiente, veja a [práticas para o acesso condicional no Azure Active Directory recomendadas](best-practices.md).



<!--Image references-->
[1]: ./media/technical-reference/01.png


