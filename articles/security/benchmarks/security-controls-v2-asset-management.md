---
title: Azure Security Benchmark V2 - Gestão de Ativos
description: Azure Security Benchmark V2 Gestão de Ativos
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 845c4f3194f35c68306e74734e5ecc6109d9c103
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318029"
---
# <a name="security-control-v2-asset-management"></a>Controlo de Segurança V2: Gestão de Ativos

A Gestão de Ativos cobre controlos para garantir visibilidade e governação de segurança sobre os recursos da Azure. Isto inclui recomendações sobre permissões para pessoal de segurança, acesso de segurança ao inventário de ativos, e gestão de aprovações para serviços e recursos (inventário, pista e correto).

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Garantir que a equipa de segurança tem visibilidade em riscos para os ativos

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| AM-1 | 1.1, 1.2 | CM-8, PM-5 |

Certifique-se de que as equipas de segurança recebem permissões de Leitor de Segurança no seu inquilino Estaure e subscrições para que possam monitorizar riscos de segurança usando o Centro de Segurança Azure. 

Dependendo da forma como as responsabilidades das equipas de segurança são estruturadas, a monitorização dos riscos de segurança pode ser da responsabilidade de uma equipa de segurança central ou de uma equipa local. Dito isto, os conhecimentos de segurança e os riscos devem ser sempre agregados centralmente dentro de uma organização. 

As permissões do Security Reader podem ser aplicadas amplamente a um inquilino inteiro (Root Management Group) ou a grupos de gestão ou subscrições específicas. 

Nota: Podem ser necessárias permissões adicionais para obter visibilidade em cargas de trabalho e serviços. 

- [Visão geral do papel do leitor de segurança](../../role-based-access-control/built-in-roles.md#security-reader)

- [Visão geral dos Grupos de Gestão Azure](../../governance/management-groups/overview.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Garantir que a equipa de segurança tem acesso ao inventário de ativos e metadados

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| AM-2 | 1.1, 1.2, 1.4, 1.5, 9.1, 12.1 | CM-8, PM-5 |

Certifique-se de que as equipas de segurança têm acesso a um inventário continuamente atualizado de ativos em Azure. As equipas de segurança precisam frequentemente deste inventário para avaliar a exposição potencial da sua organização a riscos emergentes, e como um contributo para melhorias contínuas de segurança. 

A funcionalidade de inventário do Azure Security Center e o Azure Resource Graph podem consultar e descobrir todos os recursos nas suas subscrições, incluindo serviços Azure, aplicações e recursos de rede.  

Organizar logicamente ativos de acordo com a taxonomia da sua organização usando Tags, bem como outros metadados em Azure (Nome, Descrição e Categoria).  

- [Como criar consultas com o Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

- [Gestão de inventário de ativos do Azure Security Center](../../security-center/asset-inventory.md)

- [Para obter mais informações sobre a marcação de ativos, consulte o guia de decisão de nomeação e marcação de recursos](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>AM-3: Utilize apenas serviços Azure aprovados

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| AM-3 | 2.3, 2.4 | CM-7, CM-8 |

Utilize a Política Azure para auditar e restringir quais os serviços que os utilizadores podem prestação no seu ambiente. Utilize o Gráfico de Recursos Azure para consultar e descobrir recursos dentro das suas subscrições.  Também pode utilizar o Azure Monitor para criar regras para desencadear alertas quando for detetado um serviço não aprovado.

- [Configure e gere a Política Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](/azure/governance/policy/samples/not-allowed-resource-types)

- [Como criar consultas com o Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garantir a segurança da gestão do ciclo de vida dos ativos

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| AM-4 | 2.3, 2.4, 2.5 | CM-7, CM-8, CM-10, CM-11 |

Estabeleça ou atualize políticas de segurança que abordem processos de gestão do ciclo de vida dos ativos para modificações de impacto potencialmente elevadas. Estas alterações incluem alterações a: fornecedores de identidade e acesso, sensibilidade de dados, configuração de rede e atribuição de privilégios administrativos.

Remova os recursos de Azure quando já não forem necessários.

- [Eliminar grupo de recursos Escaure e recursos](../../azure-resource-manager/management/delete-resource-group.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| AM-5 | 2.9 | AC-3 |

Utilize o Acesso Condicional AD Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a App "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: Utilize apenas aplicações aprovadas em recursos computacional

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| AM-6 | 2.6, 2.7 | AC-3, CM-7, CM-8, CM-10, CM-11 |

Certifique-se de que apenas o software autorizado executa e todo o software não autorizado está bloqueado de ser executado em Azure Virtual Machines.

Utilize controlos de aplicação adaptativos do Azure Security Center (ASC) para descobrir e gerar uma lista de admissões. Também pode utilizar controlos de aplicação adaptativa ASC para garantir que apenas o software autorizado executa e todo o software não autorizado está bloqueado de ser executado em Azure Virtual Machines.

Utilize o Azure Automation Change Tracking and Inventory para automatizar a recolha de informações de inventário dos seus VMs Windows e Linux. O nome do software, versão, editor e tempo de atualização estão disponíveis a partir do portal Azure. Para obter a data de instalação do software e outras informações, ative os diagnósticos ao nível do hóspede e direcione os Registos de Eventos do Windows para o espaço de trabalho do Log Analytics.

Dependendo do tipo de scripts, pode utilizar configurações específicas do sistema operativo ou recursos de terceiros para limitar a capacidade dos utilizadores de executar scripts em recursos computatórios Azure. 

Também pode usar uma solução de terceiros para descobrir e identificar software não aprovado.

- [Como utilizar controlos de aplicação adaptativos do Azure Security Center](../../security-center/security-center-adaptive-application.md)

- [Compreender o rastreio e inventário de mudança de automatização da Azure](../../automation/change-tracking.md)

- [Como controlar a execução do script PowerShell em ambientes Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

