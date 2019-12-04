---
title: Azure AD Connect topologias e cenários com suporte de provisionamento em nuvem
description: Este tópico descreve os pré-requisitos e os requisitos de hardware do provisionamento de nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 142974423816b07d754a5425017aedc3195e2f4e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794000"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Azure AD Connect topologias e cenários com suporte de provisionamento em nuvem
Este artigo descreve várias topologias locais e Azure Active Directory (Azure AD) que usam Azure AD Connect provisionamento de nuvem. Este artigo inclui apenas cenários e configurações com suporte.

> [!IMPORTANT]
> A Microsoft não dá suporte à modificação ou operação Azure AD Connect provisionamento de nuvem fora das configurações ou ações documentadas formalmente. Qualquer uma dessas configurações ou ações pode resultar em um estado inconsistente ou sem suporte de Azure AD Connect provisionamento de nuvem. Como resultado, a Microsoft não pode possível fornecer o suporte técnico para implementações deste tipo.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Coisas a serem lembradas sobre todos os cenários e topologias
Veja a seguir uma lista de informações para ter em mente ao selecionar uma solução.

- Usuários e grupos devem ser identificados exclusivamente em todas as florestas
- A correspondência entre florestas não ocorre com o provisionamento de nuvem
- Um usuário ou grupo deve ser representado apenas uma vez em todas as florestas
- A âncora de origem dos objetos é escolhida automaticamente.  Ele usa MS-DS-ConsistencyGuid, se presente, caso contrário, objectGUID será usado.
- Não é possível alterar o atributo usado para a âncora de origem.



## <a name="multi-forest-single-azure-ad-tenant"></a>Locatário único do Azure AD de várias florestas
![Topologia para várias florestas e um único locatário](media/plan-cloud-provisioning-topologies/multi-forest.png)

A topologia mais comum é uma várias florestas do AD, com um ou vários domínios e um único locatário do Azure AD.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Floresta existente com Azure AD Connect, nova floresta com provisionamento de nuvem
![Topologia para uma única floresta e um único locatário](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

Esse cenário é a topologia semelhante ao cenário de várias florestas, no entanto, isso envolve um ambiente de Azure AD Connect existente e, em seguida, colocar uma nova floresta usando o provisionamento de nuvem Azure AD Connect.  Para obter um exemplo desse cenário, consulte [tutorial: uma floresta existente com um único locatário do Azure ad](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>Piloto Azure AD Connect provisionamento de nuvem em uma floresta híbrida existente do AD
![topologia para uma única floresta e um único locatário](media/plan-cloud-provisioning-topologies/migrate.png) cenário piloto envolve a existência de Azure AD Connect e Azure AD Connect o provisionamento de nuvem na mesma floresta e o escopo dos usuários e grupos de acordo. Observação: um objeto deve estar no escopo em apenas uma das ferramentas. 

Para obter um exemplo desse cenário, consulte [tutorial: piloto Azure ad Connect provisionamento de nuvem em uma floresta do AD sincronizada existente](tutorial-pilot-aadc-aadccp.md)

## <a name="single-forest-single-azure-ad-tenant"></a>Única floresta, único locatário do Azure AD
![Topologia para uma única floresta e um único locatário](media/plan-cloud-provisioning-topologies/single-forest.png)

A topologia mais simples é uma floresta local única, com um ou vários domínios, e um único locatário do Azure AD.  Para obter um exemplo desse cenário, consulte [tutorial: uma única floresta com um único locatário do Azure ad](tutorial-single-forest.md)

## <a name="next-steps"></a>Passos seguintes 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é Azure AD Connect provisionamento de nuvem?](what-is-cloud-provisioning.md)

