---
title: Dependências de serviços de acesso condicional - Diretório Ativo Azure
description: Saiba como as condições são usadas no Acesso Condicional do Diretório Ativo Azure para desencadear uma política.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 05/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b0d7816dc83a7c3536e44ff2461d85ea6178ff1
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82778486"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>O que são dependências de serviço no Acesso Condicional do Diretório Ativo Azure? 

Com as políticas de Acesso Condicional, pode especificar os requisitos de acesso a websites e serviços. Por exemplo, os seus requisitos de acesso podem incluir a necessidade de autenticação multifactor (MFA) ou [dispositivos geridos](require-managed-devices.md). 

Quando se acede diretamente a um site ou serviço, o impacto de uma política relacionada é tipicamente fácil de avaliar. Por exemplo, se tiver uma política que requer autenticação multifactor (MFA) para configuração Online do SharePoint, o MFA é aplicado para cada sessão no portal web SharePoint. No entanto, nem sempre é direto avaliar o impacto de uma política porque existem aplicações na nuvem com dependências de outras aplicações na nuvem. Por exemplo, as Equipas Microsoft podem fornecer acesso a recursos no SharePoint Online. Assim, ao aceder às Equipas Microsoft no nosso cenário atual, também está sujeito à política do SharePoint MFA. 

## <a name="policy-enforcement"></a>Aplicação da política 

Se tiver uma dependência de serviço configurada, a apólice pode ser aplicada utilizando a aplicação precoce ou tardia. 

- **Aplicação da política precoce** significa que um utilizador deve satisfazer a política de serviço dependente antes de aceder à aplicação de chamada. Por exemplo, um utilizador deve satisfazer a política do SharePoint antes de iniciar sessão nas Equipas MS. 
- **A aplicação da política tardia** ocorre após o utilizador assinar na aplicação de chamadas. A aplicação é adiada para quando ligar para pedidos de aplicações, um símbolo para o serviço a jusante. Exemplos incluem MS Teams a aceder ao Planner e Office.com aceder ao SharePoint. 

O diagrama abaixo ilustra as dependências de serviço das Equipas MS. Setas sólidas indicam a aplicação precoce da seta tracejada para o Planejador indica a aplicação tardia. 

![Dependências de serviço das Equipas MS](./media/service-dependencies/01.png)

Como uma boa prática, deve definir políticas comuns entre aplicações e serviços relacionados sempre que possível. Ter uma postura de segurança consistente proporciona-lhe a melhor experiência do utilizador. Por exemplo, a definição de uma política comum em exchange online, SharePoint Online, Microsoft Teams e Skype para negócios reduz significativamente as indicações inesperadas que podem surgir de diferentes políticas que estão a ser aplicadas a serviços a jusante. 

Uma ótima maneira de o conseguir com aplicações na stack do Office é utilizar o [Office 365 (pré-visualização)](concept-conditional-access-cloud-apps.md#office-365-preview) em vez de direcionar aplicações individuais.

A tabela abaixo lista dependências adicionais de serviço, onde as aplicações do cliente devem satisfazer  

| Aplicações do cliente         | Serviço a jusante                          | Execução |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure Management (portal e API) | Atado cedo |
| Sala de Aula da Microsoft | Troca                                    | Atado cedo |
|                     | SharePoint                                  | Atado cedo |
| Microsoft Teams     | Troca                                    | Atado cedo |
|                     | Sra. Planejador                                  | Atrasado  |
|                     | SharePoint                                  | Atado cedo |
|                     | Skype para Empresas Online                   | Atado cedo |
| Portal do Escritório       | Troca                                    | Atrasado  |
|                     | SharePoint                                  | Atrasado  |
| Grupos de perspetivas      | Troca                                    | Atado cedo |
|                     | SharePoint                                  | Atado cedo |
| PowerApps           | Microsoft Azure Management (portal e API) | Atado cedo |
|                     | Microsoft Azure Active Directory              | Atado cedo |
| Project             | Dynamics CRM                                | Atado cedo |
| Skype para Empresas  | Troca                                    | Atado cedo |
| Visual Studio       | Microsoft Azure Management (portal e API) | Atado cedo |
| Microsoft Forms     | Troca                                    | Atado cedo |
|                     | SharePoint                                  | Atado cedo |
| Microsoft To-Do     | Troca                                    | Atado cedo |

## <a name="next-steps"></a>Passos seguintes

Para aprender a implementar o Acesso Condicional no seu ambiente, consulte [Planize a sua implementação de Acesso Condicional no Diretório Ativo Azure](plan-conditional-access.md).
