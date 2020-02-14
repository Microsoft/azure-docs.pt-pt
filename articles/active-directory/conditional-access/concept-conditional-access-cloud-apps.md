---
title: Aplicativos ou ações em nuvem na política de acesso condicional - Diretório Ativo Azure
description: O que são aplicações ou ações em nuvem numa política de acesso condicional Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9d2780e09c099d76aa2ef4ec2638a410793481d
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186393"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Acesso Condicional: Aplicações ou ações em nuvem

As aplicações ou ações em nuvem são um sinal chave numa política de Acesso Condicional. As políticas de acesso condicional permitem que os administradores atribuam controles a aplicativos ou ações específicas.

- Os administradores podem escolher entre a lista de aplicações que incluem aplicações incorporadas da Microsoft e quaisquer [aplicações integradas da Azure AD,](../manage-apps/what-is-application-management.md) incluindo galeria, não galeria e aplicações publicadas através do [Application Proxy.](../manage-apps/what-is-application-proxy.md)
- Os administradores podem optar por definir a política não com base em um aplicativo de nuvem, mas em uma ação do usuário. A única ação apoiada é registar informações de segurança (pré-visualização), permitindo o Acesso Condicional para impor controlos em torno da experiência combinada de registo de informações de [segurança.](../authentication/howto-registration-mfa-sspr-combined.md)

![Definir uma política de acesso condicional e especificar aplicativos de nuvem](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Aplicações na nuvem da Microsoft

Muitos dos aplicativos de nuvem da Microsoft existentes estão incluídos na lista de aplicativos que você pode selecionar. 

Os administradores podem atribuir uma política de acesso condicional aos seguintes aplicativos de nuvem da Microsoft. Alguns aplicativos como o Office 365 (visualização) e o gerenciamento de Microsoft Azure incluem vários aplicativos ou serviços filho relacionados. A lista a seguir não é exaustiva e está sujeita a alterações.

- [Escritório 365 (pré-visualização)](#office-365-preview)
- Azure Analysis Services
- DevOps do Azure
- [Base de Dados EArmazém de Dados Azure SQL](../../sql-database/sql-database-conditional-access.md)
- Dinâmica CRM Online
- Microsoft Application Insights Analytics
- [Proteção de Informação do Microsoft Azure](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Gestão Microsoft Azure](#microsoft-azure-management)
- Gestão de Subscrição do Microsoft Azure
- Microsoft Cloud App Security
- Portal de Controlo de Acesso de Ferramentas de Comércio da Microsoft
- Serviço de Autenticação de Ferramentas de Comércio microsoft
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Inscrição intune da Microsoft](https://docs.microsoft.com/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Pesquisa do Microsoft em Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Escritório 365 Exchange Online
- Office 365 SharePoint Online
- Escritório 365 Yammer
- Office Delve
- Poder de escritório
- Grupos do Outlook
- Serviço Power BI
- Project Online
- Skype para Empresas Online
- Rede Privada Virtual (VPN)
- Windows Defender ATP

### <a name="office-365-preview"></a>Escritório 365 (pré-visualização)

O Office 365 fornece serviços de produtividade e colaboração baseados na nuvem, como Exchange, SharePoint e Microsoft Teams. Os serviços de nuvem do Office 365 estão profundamente integrados para garantir experiências suaves e colaborativas. Essa integração pode causar confusão ao criar políticas, já que alguns aplicativos, como o Microsoft Teams, têm dependências em outras pessoas, como o SharePoint ou o Exchange.

A aplicação Office 365 (pré-visualização) permite direcionar estes serviços de uma só vez. É recomendável usar o novo aplicativo Office 365 (versão prévia), em vez de direcionar aplicativos de nuvem individuais. Direcionar esse grupo de aplicativos ajuda a evitar problemas que podem surgir devido a políticas e dependências inconsistentes.

Os administradores podem optar por excluir aplicativos específicos da política se quiserem incluindo o aplicativo Office 365 (versão prévia) e excluindo os aplicativos específicos de sua escolha na política.

Aplicações-chave que estão incluídas na aplicação de clientes office 365 (pré-visualização):

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Escritório 365 Exchange Online
   - Office 365 SharePoint Online
   - Serviço de Pesquisa do Office 365
   - Escritório 365 Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype para Empresas Online
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure Management

O aplicativo de gerenciamento de Microsoft Azure inclui vários serviços subjacentes. 

   - Portal do Azure
   - Provedor de Azure Resource Manager
   - APIs do modelo de implantação clássica
   - Azure PowerShell
   - Portal do administrador de assinaturas do Visual Studio
   - DevOps do Azure
   - Portal de Azure Data Factory

> [!NOTE]
> O aplicativo de gerenciamento de Microsoft Azure aplica-se a Azure PowerShell, que chama a API Azure Resource Manager. Ele não se aplica ao Azure AD PowerShell, que chama Microsoft Graph.

## <a name="other-applications"></a>Outras aplicações

Além dos aplicativos da Microsoft, os administradores podem adicionar qualquer aplicativo registrado do Azure AD às políticas de acesso condicional. Esses aplicativos podem incluir: 

- Aplicações publicadas através do [Procurador de Aplicação da AD Azure](../manage-apps/what-is-application-proxy.md)
- [Candidaturas adicionadas a partir da galeria](../manage-apps/add-application-portal.md)
- [Aplicações personalizadas não na galeria](../manage-apps/add-non-gallery-app.md)
- [Aplicações antigas publicadas através de controladores e redes de entrega de aplicações](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>Ações do utilizador

As ações do usuário são tarefas que podem ser executadas por um usuário. A única ação atualmente suportada é o Registo de informações de **segurança (pré-visualização),** que permite que a política de Acesso Condicional aplique quando os utilizadores que estão habilitados para uma tentativa de registo combinado de registo de informações de segurança. Mais informações podem ser encontradas no artigo, Registo combinado de informações de [segurança (pré-visualização)](../authentication/concept-registration-mfa-sspr-combined.md).

## <a name="next-steps"></a>Passos seguintes

- [Acesso Condicional: Condições](concept-conditional-access-conditions.md)

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)
- [Dependências de aplicação de clientes](service-dependencies.md)
