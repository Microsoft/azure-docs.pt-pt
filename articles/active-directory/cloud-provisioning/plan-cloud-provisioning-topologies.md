---
title: Azure AD Connect cloud provisioning toplogis e cenários suportados
description: Saiba mais sobre várias topologias no local e Azure Ative Directory (Azure AD) que utilizam o fornecimento de nuvem Azure AD Connect.
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
ms.openlocfilehash: 172e836a212f9ce097c2c4e392a7386a510c2c6b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91266297"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Azure AD Connect cloud provisioning toplogis e cenários suportados
Este artigo descreve várias topologias no local e Azure Ative Directory (Azure AD) que utilizam o fornecimento de nuvem Azure AD Connect. Este artigo inclui apenas configurações e cenários suportados.

> [!IMPORTANT]
> A Microsoft não suporta modificar ou operar o Azure AD Connect, fornecendo a nuvem fora das configurações ou ações que estão formalmente documentadas. Qualquer uma destas configurações ou ações pode resultar num estado inconsistente ou não apoiado do fornecimento de nuvem Azure AD Connect. Como resultado, a Microsoft não pode possível fornecer o suporte técnico para implementações deste tipo.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Coisas a lembrar sobre todos os cenários e topologias
Segue-se uma lista de informações a ter em conta ao selecionar uma solução.

- Os utilizadores e grupos devem ser identificados de forma única em todas as florestas
- A correspondência entre florestas não ocorre com o provisionamento de nuvens
- Um utilizador ou grupo deve ser representado apenas uma vez em todas as florestas
- A âncora de origem dos objetos é escolhida automaticamente.  Utiliza ms-DS-ConsistencyGuid se estiver presente, caso contrário o ObjectGUID é utilizado.
- Não é possível alterar o atributo que é utilizado para a âncora de origem.

## <a name="single-forest-single-azure-ad-tenant"></a>Floresta única, único inquilino AZure AD
![Diagrama que mostra a topologia para uma única floresta e um único inquilino.](media/plan-cloud-provisioning-topologies/single-forest.png)

A topologia mais simples é uma única floresta no local, com um ou vários domínios, e um único inquilino AD AZure.  Para um exemplo deste cenário ver [Tutorial: Uma única floresta com um único inquilino AD AZure](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>Multi-floresta, único inquilino AD AZure
![Topologia para uma multi-floresta e um único inquilino](media/plan-cloud-provisioning-topologies/multi-forest.png)

Uma topologia comum é uma floresta múltipla de AD, com um ou vários domínios, e um único inquilino AD AZure.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Floresta existente com Azure AD Connect, nova floresta com provisão de nuvens
![Diagrama que mostra a topologia para uma floresta existente e uma nova floresta.](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

Este cenário é semelhante ao cenário multi-florestal, no entanto este envolve um ambiente Azure AD Connect existente e, em seguida, trazendo uma nova floresta usando o fornecimento de nuvem AZure AD Connect.  Para um exemplo deste cenário ver [Tutorial: Uma floresta existente com um único inquilino AD AZure](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>Pilotagem Azure AD Connect provisões em nuvem numa floresta híbrida híbrida existente
![Topologia para uma única floresta e um único inquilino ](media/plan-cloud-provisioning-topologies/migrate.png) O cenário de pilotagem envolve a existência de Azure AD Connect e Azure AD Connect na mesma floresta e descoduturar os utilizadores e grupos em conformidade. NOTA: Um objeto deve estar no âmbito de uma única das ferramentas. 

Para um exemplo deste cenário ver [Tutorial: Pilot Azure AD Connect cloud provisioning in a existing AD forest](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)

