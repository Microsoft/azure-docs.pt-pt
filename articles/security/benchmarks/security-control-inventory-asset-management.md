---
title: Controlo de Segurança Azure - Inventário e Gestão de Ativos
description: Inventário de Controlo de Segurança e Gestão de Ativos
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 2f24012a69a7673426644a8ded6162cd1021938e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75930067"
---
# <a name="security-control-inventory-and-asset-management"></a>Controlo de Segurança: Inventário e Gestão de Ativos

As recomendações de Inventário e Gestão de Ativos focam-se na abordagem de questões relacionadas com a gestão ativa (inventário, pista e correto) de todos os recursos do Azure para que apenas os recursos autorizados tenham acesso, e os recursos não autorizados e não geridos são identificado e removido.

## <a name="61-use-azure-asset-discovery"></a>6.1: Utilizar a Descoberta de Ativos Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.1 | 1.1, 1.2, 1.3, 1.4, 9.1, 12.1 | Cliente |

Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua subscrição(s).  Certifique-se de permissões (ler) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Resource Graph, é altamente recomendado criar e utilizar recursos do Gestor de Recursos Azure para avançar.

Como criar consultas com o Gráfico de Recursos Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como visualizar as suas Assinaturas Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Compreender o Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

## <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.2 | 1.5 | Cliente |

Aplique etiquetas nos recursos do Azure dando metadados para logicamente organizá-los numa taxonomia.

Como criar e utilizar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos Azure não autorizados

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.3 | 1.6 | Cliente |

Utilize a marcação, os grupos de gestão e as assinaturas separadas, se for caso disso, para organizar e rastrear ativos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição atempadamente.

Como criar subscrições adicionais do Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:

https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e utilizar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos títulos aprovados de recursos e software do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.4 | 2.1 | Cliente |

Defina os recursos aprovados do Azure e o software aprovado para os recursos da computação.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para os recursos Azure não aprovados

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.5 | 2.3, 2.4 | Cliente |

Utilize a Política Azure para colocar restrições ao tipo de recursos que podem ser criados na sua subscrição.

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição. &nbsp;Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

Como configurar e gerir a Política Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos do cálculo

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.6 | 2.3/2.4 | Cliente |

Utilize o inventário da máquina virtual Azure para automatizar a recolha de informações sobre todo o software em Máquinas Virtuais. O tempo de nome, versão, editor e refresh do software está disponível no portal Azure. Para ter acesso à instalação de datas e outras informações, ative diagnósticos ao nível dos hóspedes e traga os Registos de Eventos do Windows para um Espaço de Trabalho de Log Analytics.

Como ativar o inventário da máquina virtual Azure:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos não aprovados do Azure e aplicações de software

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.7 | 2.5 | Cliente |

Utilize a monitorização da integridade do ficheiro do Azure Security Center (Change Tracking) e o inventário de máquinas virtuais para identificar todo o software instalado em Máquinas Virtuais. Pode implementar o seu próprio processo para remover software não autorizado. Também pode utilizar uma solução de terceiros para identificar software não aprovado.

Como utilizar a Monitorização da Integridade do Ficheiro:

https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring

Compreender o rastreio de mudanças azure:

https://docs.microsoft.com/azure/automation/change-tracking

Como ativar o inventário de máquinas virtuais Azure:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="68-use-only-approved-applications"></a>6.8: Utilize apenas pedidos aprovados

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.8 | 2,6 | Cliente |

Utilize controlos de aplicações adaptativas do Azure Security Center para garantir que apenas o software autorizado executa e todo o software não autorizado está bloqueado de executar em Máquinas Virtuais Azure.

Como utilizar os controlos adaptivos de aplicações adaptivos do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas os serviços Azure aprovados

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.9 | 2,6 | Cliente |

Utilize a Política Azure para restringir os serviços que pode fornecer no seu ambiente.

Como configurar e gerir a Política Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo específico de recurso com a Política Azure:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

## <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.1 | 2.7 | Cliente |

Utilize controlos de aplicação adaptativas do Centro de Segurança Azure para especificar a que tipos de ficheiros uma regra pode ou não aplicar- se.

Implementar solução de terceiros se tal não cumprir o requisito.

Como utilizar os controlos adaptivos de aplicações adaptivos do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11: Limitar a capacidade dos utilizadores de interagir com o Gestor de Recursos Azure através de scripts

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.11 | 2,8 | Cliente |

Utilize o Acesso Condicional Azure para limitar a capacidade dos &quot;utilizadores&quot; de &quot;interagirem&quot; com o Gestor de Recursos Azure, configurando o acesso ao Bloco para a App de Gestão Microsoft Azure.

Como configurar o Acesso Condicional ao acesso ao portal Azure Resource Manager:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacionais

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.12 | 2,8 | Cliente |

Utilize configurações específicas do sistema operativo ou recursos de terceiros para limitar a capacidade dos utilizadores de executarscripts dentro dos recursos computacionais do Azure.

Por exemplo, como controlar a execução do script PowerShell em Ambientes Windows:

https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar fisicamente ou logicamente aplicações de alto risco

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.13 | 2.9 | Cliente |

O software que é necessário para operações comerciais, mas que pode incorrer em maior risco para a organização, deve ser isolado dentro da sua própria máquina virtual e/ou rede virtual e suficientemente seguro com um Azure Firewall ou Um Grupo de Segurança de Rede.

Como criar uma rede virtual:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Como criar um NSG com um config de segurança:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="next-steps"></a>Passos seguintes

Consulte o próximo controlo de segurança: [Configuração segura](security-control-secure-configuration.md)
