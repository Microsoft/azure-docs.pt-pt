---
title: Gestão central do Azure Firewall
description: Saiba mais sobre a gestão central do Azure Firewall Manager
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: victorh
ms.openlocfilehash: 23a7682d8a64de57db4ff9ae785ada90d4a06944
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87084675"
---
# <a name="azure-firewall-central-management"></a>Gestão central do Azure Firewall

Se gerir várias firewalls, sabe que mudar continuamente as regras de firewall dificulta mantê-las sincronizadas. As equipas centrais de TI precisam de uma forma de definir as políticas de firewall base e executá-las em várias unidades de negócio. Ao mesmo tempo, as equipas da DevOps querem criar as suas próprias políticas de firewall derivadas locais para uma melhor agilidade.

O Azure Firewall Manager pode ajudar a resolver estes problemas.


## <a name="azure-firewall-manager"></a>Azure Firewall Manager

O Azure Firewall Manager é um serviço de gestão de segurança de rede que fornece política de segurança central e gestão de rotas para perímetros de segurança baseados na nuvem. Torna mais fácil para as equipas de TI da Enterprise definir centralmente regras de nível de rede e aplicação para filtragem de tráfego em vários casos de Azure Firewall. Você pode abranger diferentes regiões e subscrições de Azure no centro e falou de arquiteturas para governação e proteção de tráfego. Também fornece aos DevOps uma melhor agilidade com políticas de segurança locais derivadas que são implementadas em todas as organizações.

### <a name="firewall-policy"></a>Política de firewall

Uma política de Firewall é um recurso Azure que contém recolhas de regras de rede e aplicações e configurações de Inteligência de Ameaças. É um recurso global que pode ser usado em vários casos de Firewall Azure em *Secured Virtual Hubs* e *Hub Virtual Networks*. As novas políticas podem ser criadas do zero ou herdadas das políticas existentes. A herança permite que os DevOps criem políticas locais de firewall em cima da política de base mandatada pela organização. As políticas funcionam em regiões e assinaturas.
 
Pode criar Política de Firewall e associações com o Azure Firewall Manager. No entanto, também pode criar e gerir uma política usando a API REST, modelos, Azure PowerShell e CLI. Uma vez que crie uma política, pode associá-la a uma firewall num hub WAN virtual, tornando-o um *Hub Virtual Seguro* e/ou uma firewall numa rede virtual que o torna o Hub Virtual *Network*.

### <a name="pricing"></a>Preços

As políticas são faturadas com base em associações de firewall. Uma política com zero ou uma associação de firewall é gratuita. Uma política com várias associações de firewall é cobrada a uma taxa fixa. Para obter mais informações, consulte o Preço do Gestor de Firewall da [Azure.](https://azure.microsoft.com/pricing/details/firewall-manager/)

## <a name="azure-firewall-management-partners"></a>Parceiros de Gestão de Firewall da Azure

As seguintes soluções líderes de terceiros suportam a gestão central do Azure Firewall utilizando apis padrão Azure REST. Cada uma destas soluções tem as suas características e características únicas:

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Guardião da Segurança da Nuvem de Barracuda](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin Orca](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Azure Firewall Manager, veja [o que é O Gestor de Firewall da Azure?](../firewall-manager/overview.md)