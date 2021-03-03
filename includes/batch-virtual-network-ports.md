---
title: incluir ficheiro
description: incluir ficheiro
services: batch
documentationcenter: ''
author: JnHs
manager: evansma
editor: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.date: 01/13/2021
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: c625253585cc99c035852b8b9042f939284bad19
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750711"
---
### <a name="general-requirements"></a>Requisitos gerais

* A VNet tem de estar na mesma subscrição e região da conta do Batch utilizada para criar o conjunto.

* A sub-rede especificada para o conjunto deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs direcionadas para o conjunto; ou seja, a soma de propriedades `targetDedicatedNodes` e `targetLowPriorityNodes` do conjunto. Se a sub-rede não tiver endereços IP não atribuídos suficientes, o conjunto atribui parcialmente os nós de computação e ocorre um erro de redimensionamento.

* O ponto final do Armazenamento do Azure tem de ser resolvido por qualquer servidor DNS personalizado que sirva a sua VNet. Mais concretamente, devem ser resolvíveis os URLs no formato `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` e `<account>.blob.core.windows.net`.

* Várias piscinas podem ser criadas no mesmo VNet ou na mesma sub-rede (desde que tenha espaço de endereço suficiente). Uma única piscina não pode existir em vários VNets ou sub-redes.

Os requisitos adicionais de VNet diferem, consoante o conjunto do Batch está na configuração da Máquina Virtual ou na configuração dos Serviços Cloud. Para novas implementações de conjuntos numa VNet, é recomendada a configuração de Máquina Virtual.

### <a name="pools-in-the-virtual-machine-configuration"></a>Conjuntos na configuração de Máquina Virtual

**VNets suportadas** - apenas VNets baseadas no Azure Resource Manager

**ID de sub-rede** - quando especificar a sub-rede com as APIs do Batch, utilize o *identificador de recurso* da sub-rede. O identificador da sub-rede tem o formato:

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}`

**Permissões** - verifique se as suas políticas de segurança ou bloqueios na subscrição ou no grupo de recursos da VNet restringem as permissões de um utilizador para gerir a VNet.

**Recursos de rede adicionais** - o Batch aloca automaticamente recursos de rede adicionais no grupo de recursos que contém a VNet.

> [!IMPORTANT]
> Para cada 100 nós dedicados ou de baixa prioridade, o Batch atribui: um grupo de segurança de rede (NSG), um endereço IP público e um equilibrador de carga. Estes recursos estão limitados pelas [quotas de recursos](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) da subscrição. Para conjuntos grandes, poderá ter de pedir um aumento de quota para um ou mais destes recursos.

#### <a name="network-security-groups-batch-default"></a>Grupos de segurança de rede: Predefinição do Batch

A sub-rede tem de permitir comunicação de entrada a partir do serviço Batch para conseguir agendar tarefas nos nós de computação e comunicação de saída para comunicar com o Armazenamento do Azure ou outros recursos, conforme necessário pela sua carga de trabalho. Para conjuntos na configuração de Máquina Virtual, o Batch adiciona NSGs ao nível das interfaces de rede (NICs) ligadas aos nós de computação. Estes NSGs são configurados com as regras adicionais seguintes:

* Tráfego TCP de entrada nas portas 29876 e 29877 a partir dos endereços IP do serviço Batch que correspondem à etiqueta de serviço `BatchNodeManagement`.
* Tráfego TCP de entrada nas portas 22 (nós do Linux) ou 3389 (nós do Windows) para permitir acesso remoto. Para certos tipos de tarefas multi-instâncias em Linux (como MPI), terá também de permitir o tráfego da porta SSH 22 para IPs na sub-rede que contém os nós de computação do Batch. As regras de NSG ao nível da sub-rede podem impedir esta situação (veja abaixo).
* Tráfego de saída em qualquer porta para a rede virtual. As regras de NSG ao nível da sub-rede podem corrigir esta situação (veja abaixo).
* Tráfego de saída em qualquer porta para a Internet. As regras de NSG ao nível da sub-rede podem corrigir esta situação (veja abaixo).

> [!IMPORTANT]
> Tenha cuidado se modificar ou adicionar regras de entrada ou saída em NSGs configurados em Lote. Se a comunicação aos nós computacional na sub-rede especificada for negada por um NSG, o serviço Batch definirá o estado dos nós computacional para **inutilizáveis**. Além disso, não devem ser aplicados bloqueios de recursos a qualquer recurso criado pelo Batch, uma vez que isso pode impedir a limpeza de recursos como resultado de ações iniciadas pelo utilizador, tais como a eliminação de uma piscina.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Grupos de segurança de rede: Especificar regras ao nível da sub-rede

Não é preciso especificar NSGs ao nível da sub-rede de rede virtual, porque o Batch configura os seus próprios NSGs (ver acima). Se tiver um NSG associado à sub-rede onde os nós computativos batch são implantados, ou se quiser aplicar regras NSG personalizadas para anular os predefinidos aplicados, deve configurar este NSG com, pelo menos, as regras de segurança de entrada e saída indicadas nas seguintes tabelas.

Configure o tráfego de entrada na porta 3389 (Windows) ou 22 (Linux) apenas se tiver de permitir acesso remoto aos nós de computação fora das origens. Se precisar de suporte para tarefas de multi-instâncias com determinados runtimes MPI, poderá ter de ativar as regras da porta 22 no Linux. Não é estritamente obrigatório permitir o tráfego nestas portas para que os nós de computação do conjunto podem ser utilizáveis.

> [!WARNING]
> Os endereços IP do serviço Batch podem ser alterados ao longo do tempo. Por isso, recomendamos vivamente que utilize a etiqueta de `BatchNodeManagement` serviço (ou uma variante regional) para as regras NSG indicadas nas tabelas seguintes. Evite povoar as regras NSG com endereços IP de serviço de lote específicos.

**Regras de segurança de entrada**

| Endereços IP de origem | Etiqueta de serviço de origem | Portas de origem | Destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- | --- |
| N/D | `BatchNodeManagement`[tag de serviço](../articles/virtual-network/network-security-groups-overview.md#service-tags) (se utilizar uma variante regional, na mesma região que a sua conta Batch) | * | Qualquer | 29876-29877 | TCP | Permitir |
| IPs de origem do utilizador para aceder remotamente aos nós de computação e/ou à sub-rede dos nós de computação para tarefas de multi-instâncias em Linux, se necessário. | N/D | * | Qualquer | 3389 (Windows), 22 (Linux) | TCP | Permitir |

**Regras de segurança de saída**

| Origem | Portas de origem | Destino | Etiqueta do serviço de destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- | --- |
| Qualquer | * | [Etiqueta de serviço](../articles/virtual-network/network-security-groups-overview.md#service-tags) | `Storage` (se estiver a ser utilizada uma variante regional, na mesma região da sua conta do Batch) | 443 | TCP | Permitir |
| Qualquer | * | [Etiqueta de serviço](../articles/virtual-network/network-security-groups-overview.md#service-tags) | `BatchNodeManagement` (se estiver a ser utilizada uma variante regional, na mesma região da sua conta do Batch) | 443 | TCP | Permitir |

A saída `BatchNodeManagement` é necessária para contactar o serviço Batch a partir dos nós de computação, tais como para tarefas de Gestor de Emprego.

### <a name="pools-in-the-cloud-services-configuration"></a>Conjuntos na configuração dos Serviços Cloud

> [!WARNING]
> As piscinas de configuração do serviço de nuvem são depreciadas. Utilize, em vez disso, piscinas de configuração de máquinas virtuais.

**VNets suportadas** - Apenas VNets clássicas

**ID de sub-rede** - quando especificar a sub-rede com as APIs do Batch, utilize o *identificador de recurso* da sub-rede. O identificador da sub-rede tem o formato:

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}`

**Permissões** - O `Microsoft Azure Batch` diretor de serviço deve ter a `Classic Virtual Machine Contributor` função Azure para o VNet especificado.

#### <a name="network-security-groups"></a>Grupos de segurança de rede

A sub-rede tem de permitir comunicação de entrada a partir do serviço Batch para conseguir agendar tarefas nos nós de computação e comunicação de saída para comunicar com o Armazenamento do Azure ou outros recursos.

Não é necessário especificar um NSG, porque o Batch configura a comunicação de entrada apenas a partir dos endereços IP do Batch para os nós do conjunto. No entanto, se a sub-rede especificada tiver NSGs associados e/ou uma firewall, configure as regras de segurança de entrada e saída conforme mostrado nas tabelas seguintes. Se a comunicação aos nós computacional na sub-rede especificada for negada por um NSG, o serviço Batch define o estado dos nós de computação para **inutilizáveis**.

Configure o tráfego de entrada na porta 3389 para Windows se tiver de permitir acesso RDP aos nós do conjunto. Isto não é necessário para que os nós da piscina sejam utilizáveis.

**Regras de segurança de entrada**

| Endereços IP de origem | Portas de origem | Destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- |
Qualquer <br /><br />Apesar de isto necessitar efetivamente de "permitir todos", o serviço Batch aplica uma regra ACL ao nível de cada nó que filtra todos os endereços IP de serviço não pertencentes ao Batch. | * | Qualquer | 10100, 20100, 30100 | TCP | Permitir |
| Opcionalmente, para permitir o acesso RDP aos nós de computação. | * | Qualquer | 3389 | TCP | Permitir |

**Regras de segurança de saída**

| Origem | Portas de origem | Destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- |
| Qualquer | * | Qualquer | 443  | Qualquer | Permitir |
