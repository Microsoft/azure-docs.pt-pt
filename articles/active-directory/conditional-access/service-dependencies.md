---
title: Quais são as dependências do serviço no Azure Active Directory condicional acesso? | Microsoft Docs
description: Saiba como as condições são usadas no Azure Active Directory condicional acesso para acionar uma política.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9aca2e4ea5e107358ff72e83562057830ece2cc
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509347"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Quais são as dependências do serviço no Azure Active Directory condicional acesso? 

Com as políticas de acesso condicional, pode especificar requisitos de acesso aos Web sites e serviços. Por exemplo, os seus requisitos de acesso podem incluir a exigir autenticação multifator (MFA) ou [dispositivos geridos](require-managed-devices.md). 

Quando acessa um site ou serviço diretamente, o impacto de uma diretiva relacionada é normalmente fácil avaliar. Por exemplo, se tiver uma política que requer a MFA para o SharePoint Online configurado, MFA é imposta para cada início de sessão para o portal web do SharePoint. No entanto, nem sempre é direta para avaliar o impacto de uma política, uma vez que existem aplicações na cloud com as dependências para outras aplicações na cloud. Por exemplo, Microsoft Teams pode fornecer acesso a recursos no SharePoint Online. Assim, quando acessar o Microsoft Teams no nosso cenário atual, está também sujeita à política de MFA de SharePoint.   

## <a name="policy-enforcement"></a>Aplicação da política 

Se tiver uma dependência de serviço configurada, a política pode ser aplicada imposição antecipadas ou ligação tardia. 

- **Imposição de políticas de antecipadas** significa que um utilizador tem de cumprir a política de serviço dependentes antes de aceder à aplicação da chamada. Por exemplo, um utilizador deve cumprir a política do SharePoint antes de iniciar sessão no MS Teams. 
- **Imposição de políticas de ligação tardia** ocorre depois do utilizador inicia sessão na aplicação da chamada. Imposição é adiada para ao chamar pedidos de aplicação, um token para o serviço downstream. Os exemplos incluem o MS Teams aceder Planner e Office.com aceder ao SharePoint. 

O diagrama abaixo ilustra as dependências de serviço MS Teams. Sólido setas indicam antecipadas imposição a seta tracejada para Planner indica a imposição de ligação tardia. 

![Dependências de serviço MS Teams](./media/service-dependencies/01.png)

Como melhor prática, deve definir políticas comuns em aplicações relacionadas e serviços sempre que possível. Ter uma postura de segurança consistente fornece a melhor experiência de utilizador. Por exemplo, definir uma política comuns no Exchange Online, SharePoint Online, Microsoft Teams e Skype para empresas significativamente reduz inesperados de pedidos que podem surgir na políticas diferentes a serem aplicadas aos serviços downstream. 

A tabela a seguir apresenta uma lista de dependências de serviço adicionais, onde tem de cumprir as aplicações de cliente  

| Aplicações de cliente         | Serviço Downstream                          | Imposição |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Gestão do Microsoft Azure (portal e a API) | Antecipadas |
| Microsoft Classroom | Exchange                                    | Antecipadas |
|                     | SharePoint                                  | Antecipadas  |
| Microsoft Teams     | Exchange                                    | Antecipadas |
|                     | Planeador de implementações do MS                                  | Ligação tardia  |
|                     | SharePoint                                  | Antecipadas |
|                     | Skype para Empresas Online                   | Antecipadas |
| Portal do Office       | Exchange                                    | Ligação tardia  |
|                     | SharePoint                                  | Ligação tardia  |
| Grupos do Outlook      | Exchange                                    | Antecipadas |
|                     | SharePoint                                  | Antecipadas |
| PowerApps           | Gestão do Microsoft Azure (portal e a API) | Antecipadas |
|                     | Windows Azure Active Directory              | Antecipadas |
| Project             | Dynamics CRM                                | Antecipadas |
| Skype para Empresas  | Exchange                                    | Antecipadas |
| Visual Studio       | Gestão do Microsoft Azure (portal e a API) | Antecipadas |

## <a name="next-steps"></a>Passos Seguintes

Para saber como implementar o acesso condicional no seu ambiente, consulte [planear a implementação de acesso condicional no Azure Active Directory](plan-conditional-access.md).
