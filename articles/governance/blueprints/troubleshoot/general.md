---
title: Resolver erros comuns
description: Aprenda a resolver problemas criando, atribuindo e removendo plantas como violações de políticas e funções de parâmetros de plantas.
ms.date: 01/15/2020
ms.topic: troubleshooting
ms.openlocfilehash: 7306e344a479008a87164a954c4444d375950b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157088"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Erros de resolução de problemas usando plantas azure

Pode ter erros ao criar, atribuir ou remover plantas. Este artigo descreve vários erros que podem ocorrer e como resolvê-los.

## <a name="finding-error-details"></a>Encontrar detalhes de erro

Muitos erros serão o resultado da atribuição de uma planta a um âmbito. Quando uma atribuição falha, a planta fornece detalhes sobre a implementação falhada. Estas informações indicam a questão para que possa ser corrigida e a próxima implementação tenha sucesso.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione **as plantas atribuídas** a partir da página à esquerda e utilize a caixa de pesquisa para filtrar as atribuições de plantas para encontrar a atribuição falhada. Também pode classificar a tabela de atribuições pela coluna do **Estado de Provisionamento** para ver todas as atribuições falhadas agruparadas.

1. Clique à esquerda na planta com o estado _falhado_ ou clique à direita e selecione **Ver detalhes**de atribuição .

1. Um aviso vermelho de que a atribuição falhou está no topo da página de atribuição de plantas. Clique em qualquer lugar do banner para obter mais detalhes.

É comum que o erro seja causado por um artefacto e não pela planta como um todo. Se um artefacto criar um Cofre chave e uma política azure impedir a criação do Cofre Chave, toda a missão falhará.

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-policy-violation"></a><a name="policy-violation"></a>Cenário: Violação de políticas

#### <a name="issue"></a>Problema

A implantação do modelo falhou devido à violação da política.

#### <a name="cause"></a>Causa

Uma política pode entrar em conflito com a implantação por uma série de razões:

- O recurso que está a ser criado é restringido pela política (geralmente SKU ou restrições de localização)
- A implantação é definir campos que são configurados por política (comum com etiquetas)

#### <a name="resolution"></a>Resolução

Mude o plano para que não entre em conflito com as políticas nos detalhes do erro. Se esta mudança não for possível, uma opção alternativa é alterar o âmbito da atribuição de políticas para que o projeto deixe de estar em conflito com a política.

### <a name="scenario-blueprint-parameter-is-a-function"></a><a name="escape-function-parameter"></a>Cenário: O parâmetro de planta é uma função

#### <a name="issue"></a>Problema

Os parâmetros de plantas que são funções são processados antes de serem passados para artefactos.

#### <a name="cause"></a>Causa

Passar um parâmetro de planta que `[resourceGroup().tags.myTag]`usa uma função, como, por exemplo, para um artefacto resulta no resultado processado da função que está a ser definida no artefacto em vez da função dinâmica.

#### <a name="resolution"></a>Resolução

Para passar uma função como parâmetro, escape `[` de toda a `[[resourceGroup().tags.myTag]`corda com tal que o parâmetro de planta se pareça . O personagem de fuga faz com que as Plantas tratem o valor como uma corda ao processar a planta. As plantas colocam então a função no artefacto permitindo que seja dinâmica como esperado. Para mais informações, consulte [Syntax e expressões nos modelos do Gestor de Recursos Azure](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="delete-errors"></a>Eliminar erros

### <a name="scenario-assignment-deletion-timeout"></a><a name="assign-delete-timeout"></a>Cenário: Tempo de eliminação da atribuição

#### <a name="issue"></a>Problema

A eliminação de uma atribuição de plantas não está completa.

#### <a name="cause"></a>Causa

Uma atribuição de plantas pode ficar presa num estado não terminal quando eliminada. Este estado é causado quando os recursos criados pela atribuição do projeto ainda estão pendentes de eliminação ou não devolvem um código de estado às Plantas Azure.

#### <a name="resolution"></a>Resolução

As atribuições de plantas num estado não terminal são automaticamente marcadas **falhadas** após um intervalo de _6 horas._ Uma vez que o tempo de tempo tenha ajustado o estado da atribuição da planta, o exclusão pode ser novamente experimentado.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

- Obtenha respostas de especialistas do Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
- Se precisar de mais ajuda, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.