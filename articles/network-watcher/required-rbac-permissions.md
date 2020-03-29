---
title: Permissões RBAC necessárias para usar capacidades
titleSuffix: Azure Network Watcher
description: Saiba quais as permissões de controlo de acesso baseadas em papéis do Azure para trabalhar com as capacidades do Observador de Rede.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: damendo
ms.openlocfilehash: f8743f19d6cd262ad140659be55a4fc57e842564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840558"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Permissões de controlo de acesso baseadas em funções necessárias para utilizar capacidades de Observador de Rede

O controlo de acesso baseado em funções azure (RBAC) permite-lhe atribuir apenas as ações específicas aos membros da sua organização que necessitam para completar as suas responsabilidades atribuídas. Para utilizar as capacidades do Observador de Rede, a conta com que inicia sessão no Azure deve ser atribuída ao [Proprietário,](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner) [Colaborador](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)ou Colaborador da [Rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) em butino ou atribuída a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) que seja atribuída às ações listadas para cada capacidade do Observador de Rede nas secções seguintes. Para saber mais sobre as capacidades do Observador da Rede, veja o que é o Observador da [Rede?](network-watcher-monitoring-overview.md)

## <a name="network-watcher"></a>Observador de Rede

| Ação                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Obter um observador de rede                                          |
| Microsoft.Network/networkWatchers/write                             | Criar ou atualizar um observador de rede                             |
| Microsoft.Network/networkWatchers/delete                            | Eliminar um observador de rede                                       |

## <a name="nsg-flow-logs"></a>Registos do fluxo do NSG

| Ação                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Configure um registo de fluxo                                           |
| Microsoft.Network/networkWatchers/consultaFlowLogStatus/action         | Estado de consulta para um registo de fluxo                                    |

## <a name="connection-troubleshoot"></a>Resolução de problemas de ligação

| Ação                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | Iniciar um teste de resolução de problemas de ligação
| Microsoft.Network/networkWatchers/consultaTroubleshootResult/action    | Resultados da consulta de um teste de resolução de problemas de ligação                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Executar um teste de resolução de problemas de ligação                             |

## <a name="connection-monitor"></a>Monitor de ligação

| Ação                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Inicie um monitor de ligação                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Parar um monitor de ligação                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/consulta/ação   | Consulta de um monitor de ligação                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Obtenha um monitor de ligação                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Criar um monitor de ligação                                    |
| Microsoft.Network/NetworkWatchers/connectionMonitors/delete         | Eliminar um monitor de ligação                                    |

## <a name="packet-capture"></a>Captura de pacotes

| Ação                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/consultaStatus/action | Consulta do estado de uma captura de pacote                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Parar uma captura de pacote                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Obtenha uma captura de pacote                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Criar uma captura de pacote                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Eliminar uma captura de pacote                                        |

## <a name="ip-flow-verify"></a>Verificação do fluxo de IP

| Ação                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowCheck/action               | Verifique um fluxo ip                                              |

## <a name="next-hop"></a>Próximo salto

| Ação                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | Obtenha o próximo salto de um VM                                     |

## <a name="network-security-group-view"></a>Vista de grupo de segurança de rede

| Ação                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Ver grupos de segurança                                           |

## <a name="topology"></a>Topologia

| Ação                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topologia/ação                   | Obter topologia                                                   |

## <a name="reachability-report"></a>Relatório de alcance

| Ação                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Obtenha um relatório de alcance do Azure                               |


## <a name="additional-actions"></a>Ações adicionais

As capacidades do Observador de Rede também requerem as seguintes ações:

| Ação(s)                                                           | Descrição                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft.Authorization/\*/Read                                     | Usado para buscar atribuições de papel RBAC e definições políticas          |
| Microsoft.Recursos/subscrições/recursosGroups/Read               | Usado para enumerar todos os grupos de recursos em uma subscrição    |
| Microsoft.Storage/storageAccounts/Read                              | Usado para obter as propriedades para a conta de armazenamento especificada   |
| Microsoft.Storage/storageAccounts/listServiceSas/Action, </br> Microsoft.Storage/storageAccounts/listAccountSas/Action, <br> Microsoft.Storage/storageAccounts/listKeys/Action| Usado para obter assinaturas de acesso partilhado (SAS) permitindo [o acesso seguro à conta](https://docs.microsoft.com/azure/storage/common/storage-sas-overview) de armazenamento e escrever na conta de armazenamento |
| Microsoft.Compute/virtualMachines/Read, </br> Microsoft.Compute/virtualMachines/Write| Usado para iniciar sessão no VM, fazer uma captura de pacote e carregá-lo para a conta de armazenamento|
| Microsoft.Compute/virtualMachines/extensions/Read </br> Microsoft.Compute/virtualMachines/extensions/Write| Usado para verificar se a extensão do Observador de Rede está presente e instalar se necessário |
| Microsoft.Compute/virtualMachineScaleSets/Read, </br> Microsoft.Compute/virtualMachineScaleSets/Write| Usado para aceder a conjuntos de escala de máquinas virtuais, fazer capturas de pacotes e carregá-los para conta de armazenamento|
| Microsoft.Compute/virtualMachineScaleSets/extensões/Read, </br> Microsoft.Compute/virtualMachineScaleSets/extensões/Write| Usado para verificar se a extensão do Observador de Rede está presente e instalar se necessário |
| Microsoft.Insights/alertRules/*                                     | Usado para configurar alertas métricos                                     |
| Microsoft.Support/*                                                 | Usado para criar e atualizar bilhetes de suporte do Network Watcher |
