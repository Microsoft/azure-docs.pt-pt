---
title: Controlo de Segurança Azure - Inventário e Gestão de Ativos
description: Inventário de Controlo de Segurança Azure e Gestão de Ativos
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: aece7278d134145e00b027c184f3817d5566e1e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81408367"
---
# <a name="security-control-inventory-and-asset-management"></a>Controlo de Segurança: Inventário e Gestão de Ativos

As recomendações de Inventário e Gestão de Ativos centram-se na abordagem de questões relacionadas com a gestão ativa (inventário, track e correto) de todos os recursos Azure para que apenas os recursos autorizados tenham acesso, e os recursos não autorizados e não geridos sejam identificados e removidos.

## <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize a solução automatizada de Deteção de Ativos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.1 | 1.1, 1.2, 1.3, 1.4, 9.1, 12.1 | Cliente |

Utilize o Gráfico de Recursos Azure para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua subscrição(s).  Certifique-se de permissões (de leitura) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como os recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

- [Como criar consultas com gráfico de recursos Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Como ver as suas Subscrições Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Compreender Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.2 | 1.5 | Cliente |

Aplique etiquetas nos recursos Azure, dando metadados para organizá-los logicamente numa taxonomia.

- [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.3 | 1.6 | Cliente |

Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear ativos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

- [Como criar subscrições adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definir e Manter um inventário dos recursos aprovados do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.4 | 2.1 | Cliente |

Crie um inventário de recursos Azure aprovados e software aprovado para recursos de computação de acordo com as nossas necessidades organizacionais.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.5 | 2.3, 2.4 | Cliente |

Utilize a Azure Policy para impor restrições ao tipo de recursos que podem ser criados na sua subscrição.

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição.  Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

- [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Como criar consultas com Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.6 | 2.3, 2.4 | Cliente |

Utilize o Inventário de Máquinas Virtuais Azure para automatizar a recolha de informações sobre todo o software em Máquinas Virtuais. O nome do software, versão, editor e tempo de atualização estão disponíveis a partir do portal Azure. Para ter acesso à data de instalação e outras informações, ative diagnósticos ao nível dos hóspedes e leve os Registos de Eventos do Windows para um espaço de trabalho do Log Analytics.

- [Como ativar o inventário de máquinas virtuais Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.7 | 2.5 | Cliente |

Utilize o Monitor de Integridade de Ficheiros do Azure Security Center (Change Tracking) e o inventário de máquinas virtuais para identificar todo o software instalado em Máquinas Virtuais. Pode implementar o seu próprio processo de remoção de software não autorizado. Também pode utilizar uma solução de terceiros para identificar software não aprovado.

- [Como utilizar o Monitor de Integridade do Ficheiro](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring)

- [Compreenda o rastreio da mudança de Azure](https://docs.microsoft.com/azure/automation/change-tracking)

- [Como ativar o inventário de máquinas virtuais Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.8 | 2,6 | Cliente |

Utilize controlos de aplicação adaptativos do Azure Security Center para garantir que apenas o software autorizado executa e todo o software não autorizado está bloqueado de ser executado em Máquinas Virtuais Azure.

- [Como utilizar os controlos de aplicações adaptativos do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.9 | 2,6 | Cliente |

Utilize a Política Azure para restringir os serviços que pode prestação no seu ambiente.

- [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Como negar um tipo específico de recurso com a Política Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

## <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.10 | 2.7 | Cliente |

Utilize controlos de aplicação adaptativos do Centro de Segurança Azure para especificar quais os tipos de ficheiros a que uma regra pode ou não aplicar.

Implementar solução de terceiros se esta não cumprir o requisito.

- [Como utilizar os controlos de aplicações adaptativos do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.11 | 2.9 | Cliente |

Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resources Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.12 | 2.9 | Cliente |

Dependendo do tipo de scripts, pode utilizar configurações específicas do sistema operativo ou recursos de terceiros para limitar a capacidade dos utilizadores de executar scripts dentro dos recursos de computação Azure.  Também pode aproveitar os Controlos de Aplicação Adaptativa do Azure Security Center para garantir que apenas o software autorizado executa e todo o software não autorizado está bloqueado de ser executado em Máquinas Virtuais Azure.

- [Como controlar a execução do script PowerShell em Ambientes windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [Como utilizar os controlos de aplicações adaptativos do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.13 | 2.9 | Cliente |

O software que é necessário para operações comerciais, mas que pode incorrer em maior risco para a organização, deve ser isolado dentro da sua própria máquina virtual e/ou rede virtual e suficientemente protegido com um Azure Firewall ou Grupo de Segurança de Rede.

- [Como criar uma rede virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Como criar um NSG com um config de segurança](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)


## <a name="next-steps"></a>Passos seguintes

- Ver o próximo controlo de segurança: [Configuração segura](security-control-secure-configuration.md)