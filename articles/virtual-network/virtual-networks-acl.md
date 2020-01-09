---
title: O que é uma lista de controle de acesso de rede do Azure?
description: Saiba mais sobre as listas de controle de acesso no Azure
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: 0002e61827817af958007e1f789219e9291990d8
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647769"
---
# <a name="what-is-an-endpoint-access-control-list"></a>O que é uma lista de controle de acesso de ponto de extremidade?

> [!IMPORTANT]
> O Azure tem dois [modelos de implantação](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) diferentes para criar e trabalhar com recursos: Gerenciador de recursos e clássico. Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que a maioria das implantações novas usem o modelo de implantação do Gerenciador de recursos. 

Uma ACL (lista de controle de acesso) de ponto de extremidade é um aprimoramento de segurança disponível para sua implantação do Azure. Uma ACL fornece a capacidade de permitir ou negar seletivamente o tráfego para um ponto de extremidade de máquina virtual. Esse recurso de filtragem de pacotes fornece uma camada adicional de segurança. Você pode especificar ACLs de rede somente para pontos de extremidade. Você não pode especificar uma ACL para uma rede virtual ou uma sub-rede específica contida em uma rede virtual. É recomendável usar NSGs (grupos de segurança de rede) em vez de ACLs, sempre que possível. Ao usar NSGs, a lista de controle de acesso do ponto de extremidade será substituída e não mais imposta. Para saber mais sobre o NSGs, consulte [visão geral do grupo de segurança de rede](security-overview.md)

As ACLs podem ser configuradas usando o PowerShell ou o portal do Azure. Para configurar uma ACL de rede usando o PowerShell, consulte [Gerenciando listas de controle de acesso para pontos de extremidade usando o PowerShell](virtual-networks-acl-powershell.md). Para configurar uma ACL de rede usando o portal do Azure, consulte [como configurar pontos de extremidade para uma máquina virtual](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Usando ACLs de rede, você pode fazer o seguinte:

* Permitir ou negar seletivamente o tráfego de entrada com base no intervalo de endereços IPv4 da sub-rede remota para um ponto de extremidade de entrada da máquina virtual.
* Endereços IP de lista de bloqueios
* Criar várias regras por ponto de extremidade de máquina virtual
* Usar a ordenação de regra para garantir que o conjunto correto de regras seja aplicado em um determinado ponto de extremidade da máquina virtual (mais baixo ao mais alto)
* Especifique uma ACL para um endereço IPv4 de sub-rede remota específica.

Consulte o artigo [limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) para obter limites de ACL.

## <a name="how-acls-work"></a>Como as ACLs funcionam
Uma ACL é um objeto que contém uma lista de regras. Quando você cria uma ACL e a aplica a um ponto de extremidade de máquina virtual, a filtragem de pacotes ocorre no nó de host da VM. Isso significa que o tráfego de endereços IP remotos é filtrado pelo nó de host para as regras de ACL correspondentes em vez de em sua VM. Isso impede que sua VM gaste os preciosos ciclos de CPU na filtragem de pacotes.

Quando uma máquina virtual é criada, uma ACL padrão é colocada em vigor para bloquear todo o tráfego de entrada. No entanto, se um ponto de extremidade for criado para (porta 3389), a ACL padrão será modificada para permitir todo o tráfego de entrada para esse ponto de extremidade. O tráfego de entrada de qualquer sub-rede remota é permitido para esse ponto de extremidade e nenhum provisionamento de firewall é necessário. Todas as outras portas são bloqueadas para tráfego de entrada, a menos que os pontos de extremidade sejam criados para essas portas. O tráfego de saída é permitido por padrão.

**Exemplo de tabela ACL padrão**

| **Régua #** | **Sub-rede remota** | **Endpoint** | **Permitir/negar** |
| --- | --- | --- | --- |
| 100 |0.0.0.0/0 |3389 |Oferecem |

## <a name="permit-and-deny"></a>Permitir e negar
Você pode permitir ou negar seletivamente o tráfego de rede para um ponto de extremidade de entrada da máquina virtual criando regras que especificam "permitir" ou "negar". É importante observar que, por padrão, quando um ponto de extremidade é criado, todo o tráfego é permitido para o ponto de extremidade. Por esse motivo, é importante entender como criar regras de permissão/negação e colocá-las na ordem de precedência apropriada se você quiser um controle granular sobre o tráfego de rede que você escolher para permitir o acesso ao ponto de extremidade da máquina virtual.

Pontos a considerar:

1. **Sem ACL –** Por padrão, quando um ponto de extremidade é criado, permitimos todos para o ponto de extremidade.
2. **Permitir-** Ao adicionar um ou mais intervalos de "permissão", você está negando todos os outros intervalos por padrão. Somente os pacotes do intervalo IP permitido poderão se comunicar com o ponto de extremidade da máquina virtual.
3. **Negar-** Ao adicionar um ou mais intervalos de "negação", você está permitindo todos os outros intervalos de tráfego por padrão.
4. **Combinação de permitir e negar-** Você pode usar uma combinação de "permitir" e "negar" quando desejar que um intervalo de IP específico seja permitido ou negado.

## <a name="rules-and-rule-precedence"></a>Regras e precedência de regra
As ACLs de rede podem ser configuradas em pontos de extremidade de máquina virtual específicos. Por exemplo, você pode especificar uma ACL de rede para um ponto de extremidade RDP criado em uma máquina virtual que bloqueia o acesso a determinados endereços IP. A tabela a seguir mostra uma maneira de conceder acesso a IPs virtuais públicos (VIPs) de um determinado intervalo para permitir o acesso ao RDP. Todos os outros IPs remotos são negados. Seguimos uma ordem de regra de *precedência mais baixa* .

### <a name="multiple-rules"></a>Várias regras
No exemplo a seguir, se você quiser permitir o acesso ao ponto de extremidade RDP somente de dois intervalos de endereços IPv4 públicos (65.0.0.0/8 e 159.0.0.0/8), poderá conseguir isso especificando duas regras de *permissão* . Nesse caso, como o RDP é criado por padrão para uma máquina virtual, talvez você queira bloquear o acesso à porta RDP com base em uma sub-rede remota. O exemplo a seguir mostra uma maneira de conceder acesso a IPs virtuais públicos (VIPs) de um determinado intervalo para permitir o acesso ao RDP. Todos os outros IPs remotos são negados. Isso funciona porque as ACLs de rede podem ser configuradas para um ponto de extremidade de máquina virtual específico e o acesso é negado por padrão.

**Exemplo – várias regras**

| **Régua #** | **Sub-rede remota** | **Endpoint** | **Permitir/negar** |
| --- | --- | --- | --- |
| 100 |65.0.0.0/8 |3389 |Oferecem |
| 200 |159.0.0.0/8 |3389 |Oferecem |

### <a name="rule-order"></a>Ordem da regra
Como várias regras podem ser especificadas para um ponto de extremidade, deve haver uma maneira de organizar regras para determinar qual regra tem precedência. A ordem da regra especifica a precedência. As ACLs de rede seguem uma ordem de regra de *precedência mais baixa* . No exemplo abaixo, o ponto de extremidade na porta 80 recebe acesso seletivo somente a determinados intervalos de endereços IP. Para configurar isso, temos uma regra Deny (regra \# 100) para endereços no espaço espaço 175.1.0.1/24. Uma segunda regra é especificada com a precedência 200 que permite o acesso a todos os outros endereços em 175.0.0.0/8.

**Exemplo – precedência de regra**

| **Régua #** | **Sub-rede remota** | **Endpoint** | **Permitir/negar** |
| --- | --- | --- | --- |
| 100 |175.1.0.1/24 |80 |Negar |
| 200 |175.0.0.0/8 |80 |Oferecem |

## <a name="network-acls-and-load-balanced-sets"></a>ACLs de rede e conjuntos com balanceamento de carga
As ACLs de rede podem ser especificadas em um ponto de extremidade de conjunto com balanceamento de carga. Se uma ACL for especificada para um conjunto com balanceamento de carga, a ACL de rede será aplicada a todas as máquinas virtuais nesse conjunto de balanceamento de carga. Por exemplo, se um conjunto com balanceamento de carga for criado com "porta 80" e o conjunto com balanceamento de carga contiver 3 VMs, a ACL de rede criada no ponto de extremidade "porta 80" de uma VM será aplicada automaticamente às outras VMs.

![ACLs de rede e conjuntos com balanceamento de carga](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Próximos Passos
[Gerenciar listas de controle de acesso para pontos de extremidade usando o PowerShell](virtual-networks-acl-powershell.md)

