---
title: O que é uma lista de controlo de acesso de rede do Azure?
description: Saiba mais sobre listas de controlo de acesso no Azure
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 83d66c84-8f6b-4388-8767-cd2de3e72d76
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 3a7155380a51273d376226c6be7a004f386181ce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61035258"
---
# <a name="what-is-an-endpoint-access-control-list"></a>O que é uma lista de controlo de acesso de ponto final?

> [!IMPORTANT]
> O Azure tem dois diferentes [modelos de implementação](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para criar e trabalhar com recursos: Resource Manager e clássica. Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo de implementação do Resource Manager. 

Uma lista de controlo de acesso de ponto final (ACL) é um aprimoramento de segurança disponível para a implementação do Azure. Uma ACL fornece a capacidade para seletivamente permitir ou negar o tráfego para um ponto de final de máquina virtual. Esta capacidade de filtragem de pacotes fornece uma camada adicional de segurança. Pode especificar ACLs de rede para pontos finais apenas. Não é possível especificar uma ACL para uma rede virtual ou uma sub-rede específica contidos numa rede virtual. É recomendado utilizar grupos de segurança de rede (NSGs) em vez de ACLs, sempre que possível. Quando utilizar NSGs, lista de controle de acesso de ponto final será substituída e já não é imposta. Para saber mais sobre NSGs, consulte [descrição geral de grupo de segurança de rede](security-overview.md)

ACLs podem ser configuradas ao utilizar o PowerShell ou o portal do Azure. Para configurar uma ACL de rede com o PowerShell, consulte [apresenta uma lista de controlo de acesso de gerenciamento para pontos finais com o PowerShell](virtual-networks-acl-powershell.md). Para configurar uma ACL de rede com o portal do Azure, consulte [como configurar pontos finais para uma máquina virtual](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

ACLs de rede, pode fazer com o seguinte:

* Seletivamente permitir ou negar o tráfego de entrada com base no intervalo de endereços de IPv4 para um ponto de final de entrada de máquina virtual de sub-rede remota.
* Endereços IP da lista de bloqueios
* Criar várias regras por ponto final de máquina virtual
* Ordenação da regra de utilização para garantir que o conjunto correto de regras são aplicadas num ponto final de máquina virtual especificada (valor mais baixo e o mais alto)
* Especifique uma ACL para uma endereço IPv4 de sub-rede remota específica.

Consulte a [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) artigo para limites ACL.

## <a name="how-acls-work"></a>Como as ACLs funcionam
Uma ACL é um objeto que contém uma lista de regras. Quando cria uma ACL e aplicá-la para um ponto de final de máquina virtual, filtragem de pacotes ocorre no nó de anfitrião da VM. Isso significa que o tráfego de endereços IP remotos é filtrado pelo nó de anfitrião para regras de ACL correspondência em vez de na sua VM. Isto impede que a VM gastos os ciclos de CPU valiosos de filtragem de pacotes.

Quando é criada uma máquina virtual, uma ACL predefinida é colocada no local para bloquear todo o tráfego de entrada. No entanto, se um ponto final for criado para (porta 3389), em seguida, a predefinição ACL é modificada para permitir todo o tráfego de entrada para o ponto de extremidade. Tráfego de entrada de qualquer sub-rede remota, em seguida, é permitido para esse ponto final e nenhum tipo de aprovisionamento de firewall é necessário. Todas as outras portas estão bloqueadas para tráfego de entrada, a menos que, pontos finais são criados para essas portas. Por predefinição, o tráfego de saída é permitido.

**Tabela de ACL predefinida de exemplo**

| **N. º de regra** | **Sub-rede remota** | **Endpoint** | **Permitir/recusar** |
| --- | --- | --- | --- |
| 100 |0.0.0.0/0 |3389 |Permitir |

## <a name="permit-and-deny"></a>Permitir e negar
Seletivamente pode permitir ou negar o tráfego de rede para um ponto de final de entrada de máquina virtual através da criação de regras que especificam a "Permitir" ou "Negar". É importante observar que por padrão, quando é criado um ponto de extremidade, todo o tráfego é permitido para o ponto final. Por esse motivo, é importante compreender como criar regras de permitir/recusar e colocá-las na ordem correta de precedência, se pretender que o controle granular sobre o tráfego de rede que optar por permitir a alcançar o ponto final de máquina virtual.

Pontos a considerar:

1. **Não existem ACL –** por predefinição quando é criado um ponto de extremidade, vamos permitir todos os para o ponto final.
2. **Permitir -** quando adiciona um ou mais "permitem" intervalos, são negar todos os outros intervalos por predefinição. Apenas os pacotes do intervalo IP permitido será capazes de comunicar com o ponto final de máquina virtual.
3. **Negar -** quando adiciona um ou mais "Negar" intervalos, estão permitindo que todos os outros intervalos de tráfego por predefinição.
4. **Combinação de permissão e negação -** pode utilizar uma combinação de "Permitir" e "Negar" quando deseja extrair um intervalo IP específico para ser permitido ou negado.

## <a name="rules-and-rule-precedence"></a>Regras e precedência da regra
ACLs de rede podem ser configuradas nos pontos finais da máquina virtual específica. Por exemplo, pode especificar uma ACL de rede para um ponto de final RDP criado numa máquina virtual que bloqueia acesso para determinados os endereços IP. A tabela abaixo mostra uma forma de conceder acesso ao público IPs virtuais (VIPs) de um determinado intervalo para permitir o acesso para RDP. Todos os outros IPs remoto serão negadas. Seguimos uma *menor tem precedência* ordem de regra.

### <a name="multiple-rules"></a>Várias regras
No exemplo abaixo, se pretender permitir o acesso para o ponto de final RDP apenas a partir de dois IPv4 intervalos de endereços públicos (65.0.0.0/8 e 159.0.0.0/8), pode conseguir isto ao especificar dois *permitir* regras. Neste caso, uma vez que o RDP é criado por predefinição para uma máquina virtual, pode querer bloquear o acesso à porta RDP com base numa sub-rede remota. O exemplo abaixo mostra uma forma de conceder acesso ao público IPs virtuais (VIPs) de um determinado intervalo para permitir o acesso para RDP. Todos os outros IPs remoto serão negadas. Isso funciona, pois as ACLs de rede podem ser definida para um ponto de final de máquina virtual específica e o acesso é negado por predefinição.

**Exemplo – o várias regras**

| **N. º de regra** | **Sub-rede remota** | **Endpoint** | **Permitir/recusar** |
| --- | --- | --- | --- |
| 100 |65.0.0.0/8 |3389 |Permitir |
| 200 |159.0.0.0/8 |3389 |Permitir |

### <a name="rule-order"></a>Ordem de regra
Uma vez que várias regras podem ser especificadas para um ponto de extremidade, deve haver uma forma de organizar regras para determinar a precedência a regra. A ordem de regra especifica precedência. Siga as ACLs de rede uma *menor tem precedência* ordem de regra. No exemplo abaixo, o ponto final na porta 80 seletivamente é concedido acesso a apenas determinados intervalos de endereços IP. Para configurar esta opção, temos uma regra de negação (regra \# 100) para endereços no espaço de 175.1.0.1/24. Uma segunda regra, em seguida, é especificada com precedência 200, que permite o acesso a todos os outros endereços em 175.0.0.0/8.

**Exemplo – precedência da regra**

| **N. º de regra** | **Sub-rede remota** | **Endpoint** | **Permitir/recusar** |
| --- | --- | --- | --- |
| 100 |175.1.0.1/24 |80 |Negar |
| 200 |175.0.0.0/8 |80 |Permitir |

## <a name="network-acls-and-load-balanced-sets"></a>ACLs de rede e conjuntos com balanceamento de carga
ACLs de rede podem ser especificadas num ponto de extremidade do conjunto com balanceamento de carga. Se uma ACL é especificada para um conjunto com balanceamento de carga, a rede ACL é aplicada a todas as máquinas virtuais em conjunto com balanceamento de carga. Por exemplo, se uma carga balanceada é criado um conjunto com a "Porta 80" e o conjunto com balanceamento de carga contém 3 VMs, a ACL de rede criada no ponto final "A porta 80" de uma que VM será aplicado automaticamente às outras VMs.

![ACLs de rede e conjuntos com balanceamento de carga](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Próximos Passos
[Gerir listas de controlo de acesso para pontos finais com o PowerShell](virtual-networks-acl-powershell.md)

