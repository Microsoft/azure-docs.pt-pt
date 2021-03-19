---
title: Aplicativos em nuvem ou ações na política de acesso condicional - Azure Ative Directory
description: O que são aplicativos em nuvem ou ações em uma política de acesso condicional AD Azure
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ff9fe969c90a2bb4aa9f954b984f511fb490ba3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579149"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Acesso Condicional: aplicativos ou ações na nuvem

Aplicações ou ações na nuvem são um sinal chave numa política de Acesso Condicional. As políticas de acesso condicional permitem aos administradores atribuir controlos a aplicações ou ações específicas.

- Os administradores podem escolher entre a lista de aplicações que incluem aplicações integradas da Microsoft e quaisquer [aplicações integradas AZURE AD,](../manage-apps/what-is-application-management.md) incluindo galeria, não-galeria, e aplicações publicadas através do [Application Proxy](../manage-apps/what-is-application-proxy.md).
- Os administradores podem optar por definir a política não com base numa aplicação em nuvem, mas numa ação do utilizador. A única ação suportada é registar informações de segurança (pré-visualização), permitindo o Acesso Condicional para impor controlos em torno da [experiência de registo de informações de segurança combinadas.](../authentication/howto-registration-mfa-sspr-combined.md)

![Defina uma política de acesso condicional e especifique aplicações na nuvem](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Aplicações em nuvem da Microsoft

Muitas das aplicações em nuvem da Microsoft existentes estão incluídas na lista de aplicações a partir das quais pode selecionar. 

Os administradores podem atribuir uma política de acesso condicional às seguintes aplicações na nuvem da Microsoft. Algumas aplicações como o Office 365 e o Microsoft Azure Management incluem várias aplicações ou serviços relacionados com crianças. A seguinte lista não é exaustiva e está sujeita a alterações.

- [Office 365](#office-365)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database e Azure Synapse Analytics](../../azure-sql/database/conditional-access-configure.md)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure Management](#microsoft-azure-management)
- Microsoft Azure Gestão de Subscrições
- Microsoft Cloud App Security
- Microsoft Commerce Tools Access Control Portal
- Serviço de autenticação de ferramentas de comércio do Microsoft
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Inscrição microsoft Intune](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Pesquisa da Microsoft em Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Stream
- Exchange Online
- SharePoint
- Yammer
- Office Delve
- Balanço de Escritório
- Grupos do Outlook
- Serviço Power BI
- Project Online
- Skype para Empresas Online
- Rede Privada Virtual (VPN)
- Windows Defender ATP

### <a name="office-365"></a>Office 365

O Microsoft 365 fornece serviços de produtividade e colaboração baseados na nuvem, como Exchange, SharePoint e Microsoft Teams. Os serviços em nuvem da Microsoft 365 estão profundamente integrados para garantir experiências suaves e colaborativas. Esta integração pode causar confusão ao criar políticas, uma vez que algumas aplicações como as Microsoft Teams têm dependências de outras, como o SharePoint ou o Exchange.

A aplicação Office 365 permite direcionar estes serviços de uma só vez. Recomendamos a utilização da nova aplicação Office 365, em vez de direcionar as aplicações individuais para a nuvem para evitar problemas com [dependências de serviços.](service-dependencies.md) Direcionar este grupo de aplicações ajuda a evitar problemas que possam surgir devido a políticas e dependências inconsistentes.

Os administradores podem optar por excluir aplicações específicas da política se assim o desejarem, incluindo a aplicação do Office 365 e excluindo as aplicações específicas da sua escolha na política.

Aplicações-chave incluídas na aplicação do cliente Office 365:

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Stream
   - Exchange Online
   - SharePoint Online
   - Serviço de Pesquisa Microsoft 365
   - Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype para Empresas Online
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure Management

A aplicação Microsoft Azure Management inclui vários serviços subjacentes. 

   - Portal do Azure
   - Fornecedor de Gestor de Recursos Azure
   - APIs modelo de implementação clássico
   - Azure PowerShell
   - CLI do Azure
   - Portal de administrador de subscrições de Estúdio Visual
   - Azure DevOps
   - Portal Azure Data Factory

> [!NOTE]
> A aplicação Microsoft Azure Management aplica-se ao Azure PowerShell, que chama a API do Gestor de Recursos Azure. Não se aplica ao Azure AD PowerShell, que chama Microsoft Graph.

## <a name="other-applications"></a>Outras aplicações

Além das aplicações da Microsoft, os administradores podem adicionar qualquer aplicação registada em Azure AD às políticas de Acesso Condicional. Estas aplicações podem incluir: 

- Candidaturas publicadas através do [Azure AD Application Proxy](../manage-apps/what-is-application-proxy.md)
- [Candidaturas adicionadas da galeria](../manage-apps/add-application-portal.md)
- [Aplicações personalizadas não na galeria](../manage-apps/view-applications-portal.md)
- [Aplicações legacy publicadas através de controladores e redes de entrega de aplicações](../manage-apps/secure-hybrid-access.md)
- Aplicações que usam [palavra-passe com base num único sign-on](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

> [!NOTE]
> Uma vez que a política de Acesso Condicional define os requisitos para aceder a um serviço, não é possível aplicá-lo a uma aplicação de cliente (público/nativo). Outras palavras, a apólice não é definida diretamente numa aplicação de cliente (público/nativo), mas é aplicada quando um cliente chama um serviço. Por exemplo, uma política definida no serviço SharePoint aplica-se aos clientes que ligam para o SharePoint. Uma política definida no Exchange aplica-se à tentativa de aceder ao e-mail utilizando o cliente Outlook. É por isso que as aplicações de cliente (público/nativo) não estão disponíveis para seleção na opção Cloud Apps picker e Conditional Access não está disponível nas configurações de aplicação para o cliente (público/nativo) registada no seu inquilino. 

## <a name="user-actions"></a>Ações do utilizador

As ações do utilizador são tarefas que podem ser executadas por um utilizador. Atualmente, o Acesso Condicional suporta duas ações do utilizador: 

- **Informações de segurança do registo**: Esta ação do utilizador permite que a política de acesso condicional aplique quando os utilizadores habilitados para o registo combinado tentam registar as suas informações de segurança. Mais informações podem ser encontradas no artigo, [registo combinado de informações de segurança.](../authentication/concept-registration-mfa-sspr-combined.md)

- **Registar ou juntar dispositivos (pré-visualização)**: Esta ação do utilizador permite aos administradores impor a política de Acesso Condicional quando os utilizadores [se registam](../devices/concept-azure-ad-register.md) ou [se juntam](../devices/concept-azure-ad-join.md) a dispositivos para a Azure AD. Existem duas considerações fundamentais com esta ação do utilizador: 
   - `Require multi-factor authentication` é o único controlo de acesso disponível com esta ação do utilizador e todos os outros estão desativados. Esta restrição impede conflitos com controlos de acesso que dependem do registo do dispositivo Azure ou não são aplicáveis ao registo do dispositivo Azure AD. 
   - Quando uma política de acesso condicional estiver ativada com esta ação do utilizador, deve definir as definições do dispositivo de **diretório ativo Azure**  >    >    -  `Devices to be Azure AD joined or Azure AD registered require Multi-Factor Authentication` para **nº**. Caso contrário, a política de acesso condicional com esta ação do utilizador não é devidamente aplicada. Mais informações sobre esta configuração do dispositivo podem ser encontradas nas [definições do dispositivo Configure](../devices/device-management-azure-portal.md#configure-device-settings). Esta ação do utilizador proporciona flexibilidade para exigir a autenticação de vários fatores para registar ou juntar dispositivos para utilizadores e grupos ou condições específicos em vez de ter uma política de todo o inquilino nas definições do Dispositivo. 
   
## <a name="next-steps"></a>Passos seguintes

- [Acesso Condicional: Condições](concept-conditional-access-conditions.md)

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)
- [Dependências de aplicações de clientes](service-dependencies.md)
