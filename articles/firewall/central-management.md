---
title: Gestão central da Azure Firewall
description: Conheça a gestão central do Azure Firewall Manager
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 58f670f3f55a63f0c1823adc13c98f5863d4d650
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77444553"
---
# <a name="azure-firewall-central-management"></a>Gestão central da Azure Firewall

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Se gere várias firewalls, sabe que as regras de firewall em constante mudança dificultam a sua sincronização. As equipas centrais de TI precisam de uma forma de definir as políticas de firewall base e aplicá-las em várias unidades de negócio. Ao mesmo tempo, as equipas da DevOps querem criar as suas próprias políticas locais de firewall derivadas para uma melhor agilidade.

A Pré-visualização do Gestor de Firewall Azure pode ajudar a resolver estes problemas.


## <a name="azure-firewall-manager-preview"></a>Pré-visualização do Gestor de Firewall Azure

Azure Firewall Manager Preview é um serviço de gestão de segurança de rede que fornece a política de segurança central e a gestão de rotas para perímetros de segurança baseados na nuvem. Torna mais fácil para as equipas de TI da Enterprise definir centralmente regras de nível de rede e de aplicação para filtragem de tráfego em várias instâncias do Azure Firewall. Você pode percorrer diferentes regiões e subscrições de Azure no hub e arquiteturas faladas para governação e proteção do tráfego. Também fornece à DevOps uma melhor agilidade com políticas de segurança locais derivadas da firewall que são implementadas entre organizações.

### <a name="firewall-policy"></a>Política de firewall

Uma política de Firewall é um recurso Azure que contém nat, recolhade regras de rede e aplicações e definições de Inteligência de Ameaça. É um recurso global que pode ser usado em várias instâncias de Firewall Azure em *Centros Virtuais Seguros* e Redes Virtuais *Hub*. Novas políticas podem ser criadas a partir do zero ou herdadas das políticas existentes. A herança permite que a DevOps crie políticas locais de firewall para além da política de base mandatada pela organização. As políticas funcionam entre regiões e subscrições.
 
Pode criar políticas de firewall e associações com o Azure Firewall Manager. No entanto, também pode criar e gerir uma política utilizando a REST API, modelos, Azure PowerShell e CLI. Assim que criar uma apólice, pode associá-la a uma firewall num hub virtual WAN, tornando-o num *Hub Virtual Seguro* e/ou numa firewall numa rede virtual que a torna hub Virtual *Network*.

### <a name="pricing"></a>Preços

As políticas são faturadas com base em associações de firewall. Uma política com zero ou uma associação de firewall é gratuita. Uma política com várias associações de firewall é cobrada a uma taxa fixa. Para mais informações, consulte [o Preço do Gestor de Firewall azure](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="azure-firewall-management-partners"></a>Parceiros azure Firewall Management

As seguintes soluções de terceiros apoiam a gestão central do Azure Firewall utilizando APIs padrão do Azure REST. Cada uma destas soluções tem as suas características e características únicas:

- [Fluxo de Nuvem de Algosec](https://www.algosec.com/azure/) 
- [Guardião de Segurança da Nuvem Barracuda](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin Orca](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre a Pré-visualização do Gestor de Firewall do Azure, consulte [o que é a Pré-visualização do Gestor de Firewall do Azure?](../firewall-manager/overview.md)