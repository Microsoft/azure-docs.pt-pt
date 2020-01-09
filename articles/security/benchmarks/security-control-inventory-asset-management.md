---
title: Controle de segurança do Azure-inventário e gerenciamento de ativos
description: Inventário de controle de segurança e gerenciamento de ativos
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 95c1834e997caae13e650df5b588df898a1e361d
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564182"
---
# <a name="security-control-inventory-and-asset-management"></a>Controle de segurança: inventário e gerenciamento de ativos

As recomendações de gerenciamento de estoque e de ativos concentram-se em resolver problemas relacionados ao gerenciamento ativo (inventário, controle e correção) de todos os recursos do Azure para que apenas os recursos autorizados recebam acesso, e recursos não-autorizados e não gerenciados sejam identificado e removido.

## <a name="61-use-azure-asset-discovery"></a>6,1: usar a descoberta de ativos do Azure

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.1 | 1,1, 1,2, 1,3, 1,4, 9,1, 12,1 | Cliente |

Use o grafo de recursos do Azure para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas e protocolos, etc.) em suas assinaturas.  Verifique as permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do grafo de recursos, é altamente recomendável criar e usar Azure Resource Manager recursos no futuro.

Como criar consultas com o grafo de recursos do Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como exibir suas assinaturas do Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Noções básicas sobre o RBAC do Azure:

https://docs.microsoft.com/azure/role-based-access-control/overview

## <a name="62-maintain-asset-metadata"></a>6,2: manter metadados de ativo

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.2 | 1.5 | Cliente |

Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

Como criar e usar marcas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="63-delete-unauthorized-azure-resources"></a>6,3: excluir recursos do Azure não autorizados

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.3 | 1.6 | Cliente |

Use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e controlar ativos. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Como criar assinaturas adicionais do Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gerenciamento:

https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e usar marcas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: manter um inventário de recursos do Azure aprovados e títulos de software

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.4 | 2.1 | Cliente |

Defina os recursos aprovados do Azure e o software aprovado para recursos de computação.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorar os recursos do Azure não aprovados

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.5 | 2.3, 2.4 | Cliente |

Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas.

Use o grafo de recursos do Azure para consultar/descobrir recursos em suas assinaturas. &nbsp;garantir que todos os recursos do Azure presentes no ambiente sejam aprovados.

Como configurar e gerenciar Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o grafo do Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorar aplicativos de software não aprovados nos recursos de computação

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.6 | 2.3/2.4 | Cliente |

Use o inventário de máquina virtual do Azure para automatizar a coleta de informações sobre todos os softwares em máquinas virtuais. O nome do software, a versão, o Publicador e o tempo de atualização estão disponíveis no portal do Azure. Para obter acesso à data de instalação e outras informações, habilite o diagnóstico de nível de convidado e traga os logs de eventos do Windows para um espaço de trabalho Log Analytics.

Como habilitar o inventário de máquina virtual do Azure:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: remover os recursos do Azure e os aplicativos de software não aprovados

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.7 | 2.5 | Cliente |

Use o Controle de Alterações (monitoramento de integridade de arquivo) e o inventário de máquina virtual da central de segurança do Azure para identificar todos os softwares instalados nas máquinas virtuais. Você pode implementar seu próprio processo para remover software não autorizado. Você também pode usar uma solução de terceiros para identificar softwares não aprovados.

Como usar o monitoramento de integridade de arquivo:

https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring

Noções básicas sobre Controle de Alterações do Azure:

https://docs.microsoft.com/azure/automation/change-tracking

Como habilitar o inventário de máquina virtual do Azure:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="68-use-only-approved-applications"></a>6,8: usar somente aplicativos aprovados

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.8 | 2,6 | Cliente |

Use os controles de aplicativo adaptáveis da central de segurança do Azure para garantir que apenas o software autorizado seja executado e todos os softwares não autorizados sejam impedidos de serem executados em máquinas virtuais do Azure.

Como usar os controles de aplicativo adaptáveis da central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="69-use-only-approved-azure-services"></a>6,9: usar somente os serviços do Azure aprovados

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.9 | 2,6 | Cliente |

Use Azure Policy para restringir quais serviços você pode provisionar em seu ambiente.

Como configurar e gerenciar Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo de recurso específico com Azure Policy:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

## <a name="610-implement-approved-application-list"></a>6,10: implementar a lista de aplicativos aprovados

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.1 | 2.7 | Cliente |

Use os controles de aplicativo adaptáveis da central de segurança do Azure para especificar para quais tipos de arquivo uma regra pode ou não se aplicar.

Implemente uma solução de terceiros se isso não atender ao requisito.

Como usar os controles de aplicativo adaptáveis da central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager por meio de scripts

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6,11 | 2.8 | Cliente |

Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com Azure Resource Manager Configurando &quot;&quot; bloquear acesso para o aplicativo &quot;Microsoft Azure&quot; de gerenciamento.

Como configurar o acesso condicional para bloquear o acesso a Azure Resource Manager:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: limitar a capacidade dos usuários de executar scripts em recursos de computação

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6,12 | 2.8 | Cliente |

Use configurações específicas do sistema operacional ou recursos de terceiros para limitar a capacidade dos usuários de executar scripts nos recursos de computação do Azure.

Por exemplo, como controlar a execução de script do PowerShell em ambientes Windows: https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: separar fisicamente ou logicamente os aplicativos de alto risco

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6,13 | 2.9 | Cliente |

Software necessário para operações de negócios, mas pode incorrer em um risco maior para a organização, deve ser isolado em sua própria máquina virtual e/ou rede virtual e suficientemente protegido com um firewall do Azure ou grupo de segurança de rede.

Como criar uma rede virtual:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Como criar um NSG com uma configuração de segurança:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="next-steps"></a>Passos seguintes

Consulte o próximo controle de segurança: [configuração segura](security-control-secure-configuration.md)
