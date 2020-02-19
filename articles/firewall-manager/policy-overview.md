---
title: Visão geral da política de pré-visualização do Gestor de Firewall Azure
description: Conheça as políticas do Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 1308f4ba3335f2fd2633f6e39a679cd6477a4b5c
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77445022"
---
# <a name="azure-firewall-manager-preview-policy-overview"></a>Visão geral da política de pré-visualização do Gestor de Firewall Azure

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

A política de firewall é um recurso Azure que contém coleções de regras na NAT, rede e aplicação, bem como configurações de Inteligência de Ameaça. É um recurso global que pode ser usado em várias instâncias de Firewall Azure em Centros Virtuais Seguros e Redes Virtuais Hub. As políticas funcionam entre regiões e subscrições.

![Política do Gestor de Firewall Azure](media/policy-overview/policy-overview.png)

## <a name="policy-creation-and-association"></a>Criação e associação de políticas

Uma política pode ser criada e gerida de várias maneiras, incluindo o portal Azure, REST API, modelos, Azure PowerShell e CLI.

Também pode migrar as regras existentes a partir do Azure Firewall utilizando o portal ou o Azure PowerShell para criar políticas. Para mais informações, consulte Como migrar as configurações da [Firewall Azure para a política de Firewall Azure (pré-visualização)](migrate-to-policy.md). 

As políticas podem ser associadas a um ou mais centros virtuais ou VNets. A firewall pode estar em qualquer subscrição associada à sua conta e em qualquer região.

## <a name="hierarchical-policies"></a>Políticas hierárquicas

Novas políticas podem ser criadas a partir do zero ou herdadas das políticas existentes. A herança permite que a DevOps crie políticas locais de firewall para além da política de base mandatada pela organização.

As políticas criadas com políticas parentais não vazias herdam todas as coleções de regras da política dos pais. As coleções de regras de rede herdadas de uma política-mãe são sempre prioritárias acima das coleções de regras de rede definidas como parte de uma nova política. A mesma lógica aplica-se também às coleções de regras de aplicação. No entanto, as coleções de regras de rede são sempre processadas antes das coleções de regras de aplicação, independentemente da herança.

O modo De inteligência ameaça também é herdado da política dos pais. Podes definir o teu modo de inteligência ameaçador a um valor diferente para anular este comportamento, mas não podes desligá-lo. Só é possível anular com um valor mais rigoroso. Por exemplo, se a sua política de pais estiver definida apenas para **alertar,** pode configurar esta política local para **alertar e negar**.

As coleções de regras da NAT não são herdadas porque são específicas de uma dada firewall.

Com a herança, quaisquer alterações à política dos pais são automaticamente aplicadas às políticas associadas para crianças firewall.

## <a name="traditional-rules-and-policies"></a>Regras e políticas tradicionais

O Azure Firewall apoia as regras e políticas tradicionais. O quadro seguinte compara políticas e regras:


|         |Política  |Regras  |
|---------|---------|---------|
|Contains     |NAT, Rede, Regras de Aplicação e Definições de Inteligência de Ameaças|Regras na NAT, Rede e Aplicação |
|Protege     |Centros virtuais e redes virtuais|Apenas redes virtuais|
|Experiência do portal     |Gestão central usando Firewall Manager|Experiência de firewall autónoma|
|Suporte de firewall múltiplo     |Firewall Policy é um recurso separado que pode ser usado através de firewalls|Regras de exportação e importação manualmente, ou utilizando soluções de gestão de terceiros |
|Preços     |Faturado com base na associação de firewall. Ver [Preços](#pricing).|Gratuito|
|Mecanismos de implantação apoiados     |Portal, REST API, modelos, Azure PowerShell e CLI|Portal, REST API, modelos, PowerShell e CLI. |
|Estado da Versão     |Pré-visualização pública|Disponibilidade Geral|

## <a name="pricing"></a>Preços

As políticas são faturadas com base em associações de firewall. Uma política com zero ou uma associação de firewall é gratuita. Uma política com várias associações de firewall é cobrada a uma taxa fixa. Para mais informações, consulte [o Preço do Gestor de Firewall azure](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="next-steps"></a>Passos seguintes

Para aprender a implementar uma Firewall Azure, consulte Tutorial: Proteja a [sua rede de nuvem com o Azure Firewall Manager Preview utilizando o portal Azure](secure-cloud-network.md).
