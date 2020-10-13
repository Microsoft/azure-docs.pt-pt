---
title: Linha de segurança Azure Resource Graph para referência de segurança Azure
description: A linha de base de segurança Azure Resource Graph fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a85fdf1e1e27b1ce5abb0c7890b717d48ba3bd3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89230454"
---
# <a name="azure-resource-graph-security-baseline-for-azure-security-benchmark"></a>Linha de segurança Azure Resource Graph para referência de segurança Azure

Esta linha de base de segurança aplica orientações do Benchmark de [Segurança Azure](../../../security/benchmarks/overview.md) ao Azure Resource Graph. O Azure Security Benchmark fornece recomendações sobre como pode proteger as suas soluções em nuvem no Azure. O conteúdo é agrupado pelos controlos de **segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Azure Resource Graph. Foram excluídos **os controlos** não aplicáveis ao Azure Resource Graph. Para ver como o Azure Resource Graph mapeia completamente para o Benchmark de Segurança Azure, consulte o ficheiro completo de [mapeamento de base de base de segurança da Rede Virtual Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).



## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para obter mais informações, consulte [controlo de segurança: Identidade e controlo de acesso.](../../../security/benchmarks/security-control-identity-access-control.md)*

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Resource Graph fornece acesso a tipos e propriedades de recursos baseados no controlo de acesso baseado em funções Azure (Azure RBAC). Auditar e rever o acesso concedido aos princípios de segurança (utilizadores, grupos e contas de serviço) regularmente para garantir que as consultas retornam os resultados dos recursos adequados.

* [Permissões no Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

* [Como utilizar comentários sobre acesso à identidade do Azure](../../../active-directory/governance/access-reviews-overview.md)


**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [o controlo de segurança: proteção de dados](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para controlar o acesso a dados e recursos. Para utilizar o Azure Resource Graph, também deve ter acesso adequado aos recursos que pretende consultar. Este acesso deve ser apenas examinado para ser lido e ser concedido apenas ao pessoal necessário.

* [Permissões no Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

* [Como configurar o Azure RBAC](../../../role-based-access-control/role-assignments-rest.md)


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para obter mais informações, consulte [controlo de segurança: Inventário e gestão de ativos.](../../../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Use o Azure Resource Graph para consultar e descobrir todos os recursos suportados dentro das suas subscrições, grupos de gestão e inquilinos. Certifique-se de que tem permissões adequadas no seu inquilino e é capaz de enumerar todas as subscrições do Azure, bem como recursos dentro das suas subscrições.

* [Como criar consultas com gráfico de recursos Azure](../first-query-portal.md)

* [Compreender Azure RBAC](../../../role-based-access-control/overview.md)


**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação**: Crie um inventário de recursos Azure aprovados e software aprovado para recursos de computação de acordo com as suas necessidades organizacionais. Utilize o Gráfico de Recursos Azure para consultar os recursos Azure aprovados e alterar o histórico (pré-visualização) para rever as imagens e ver o que mudou.

* [Recursos de consulta Azure com Gráfico de Recursos Azure](../first-query-portal.md)

* [Obter alterações do recurso](../how-to/get-resource-changes.md)


**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Use o Azure Resource Graph para consultar e descobrir recursos nas suas assinaturas, grupos de gestão e inquilinos. Certifique-se de que todos os recursos da Azure no ambiente são aprovados.

* [Recursos de consulta Azure com Gráfico de Recursos Azure](../first-query-portal.md)

* [Amostras: Consultas de início para gráfico de recursos Azure](../samples/starter.md)


**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../../../security/benchmarks/overview.md)
- Saiba mais sobre [as linhas de base de segurança da Azure](../../../security/benchmarks/security-baselines-overview.md)
