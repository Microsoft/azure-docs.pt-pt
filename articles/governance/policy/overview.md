---
title: Descrição Geral do Azure Policy
description: O Azure Policy é um serviço no Azure utilizado para criar, atribuir e gerir definições de política no seu ambiente do Azure.
ms.date: 11/25/2019
ms.topic: overview
ms.openlocfilehash: db6a7c592213b0ef8a17466300c37c859e96476b
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484021"
---
# <a name="what-is-azure-policy"></a>O que é o Azure Policy?

Governance validates that your organization can achieve its goals through effective and efficient use of IT. It meets this need by creating clarity between business goals and IT projects.

A sua empresa tem um número significativo de problemas de TI que parece nunca ficarem resolvidos? Uma boa governação de TI envolve o planeamento das suas iniciativas e a definição de prioridades num nível estratégico para ajudar a gerir e evitar problemas. This strategic need is where Azure Policy comes in.

O Azure Policy é um serviço do Azure que utiliza para criar, atribuir e gerir políticas. Estas políticas impõem diferentes regras e efeitos aos recursos, de forma a que esses recursos se mantenham em conformidade com as normas empresariais e os contratos de nível de serviço. Azure Policy meets this need by evaluating your resources for non-compliance with assigned policies. Por exemplo, pode ter uma política para permitir apenas um determinado tamanho de SKU de máquinas virtuais no seu ambiente. Once this policy is implemented, new and existing resources are evaluated for compliance. With the right type of policy, existing resources can be brought into compliance. Later in this documentation, we'll go over more details on how to create and implement policies with Azure Policy.

> [!IMPORTANT]
> A avaliação de conformidade do Azure Policy é agora fornecida para todas as atribuições, independentemente do escalão de preço. Se a suas atribuições não mostrarem os dados de conformidade, certifique-se de que a subscrição está registada com o fornecedor de recursos Microsoft.PolicyInsights.

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-is-it-different-from-rbac"></a>Em que medida é diferente do RBAC?

There are a few key differences between Azure Policy and role-based access control (RBAC). O RBAC concentra-se nas ações do utilizador em âmbitos diferentes. You might be added to the contributor role for a resource group, allowing you to make changes to that resource group. Azure Policy focuses on resource properties during deployment and for already existing resources. Azure Policy controls properties such as the types or locations of resources. Unlike RBAC, Azure Policy is a default allow and explicit deny system.

### <a name="rbac-permissions-in-azure-policy"></a>Permissões RBAC no Azure Policy

O Azure Policy tem várias permissões, conhecidas como operações, em dois Fornecedores de Recursos:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Muitas Funções incorporadas concedem permissão aos recursos do Azure Policy. The **Resource Policy Contributor** role includes most Azure Policy operations. **Owner** has full rights. Both **Contributor** and **Reader** can use all read Azure Policy operations, but **Contributor** can also trigger remediation.

Se nenhuma das Funções incorporadas tiver as permissões exigidas, crie uma [função personalizada](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Definição de política

O percurso de criar e implementar uma política no Azure Policy começa pela criação de uma definição de política. Every policy definition has conditions under which it's enforced. And, it has a defined effect that takes place if the conditions are met.

In Azure Policy, we offer several built-in policies that are available by default. Por exemplo:

- **Allowed Storage Account SKUs**: Determines if a storage account being deployed is within a set of SKU sizes. Its effect is to deny all storage accounts that don't adhere to the set of defined SKU sizes.
- **Allowed Resource Type**: Defines the resource types that you can deploy. Its effect is to deny all resources that aren't part of this defined list.
- **Allowed Locations**: Restricts the available locations for new resources. O efeito é utilizado para impor os requisitos de geoconformidade.
- **Allowed Virtual Machine SKUs**: Specifies a set of virtual machine SKUs that you can deploy.
- **Add a tag to resources**: Applies a required tag and its default value if it's not specified by the deploy request.
- **Enforce tag and its value**: Enforces a required tag and its value to a resource.
- **Not allowed resource types**: Prevents a list of resource types from being deployed.

To implement these policy definitions (both built-in and custom definitions), you'll need to assign them. Pode atribuir qualquer uma destas políticas através do portal do Azure, do PowerShell ou da CLI do Azure.

Policy evaluation happens with several different actions, such as policy assignment or policy updates. For a complete list, see [Policy evaluation triggers](./how-to/get-compliance-data.md#evaluation-triggers).

Para saber mais sobre as estruturas de definições de política, veja [Estrutura de Definição de Política](./concepts/definition-structure.md).

## <a name="policy-assignment"></a>Atribuição de política

Uma atribuição de política é uma definição de política que foi atribuída para ter lugar num âmbito específico. Este âmbito pode ir desde um [grupo de gestão](../management-groups/overview.md) a um grupo de recursos. O termo *âmbito* refere-se a todos os grupos de recursos, subscrições ou grupos de gestão a que a definição de política está atribuída. As atribuições de política são herdadas por todos os recursos subordinados. This design means that a policy applied to a resource group is also applied to resources in that resource group. No entanto, pode excluir um subâmbito da atribuição de política.

Por exemplo, no âmbito da subscrição, pode atribuir uma política que impede a criação de recursos de rede. You could exclude a resource group in that subscription that is intended for networking infrastructure. You then grant access to this networking resource group to users that you trust with creating networking resources.

In another example, you might want to assign a resource type allow list policy at the management group level. Em seguida, atribua uma política mais permissiva (permitindo mais tipos de recursos) num grupo de gestão subordinado ou mesmo diretamente em subscrições. No entanto, este exemplo não funciona porque a política é um sistema de negação explícita. Em vez disso, tem de excluir o grupo de gestão do subordinado ou uma subscrição da atribuição de política ao nível do grupo de gestão. Em seguida, atribua a política mais permissiva no grupo de gestão subordinado ou ao nível da subscrição. If any policy results in a resource getting denied, then the only way to allow the resource is to modify the denying policy.

Para obter mais informações sobre a configuração de definições e atribuições de política através do portal, veja [Criar uma atribuição de política para identificar recursos não conformes no ambiente do Azure](assign-policy-portal.md). Também estão disponíveis passos para o [PowerShell](assign-policy-powershell.md) e a [CLI do Azure](assign-policy-azurecli.md).

## <a name="policy-parameters"></a>Parâmetros de política

Os parâmetros de política ajudam a simplificar a gestão de políticas ao reduzir o número de definições de política que tem de criar. Pode definir os parâmetros durante a criação de uma definição de política para torná-la mais genérica. Em seguida, pode reutilizar essa definição de política para diferentes cenários. Pode fazê-lo ao transmitir diferentes valores quando atribui a definição de política. Por exemplo, pode especificar um conjunto de localizações para uma subscrição.

Parameters are defined when creating a policy definition. When a parameter is defined, it's given a name and optionally given a value. Por exemplo, pode definir um parâmetro para uma política intitulada *localização*. Em seguida, pode atribuir-lhe valores diferentes, tais como *EastUS* ou *WestUS* quando atribui uma política.

For more information about policy parameters, see [Definition structure - Parameters](./concepts/definition-structure.md#parameters).

## <a name="initiative-definition"></a>Definição de iniciativa

Uma definição de iniciativa é uma coleção de definições de política adaptadas para alcançar um objetivo único abrangente. As definições de iniciativa simplificam a gestão e a atribuição de definições de política. Simplificam através do agrupamento de um conjunto de políticas num único item. Por exemplo, pode criar uma iniciativa intitulada **Ativar Monitorização no Centro de Segurança do Azure**, com o objetivo de monitorizar todas as recomendações de segurança disponíveis no Centro de Segurança do Azure.

> [!NOTE]
> The SDK, such as Azure CLI and Azure PowerShell, use properties and parameters named **PolicySet** to refer to initiatives.

Ao abrigo desta iniciativa, terá definições de política como:

- **Monitorizar a Base de Dados SQL não encriptada no Centro de Segurança** – Para monitorização de servidores e bases de dados SQL não encriptados.
- **Monitor OS vulnerabilities in Security Center** – For monitoring servers that don't satisfy the configured baseline.
- **Monitorizar a Proteção de Ponto Final em falta no Centro de Segurança** – Para monitorização de servidores sem um agente de proteção de ponto final instalado.

## <a name="initiative-assignment"></a>Atribuição de iniciativa

Tal como uma atribuição de política, uma atribuição de iniciativa é uma definição de iniciativa atribuída a um âmbito específico. As atribuições de iniciativa reduzem a necessidade de efetuar várias definições de iniciativa para cada âmbito. Este âmbito também pode ir desde um grupo de gestão a um grupo de recursos.

Each initiative is assignable to different scopes. One initiative can be assigned to both **subscriptionA** and **subscriptionB**.

## <a name="initiative-parameters"></a>Parâmetros de iniciativa

Tal como os parâmetros de política, os parâmetros de iniciativa ajudam a simplificar a gestão de iniciativas ao reduzir a redundância. Initiative parameters are parameters being used by the policy definitions within the initiative.

Por exemplo, considere um cenário em que tem uma definição de iniciativa - **initiativeC**, com as definições de política **policyA** e **policyB**, em que cada uma espera um tipo diferente de parâmetro:

| Política | Nome do parâmetro |Tipo de parâmetro  |Nota |
|---|---|---|---|
| policyA | allowedLocations | array  |Este parâmetro espera uma lista de cadeias para um valor, uma vez que o tipo de parâmetro foi definido como uma matriz |
| policyB | allowedSingleLocation |string |Este parâmetro espera uma palavra para um valor, uma vez que o tipo de parâmetro foi definido como uma cadeia |

Neste cenário, quando define os parâmetros da iniciativa para **initiativeC**, tem três opções:

- Utilizar os parâmetros das definições de política nesta iniciativa: neste exemplo, *allowedLocations* e *allowedSingleLocation* tornam-se parâmetros de iniciativa para **initiativeC**.
- Indicar os valores para os parâmetros das definições de política nesta definição de iniciativa. In this example, you can provide a list of locations to **policyA's parameter – allowedLocations** and **policyB's parameter – allowedSingleLocation**. Também pode fornecer valores quando atribui esta iniciativa.
- Fornecer uma lista das opções de *valor* que podem ser utilizadas quando atribui esta iniciativa. Quando atribui esta iniciativa, os parâmetros herdados das definições de política na iniciativa apenas podem ter valores desta lista fornecida.

When creating value options in an initiative definition, you're unable to input a different value during the initiative assignment because it's not part of the list.

## <a name="maximum-count-of-azure-policy-objects"></a>Maximum count of Azure Policy objects

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Recomendações para a gestão de políticas

Here are a few pointers and tips to keep in mind:

- Start with an audit effect instead of a deny effect to track impact of your policy definition on the resources in your environment. If you have scripts already in place to autoscale your applications, setting a deny effect may hinder such automation tasks already in place.

- Consider organizational hierarchies when creating definitions and assignments. We recommend creating definitions at higher levels such as the management group or subscription level. Then, create the assignment at the next child level. If you create a definition at a management group, the assignment can be scoped down to a subscription or resource group within that management group.

- We recommend creating and assigning initiative definitions even for a single policy definition.
  For example, you have policy definition *policyDefA* and create it under initiative definition *initiativeDefC*. If you create another policy definition later for *policyDefB* with goals similar to *policyDefA*, you can add it under *initiativeDefC* and track them together.

- Once you've created an initiative assignment, policy definitions added to the initiative also become part of that initiatives assignments.

- When an initiative assignment is evaluated, all policies within the initiative are also evaluated.
  If you need to evaluate a policy individually, it's better to not include it in an initiative.

## <a name="video-overview"></a>Video overview

A seguinte descrição geral do Azure Policy é do Build 2018. For slides or video download, visit [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) on Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma ideia geral do Azure Policy e de alguns dos principais conceitos, seguem-se os passos sugeridos seguintes:

- [Assign a policy definition using the portal](./assign-policy-portal.md).
- [Assign a policy definition using the Azure CLI](./assign-policy-azurecli.md).
- [Assign a policy definition using PowerShell](./assign-policy-powershell.md).