---
title: Quais são as dependências do serviço de acesso condicional do Azure Active Directory? | Microsoft Docs
description: Saiba como as condições são usadas no acesso condicional do Azure Active Directory para acionar uma política.
services: active-directory
keywords: acesso condicional a aplicações, acesso condicional com o Azure AD, acesso seguro a recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/14/2019
ms.author: markvi
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e871c241c92fbdac00223df94dde203745b8edae
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58166105"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Quais são as dependências do serviço de acesso condicional do Azure Active Directory? 


Com as políticas de acesso condicional, pode especificar requisitos de acesso aos Web sites e serviços. Por exemplo, os seus requisitos de acesso podem incluir a exigir autenticação multifator (MFA) ou [dispositivos geridos](require-managed-devices.md). 


Quando acessa um site ou serviço diretamente, o impacto de uma diretiva relacionada é normalmente fácil avaliar. Por exemplo, se tiver uma política que requer a MFA para o SharePoint Online configurado, MFA é imposta para cada início de sessão para o portal web do SharePoint. No entanto, nem sempre é direta para avaliar o impacto de uma política, uma vez que existem aplicações na cloud com as dependências para outras aplicações na cloud. Por exemplo, Microsoft Teams tira partido do SharePoint online. Assim, quando acessar o Microsoft Teams no nosso cenário atual, está também sujeita à política de MFA de SharePoint.   


## <a name="policy-enforcement"></a>Aplicação da política 

Se tiver uma dependência de serviço configurada, a política pode ser aplicada imposição antecipadas ou ligação tardia. 

**Imposição de políticas de antecipadas** significa que um utilizador tem de cumprir a política de serviço dependentes antes de aceder à aplicação da chamada. Por exemplo, um utilizador deve cumprir a política do SharePoint antes de iniciar sessão no MS Teams. 

**Imposição de políticas de ligação tardia** ocorre depois do utilizador inicia sessão na aplicação da chamada. Imposição é adiada para ao chamar pedidos de aplicação, um token para o serviço downstream. Os exemplos incluem o MS Teams aceder Planner e Office.com aceder ao SharePoint. 

O diagrama abaixo ilustra as dependências de serviço MS Teams. Sólido setas indicam antecipadas imposição a seta tracejada para Planner indica a imposição de ligação tardia. 



![Dependências de serviço MS Teams](./media/service-dependencies/01.png)



  

Como melhor prática, deve definir políticas comuns em aplicações relacionadas e serviços sempre que possível. Ter uma postura de segurança consistente fornece a melhor experiência de utilizador. Por exemplo, definir uma política comuns entre o Exchange Online, SharePoint Online, o MS Teams e Skype para empresas reduz significativamente inesperados de pedidos que podem surgir na políticas diferentes a serem aplicadas aos serviços downstream. 

A tabela a seguir apresenta uma lista de dependências de serviço adicionais, onde tem de cumprir as aplicações de cliente  

| Aplicações do cliente         | Serviço Downstream                          | Imposição |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Gestão do Microsoft Azure (portal e a API) | Antecipadas |
| Microsoft Classroom | Troca                                    | Antecipadas |
|                     | SharePoint                                  | Ligação tardia  |
| Microsoft Teams     | Troca                                    | Antecipadas |
|                     | Planeador de implementações do MS                                  | Ligação tardia  |
|                     | SharePoint                                  | Antecipadas |
|                     | Skype para Empresas Online                   | Antecipadas |
| Portal do Office       | Troca                                    | Ligação tardia  |
|                     | SharePoint                                  | Ligação tardia  |
| Grupos do Outlook      | Troca                                    | Antecipadas |
|                     | SharePoint                                  | Antecipadas |
| PowerApps           | Gestão do Microsoft Azure (portal e a API) | Antecipadas |
|                     | Windows Azure Active Directory              | Antecipadas |
| Project             | Dynamics CRM                                | Antecipadas |
| Skype para Empresas  | Troca                                    | Antecipadas |
| Visual Studio       | Gestão do Microsoft Azure (portal e a API) | Antecipadas |



## <a name="next-steps"></a>Passos Seguintes

Para saber como implementar o acesso condicional no seu ambiente, consulte [planear a implementação de acesso condicional no Azure Active Directory](plan-conditional-access.md).
