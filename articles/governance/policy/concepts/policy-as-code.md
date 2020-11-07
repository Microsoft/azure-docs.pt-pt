---
title: Criar fluxos de trabalho do Azure Policy como Código
description: Aprenda a desenhar fluxos de trabalho para implementar as definições da Política Azure como código e valide automaticamente os recursos.
ms.date: 10/20/2020
ms.topic: conceptual
ms.openlocfilehash: 74d2097e4db4442e6e65f30541864fb554f7379d
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359685"
---
# <a name="design-azure-policy-as-code-workflows"></a>Criar fluxos de trabalho do Azure Policy como Código

À medida que progride na sua jornada com a Cloud Governance, você vai querer passar de gestão manual de cada definição de política no portal Azure ou através dos vários SDKs para algo mais manejável e repetível à escala empresarial. Duas das abordagens predominantes para gerir sistemas em escala na nuvem são:

- Infraestrutura como Código: A prática de tratar o conteúdo que define os seus ambientes, desde modelos de Gestor de Recursos Azure (modelos ARM) até definições de Política Azure para Azure Blueprints, como código fonte.
- DevOps: A união de pessoas, processos e produtos para permitir a entrega contínua de valor aos nossos utilizadores finais.

A política azure como código é a combinação destas ideias. Essencialmente, mantenha as definições de política no controlo de origem e sempre que uma alteração é feita, teste e valide essa mudança. No entanto, esta não deve ser a extensão das políticas de envolvimento com infraestruturas como Código ou DevOps.

O passo de validação deve também ser um componente de outros fluxos de trabalho de integração contínua ou de implantação contínua. Exemplos incluem a implantação de um ambiente de aplicação ou infraestrutura virtual. Ao fazer da Azure Policy validar um componente precoce do processo de construção e implantação, as equipas de aplicação e operações descobrem se as suas alterações não são compatíveis, muito antes de ser tarde demais e estão a tentar implementar na produção.

## <a name="definitions-and-foundational-information"></a>Definições e informações fundamentais

Antes de entrar nos detalhes da Política Azure como fluxo de trabalho de código, reveja as seguintes definições e exemplos:

- [Definição de política](./definition-structure.md)
- [Definição de iniciativa](./initiative-definition-structure.md)

Os nomes dos ficheiros alinham-se com partes da definição de política ou de iniciativa:
- `policy(set).json` - Toda a definição
- `policy(set).parameters.json` - A `properties.parameters` parte da definição
- `policy.rules.json` - A `properties.policyRule` parte da definição
- `policyset.definitions.json` - A `properties.policyDefinitions` parte da definição

Exemplos destes formatos de ficheiros estão disponíveis no [Azure Policy GitHub Repo:](https://github.com/Azure/azure-policy/)

- Definição de política: [Adicione uma etiqueta aos recursos](https://github.com/Azure/azure-policy/tree/master/samples/Tags/add-tag)
- Definição de iniciativa: [Etiquetas de faturação](https://github.com/Azure/azure-policy/tree/master/samples/PolicyInitiatives/multiple-billing-tags)

## <a name="workflow-overview"></a>Visão geral do fluxo de trabalho

O fluxo de trabalho geral recomendado da Política Azure como Código parece este diagrama:

:::image type="complex" source="../media/policy-as-code/policy-as-code-workflow.png" alt-text="Diagrama mostrando a Política de Azure como caixas de fluxo de trabalho de Código de Criar para Testar para Implementar." border="false":::
   O diagrama que mostra a Política de Azure como caixas de fluxo de trabalho de código. Criar capas de criação das definições de política e iniciativa. O teste cobre a atribuição com o modo de execução desativado. Segue-se uma verificação de porta de entrada para o estado de conformidade, concedendo às atribuições permissões M S I e recursos de reparação.  Implementar capas que actualizássem a atribuição com o modo de execução ativado.
:::image-end:::

### <a name="create-and-update-policy-definitions"></a>Criar e atualizar definições de políticas

As definições de política são criadas usando json, e armazenadas no controlo de fontes. Cada política tem o seu próprio conjunto de ficheiros, tais como os parâmetros, regras e parâmetros ambientais, que devem ser armazenados na mesma pasta. A seguinte estrutura é uma forma recomendada de manter as suas definições políticas no controlo de fontes.

```text
.
|
|- policies/  ________________________ # Root folder for policy resources
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- assign.<name1>.json _________ # Assignment 1 for this policy definition
|     |- assign.<name2>.json _________ # Assignment 2 for this policy definition
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- assign.<name1>.json _________ # Assignment 1 for this policy definition
|     |- assign.<name2>.json _________ # Assignment 2 for this policy definition
|
```

Quando uma nova política é adicionada ou uma existente atualizada, o fluxo de trabalho deve atualizar automaticamente a definição de política em Azure. O teste da nova definição de política nova ou atualizada surge num passo posterior.

Além disso, [reveja os recursos da Política de Exportação](../how-to/export-resources.md) para obter as definições e atribuições existentes no ambiente de gestão de códigos de origem [GitHub](https://www.github.com).

### <a name="create-and-update-initiative-definitions"></a>Criar e atualizar definições de iniciativa

Da mesma forma, as iniciativas têm o seu próprio ficheiro JSON e ficheiros relacionados que devem ser armazenados na mesma pasta. A definição de iniciativa requer que a definição de política já exista, pelo que não pode ser criada ou atualizada até que a fonte da apólice tenha sido atualizada no controlo de origem e depois atualizada em Azure. A seguinte estrutura é uma forma recomendada de manter as definições de iniciativa no controlo de fontes:

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- assign.<name1>.json _________ # Assignment 1 for this policy initiative
|     |- assign.<name2>.json _________ # Assignment 2 for this policy initiative
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- assign.<name1>.json _________ # Assignment 1 for this policy initiative
|     |- assign.<name2>.json _________ # Assignment 2 for this policy initiative
|
```

Tal como as definições políticas, ao adicionar ou atualizar uma iniciativa existente, o fluxo de trabalho deve atualizar automaticamente a definição de iniciativa em Azure. O teste da nova definição de iniciativa ou atualizada surge num passo posterior.

### <a name="test-and-validate-the-updated-definition"></a>Teste e valida a definição atualizada

Uma vez que a automatização tomou as suas definições de política ou iniciativa recentemente criadas ou atualizadas e fez a atualização para o objeto em Azure, é hora de testar as alterações que foram feitas. Ou a política ou a iniciativa de que faz parte deve então ser atribuída a recursos no ambiente mais afastados da produção. Este ambiente é tipicamente _Dev._

A atribuição deve utilizar [a aplicação da leiMode](./assignment-structure.md#enforcement-mode) de _deficientes_ para que a criação de recursos e as atualizações não sejam bloqueadas, mas que os recursos existentes ainda sejam auditados para o cumprimento da definição de política atualizada. Mesmo com a aplicação da Lei, recomenda-se que o âmbito de atribuição seja um grupo de recursos ou uma subscrição que seja especificamente para validar políticas.

> [!NOTE]
> Embora o modo de aplicação seja útil, não é um substituto para testar completamente uma definição de política sob várias condições. A definição de política deve ser testada com `PUT` chamadas de API e `PATCH` REST, recursos conformes e não conformes, e casos de borda como uma propriedade em falta do recurso.

Após a implementação da atribuição, utilize a SDK da Política Azure, a [Azure Policy Compliance Scan GitHub Action,](https://github.com/marketplace/actions/azure-policy-compliance-scan)ou a [tarefa de Segurança e Avaliação de Conformidade dos Oleodutos Azure](/azure/devops/pipelines/tasks/deploy/azure-policy) para obter [dados de conformidade](../how-to/get-compliance-data.md) para a nova atribuição. O ambiente utilizado para testar as políticas e atribuições deve ter recursos conformes e não conformes.
Como um bom teste de unidade para código, você quer testar que os recursos são como esperado e que você também não tem falsos positivos ou falsos negativos. Se testar e validar apenas para o que espera, pode haver um impacto inesperado e não identificado da apólice. Para obter mais informações, consulte [avaliar o impacto de uma nova definição de Política Azure.](./evaluate-impact.md)

### <a name="enable-remediation-tasks"></a>Permitir tarefas de remediação

Se a validação da atribuição corresponde às expectativas, o próximo passo é validar a reparação.
As políticas que utilizam [osIfNotExists](./effects.md#deployifnotexists) ou [modificam](./effects.md#modify) podem ser transformadas numa tarefa de remediação e recursos corretos de um estado não conforme.

O primeiro passo para a reparação de recursos é conceder à atribuição de políticas a atribuição de funções definida na definição de política. Esta atribuição de funções confere à atribuição de identidade gerida a uma política suficientes para fazer as alterações necessárias para tornar o recurso conforme.

Uma vez que a atribuição da política tenha os direitos adequados, use o Policy SDK para desencadear uma tarefa de reparação contra um conjunto de recursos que se sabe não conformes. Devem ser efetuados três ensaios contra estas tarefas remediadas antes de prosseguir:

- Validar que a tarefa de remediação concluída com sucesso
- Executar avaliação de política para ver que os resultados da conformidade da política são atualizados como esperado
- Executar um teste de unidade ambiental contra os recursos diretamente para validar as suas propriedades mudaram

Testar tanto os resultados atualizados da avaliação de políticas como o ambiente fornecem diretamente a confirmação de que as tarefas de reparação alteraram o que era esperado e que a definição de política viu a mudança de conformidade como esperado.

### <a name="update-to-enforced-assignments"></a>Atualização para atribuições forçadas

Depois de concluídos todos os portões de validação, atualize a atribuição para utilizar **o enforcementMode** de _habilitado_. Recomenda-se fazer esta mudança inicialmente no mesmo ambiente longe da produção. Uma vez que esse ambiente seja validado como funcionamento como esperado, a mudança deve então ser scopedada para incluir o próximo ambiente, e assim por diante, até que a política seja implementada para os recursos produtivos.

## <a name="process-integrated-evaluations"></a>Avaliações integradas do processo

O fluxo de trabalho geral para a Política Azure como Código destina-se a desenvolver e implementar políticas e iniciativas para um ambiente em escala. No entanto, a avaliação de políticas deve fazer parte do processo de implantação de qualquer fluxo de trabalho que implemente ou crie recursos em Azure, tais como a implementação de aplicações ou a execução de modelos ARM para criar infraestruturas.

Nestes casos, após a aplicação ou implantação da infraestrutura a um grupo de subscrição de teste ou de recursos, deve ser feita uma avaliação das políticas para esse âmbito de verificação de todas as políticas e iniciativas existentes. Embora possam ser configurados como **enforcementMode** _desativado_ em tal ambiente, é útil saber cedo se uma aplicação ou implantação de infraestrutura está violando as definições políticas precocemente. Esta avaliação política deve, portanto, ser um passo nesses fluxos de trabalho e falhar nas implementações que criam recursos não conformes.

## <a name="review"></a>Revisão

Este artigo abrange o fluxo de trabalho geral para a Política Azure como Código e também onde a avaliação das políticas deve fazer parte de outros fluxos de trabalho de implantação. Este fluxo de trabalho pode ser usado em qualquer ambiente que suporte passos scripted e automação com base em gatilhos. Para um tutorial sobre a utilização deste fluxo de trabalho no GitHub, consulte [Tutorial: Implement Azure Policy as Code with GitHub](../tutorials/policy-as-code-github.md).

## <a name="next-steps"></a>Passos seguintes

- Conheça a estrutura de [definição de políticas.](./definition-structure.md)
- Conheça a [estrutura de atribuição de políticas.](./assignment-structure.md)
- Entenda como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade.](../how-to/get-compliance-data.md)
- Saiba como [remediar recursos não conformes.](../how-to/remediate-resources.md)
- Reveja o que é um grupo de gestão com [Organizar os seus recursos com grupos de gestão Azure.](../../management-groups/overview.md)
