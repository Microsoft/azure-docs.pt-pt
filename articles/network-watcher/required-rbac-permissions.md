---
title: Permissões necessárias para usar os recursos do observador de rede do Azure | Microsoft Docs
description: Saiba quais permissões de controle de acesso baseado em função do Azure são necessárias para trabalhar com os recursos do observador de rede.
services: network-watcher
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: kumud
ms.openlocfilehash: 5bd7e30a6a95d60bda4b7c3da44be1b8046bb71f
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163800"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Permissões de controle de acesso baseado em função necessárias para usar os recursos do observador de rede

O RBAC (controle de acesso baseado em função) do Azure permite que você atribua apenas as ações específicas aos membros da sua organização que eles precisam para concluir suas responsabilidades atribuídas. Para usar os recursos do observador de rede, a conta que você faz logon no Azure, deve ser atribuída às funções internas de [proprietário](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [colaborador](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)ou [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) , ou atribuída a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) atribuída às ações listadas para cada recurso do observador de rede nas seções a seguir. Para saber mais sobre os recursos do observador de rede, consulte [o que é o observador de rede?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Observador de Rede

| Action                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Obter um observador de rede                                          |
| Microsoft.Network/networkWatchers/write                             | Criar ou atualizar um observador de rede                             |
| Microsoft.Network/networkWatchers/delete                            | Excluir um observador de rede                                       |

## <a name="nsg-flow-logs"></a>Registos do fluxo do NSG

| Action                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Configurar um log de fluxo                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Status da consulta para um log de fluxo                                    |

## <a name="connection-troubleshoot"></a>Resolver problemas de ligação

| Action                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | Iniciar um teste de solução de problemas de conexão
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Resultados da consulta de um teste de solução de problemas de conexão                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Executar um teste de solução de problemas de conexão                             |

## <a name="connection-monitor"></a>Monitor de ligação

| Action                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Iniciar um monitor de conexão                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Parar um monitor de conexão                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Consultar um monitor de conexão                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Obter um monitor de conexão                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Criar um monitor de ligação                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Excluir um monitor de conexão                                    |

## <a name="packet-capture"></a>Captura de pacotes

| Action                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Consultar o status de uma captura de pacote                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Parar uma captura de pacote                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Obter uma captura de pacote                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Criar uma captura de pacote                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Excluir uma captura de pacote                                        |

## <a name="ip-flow-verify"></a>Verificação do fluxo de IP

| Action                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Verificar um fluxo de IP                                              |

## <a name="next-hop"></a>Próximo salto

| Action                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | Obter o próximo salto de uma VM                                     |

## <a name="network-security-group-view"></a>Vista de grupo de segurança de rede

| Action                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Exibir grupos de segurança                                           |

## <a name="topology"></a>Topologia

| Action                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | Obter topologia                                                   |

## <a name="reachability-report"></a>Relatório de acessibilidade

| Action                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Obter um relatório de acessibilidade do Azure                               |


## <a name="additional-actions"></a>Ações adicionais

Os recursos do observador de rede também exigem as seguintes ações:

| Ação (ões)                                                           | Descrição                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft. Authorization/\*/Read                                     | Usado para buscar as atribuições de função e as definições de política do RBAC          |
| Microsoft.Resources/subscriptions/resourceGroups/Read               | Usado para enumerar todos os grupos de recursos em uma assinatura    |
| Microsoft.Storage/storageAccounts/Read                              | Usado para obter as propriedades da conta de armazenamento especificada   |
| Microsoft. Storage/storageAccounts/listServiceSas/Action, </br> Microsoft. Storage/storageAccounts/listAccountSas/Action, <br> Microsoft.Storage/storageAccounts/listKeys/Action| Usado para buscar SAS (assinaturas de acesso compartilhado), permitindo o [acesso seguro à conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-sas-overview) e gravar na conta de armazenamento |
| Microsoft. Compute/virtualMachines/Read, </br> Microsoft.Compute/virtualMachines/Write| Usado para fazer logon na VM, fazer uma captura de pacote e carregá-la na conta de armazenamento|
| Microsoft.Compute/virtualMachines/extensions/Read </br> Microsoft.Compute/virtualMachines/extensions/Write| Usado para verificar se a extensão do observador de rede está presente e instalar, se necessário |
| Microsoft. Compute/virtualMachineScaleSets/Read, </br> Microsoft.Compute/virtualMachineScaleSets/Write| Usado para acessar conjuntos de dimensionamento de máquinas virtuais, fazer capturas de pacote e carregá-los na conta de armazenamento|
| Microsoft. Compute/virtualMachineScaleSets/Extensions/Read, </br> Microsoft.Compute/virtualMachineScaleSets/extensions/Write| Usado para verificar se a extensão do observador de rede está presente e instalar, se necessário |
| Microsoft.Insights/alertRules/*                                     | Usado para configurar alertas de métricas                                     |
| Microsoft.Support/*                                                 | Usado para criar e atualizar tíquetes de suporte do observador de rede |
