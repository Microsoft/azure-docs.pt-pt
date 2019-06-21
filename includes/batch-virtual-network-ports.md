---
title: incluir ficheiro
description: incluir ficheiro
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 04/10/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 711b662c35b5f8fec96f1edee765696bc1028bf8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184611"
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

**Recursos de rede adicionais** - o Batch aloca automaticamente recursos de rede adicionais no grupo de recursos que contém a VNet. Para cada 50 nós dedicados (ou cada 20 nós de baixa prioridade), o Batch aloca: o grupo de segurança de 1 de rede (NSG), 1 endereço IP público e 1 Balanceador de carga. Estes recursos estão limitados pelas [quotas de recursos](../articles/azure-subscription-service-limits.md) da subscrição. Para conjuntos grandes pode ter de pedir um aumento de quota para um ou mais destes recursos.

#### <a name="network-security-groups"></a>Grupos de segurança de rede

A sub-rede tem de permitir comunicação de entrada a partir do serviço Batch para conseguir agendar tarefas nos nós de computação e comunicação de saída para comunicar com o Armazenamento do Azure ou outros recursos. Para conjuntos na configuração de Máquina Virtual, o Batch adiciona NSGs ao nível das interfaces de rede (NICs) ligadas às VMs. Estes NSGs configuram automaticamente regras de entrada e saída para permitir o tráfego seguinte:

* Tráfego TCP de entrada nas portas 29876 e 29877 a partir de endereços IP de função do serviço Batch. 
* Tráfego TCP de entrada nas portas 22 (nós do Linux) ou 3389 (nós do Windows) para permitir acesso remoto. Para determinados tipos de tarefas de várias instâncias no Linux (por exemplo, MPI), precisará também permitem o tráfego de porta 22 de SSH para IPs na sub-rede que contém os nós de computação do Batch.
* Tráfego de saída em qualquer porta para a rede virtual.
* Tráfego de saída em qualquer porta para a Internet.

> [!IMPORTANT]
> Tenha cuidado se modificar ou adicionar regras de entrada ou saída em NSGs configurados pelo Batch. Se a comunicação com os nós de computação na sub-rede especificada for recusada por um NSG, o serviço Batch define o estado dos nós de computação como **inutilizável**.

Não é necessário especificar NSGs ao nível da sub-rede porque o Batch configura o seus próprios NSGs. No entanto, se a sub-rede especificada tiver Grupos de Segurança de Rede (NSGs) associados e/ou uma firewall, configure as regras de segurança de entrada e saída conforme mostrado nas tabelas seguintes. Configure o tráfego de entrada na porta 3389 (Windows) ou 22 (Linux), apenas se tiver de permitir o acesso remoto para as VMs de conjunto provenientes de fontes externas. Não é necessário para as VMs do conjunto serem utilizáveis. Tenha em atenção que será necessário permitir tráfego de sub-rede de rede virtual na porta 22 para Linux, se utilizar determinados tipos de tarefas de várias instâncias, como MPI.

**Regras de segurança de entrada**

| Endereços IP de origem | Etiqueta de serviço de origem | Portas de origem | Destino | Portas de destino | Protocol | Ação |
| --- | --- | --- | --- | --- | --- | --- |
| N/A | `BatchNodeManagement` [Etiqueta de serviço](../articles/virtual-network/security-overview.md#service-tags) | * | Qualquer | 29876-29877 | TCP | Permitir |
| Utilizador IPs de origem para aceder remotamente a nós de computação e/ou a sub-rede de nó de computação para tarefas de várias instâncias do Linux, se necessário. | N/A | * | Qualquer | 3389 (Windows), 22 (Linux) | TCP | Permitir |

**Regras de segurança de saída**

| source | Portas de origem | Destino | Etiqueta do serviço de destino | Protocol | Ação |
| --- | --- | --- | --- | --- | --- |
| Qualquer | 443 | [Etiqueta de serviço](../articles/virtual-network/security-overview.md#service-tags) | `Storage` (na mesma região que a sua conta do Batch e a VNet)  | Qualquer | Permitir |

### <a name="pools-in-the-cloud-services-configuration"></a>Conjuntos na configuração dos Serviços Cloud

**VNets suportadas** - Apenas VNets clássicas

**ID de sub-rede** - quando especificar a sub-rede com as APIs do Batch, utilize o *identificador de recurso* da sub-rede. O identificador da sub-rede tem o formato:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicVirtualNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Permissões**  - O principal de serviço `MicrosoftAzureBatch` tem de ter a função de Controlo de Acesso Baseado em Funções (RBAC) `Classic Virtual Machine Contributor` na VNet especificada.

#### <a name="network-security-groups"></a>Grupos de segurança de rede

A sub-rede tem de permitir comunicação de entrada a partir do serviço Batch para conseguir agendar tarefas nos nós de computação e comunicação de saída para comunicar com o Armazenamento do Azure ou outros recursos.

Não é necessário especificar um NSG, porque o Batch configura a comunicação de entrada apenas a partir dos endereços IP do Batch para os nós do conjunto. No entanto, se a sub-rede especificada tiver NSGs associados e/ou uma firewall, configure as regras de segurança de entrada e saída conforme mostrado nas tabelas seguintes. Se a comunicação com os nós de computação na sub-rede especificada for recusada por um NSG, o serviço Batch define o estado dos nós de computação como **inutilizável**.

Configure o tráfego de entrada na porta 3389 para Windows se tiver de permitir o acesso do RDP para nós do conjunto. Não é necessário para os nós do conjunto serem utilizáveis.

**Regras de segurança de entrada**

| Endereços IP de origem | Portas de origem | Destino | Portas de destino | Protocol | Ação |
| --- | --- | --- | --- | --- | --- |
Qualquer <br /><br />Apesar de isto necessitar efetivamente de "permitir todos", o serviço Batch aplica uma regra ACL ao nível de cada nó que filtra todos os endereços IP de serviço não pertencentes ao Batch. | * | Qualquer | 10100, 20100, 30100 | TCP | Permitir |
| Opcional, para permitir o acesso RDP em nós de computação. | * | Qualquer | 3389 | TCP | Permitir |

**Regras de segurança de saída**

| source | Portas de origem | Destino | Portas de destino | Protocol | Ação |
| --- | --- | --- | --- | --- | --- |
| Qualquer | * | Qualquer | 443  | Qualquer | Permitir |
