---
title: Linha de base de segurança Azure para gráfico de recursos Azure
description: A linha de base de segurança Azure Resource Graph fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ad8968fdb6548da29a031f0e44bd3671f67b5553
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557722"
---
# <a name="azure-security-baseline-for-azure-resource-graph"></a>Linha de base de segurança Azure para gráfico de recursos Azure

Esta linha de base de segurança aplica orientações da [versão 1.0 do Azure Security Benchmark](../../../security/benchmarks/overview-v1.md) para O Gráfico de Recursos Azure. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos controlos de **segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Azure Resource Graph. Foram excluídos **os controlos** não aplicáveis ao Azure Resource Graph.

 
Para ver como o Azure Resource Graph mapeia completamente para o Benchmark de Segurança Azure, consulte o ficheiro completo de [mapeamento de base de base de segurança do Azure Resource Graph](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../../../security/benchmarks/security-control-identity-access-control.md)*

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Resource Graph fornece acesso a tipos de recursos e propriedades com base em controlos de acesso baseados em funções Azure (Azure RBAC). Auditar e rever o acesso concedido aos princípios de segurança (utilizadores, grupos e contas de serviço) regularmente para garantir que as consultas retornam os resultados dos recursos adequados.

- [Permissões no Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

- [Como utilizar comentários sobre acesso à identidade do Azure](../../../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos 

**Orientação**: Utilize o Azure RBAC para controlar o acesso a dados e recursos. Para utilizar o Azure Resource Graph, também deve ter acesso adequado aos recursos que pretende consultar. Este acesso deve ser apenas examinado para ser lido e ser concedido apenas ao pessoal necessário.

- [Permissões no Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

- [Como configurar o Azure RBAC](../../../role-based-access-control/role-assignments-rest.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../../../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Use o Azure Resource Graph para consultar e descobrir todos os recursos suportados dentro das suas subscrições, grupos de gestão e inquilinos. Certifique-se de que tem permissões adequadas no seu inquilino e é capaz de enumerar todas as subscrições do Azure, bem como recursos dentro das suas subscrições.

- [Como criar consultas com gráfico de recursos Azure](../first-query-portal.md)

- [Compreender Azure RBAC](../../../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação**: Crie um inventário de recursos Azure aprovados e software aprovado para recursos de computação de acordo com as suas necessidades organizacionais. Utilize o Gráfico de Recursos Azure para consultar os recursos Azure aprovados e alterar o histórico (pré-visualização) para rever as imagens e ver o que mudou.

- [Recursos de consulta Azure com Gráfico de Recursos Azure](../first-query-portal.md)

- [Obter alterações do recurso](../how-to/get-resource-changes.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Use o Azure Resource Graph para consultar e descobrir recursos nas suas assinaturas, grupos de gestão e inquilinos. Certifique-se de que todos os recursos da Azure no ambiente são aprovados.

- [Recursos de consulta Azure com Gráfico de Recursos Azure](../first-query-portal.md)

- [Amostras: Consultas de início para gráfico de recursos Azure](../samples/starter.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](../../../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../../../security/benchmarks/security-baselines-overview.md)