---
title: Visão geral da política do Azure Firewall Manager
description: Saiba mais sobre as políticas do Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 34134f2c790851d34db7b5327aa76350d54d137d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89075468"
---
# <a name="azure-firewall-manager-policy-overview"></a>Visão geral da política do Azure Firewall Manager

A política de firewall é um recurso Azure que contém recolhas de regras de NAT, rede e aplicações, bem como configurações de Inteligência de Ameaças. É um recurso global que pode ser usado em vários casos de Firewall Azure em Secured Virtual Hubs e Hub Virtual Networks. As políticas funcionam em regiões e assinaturas.

![Política do Gestor de Firewall Azure](media/policy-overview/policy-overview.png)

## <a name="policy-creation-and-association"></a>Criação de políticas e associação

Uma política pode ser criada e gerida de várias maneiras, incluindo o portal Azure, REST API, modelos, Azure PowerShell e CLI.

Também pode migrar as regras existentes a partir do Azure Firewall usando o portal ou Azure PowerShell para criar políticas. Para obter mais informações, consulte [Como migrar as configurações do Azure Firewall para a política do Azure Firewall](migrate-to-policy.md). 

As políticas podem ser associadas a um ou mais centros virtuais ou VNets. A firewall pode estar em qualquer subscrição associada à sua conta e em qualquer região.

## <a name="hierarchical-policies"></a>Políticas hierárquicas

As novas políticas podem ser criadas do zero ou herdadas das políticas existentes. A herança permite que os DevOps criem políticas locais de firewall em cima da política de base mandatada pela organização.

As políticas criadas com políticas parentais não vazias herdam todas as coleções de regras da política dos pais. As coleções de regras de rede herdadas de uma política-mãe são sempre prioritárias acima das coleções de regras de rede definidas como parte de uma nova política. A mesma lógica aplica-se também às coleções de regras de aplicação. No entanto, as coleções de regras de rede são sempre processadas antes das recolhas de regras de aplicação, independentemente da herança.

O modo de Inteligência de Ameaças também é herdado da política dos pais. Podes definir o teu modo de inteligência de ameaça para um valor diferente para anular este comportamento, mas não podes desligá-lo. Só é possível anular com um valor mais rigoroso. Por exemplo, se a sua política de pais estiver definida apenas para **Alerta,** pode configurar esta política local para **alertar e negar**.

Tal como o modo De Inteligência de Ameaça, a lista de autorizações de ameaça é herdada da política dos pais. A política infantil pode adicionar endereços IP adicionais à lista de autorizações.

As coleções de regras nat não são herdadas porque são específicas de uma determinada firewall.

Com a herança, quaisquer alterações à política dos pais são automaticamente aplicadas às políticas associadas à criança firewall.

## <a name="traditional-rules-and-policies"></a>Regras e políticas tradicionais

O Azure Firewall apoia as regras e políticas tradicionais. O quadro que se segue compara as políticas e as regras:


| Assunto | Política  | Regras |
| ------- | ------- | ----- |
|Contains     |NAT, Rede, Regras de aplicação, definições personalizadas de DNS e DNS proxy, configurações de Grupos IP e Inteligência de Ameaças (incluindo lista de autorizações)|Regras de NAT, Rede e Aplicação, definições personalizadas de DNS e DNS proxy, configurações de Grupos IP e Inteligência de Ameaças (incluindo lista de autorizações)|
|Protege     |Centros virtuais e Redes Virtuais|Apenas redes virtuais|
|Experiência do portal     |Gestão central usando Firewall Manager|Experiência de firewall autónoma|
|Suporte de várias firewall     |Firewall Policy é um recurso separado que pode ser usado através de firewalls|Regras de exportação e importação manualmente, ou usando soluções de gestão de terceiros |
|Preços     |Faturado com base na associação de firewall. Ver [Preços.](#pricing)|Gratuito|
|Mecanismos de implantação apoiados     |Portal, REST API, modelos, Azure PowerShell e CLI|Portal, REST API, modelos, PowerShell e CLI. |

## <a name="pricing"></a>Preços

As políticas são faturadas com base em associações de firewall. Uma política com zero ou uma associação de firewall é gratuita. Uma política com várias associações de firewall é cobrada a uma taxa fixa. Para obter mais informações, consulte o Preço do Gestor de Firewall da [Azure.](https://azure.microsoft.com/pricing/details/firewall-manager/)

## <a name="next-steps"></a>Passos seguintes

Para aprender a implementar uma Firewall Azure, consulte [Tutorial: Fixe a sua rede de nuvem com o Azure Firewall Manager utilizando o portal Azure](secure-cloud-network.md).
