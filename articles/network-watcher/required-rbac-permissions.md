---
title: Permissões necessárias para utilizar as capacidades do observador de rede do Azure | Documentos da Microsoft
description: Saiba quais as permissões de controlo de acesso baseado em função do Azure são necessários para trabalhar com as capacidades do observador de rede.
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
ms.openlocfilehash: 8c8fe6125d9c638fedadc3d299ff0ac0d601fd61
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64685699"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Permissões de controlo de acesso baseado em funções necessárias para utilizar as capacidades do observador de rede

Controlo de acesso baseado em função do Azure (RBAC) permite-lhe atribuir apenas as ações específicas para membros da sua organização que necessitam para concluir as suas responsabilidades atribuídas. Para utilizar as capacidades do observador de rede, deve ser atribuída a conta iniciar sessão para o Azure, para o [proprietário](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor), ou [Contribuidor de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) funções incorporadas, ou atribuído a um [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) atribuída as ações listadas para cada recurso de observador de rede nas secções que se seguem. Para saber mais sobre as funcionalidades do observador de rede, veja [o que é o observador de rede?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Observador de Rede

| Ação                                                              | Name                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Obter um observador de rede                                          |
| Microsoft.Network/networkWatchers/write                             | Criar ou atualizar um observador de rede                             |
| Microsoft.Network/networkWatchers/delete                            | Eliminar um observador de rede                                       |

## <a name="nsg-flow-logs"></a>Registos de fluxo NSG

| Ação                                                              | Name                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Configurar um fluxo de registo                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Consultar o estado para um registo de fluxo                                    |

## <a name="connection-troubleshoot"></a>Resolver problemas de ligação

| Ação                                                              | Name                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | Iniciar uma ligação de teste de resolução de problemas
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Resolver problemas relacionados com resultados da consulta de uma ligação de teste                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Resolução de problemas de execução de uma ligação de teste                             |

## <a name="connection-monitor"></a>Monitor de ligação

| Ação                                                              | Name                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Iniciar um monitor de ligação                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Parar um monitor de ligação                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Um monitor de ligação de consulta                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Obter um monitor de ligação                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Criar um monitor de ligação                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Eliminar um monitor de ligação                                    |

## <a name="packet-capture"></a>Captura de pacotes

| Ação                                                              | Name                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Consultar o estado de uma captura de pacotes                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Parar uma captura de pacotes                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Obtenha uma captura de pacotes                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | criar uma captura de pacotes                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | eliminar uma captura de pacotes                                        |

## <a name="ip-flow-verify"></a>Verificação do fluxo de IP

| Ação                                                              | Name                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Certifique-se de um fluxo de IP                                              |

## <a name="next-hop"></a>Próximo salto

| Ação                                                              | Name                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | Obter o salto seguinte a partir de uma VM                                     |

## <a name="network-security-group-view"></a>Vista de grupo de segurança de rede

| Ação                                                              | Name                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Ver grupos de segurança                                           |

## <a name="topology"></a>Topologia

| Ação                                                              | Name                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | Obter a topologia                                                   |

## <a name="reachability-report"></a>Relatório de acessibilidade

| Ação                                                              | Name                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Obter um relatório de acessibilidade do Azure                               |

## <a name="additional-actions"></a>Ações adicionais

Capacidades do observador de rede também exigem as seguintes ações:

- Microsoft.Authorization/\*/Read
- Microsoft.Resources/subscriptions/resourceGroups/Read
- Microsoft.Storage/storageAccounts/Read
- Microsoft.Storage/storageAccounts/listServiceSas/Action
- Microsoft.Storage/storageAccounts/listAccountSas/Action
- Microsoft.Storage/storageAccounts/listKeys/Action
- Microsoft.Compute/virtualMachines/Read
- Microsoft.Compute/virtualMachines/Write
- Microsoft.Compute/virtualMachines/extensions/Read
- Microsoft.Compute/virtualMachines/extensions/Write
- Microsoft.Compute/virtualMachineScaleSets/Read
- Microsoft.Compute/virtualMachineScaleSets/Write
- Microsoft.Compute/virtualMachineScaleSets/extensions/Read
- Microsoft.Compute/virtualMachineScaleSets/extensions/Write
- Microsoft.Insights/alertRules/*
- Microsoft.Support/*
