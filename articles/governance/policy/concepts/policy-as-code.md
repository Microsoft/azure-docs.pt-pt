---
title: Criar fluxos de trabalho de Política como Código
description: Aprenda a desenhar fluxos de trabalho para implementar as definições da Política Azure como código e validar automaticamente os recursos.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: fd77fdd4011c3e1e83f8dfa9f30045bb72881c25
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187737"
---
# <a name="design-policy-as-code-workflows"></a>Criar fluxos de trabalho de Política como Código

À medida que avança na sua jornada com a Cloud Governance, vai querer passar de gerir manualmente cada definição de política no portal Azure ou através dos vários SDKs para algo mais manejável e repetível à escala empresarial. Duas das abordagens predominantes para gerir sistemas em escala na nuvem são:

- Infraestrutura como Código: A prática de tratar o conteúdo que define os seus ambientes, desde modelos de Gestor de Recursos até definições de Política Azure até Plantas Azure, como código fonte.
- DevOps: A união de pessoas, processos e produtos para permitir a entrega contínua de valor aos nossos utilizadores finais.

Política como Código é a combinação destas ideias. Essencialmente, mantenha as definições de política no controlo de fontes e sempre que for feita uma alteração, teste e valide essa alteração. No entanto, não deve ser essa a extensão das políticas de envolvimento com a Infraestrutura como Código ou DevOps.

O passo de validação deve também ser um componente de outros fluxos de trabalho de integração contínua ou de implantação contínua. Exemplos incluem a implantação de um ambiente de aplicação ou infraestrutura virtual. Ao fazer da validação da Política Azure um componente precoce do processo de construção e implementação, as equipas de aplicação e operações descobrem se as suas alterações não são reclamadas, muito antes de ser tarde demais e estão a tentar implementar na produção.

## <a name="workflow-overview"></a>Visão geral do fluxo de trabalho

O fluxo de trabalho geral recomendado da Política como Código se parece com este diagrama:

:::image type="content" source="../media/policy-as-code/policy-as-code-workflow.png" alt-text="Política como visão geral do fluxo de trabalho do código" border="false":::

### <a name="create-and-update-policy-definitions"></a>Criar e atualizar definições políticas

As definições de política são criadas usando jSON, e armazenadas no controlo de fontes. Cada política tem o seu próprio conjunto de ficheiros, como os parâmetros, regras e parâmetros ambientais, que devem ser armazenados na mesma pasta. A estrutura que se segue é uma forma recomendada de manter as definições de política no controlo de fontes.

```text
.
|
|- policies/  ________________________ # Root folder for policies
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Quando uma nova política é adicionada ou uma atualizada existente, o fluxo de trabalho deve atualizar automaticamente a definição de política em Azure. O teste da nova definição de política ou atualizada surge num passo posterior.

### <a name="create-and-update-initiative-definitions"></a>Criar e atualizar definições de iniciativa

Da mesma forma, as iniciativas têm o seu próprio ficheiro JSON e ficheiros relacionados que devem ser armazenados na mesma pasta. A definição de iniciativa requer que a definição de política já exista, pelo que não pode ser criada ou atualizada até que a fonte da política seja atualizada no controlo de fontes e depois atualizada no Azure. A estrutura seguinte é uma forma recomendada de manter as definições de iniciativa no controlo de origem:

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Tal como as definições de políticas, ao adicionar ou atualizar uma iniciativa existente, o fluxo de trabalho deve atualizar automaticamente a definição de iniciativa em Azure. O teste da nova definição de iniciativa ou atualização surge num passo posterior.

### <a name="test-and-validate-the-updated-definition"></a>Testar e validar a definição atualizada

Uma vez que a automatização tenha tomado as suas definições de política ou iniciativa recém-criadas ou atualizadas e feito a atualização para o objeto em Azure, é hora de testar as mudanças que foram feitas. Ou a política ou a iniciativa de que faz parte devem então ser atribuídas a recursos no ambiente mais distantes da produção. Este ambiente é tipicamente _Dev_.

A atribuição deve utilizar o modo de [execução](./assignment-structure.md#enforcement-mode) dos _deficientes_ para que a criação de recursos e as atualizações não sejam bloqueadas, mas que os recursos existentes ainda são auditados para o cumprimento da definição de política atualizada. Mesmo com o modo de execução, recomenda-se que o âmbito de atribuição seja um grupo de recursos ou uma subscrição especificamente usada para validar políticas.

> [!NOTE]
> Embora o modo de aplicação seja útil, não é um substituto para testar completamente uma definição de política sob várias condições. A definição de `PUT` política `PATCH` deve ser testada e as chamadas REST API, recursos conformes e não conformes, e casos de borda como um imóvel em falta do recurso.

Após a implementação da atribuição, utilize o SDK de Política para [obter dados](../how-to/get-compliance-data.md) de conformidade para a nova atribuição. O ambiente utilizado para testar as políticas e atribuições deve dispor de recursos compatíveis e não conformes. Como um bom teste de unidade para código, você quer testar que os recursos são como esperado e que você também não tem falsos positivos ou falsos negativos. Se testar e validar apenas para o que espera, pode haver um impacto inesperado e não identificado da apólice. Para mais informações, consulte [Avaliar o impacto de uma nova definição de Política Azure](./evaluate-impact.md).

### <a name="enable-remediation-tasks"></a>Ativar tarefas de reparação

Se a validação da atribuição corresponde às expectativas, o próximo passo é validar a reparação.
As políticas que utilizam o [IfNotExist](./effects.md#deployifnotexists) ou [modificam](./effects.md#modify) podem ser transformadas numa tarefa de reparação e corrigir recursos de um estado não conforme.

O primeiro passo para o fazer é conceder à atribuição de políticas a atribuição de funções definida na definição de política. Esta atribuição de funções confere à atribuição de políticas direitos de identidade geridos suficientes para fazer as alterações necessárias para tornar o recurso conforme.

Uma vez que a atribuição de políticas tenha direitos adequados, utilize o SDK de política para desencadear uma tarefa de reparação contra um conjunto de recursos que se sabe não cumprirem. Devem ser efetuados três ensaios contra estas tarefas remediadas antes de prosseguir:

- Validar que a tarefa de reparação concluída com sucesso
- Executar avaliação política para ver que os resultados da conformidade da política são atualizados como esperado
- Executar um teste de unidade ambiental contra os recursos diretamente para validar as suas propriedades mudaram

Testar os resultados atualizados da avaliação das políticas e o ambiente fornecem diretamente a confirmação de que as tarefas de reparação alteraram o que se esperava e que a definição de política viu a alteração da conformidade como esperado.

### <a name="update-to-enforced-assignments"></a>Atualização para atribuições forçadas

Depois de todos os portões de validação terem sido concluídos, atualize a atribuição para utilizar o modo _de_ **execução** de . Esta alteração deve ser inicialmente feita no mesmo ambiente, longe da produção. Uma vez que esse ambiente seja validado como esperado, a mudança deve então ser prevista para incluir o ambiente seguinte e assim por diante até que a política seja implementada para os recursos produtivos.

## <a name="process-integrated-evaluations"></a>Avaliações integradas de processo

O fluxo geral de trabalho para a política como Código é para desenvolver e implementar políticas e iniciativas para um ambiente em escala. No entanto, a avaliação de políticas deve fazer parte do processo de implantação de qualquer fluxo de trabalho que implemente ou crie recursos no Azure, tais como a implementação de aplicações ou modelos de gestor de recursos de execução para criar infraestruturas.

Nestes casos, após a implementação da aplicação ou infraestrutura a um grupo de subscrição ou de recursos de teste, deve ser feita uma avaliação política para que esse âmbito de verificação da validação de todas as políticas e iniciativas existentes. Embora possam ser configurados como **enforcementMode** _desativado_ em tal ambiente, é útil saber cedo se uma aplicação ou implementação de infraestrutura está violando as definições de políticas mais cedo. Esta avaliação política deve, portanto, ser um passo nesses fluxos de trabalho e falhar nas implementações que criam recursos não conformes.

## <a name="review"></a>Rever

Este artigo abrange o fluxo geral de trabalho para a política como código e também onde a avaliação das políticas deve fazer parte de outros fluxos de trabalho de implantação. Este fluxo de trabalho pode ser usado em qualquer ambiente que suporte passos scripted e automação com base em gatilhos.

## <a name="next-steps"></a>Passos seguintes

- Conheça a estrutura da definição de [políticas.](./definition-structure.md)
- Conheça a estrutura de atribuição de [políticas.](./assignment-structure.md)
- Compreender como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como obter dados de [conformidade.](../how-to/get-compliance-data.md)
- Aprenda a [remediar recursos não conformes](../how-to/remediate-resources.md).
- Reveja o que é um grupo de gestão com organizar os seus recursos com grupos de [gestão Azure.](../../management-groups/overview.md)