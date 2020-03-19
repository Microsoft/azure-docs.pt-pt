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
ms.date: 03/04/2020
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: e9460108499ca76d1b149b61cebe3d3081bf6544
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086282"
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
>Para cada 50 nós dedicados (ou cada 20 nós de baixa prioridade), o Batch aloca um grupo de segurança de rede (NSG), um endereço IP público e um balanceador de carga. Estes recursos estão limitados pelas [quotas de recursos](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) da subscrição. Para conjuntos grandes, poderá ter de pedir um aumento de quota para um ou mais destes recursos.

#### <a name="network-security-groups-batch-default"></a>Grupos de segurança de rede: Predefinição do Batch

A sub-rede tem de permitir comunicação de entrada a partir do serviço Batch para conseguir agendar tarefas nos nós de computação e comunicação de saída para comunicar com o Armazenamento do Azure ou outros recursos, conforme necessário pela sua carga de trabalho. Para conjuntos na configuração de Máquina Virtual, o Batch adiciona NSGs ao nível das interfaces de rede (NICs) ligadas aos nós de computação. Estes NSGs são configurados com as regras adicionais seguintes:

* Tráfego TCP de entrada nas portas 29876 e 29877 a partir dos endereços IP do serviço Batch que correspondem à etiqueta de serviço `BatchNodeManagement`.
* Tráfego TCP de entrada nas portas 22 (nós do Linux) ou 3389 (nós do Windows) para permitir acesso remoto. Para certos tipos de tarefas multi-instâncias em Linux (como MPI), terá também de permitir o tráfego da porta SSH 22 para IPs na sub-rede que contém os nós de computação do Batch. As regras de NSG ao nível da sub-rede podem impedir esta situação (veja abaixo).
* Tráfego de saída em qualquer porta para a rede virtual. As regras de NSG ao nível da sub-rede podem corrigir esta situação (veja abaixo).
* Tráfego de saída em qualquer porta para a Internet. As regras de NSG ao nível da sub-rede podem corrigir esta situação (veja abaixo).

> [!IMPORTANT]
> Tenha cuidado se modificar ou adicionar regras de entrada ou saída em NSGs configurados pelo Batch. Se a comunicação com os nós de computação na sub-rede especificada for recusada por um NSG, o serviço Batch define o estado dos nós de computação como **inutilizável**. Além disso, não devem ser aplicados bloqueios de recursos a nenhum recurso criado pelo Batch, pois podem impedir a limpeza dos recursos como resultado de ações iniciadas pelo utilizador, como a eliminação de um conjunto.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Grupos de segurança de rede: Especificar regras ao nível da sub-rede

Não é necessário especificar NSGs ao nível da sub-rede da rede virtual porque o Batch configura os seus próprios NSGs (veja acima). Se tiver um NSG associado à sub-rede onde os nós de computação do Batch estão implementados ou se pretender aplicar regras de NSG personalizadas para substituir as predefinições aplicadas, tem de configurar este NSG com, pelo menos, as regras de segurança de entrada e saída, conforme mostrado nas tabelas seguintes.

Configure o tráfego de entrada na porta 3389 (Windows) ou 22 (Linux) apenas se tiver de permitir acesso remoto aos nós de computação fora das origens. Se precisar de suporte para tarefas de multi-instâncias com determinados runtimes MPI, poderá ter de ativar as regras da porta 22 no Linux. Não é estritamente obrigatório permitir o tráfego nestas portas para que os nós de computação do conjunto podem ser utilizáveis.

**Regras de segurança de entrada**

| Endereços IP de origem | Etiqueta de serviço de origem | Portas de origem | Destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- | --- |
| N/D | `BatchNodeManagement` [Etiqueta de serviço](../articles/virtual-network/security-overview.md#service-tags) (se estiver a ser utilizada uma variante regional, na mesma região da sua conta do Batch) | * | Qualquer | 29876-29877 | TCP | Permitir |
| IPs de origem do utilizador para aceder remotamente aos nós de computação e/ou à sub-rede dos nós de computação para tarefas de multi-instâncias em Linux, se necessário. | N/D | * | Qualquer | 3389 (Windows), 22 (Linux) | TCP | Permitir |

**Regras de segurança de saída**

| Origem | Portas de origem | Destino | Etiqueta do serviço de destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- | --- |
| Qualquer | * | [Etiqueta de serviço](../articles/virtual-network/security-overview.md#service-tags) | `Storage` (se estiver a ser utilizada uma variante regional, na mesma região da sua conta do Batch) | 443 | TCP | Permitir |

### <a name="pools-in-the-cloud-services-configuration"></a>Conjuntos na configuração dos Serviços Cloud

**VNets suportadas** - Apenas VNets clássicas

**ID de sub-rede** - quando especificar a sub-rede com as APIs do Batch, utilize o *identificador de recurso* da sub-rede. O identificador da sub-rede tem o formato:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Permissões**  - O principal de serviço `Microsoft Azure Batch` tem de ter a função de Controlo de Acesso Baseado em Funções (RBAC) `Classic Virtual Machine Contributor` na VNet especificada.

#### <a name="network-security-groups"></a>Grupos de segurança de rede

A sub-rede tem de permitir comunicação de entrada a partir do serviço Batch para conseguir agendar tarefas nos nós de computação e comunicação de saída para comunicar com o Armazenamento do Azure ou outros recursos.

Não é necessário especificar um NSG, porque o Batch configura a comunicação de entrada apenas a partir dos endereços IP do Batch para os nós do conjunto. No entanto, se a sub-rede especificada tiver NSGs associados e/ou uma firewall, configure as regras de segurança de entrada e saída conforme mostrado nas tabelas seguintes. Se a comunicação com os nós de computação na sub-rede especificada for recusada por um NSG, o serviço Batch define o estado dos nós de computação como **inutilizável**.

Configure o tráfego de entrada na porta 3389 para Windows se tiver de permitir acesso RDP aos nós do conjunto. Não é necessário para os nós do conjunto serem utilizáveis.

**Regras de segurança de entrada**

| Endereços IP de origem | Portas de origem | Destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- |
Qualquer <br /><br />Apesar de isto necessitar efetivamente de "permitir todos", o serviço Batch aplica uma regra ACL ao nível de cada nó que filtra todos os endereços IP de serviço não pertencentes ao Batch. | * | Qualquer | 10100, 20100, 30100 | TCP | Permitir |
| Opcionalmente, para permitir o acesso RDP aos nós de computação. | * | Qualquer | 3389 | TCP | Permitir |

**Regras de segurança de saída**

| Origem | Portas de origem | Destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- |
| Qualquer | * | Qualquer | 443  | Qualquer | Permitir |
