---
title: incluir ficheiro
description: incluir ficheiro
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 04/03/2020
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: dc08dcded6418208751edbffcb5d263db059ec01
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657489"
---
### <a name="general-requirements"></a>Requisitos gerais

* A VNet tem de estar na mesma subscrição e região da conta do Batch utilizada para criar o conjunto.

* O conjunto que utiliza a VNet pode ter um máximo de 4096 nós.

* A sub-rede especificada para o conjunto deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs direcionadas para o conjunto; ou seja, a soma de propriedades `targetDedicatedNodes` e `targetLowPriorityNodes` do conjunto. Se a sub-rede não tiver endereços IP não atribuídos suficientes, o conjunto atribui parcialmente os nós de computação e ocorre um erro de redimensionamento.

* O ponto final do Armazenamento do Azure tem de ser resolvido por qualquer servidor DNS personalizado que sirva a sua VNet. Mais concretamente, devem ser resolvíveis os URLs no formato `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` e `<account>.blob.core.windows.net`.

Os requisitos adicionais de VNet diferem, consoante o conjunto do Batch está na configuração da Máquina Virtual ou na configuração dos Serviços Cloud. Para novas implementações de conjuntos numa VNet, é recomendada a configuração de Máquina Virtual.

### <a name="pools-in-the-virtual-machine-configuration"></a>Conjuntos na configuração de Máquina Virtual

**VNets suportadas** - apenas VNets baseadas no Azure Resource Manager

**ID de sub-rede** - quando especificar a sub-rede com as APIs do Batch, utilize o *identificador de recurso* da sub-rede. O identificador da sub-rede tem o formato:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}
  ```

**Permissões** - verifique se as suas políticas de segurança ou bloqueios na subscrição ou no grupo de recursos da VNet restringem as permissões de um utilizador para gerir a VNet.

**Recursos de rede adicionais** - o Batch aloca automaticamente recursos de rede adicionais no grupo de recursos que contém a VNet.

> [!IMPORTANT]
>Por cada 50 nós dedicados (ou cada 20 nós de baixa prioridade), o Lote atribui: um grupo de segurança de rede (NSG), um endereço IP público e um equilibrador de carga. Estes recursos estão limitados pelas [quotas de recursos](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) da subscrição. Para grandes piscinas, você pode precisar de solicitar um aumento de quota para um ou mais destes recursos.

#### <a name="network-security-groups-batch-default"></a>Grupos de segurança da rede: Padrão de lote

A subrede deve permitir que a comunicação de entrada do serviço Batch seja capaz de agendar tarefas nos nós de computação e comunicação de saída para comunicar com o Armazenamento Azure ou outros recursos, conforme necessário pela sua carga de trabalho. Para piscinas na configuração da Máquina Virtual, o Batch adiciona NSGs ao nível das interfaces de rede (NICs) ligados aos nós de computação. Estes NSGs são configurados com as seguintes regras adicionais:

* Tráfego de TCP de entrada nos portos 29876 e 29877 `BatchNodeManagement` dos endereços IP do serviço de lote que correspondem à etiqueta de serviço.
* Tráfego TCP de entrada nas portas 22 (nós do Linux) ou 3389 (nós do Windows) para permitir acesso remoto. Para certos tipos de tarefas multiinstâncias no Linux (como MPI), terá também de permitir o tráfego da porta SSH 22 para iPs na subnet que contém os nódosos computacionais do Batch. Isto pode ser bloqueado por regras nsg de nível subnet (ver abaixo).
* Tráfego de saída em qualquer porta para a rede virtual. Isto pode ser alterado de acordo com as regras nsg de nível subnet (ver abaixo).
* Tráfego de saída em qualquer porto para a Internet. Isto pode ser alterado de acordo com as regras nsg de nível subnet (ver abaixo).

> [!IMPORTANT]
> Tenha cuidado se modificar ou adicionar regras de entrada ou saída em NSGs configurados pelo Batch. Se a comunicação com os nós de computação na sub-rede especificada for recusada por um NSG, o serviço Batch define o estado dos nós de computação como **inutilizável**. Além disso, não devem ser aplicados bloqueios de recursos a qualquer recurso criado pelo Batch, caso contrário, isso pode resultar na prevenção da limpeza de recursos em resultado de ações iniciadas pelo utilizador, tais como a exclusão de uma piscina.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Grupos de segurança da rede: Especificar regras de nível de subnet

Não é necessário especificar NSGs ao nível da subnet de rede virtual porque o Batch configura os seus próprios NSGs (ver acima). Se tiver um NSG associado à subnet onde os nós de computação do Lote são implantados ou quiser aplicar regras de NSG personalizadas para anular os incumprimentos aplicados, então deve configurar este NSG com, pelo menos, as regras de segurança de entrada e saída, conforme mostrado nas tabelas seguintes.

Configure o tráfego de entrada na porta 3389 (Windows) ou 22 (Linux) apenas se precisar de permitir o acesso remoto aos nós de computação de fontes externas. Pode ser necessário ativar as regras da porta 22 no Linux se necessitar de apoio para tarefas de várias instâncias com determinados tempos de execução de MPI. Permitir que o tráfego nestas portas não seja estritamente necessário para que os nós de computação da piscina sejam utilizáveis.

**Regras de segurança de entrada**

| Endereços IP de origem | Etiqueta de serviço de origem | Portas de origem | Destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- | --- |
| N/D | `BatchNodeManagement`[Etiqueta de serviço](../articles/virtual-network/security-overview.md#service-tags) (se utilizar variante regional, na mesma região que a sua conta Batch) | * | Qualquer | 29876-29877 | TCP | Permitir |
| IPs de fonte de utilizador para aceder remotamente a nós de cálculo e/ou subnet de nó de computação para tarefas de várias instâncias do Linux, se necessário. | N/D | * | Qualquer | 3389 (Windows), 22 (Linux) | TCP | Permitir |

> [!WARNING]
> Os endereços IP do serviço de lote podem ser alterados ao longo do tempo. Por isso, é altamente `BatchNodeManagement` recomendado utilizar a etiqueta de serviço (ou variante regional) para as regras do NSG. Não é aconselhável preencher as regras do NSG com endereços IP do serviço de lote diretamente.

**Regras de segurança de saída**

| Origem | Portas de origem | Destino | Etiqueta do serviço de destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- | --- |
| Qualquer | * | [Etiqueta de serviço](../articles/virtual-network/security-overview.md#service-tags) | `Storage`(se utilizar a variante regional, na mesma região que a sua conta Batch) | 443 | TCP | Permitir |

### <a name="pools-in-the-cloud-services-configuration"></a>Conjuntos na configuração dos Serviços Cloud

**VNets suportadas** - Apenas VNets clássicas

**ID de sub-rede** - quando especificar a sub-rede com as APIs do Batch, utilize o *identificador de recurso* da sub-rede. O identificador da sub-rede tem o formato:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Permissões ** - O principal de serviço `Microsoft Azure Batch` tem de ter a função de Controlo de Acesso Baseado em Funções (RBAC) `Classic Virtual Machine Contributor` na VNet especificada.

#### <a name="network-security-groups"></a>Grupos de segurança de rede

A sub-rede tem de permitir comunicação de entrada a partir do serviço Batch para conseguir agendar tarefas nos nós de computação e comunicação de saída para comunicar com o Armazenamento do Azure ou outros recursos.

Não é necessário especificar um NSG, porque o Batch configura a comunicação de entrada apenas a partir dos endereços IP do Batch para os nós do conjunto. No entanto, se a sub-rede especificada tiver NSGs associados e/ou uma firewall, configure as regras de segurança de entrada e saída conforme mostrado nas tabelas seguintes. Se a comunicação com os nós de computação na sub-rede especificada for recusada por um NSG, o serviço Batch define o estado dos nós de computação como **inutilizável**.

Configure o tráfego de entrada na porta 3389 para Windows se precisar de permitir o acesso rdp aos nódosos da piscina. Não é necessário para os nós do conjunto serem utilizáveis.

**Regras de segurança de entrada**

| Endereços IP de origem | Portas de origem | Destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- |
Qualquer <br /><br />Apesar de isto necessitar efetivamente de "permitir todos", o serviço Batch aplica uma regra ACL ao nível de cada nó que filtra todos os endereços IP de serviço não pertencentes ao Batch. | * | Qualquer | 10100, 20100, 30100 | TCP | Permitir |
| Opcional, para permitir o acesso rdp aos nós de computação. | * | Qualquer | 3389 | TCP | Permitir |

**Regras de segurança de saída**

| Origem | Portas de origem | Destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- |
| Qualquer | * | Qualquer | 443  | Qualquer | Permitir |
