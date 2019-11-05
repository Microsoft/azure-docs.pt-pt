---
title: Política de design como fluxos de trabalho de código
description: Aprenda a criar fluxos de trabalho para implantar suas definições de Azure Policy como código e validar automaticamente os recursos.
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: e3fcb9996266af7e952538c7c92c665929bb9492
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518700"
---
# <a name="design-policy-as-code-workflows"></a>Política de design como fluxos de trabalho de código

Conforme você progride em sua jornada com governança de nuvem, convém mudar de gerenciar manualmente cada definição de política na portal do Azure ou por meio de vários SDKs para algo mais gerenciável e repetível em escala empresarial. Duas das abordagens predominantes para gerenciar sistemas em escala na nuvem são:

- Infraestrutura como código: a prática de tratar o conteúdo que define seus ambientes, tudo, desde modelos do Resource Manager até Azure Policy definições para plantas do Azure, como código-fonte.
- DevOps: a União de pessoas, processos e produtos para permitir a entrega contínua de valor aos nossos usuários finais.

A política como código é a combinação dessas ideias. Essencialmente, mantenha suas definições de política no controle do código-fonte e sempre que uma alteração for feita, teste e valide essa alteração. No entanto, isso não deve ser a extensão do envolvimento das políticas com a infraestrutura como código ou DevOps.

A etapa de validação também deve ser um componente de outros fluxos de trabalho de integração contínua ou de implantação contínua. Os exemplos incluem a implantação de um ambiente de aplicativo ou de uma infraestrutura virtual. Ao fazer Azure Policy validação de um componente inicial do processo de compilação e implantação, as equipes de aplicativos e operações descobrirão se suas alterações não forem reclamações, muito tempo antes que elas sejam muito atrasadas e estejam tentando implantar na produção.

## <a name="workflow-overview"></a>Visão geral do fluxo de trabalho

O fluxo de trabalho geral recomendado de política como código é semelhante a este diagrama:

![Visão geral da política como fluxo de trabalho de código](../media/policy-as-code/policy-as-code-workflow.png)

### <a name="create-and-update-policy-definitions"></a>Criar e atualizar definições de política

As definições de política são criadas usando JSON e armazenadas no controle do código-fonte. Cada política tem seu próprio conjunto de arquivos, como parâmetros, regras e parâmetros de ambiente, que devem ser armazenados na mesma pasta. A estrutura a seguir é uma maneira recomendada de manter suas definições de política no controle do código-fonte.

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

Quando uma nova política é adicionada ou uma já existente atualizada, o fluxo de trabalho deve atualizar automaticamente a definição de política no Azure. O teste da definição de política nova ou atualizada vem em uma etapa posterior.

### <a name="create-and-update-initiative-definitions"></a>Criar e atualizar definições de iniciativa

Da mesma forma, as iniciativas têm seu próprio arquivo JSON e os arquivos relacionados que devem ser armazenados na mesma pasta. A definição de iniciativa requer que a definição de política já exista, portanto, não pode ser criada ou atualizada até que a origem da política tenha sido atualizada no controle do código-fonte e, em seguida, atualizada no Azure. A seguinte estrutura é uma maneira recomendada de manter suas definições de iniciativa no controle do código-fonte:

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

Como as definições de política, ao adicionar ou atualizar uma iniciativa existente, o fluxo de trabalho deve atualizar automaticamente a definição de iniciativa no Azure. O teste da definição de iniciativa nova ou atualizada vem em uma etapa posterior.

### <a name="test-and-validate-the-updated-definition"></a>Testar e validar a definição atualizada

Depois que a automação tiver levado suas definições de política ou de iniciativa recentemente criadas ou atualizadas e fez a atualização para o objeto no Azure, é hora de testar as alterações feitas. A política ou a (s) iniciativa (is) da qual faz parte deve ser atribuída a recursos no ambiente mais distante da produção. Esse ambiente normalmente é um _desenvolvedor_.

A atribuição deve usar [imposiçãomode](./assignment-structure.md#enforcement-mode) de _desabilitada_ para que a criação e as atualizações de recursos não sejam bloqueadas, mas que os recursos existentes ainda sejam auditados quanto à conformidade com a definição de política atualizada. Mesmo com imposiçãomode, é recomendável que o escopo de atribuição seja um grupo de recursos ou uma assinatura que seja usada especificamente para validar políticas.

> [!NOTE]
> Embora o modo de imposição seja útil, não é uma substituição para um teste completo de uma definição de política sob várias condições. A definição de política deve ser testada com `PUT` e `PATCH` chamadas à API REST, recursos compatíveis e não compatíveis e casos de borda como uma propriedade ausente do recurso.

Depois que a atribuição for implantada, use o SDK de política para [obter dados de conformidade](../how-to/get-compliance-data.md) para a nova atribuição. O ambiente usado para testar as políticas e atribuições deve ter recursos compatíveis e não compatíveis. Como um bom teste de unidade para o código, você deseja testar se os recursos estão conforme o esperado e se você também não tem falsos positivos ou falsos negativos. Se você testar e validar apenas o que espera, pode haver um impacto inesperado e não identificado da política. Para obter mais informações, consulte [avaliar o impacto de uma nova política do Azure](./evaluate-impact.md).

### <a name="enable-remediation-tasks"></a>Habilitar tarefas de correção

Se a validação da atribuição atender às expectativas, a próxima etapa será validar a correção.
As políticas que usam [deployIfNotExists](./effects.md#deployifnotexists) ou [Modify](./effects.md#modify) podem ser transformadas em uma tarefa de correção e corrigir os recursos de um estado sem conformidade.

A primeira etapa para fazer isso é conceder à atribuição de política a atribuição de função definida na definição de política. Essa atribuição de função dá à identidade gerenciada de política direitos suficientes para fazer as alterações necessárias para tornar o recurso compatível.

Depois que a atribuição de política tiver direitos apropriados, use o SDK de política para disparar uma tarefa de correção em um conjunto de recursos que são conhecidos como sem conformidade. Três testes devem ser concluídos em relação a essas tarefas remediadas antes de continuar:

- Validar que a tarefa de correção foi concluída com êxito
- Executar avaliação de política para ver que os resultados de conformidade de política são atualizados conforme esperado
- Executar um teste de unidade de ambiente em relação aos recursos diretamente para validar suas propriedades foram alteradas

Testar os resultados da avaliação da política atualizada e o ambiente fornecem diretamente a confirmação de que as tarefas de correção mudaram o que era esperado e que a definição da política observou a alteração de conformidade conforme o esperado.

### <a name="update-to-enforced-assignments"></a>Atualizar para atribuições impostas

Após a conclusão de todas as Gates de validação, atualize a atribuição para usar **imposiçãomode** de _habilitado_. Essa alteração deve inicialmente ser feita no mesmo ambiente longe da produção. Depois que o ambiente é validado como funcionando conforme o esperado, a alteração deve ser delimitada para incluir o próximo ambiente e assim por diante até que a política seja implantada nos recursos de produção.

## <a name="process-integrated-evaluations"></a>Processar avaliações integradas

O fluxo de trabalho geral da política como código é para desenvolver e implantar políticas e iniciativas em um ambiente em escala. No entanto, a avaliação de política deve fazer parte do processo de implantação para qualquer fluxo de trabalho que implanta ou cria recursos no Azure, como a implantação de aplicativos ou a execução de modelos do Resource Manager para criar a infraestrutura.

Nesses casos, depois que a implantação do aplicativo ou da infraestrutura for feita em um grupo de recursos ou assinatura de teste, a avaliação da política deverá ser feita para esse escopo verificando a validação de todas as políticas e iniciativas existentes. Embora eles possam ser configurados como **imposiçãomode** _desabilitado_ em um ambiente desse tipo, é útil saber antecipadamente se uma implantação de aplicativo ou de infraestrutura está violando as definições de política no início. Essa avaliação de política deve, portanto, ser uma etapa nesses fluxos de trabalho e implantações de falha que criam recursos sem conformidade.

## <a name="review"></a>Rever

Este artigo aborda o fluxo de trabalho geral da política como código e também onde a avaliação da política deve fazer parte de outros fluxos de trabalho de implantação. Esse fluxo de trabalho pode ser usado em qualquer ambiente que ofereça suporte a etapas e automação com script com base em gatilhos.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [estrutura de definição de política](./definition-structure.md).
- Saiba mais sobre a [estrutura de atribuição de política](./assignment-structure.md).
- Entenda como [criar políticas programaticamente](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/getting-compliance-data.md).
- Saiba como [corrigir recursos sem conformidade](../how-to/remediate-resources.md).
- Examine o que é um grupo de gerenciamento e [Organize seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).