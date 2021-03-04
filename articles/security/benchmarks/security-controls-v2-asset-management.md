---
title: Azure Security Benchmark V2 - Gestão de Ativos
description: Azure Security Benchmark V2 Gestão de Ativos
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fbeb88b6aa542666481458fde97d7c63f467fa30
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051536"
---
# <a name="security-control-v2-asset-management"></a>Controlo de Segurança V2: Gestão de Ativos

A Gestão de Ativos cobre controlos para garantir visibilidade e governação de segurança sobre os recursos da Azure. Isto inclui recomendações sobre permissões para pessoal de segurança, acesso de segurança ao inventário de ativos, e gestão de aprovações para serviços e recursos (inventário, pista e correto).

Para ver a política de Azure incorporada aplicável, consulte [detalhes da iniciativa Azure Security Benchmark Regulatory Compliance built-in: Network Security](../../governance/policy/samples/azure-security-benchmark.md#asset-management)

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Certifique-se de que a equipa de segurança tem visibilidade para os riscos dos ativos

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| AM-1 | 1.1, 1.2 | CM-8, PM-5 |

Certifique-se de que as equipas de segurança recebem permissões de Leitor de Segurança no seu inquilino Estaure e subscrições para que possam monitorizar riscos de segurança usando o Centro de Segurança Azure.

Dependendo da forma como as responsabilidades das equipas de segurança são estruturadas, a monitorização dos riscos de segurança pode ser da responsabilidade de uma equipa de segurança central ou de uma equipa local. Dito isto, as informações e os riscos de segurança têm de ser sempre agregados centralmente nas organizações. 

As permissões Leitor de Segurança podem ser aplicadas de um modo amplo em todo um inquilino (Grupo de Gestão Raiz) ou dentro de âmbitos, como grupos de gestão ou subscrições específicas. 

Nota: para obter visibilidade para cargas de trabalho e serviços, poderão ser necessárias permissões adicionais.

- [Descrição Geral da Função de Leitor de Segurança](../../role-based-access-control/built-in-roles.md#security-reader)

- [Descrição Geral dos Grupos de Gestão do Azure](../../governance/management-groups/overview.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Garantir que a equipa de segurança tem acesso aos metadados e inventário dos recursos

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| AM-2 | 1.1, 1.2, 1.4, 1.5, 9.1, 12.1 | CM-8, PM-5 |

Certifique-se de que as equipas de segurança têm acesso a um inventário continuamente atualizado de ativos em Azure. As equipas de segurança, normalmente, precisam deste inventário para avaliar o potencial de exposição da organização a riscos emergentes e como um ponto de entrada para melhorias contínuas à segurança. 

A funcionalidade de inventário do Azure Security Center e o Azure Resource Graph podem consultar e descobrir todos os recursos nas suas subscrições, incluindo serviços Azure, aplicações e recursos de rede.

Organizar logicamente ativos de acordo com a taxonomia da sua organização usando Tags, bem como outros metadados em Azure (Nome, Descrição e Categoria).

- [Como criar consultas com o Explorador do Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

- [Gestão de inventário de ativos do Azure Security Center](../../security-center/asset-inventory.md)

- [Para obter mais informações sobre a marcação de ativos, consulte o guia de decisão de nomeação e marcação de recursos](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>AM-3: Utilizar apenas os serviços do Azure aprovados

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| AM-3 | 2.3, 2.4 | CM-7, CM-8 |

Utilize o Azure Policy para auditar e restringir que serviços os utilizadores podem aprovisionar no ambiente. Utilize o Azure Resource Graph para consultar e detetar recursos dentro das subscrições. Também pode utilizar o Azure Monitor para criar regras para acionar alertas quando um serviço não aprovado for detetado.

- [Configure e gere a Política Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../../governance/policy/samples/index.md)

- [Como criar consultas com o Explorador do Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garanta a segurança da gestão do ciclo de vida dos ativos

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| AM-4 | 2.3, 2.4, 2.5 | CM-7, CM-8, CM-10, CM-11 |

Estabeleça ou atualize políticas de segurança que abordem processos de gestão do ciclo de vida dos ativos para modificações de impacto potencialmente elevadas. Essas modificações incluem alterações a fornecedores de identidade e acesso, confidencialidade dos dados, configuração de rede e atribuição de privilégios administrativos.

Quando já não precisar dos recursos do Azure, remova-os.

- [Eliminar grupos de recursos e recursos do Azure](../../azure-resource-manager/management/delete-resource-group.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| AM-5 | 2.9 | AC-3 |

Utilize o Acesso Condicional AD Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a App "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: Utilize apenas aplicações aprovadas em recursos computacional

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| AM-6 | 2.6, 2.7 | AC-3, CM-7, CM-8, CM-10, CM-11 |

Certifique-se de que apenas o software autorizado executa e todo o software não autorizado está bloqueado de ser executado em Azure Virtual Machines.

Utilize controlos de aplicação adaptativos do Azure Security Center (ASC) para descobrir e gerar uma lista de admissões. Também pode utilizar controlos de aplicação adaptativa ASC para garantir que apenas o software autorizado executa e todo o software não autorizado está bloqueado de ser executado em Azure Virtual Machines.

Utilize o Azure Automation Change Tracking and Inventory para automatizar a recolha de informações de inventário dos seus VMs Windows e Linux. O nome do software, versão, editor e tempo de atualização estão disponíveis a partir do portal Azure. Para obter a data de instalação do software e outras informações, ative os diagnósticos ao nível do hóspede e direcione os Registos de Eventos do Windows para o espaço de trabalho do Log Analytics.

Dependendo do tipo de scripts, pode utilizar configurações específicas do sistema operativo ou recursos de terceiros para limitar a capacidade dos utilizadores de executar scripts em recursos computatórios Azure.

Também pode usar uma solução de terceiros para descobrir e identificar software não aprovado.

- [Como utilizar controlos de aplicação adaptativos do Azure Security Center](../../security-center/security-center-adaptive-application.md)

- [Compreender o rastreio e inventário de mudança de automatização da Azure](../../automation/change-tracking/overview.md)

- [Como controlar a execução do script PowerShell em ambientes Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)
