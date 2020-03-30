---
title: Azure AD Connect cloud provisionando topologs e cenários suportados
description: Este tópico descreve os pré-requisitos e os requisitos de hardware no fornecimento de nuvens.
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
ms.openlocfilehash: 386af46bbee623d37bc914d2ee9130c914c6c885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620878"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Azure AD Connect cloud provisionando topologs e cenários suportados
Este artigo descreve várias topologias de diretório sinuoso azure ative (Azure AD) que utilizam o fornecimento de nuvem Azure AD Connect. Este artigo inclui apenas configurações e cenários suportados.

> [!IMPORTANT]
> A Microsoft não suporta modificar ou operar o fornecimento de nuvem Azure AD Connect fora das configurações ou ações que estão formalmente documentadas. Qualquer uma destas configurações ou ações pode resultar num estado inconsistente ou não suportado de fornecimento de nuvem Azure AD Connect. Como resultado, a Microsoft não pode possível fornecer o suporte técnico para implementações deste tipo.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Coisas a lembrar sobre todos os cenários e topoologias
Segue-se uma lista de informações a ter em conta ao selecionar uma solução.

- Os utilizadores e grupos devem ser identificados de forma única em todas as florestas
- Combinar entre florestas não ocorre com o fornecimento de nuvens
- Um utilizador ou grupo deve ser representado apenas uma vez em todas as florestas
- A âncora de origem para objetos é escolhida automaticamente.  Utiliza ms-DS-Consistência-Guia se presente, caso contrário o ObjectGUID é utilizado.
- Não é possível alterar o atributo utilizado para a âncora de origem.

## <a name="single-forest-single-azure-ad-tenant"></a>Floresta única, inquilino único da AD Azure
![Topologia para uma única floresta e um único inquilino](media/plan-cloud-provisioning-topologies/single-forest.png)

A topologia mais simples é uma única floresta no local, com um ou vários domínios, e um único inquilino azure aD.  Para um exemplo deste cenário ver [Tutorial: Uma única floresta com um único inquilino Azure AD](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>Multi-floresta, inquilino único azure ad
![Topologia para uma multi-floresta e um único inquilino](media/plan-cloud-provisioning-topologies/multi-forest.png)

Uma topologia comum é uma múltipla floresta de AD, com um ou vários domínios, e um único inquilino azure aD.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Floresta existente com Azure AD Connect, nova floresta com provisionamento em nuvem
![Topologia para uma única floresta e um único inquilino](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

Este cenário é superior ao cenário multiflorestal, no entanto este envolve um ambiente Azure AD Connect existente e, em seguida, trazer uma nova floresta usando o fornecimento de nuvem Azure AD Connect.  Para um exemplo deste cenário ver [Tutorial: Uma floresta existente com um único inquilino Azure AD](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>Pilotagem Azure AD Connect cloud provisioning em uma floresta aD híbrida existente
![Topologia para uma única floresta](media/plan-cloud-provisioning-topologies/migrate.png) e um único inquilino O cenário de pilotagem envolve a existência tanto da nuvem Azure AD Connect como da Azure AD Connect na mesma floresta e de scoping os utilizadores e grupos em conformidade. NOTA: Um objeto deve estar no âmbito apenas de uma das ferramentas. 

Para um exemplo deste cenário consulte [Tutorial: Pilot Azure AD Connect cloud provisioning em uma floresta aD sincronizada existente](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)

