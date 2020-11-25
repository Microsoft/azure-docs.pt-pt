---
title: Construa resiliência na sua infraestrutura IAM com o Azure Ative Directory
description: Um guia para arquitetos e administradores de TI que não construam resiliência à rutura da sua infraestrutura IAM.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ad97a822aaa6477616a6661a579df6c4ec82729
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919893"
---
# <a name="build-resilience-in-your-identity-and-access-management-infrastructure"></a>Construa resiliência na sua infraestrutura de gestão de identidade e acessos

O Azure Ative Directory é um sistema global de gestão de identidade e acesso em nuvem que fornece serviços críticos como autenticação e autorização aos recursos da sua organização. Este documento fornece-lhe orientações para compreender, conter e mitigar o risco de interrupção de serviços de autenticação ou autorização de recursos que dependem do Azure Ative Directory (Azure AD). 

O conjunto de documentos é projetado para

* Arquitetos de Identidade

* Proprietários de Serviços de Identidade

* Equipas de Operações identitárias

Consulte também a documentação para [programadores de aplicações](https://aka.ms/azureadresilience/developer) e para [sistemas Azure AD B2C.](resilience-b2c.md)

## <a name="what-is-resilience"></a>O que é resiliência?

No contexto da sua infraestrutura de identidade, a resiliência é a capacidade de suportar perturbações em serviços como a autenticação e autorização, ou falha de outros componentes, com impacto mínimo ou nenhum no seu negócio, utilizadores e operações. O impacto da perturbação pode ser severo, e a resiliência requer um planeamento diligente.

## <a name="why-worry-about-disruption"></a>Por que se preocupar com perturbações?

Cada chamada para o sistema de autenticação está sujeita a perturbações se algum componente na cadeia da chamada para Azure AD falhar. Isto significa que se alguma parte da sua infraestrutura tiver um problema, o trabalho pode ser interrompido porque os utilizadores não podem aceder às aplicações de que necessitam. Por isso, a redução do número de chamadas de autenticação e o número de dependências nessas chamadas é importante para a sua resiliência. Os desenvolvedores de aplicações podem afirmar algum controlo sobre a frequência com que os tokens são solicitados. Por exemplo, trabalhe com os seus desenvolvedores para garantir que estão a usar identidades geridas Azure AD para as suas aplicações sempre que possível. 

Num sistema de autenticação baseado em símbolos como o Azure AD, a aplicação (cliente) de um utilizador deve adquirir um símbolo de segurança do sistema de identidade antes de poder aceder a uma aplicação ou outro recurso. Durante o período de validade, um cliente pode apresentar o mesmo símbolo várias vezes para aceder à aplicação.

Quando o token apresentado ao pedido expirar, o pedido rejeita o token, e o cliente deve adquirir um novo token da Azure AD. Adquirir um novo símbolo requer potencialmente a interação do utilizador, como as indicações de credencial. A redução da frequência de chamadas de autenticação com fichas de maior duração reduz este risco. No entanto, é preciso equilibrar a vida útil com o risco criado por menos avaliações políticas. Para obter mais informações sobre a gestão de vidas simbólicas, consulte este artigo sobre [a otimização de pedidos de reauestação](https://docs.microsoft.com/azure/active-directory/authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime).

## <a name="ways-to-increase-resilience"></a>Formas de aumentar a resiliência
O diagrama que se segue mostra seis formas concretas de aumentar a resiliência. Cada método é explicado em detalhe nos artigos ligados na parte dos próximos passos deste artigo.
  
![Diagrama mostrando visão geral da resiliência da administração](./media/resilience-in-infrastructure/admin-resilience-overview.png)

## <a name="next-steps"></a>Passos seguintes
Recursos de resiliência para administradores e arquitetos
 
* [Construir resiliência com gestão credencial](resilience-in-credentials.md)

* [Construir resiliência com estados de dispositivo](resilience-with-device-states.md)

* [Construa resiliência utilizando a Avaliação contínua de Acesso (CAE)](resilience-with-continuous-access-evaluation.md)

* [Construir resiliência na autenticação externa do utilizador](resilience-b2b-authentication.md)

* [Construa resiliência na sua autenticação híbrida](resilience-in-hybrid.md)

* [Criar resiliência no acesso à aplicação com Aplicação Proxy](resilience-on-premises-access.md)

Recursos de resiliência para desenvolvedores

* [Construa resiliência do IAM nas suas aplicações](resilience-app-development-overview.md)

* [Construa resiliência nos seus sistemas CIAM](resilience-b2c.md)
