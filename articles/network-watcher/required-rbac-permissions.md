---
title: Permissões Azure RBAC necessárias para usar capacidades
titleSuffix: Azure Network Watcher
description: Saiba quais as permissões de controlo de acesso baseadas em funções do Azure necessárias para trabalhar com as capacidades do Observador de Rede.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 47418b9c5235255ff7dbf4a1a151e51e4c9aba58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019674"
---
# <a name="azure-role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Permissões de controlo de acesso baseadas em funções Azure necessárias para utilizar as capacidades do Observador de Rede

O controlo de acesso baseado em funções (Azure RBAC) permite-lhe atribuir apenas as ações específicas aos membros da sua organização que necessitam para completar as suas responsabilidades atribuídas. Para utilizar as capacidades do Network Watcher, a conta com a qual inicia sessão no Azure, deve ser atribuída ao [Proprietário,](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner) [Contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)ou [colaborador da Rede,](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) ou atribuída a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) que seja atribuída às ações listadas para cada capacidade do Observador de Rede nas secções que se seguem. Para saber mais sobre as capacidades do Network Watcher, veja [o que é o Network Watcher?](network-watcher-monitoring-overview.md)

## <a name="network-watcher"></a>Observador de Rede

| Ação                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Obter um observador de rede                                          |
| Microsoft.Network/networkWatchers/write                             | Criar ou atualizar um observador de rede                             |
| Microsoft.Network/networkWatchers/delete                            | Excluir um observador de rede                                       |

## <a name="nsg-flow-logs"></a>Registos do fluxo do NSG

| Ação                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Configure um registo de fluxo                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Estado de consulta para um registo de fluxo                                    |

## <a name="connection-troubleshoot"></a>Resolução de problemas de ligação

| Ação                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/conectividadeCheck/action          | Iniciar um teste de resolução de problemas de ligação
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Resultados da consulta de um teste de resolução de problemas de ligação                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Executar um teste de resolução de problemas de conexão                             |

## <a name="connection-monitor"></a>Monitor de ligação

| Ação                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Iniciar um monitor de ligação                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Parar um monitor de ligação                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/consulta/ação   | Consultar um monitor de ligação                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Obtenha um monitor de ligação                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Criar um monitor de ligação                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Eliminar um monitor de ligação                                    |

## <a name="packet-capture"></a>Captura de pacotes

| Ação                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Consultar o estado de uma captura de pacotes                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Pare uma captura de pacotes                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Obtenha uma captura de pacotes                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Criar uma captura de pacotes                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Excluir uma captura de pacotes                                        |

## <a name="ip-flow-verify"></a>Verificação do fluxo de IP

| Ação                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Verifique um fluxo IP                                              |

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
| Microsoft.Network/networkWatchers/topology/action                   | Obter topologia                                                   |
| Microsoft.Network/networkWatchers/topology/read                     | Mesmo que acima                                                  |

## <a name="reachability-report"></a>Relatório de alcance

| Ação                                                              | Descrição                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Obtenha um relatório de alcance Azure                               |


## <a name="additional-actions"></a>Ações adicionais

As capacidades do Network Watcher também requerem as seguintes ações:

| Ações                                                           | Description                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft.Autorização/ \* /Ler                                     | Usado para buscar atribuições de funções azure e definições de política          |
| Microsoft.Resources/subscrições/resourceGroups/Read               | Usado para enumerar todos os grupos de recursos numa subscrição    |
| Microsoft.Storage/storageAcontas/Ler                              | Usado para obter as propriedades para a conta de armazenamento especificado   |
| Microsoft.Storage/storageAcounts/listServiceSas/Action, </br> Microsoft.Storage/storageAcounts/listAccountSas/Action, <br> Microsoft.Storage/storageAcontas/listKeys/Action| Usado para obter assinaturas de acesso compartilhado (SAS) permitindo [acesso seguro à conta de armazenamento](../storage/common/storage-sas-overview.md) e escrever na conta de armazenamento |
| Microsoft.Compute/virtualMachines/Read, </br> Microsoft.Compute/virtualMachines/Write| Usado para iniciar sessão no VM, fazer uma captura de pacote e carregá-lo para a conta de armazenamento|
| Microsoft.Compute/virtualMachines/extensões/Ler </br> Microsoft.Compute/virtualMachines/extensions/Write| Usado para verificar se a extensão do Observador de Rede está presente, e instalar se necessário |
| Microsoft.Compute/virtualMachineScaleSets/Read, </br> Microsoft.Compute/virtualMachineScaleSets/Write| Usado para aceder a conjuntos de escala de máquina virtual, fazer capturas de pacotes e carregá-los para conta de armazenamento|
| Microsoft.Compute/virtualMachineScaleSets/extensions/Read, </br> Microsoft.Compute/virtualMachineScaleSets/extensions/Write| Usado para verificar se a extensão do Observador de Rede está presente, e instalar se necessário |
| Microsoft.Insights/alertRules/*                                     | Usado para configurar alertas métricos                                     |
| Microsoft.Support/*                                                 | Usado para criar e atualizar bilhetes de suporte do Network Watcher |