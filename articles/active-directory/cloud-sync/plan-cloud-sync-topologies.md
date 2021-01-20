---
title: Azure AD Connect cloud sync suportado topologias e cenários
description: Saiba mais sobre várias topologias no local e Azure Ative Directory (Azure AD) que utilizam sincronização de nuvem Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd14ed8d149cdc5296229c52ceb74afb2ce7b23
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614030"
---
# <a name="azure-ad-connect-cloud-sync-supported-topologies-and-scenarios"></a>Azure AD Connect cloud sync suportado topologias e cenários
Este artigo descreve várias topologias no local e Azure Ative Directory (Azure AD) que utilizam sincronização em nuvem Azure AD Connect. Este artigo inclui apenas configurações e cenários suportados.

> [!IMPORTANT]
> A Microsoft não suporta modificar ou operar a nuvem Azure AD Connect sincronizada fora das configurações ou ações que estão formalmente documentadas. Qualquer uma destas configurações ou ações pode resultar num estado inconsistente ou não suportado da sincronização de nuvem Azure AD Connect. Como resultado, a Microsoft não pode fornecer suporte técnico para tais implementações.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Coisas a lembrar sobre todos os cenários e topologias
Segue-se uma lista de informações a ter em conta ao selecionar uma solução.

- Os utilizadores e grupos devem ser identificados de forma única em todas as florestas
- Combinar através das florestas não ocorre com sincronização de nuvens
- Um utilizador ou grupo deve ser representado apenas uma vez em todas as florestas
- A âncora de origem dos objetos é escolhida automaticamente.  Utiliza ms-DS-ConsistencyGuid se estiver presente, caso contrário o ObjectGUID é utilizado.
- Não é possível alterar o atributo que é utilizado para a âncora de origem.

## <a name="single-forest-single-azure-ad-tenant"></a>Floresta única, único inquilino AZure AD
![Diagrama que mostra a topologia para uma única floresta e um único inquilino.](media/tutorial-single-forest/diagram-2.png)

A topologia mais simples é uma única floresta no local, com um ou vários domínios, e um único inquilino AD AZure.  Para um exemplo deste cenário ver [Tutorial: Uma única floresta com um único inquilino AD AZure](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>Multi-floresta, único inquilino AD AZure
![Topologia para uma multi-floresta e um único inquilino](media/plan-cloud-provisioning-topologies/multi-forest-2.png)

Uma topologia comum é uma floresta múltipla de AD, com um ou vários domínios, e um único inquilino AD AZure.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Floresta existente com Azure AD Connect, nova floresta com provisão de nuvens
![Diagrama que mostra a topologia para uma floresta existente e uma nova floresta.](media/tutorial-existing-forest/existing-forest-new-forest-2.png)

Este cenário é topologia é semelhante ao cenário multi-floresta, no entanto este envolve um ambiente Azure AD Connect existente e, em seguida, trazendo uma nova floresta usando a sincronização de nuvem Azure AD Connect.  Para um exemplo deste cenário ver [Tutorial: Uma floresta existente com um único inquilino AD AZure](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-sync-in-an-existing-hybrid-ad-forest"></a>Pilotagem Azure AD Connect cloud sync em uma floresta híbrida híbrida existente
![Topologia para uma única floresta e um único inquilino ](media/tutorial-migrate-aadc-aadccp/diagram-2.png) O cenário de pilotagem envolve a existência de sincronização de nuvem Azure AD Connect e Azure AD Connect na mesma floresta e descodimento dos utilizadores e grupos em conformidade. NOTA: Um objeto deve estar no âmbito de uma única das ferramentas. 

Para um exemplo deste cenário ver [Tutorial: Pilot Azure AD Connect cloud sync em uma floresta AD sincronizada existente](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem AZure AD Connect?](what-is-cloud-sync.md)

