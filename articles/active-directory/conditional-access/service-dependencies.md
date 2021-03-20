---
title: Dependências do serviço de acesso condicional - Azure Ative Directory
description: Saiba como as condições são usadas no Azure Ative Directory Conditional Access para desencadear uma política.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6592add5e33ba240c0f1d9fdbd23d82adfe5229
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91258613"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Quais são as dependências de serviços no Azure Ative Directory Conditional Access? 

Com políticas de Acesso Condicional, pode especificar os requisitos de acesso aos websites e serviços. Por exemplo, os seus requisitos de acesso podem incluir a necessidade de autenticação de vários fatores (MFA) ou [dispositivos geridos](require-managed-devices.md). 

Quando acede diretamente a um site ou serviço, o impacto de uma política relacionada é normalmente fácil de avaliar. Por exemplo, se tiver uma política que exija autenticação de vários fatores (MFA) para o SharePoint Online configurado, o MFA é aplicado para cada súprico no portal web SharePoint. No entanto, nem sempre é simples avaliar o impacto de uma política porque existem aplicações na nuvem com dependências de outras aplicações na nuvem. Por exemplo, as Equipas microsoft podem fornecer acesso a recursos no SharePoint Online. Assim, quando acederes às Equipas da Microsoft no nosso cenário atual, também estás sujeito à política do SharePoint MFA. 

> [!TIP]
> A utilização da aplicação [Office 365](concept-conditional-access-cloud-apps.md#office-365) destinar-se-á a todas as aplicações do Office para evitar problemas com dependências de serviços na pilha do Office.

## <a name="policy-enforcement"></a>Aplicação da política 

Se tiver uma dependência de serviço configurada, a apólice pode ser aplicada utilizando uma aplicação precoce ou tardia. 

- **A aplicação da política precoce** significa que um utilizador deve satisfazer a política de serviços dependente antes de aceder à aplicação de chamadas. Por exemplo, um utilizador deve satisfazer a política do SharePoint antes de se inscrever nas Equipas de MS. 
- **A aplicação da política tardia** ocorre após o utilizador assinar na aplicação de chamadas. A aplicação é adiada para quando se chama pedidos de aplicações, um símbolo para o serviço a jusante. Exemplos incluem equipas de MS que acedem ao Planner e Office.com aceder ao SharePoint. 

O diagrama abaixo ilustra as dependências de serviço das Equipas DE MS. Setas sólidas indicam a aplicação antecipada da seta tracejada para planejador indica aplicação tardia. 

![Dependências de serviços das Equipas MS](./media/service-dependencies/01.png)

Como uma boa prática, deve definir políticas comuns em aplicações e serviços relacionados sempre que possível. Ter uma postura de segurança consistente proporciona-lhe a melhor experiência do utilizador. Por exemplo, definir uma política comum em Exchange Online, SharePoint Online, Microsoft Teams e Skype para negócios reduz significativamente as solicitações inesperadas que podem surgir de diferentes políticas que estão sendo aplicadas a serviços a jusante. 

Uma ótima maneira de o conseguir com aplicações na stack do Office é usar a [app Office 365](concept-conditional-access-cloud-apps.md#office-365) em vez de direcionar as aplicações individuais.

A tabela abaixo lista dependências adicionais de serviço, onde as aplicações do cliente devem satisfazer  

| Aplicações do cliente         | Serviço a jusante                          | Execução |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure Management (portal e API) | Inicialmente ligado |
| Sala de Aula da Microsoft | Troca                                    | Inicialmente ligado |
|                     | SharePoint                                  | Inicialmente ligado |
| Microsoft Stream     | Troca                                    | Inicialmente ligado |
|                     | MS Planner                                  | Atrasado  |
|                     | Microsoft Stream                            | Atrasado  |
|                     | SharePoint                                  | Inicialmente ligado |
|                     | Skype para Empresas Online                   | Inicialmente ligado |
| Portal do Escritório       | Troca                                    | Atrasado  |
|                     | SharePoint                                  | Atrasado  |
| Grupos de perspetivas      | Troca                                    | Inicialmente ligado |
|                     | SharePoint                                  | Inicialmente ligado |
| PowerApps           | Microsoft Azure Management (portal e API) | Inicialmente ligado |
|                     | Microsoft Azure Active Directory              | Inicialmente ligado |
| Project             | Dynamics CRM                                | Inicialmente ligado |
| Skype para Empresas  | Troca                                    | Inicialmente ligado |
| Visual Studio       | Microsoft Azure Management (portal e API) | Inicialmente ligado |
| Microsoft Forms     | Troca                                    | Inicialmente ligado |
|                     | SharePoint                                  | Inicialmente ligado |
| Microsoft To-Do     | Troca                                    | Inicialmente ligado |

## <a name="next-steps"></a>Passos seguintes

Para aprender a implementar o Acesso Condicional no seu ambiente, consulte [Planeie a sua implementação de Acesso Condicional no Diretório Ativo Azure.](plan-conditional-access.md)
