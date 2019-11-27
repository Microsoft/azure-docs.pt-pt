---
title: Dependências do serviço de acesso condicional-Azure Active Directory
description: Saiba como as condições são usadas em Azure Active Directory acesso condicional para disparar uma política.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b39238575c05d35a2d87999e08c49c0c77e99bfb
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380015"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>O que são dependências de serviço no Azure Active Directory acesso condicional? 

Com as políticas de acesso condicional, você pode especificar os requisitos de acesso para sites e serviços. Por exemplo, seus requisitos de acesso podem incluir a exigência de autenticação multifator (MFA) ou de [dispositivos gerenciados](require-managed-devices.md). 

Quando você acessa um site ou serviço diretamente, o impacto de uma política relacionada normalmente é fácil de avaliar. Por exemplo, se você tiver uma política que requer MFA para SharePoint Online configurado, a MFA será imposta para cada entrada no portal da Web do SharePoint. No entanto, nem sempre é um avanço direto para avaliar o impacto de uma política, pois há aplicativos de nuvem com dependências para outros aplicativos de nuvem. Por exemplo, o Microsoft Teams pode fornecer acesso a recursos no SharePoint Online. Portanto, ao acessar o Microsoft Teams em nosso cenário atual, você também está sujeito à política do SharePoint MFA.   

## <a name="policy-enforcement"></a>Aplicação da política 

Se você tiver uma dependência de serviço configurada, a política poderá ser aplicada usando a aplicação de ligação antecipada ou de associação tardia. 

- A **imposição de diretiva de ligação antecipada** significa que um usuário deve satisfazer a política de serviço dependente antes de acessar o aplicativo de chamada. Por exemplo, um usuário deve satisfazer a política do SharePoint antes de entrar no MS Teams. 
- A **imposição de política de ligação tardia** ocorre depois que o usuário entra no aplicativo de chamada. A imposição é adiada ao chamar solicitações de aplicativo, um token para o serviço downstream. Exemplos incluem MS Teams acessando o Planner e o Office.com acessando o SharePoint. 

O diagrama a seguir ilustra as dependências do MS Teams Service. Setas sólidas indicam a imposição de ligação inicial a seta tracejada para o planejador indica a imposição de associação tardia. 

![Dependências de serviço do MS Teams](./media/service-dependencies/01.png)

Como prática recomendada, você deve definir políticas comuns entre aplicativos e serviços relacionados sempre que possível. Ter uma postura de segurança consistente fornece a melhor experiência do usuário. Por exemplo, a definição de uma política comum no Exchange Online, no SharePoint Online, no Microsoft Teams e no Skype for Business reduz significativamente as solicitações inesperadas que podem surgir de políticas diferentes sendo aplicadas aos serviços downstream. 

A tabela abaixo lista as dependências de serviço adicionais, onde os aplicativos cliente devem satisfazer  

| Aplicativos cliente         | Serviço downstream                          | Ativação |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Gerenciamento de Microsoft Azure (portal e API) | Associação antecipada |
| Sala de aula da Microsoft | Troca                                    | Associação antecipada |
|                     | SharePoint                                  | Associação antecipada |
| Microsoft Teams     | Troca                                    | Associação antecipada |
|                     | Planejador MS                                  | Associação tardia  |
|                     | SharePoint                                  | Associação antecipada |
|                     | Skype para Empresas Online                   | Associação antecipada |
| Portal do Office       | Troca                                    | Associação tardia  |
|                     | SharePoint                                  | Associação tardia  |
| Grupos do Outlook      | Troca                                    | Associação antecipada |
|                     | SharePoint                                  | Associação antecipada |
| PowerApps           | Gerenciamento de Microsoft Azure (portal e API) | Associação antecipada |
|                     | Azure Active Directory do Windows              | Associação antecipada |
| Project             | Dynamics CRM                                | Associação antecipada |
| Skype para Empresas  | Troca                                    | Associação antecipada |
| Visual Studio       | Gerenciamento de Microsoft Azure (portal e API) | Associação antecipada |
| Microsoft Forms     | Troca                                    | Associação antecipada |
|                     | SharePoint                                  | Associação antecipada |
| Microsoft To-Do     | Troca                                    | Associação antecipada |

## <a name="next-steps"></a>Passos seguintes

Para saber como implementar o acesso condicional em seu ambiente, consulte [planejar sua implantação de acesso condicional no Azure Active Directory](plan-conditional-access.md).
