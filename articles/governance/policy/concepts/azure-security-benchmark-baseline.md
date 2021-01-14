---
title: Linha de segurança da política Azure para referência de segurança Azure
description: A linha de base de segurança da Política Azure fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: azure-policy
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e8915e1c15972341befd176b412925f4e87c94f6
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201455"
---
# <a name="azure-policy-security-baseline-for-azure-security-benchmark"></a>Linha de segurança da política Azure para referência de segurança Azure

Esta linha de base de segurança aplica orientações do Benchmark de [Segurança Azure](../../../security/benchmarks/overview.md) à Política Azure. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. O conteúdo é agrupado pelos domínios de conformidade e **controlos de segurança** **definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável à Política Azure. Foram excluídos **os controlos** não aplicáveis à Política Azure. Para ver como a Política Azure mapeia completamente para o Azure Security Benchmark, consulte o [ficheiro de mapeamento de base de segurança da Política Azure completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Para um mapeamento dos controlos de Benchmark de Segurança Azure para definições políticas incorporadas através da iniciativa incorporada, consulte [conformidade regulamentar: Benchmark de segurança azul](../samples/azure-security-benchmark.md).

A Azure Policy utiliza o termo _Propriedade_ em vez de _Responsabilidade_. Para mais informações sobre _a Propriedade_, consulte [as definições de política da Azure](./definition-structure.md#type) e [a responsabilidade partilhada na nuvem.](../../../security/fundamentals/shared-responsibility.md)


## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte [o controlo de segurança: Registar e monitorizar](../../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: A Política azul usa registos de atividade, que são automaticamente habilitados, para incluir fonte de evento, data, utilizador, timetamp, endereços de origem, endereços de destino e outros elementos úteis.

* [Como recolher registos e métricas da plataforma com o Azure Monitor](../../../azure-monitor/platform/diagnostic-settings.md)

* [Compreenda o registo e diferentes tipos de registo em Azure](../../../azure-monitor/platform/platform-logs-overview.md)


**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para obter mais informações, consulte [controlo de segurança: Identidade e controlo de acesso.](../../../security/benchmarks/security-control-identity-access-control.md)*

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas. 

Também pode ativar uma solução Just-In-Time / Just-Enough-Access utilizando funções privilegiadas de gestão de [identidade privilegiada da Azure AD](../../../active-directory/privileged-identity-management/pim-configure.md) ou [gestor de recursos Azure.](../../../azure-resource-manager/management/overview.md)


**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize PAWs (estações de trabalho privilegiadas de acesso) com MFA configurados para iniciar sessão e configurar recursos Azure.

* [Saiba mais sobre estações de trabalho de acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Como ativar o MFA no Azure](../../../active-directory/authentication/howto-mfa-getstarted.md)


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [o controlo de segurança: proteção de dados](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para controlar o acesso à Política Azure.

* [Permissões do Azure RBAC na Política Azure](../overview.md#azure-rbac-permissions-in-azure-policy)

* [Como configurar o Azure RBAC](../../../role-based-access-control/role-assignments-portal.md)


**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com registos de atividade para criar alertas para quando as alterações ocorrem na Política Azure.

* [Como criar alertas para eventos de registo de atividades do Azure](../../../azure-monitor/platform/alerts-activity-log.md)


**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para obter mais informações, consulte [controlo de segurança: Inventário e gestão de ativos.](../../../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia. Utilize o efeito _de modificação_ da Política Azure para informar e impor o cumprimento e a governação consistente da tag.

* [Tutorial: Criar e gerir políticas](../tutorials/create-and-manage.md)

* [Tutorial: Gerir a governação da tag](../tutorials/govern-tags.md)


**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definir e Manter um inventário dos recursos aprovados do Azure

**Orientação**: Crie um inventário de definições políticas aprovadas e atribuições políticas de acordo com as suas necessidades organizacionais.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Use a Política Azure para impor restrições ao tipo de recursos que podem ser criados nas suas subscrições.

* [Como configurar e gerir o Azure Policy](../tutorials/create-and-manage.md)


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../../../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../../../security/benchmarks/security-baselines-overview.md)
