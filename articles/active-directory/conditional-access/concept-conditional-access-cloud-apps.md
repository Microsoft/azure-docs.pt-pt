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
ms.openlocfilehash: 69bdd2d6825427597e9030a03aae7d219361ba25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671936"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Acesso Condicional: Aplicações ou ações em nuvem

As aplicações ou ações em nuvem são um sinal chave numa política de Acesso Condicional. As políticas de Acesso Condicional permitem que os administradores atribuam controlos a aplicações ou ações específicas.

- Os administradores podem escolher entre a lista de aplicações que incluem aplicações incorporadas da Microsoft e quaisquer [aplicações integradas da Azure AD,](../manage-apps/what-is-application-management.md) incluindo galeria, não galeria e aplicações publicadas através do [Application Proxy.](../manage-apps/what-is-application-proxy.md)
- Os administradores podem optar por definir a política não com base numa aplicação na nuvem, mas numa ação do utilizador. A única ação apoiada é registar informações de segurança (pré-visualização), permitindo o Acesso Condicional para impor controlos em torno da experiência combinada de registo de informações de [segurança.](../authentication/howto-registration-mfa-sspr-combined.md)

![Defina uma política de acesso condicional e especifique aplicações na nuvem](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Aplicações na nuvem da Microsoft

Muitas das aplicações em nuvem da Microsoft existentes estão incluídas na lista de aplicações que pode selecionar. 

Os administradores podem atribuir uma política de Acesso Condicional às seguintes aplicações na nuvem da Microsoft. Algumas aplicações como o Office 365 (pré-visualização) e a Microsoft Azure Management incluem várias aplicações ou serviços relacionados com crianças. A lista que se segue não é exaustiva e está sujeita a alterações.

- [Escritório 365 (pré-visualização)](#office-365-preview)
- Azure Analysis Services
- DevOps do Azure
- [Base de Dados SQL do Azure e Azure SQL Data Warehouse](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- [Proteção de Informação do Microsoft Azure](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Gestão Microsoft Azure](#microsoft-azure-management)
- Gestão de Subscrição do Microsoft Azure
- Microsoft Cloud App Security
- Portal de Controlo de Acesso de Ferramentas de Comércio da Microsoft
- Serviço de Autenticação de Ferramentas de Comércio microsoft
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Inscrição intune da Microsoft](/intune/enrollment/multi-factor-authentication)
- Planejador da Microsoft
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
- Power BI Service (Serviço Power BI)
- Project Online
- Skype para Empresas Online
- Rede Privada Virtual (VPN)
- Windows Defender ATP

### <a name="office-365-preview"></a>Escritório 365 (pré-visualização)

O Office 365 fornece serviços de produtividade e colaboração baseados na nuvem, como Exchange, SharePoint e Microsoft Teams. Os serviços de nuvem do Office 365 estão profundamente integrados para garantir experiências suaves e colaborativas. Esta integração pode causar confusão ao criar políticas, uma vez que algumas aplicações como as Microsoft Teams têm dependências de outras, como o SharePoint ou o Exchange.

A aplicação Office 365 (pré-visualização) permite direcionar estes serviços de uma só vez. Recomendamos a utilização da nova aplicação Office 365 (pré-visualização), em vez de direcionar as aplicações individuais para a nuvem. Direcionar este grupo de aplicações ajuda a evitar problemas que possam surgir devido a políticas e dependências inconsistentes.

Os administradores podem optar por excluir aplicações específicas da política, se assim o desejarem, incluindo a aplicação Office 365 (pré-visualização) e excluindo as aplicações específicas da sua escolha na política.

Aplicações-chave que estão incluídas na aplicação de clientes office 365 (pré-visualização):

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Escritório 365 Exchange Online
   - Office 365 SharePoint Online
   - Serviço de Pesquisa do Escritório 365
   - Escritório 365 Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype para Empresas Online
   - Sway

### <a name="microsoft-azure-management"></a>Gestão Microsoft Azure

A aplicação Microsoft Azure Management inclui vários serviços subjacentes. 

   - Portal do Azure
   - Fornecedor de Gestor de Recursos Azure
   - Modelo de implementação clássico APIs
   - Azure PowerShell
   - Portal de administrador de subscrições do Estúdio Visual
   - DevOps do Azure
   - Portal azure data factory

> [!NOTE]
> A aplicação Microsoft Azure Management aplica-se ao Azure PowerShell, que chama a API do Gestor de Recursos Azure. Não se aplica ao Azure AD PowerShell, que chama microsoft graph.

## <a name="other-applications"></a>Outras aplicações

Além das aplicações da Microsoft, os administradores podem adicionar qualquer aplicação registada do Azure AD às políticas de Acesso Condicional. Estas aplicações podem incluir: 

- Aplicações publicadas através do [Procurador de Aplicação da AD Azure](../manage-apps/what-is-application-proxy.md)
- [Candidaturas adicionadas a partir da galeria](../manage-apps/add-application-portal.md)
- [Aplicações personalizadas não na galeria](../manage-apps/add-non-gallery-app.md)
- [Aplicações antigas publicadas através de controladores e redes de entrega de aplicações](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>Ações do utilizador

As ações do utilizador são tarefas que podem ser executadas por um utilizador. A única ação atualmente suportada é o Registo de informações de **segurança (pré-visualização),** que permite que a política de Acesso Condicional aplique quando os utilizadores que estão habilitados para uma tentativa de registo combinado de registo de informações de segurança. Mais informações podem ser encontradas no artigo, Registo combinado de informações de [segurança (pré-visualização)](../authentication/concept-registration-mfa-sspr-combined.md).

## <a name="next-steps"></a>Passos seguintes

- [Acesso Condicional: Condições](concept-conditional-access-conditions.md)

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)
- [Dependências de aplicação de clientes](service-dependencies.md)
