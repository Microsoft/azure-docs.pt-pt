---
title: Resolver erros comuns
description: Aprenda a resolver problemas criando, atribuindo e removendo plantas como violações de políticas e funções de parâmetros de planta.
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.openlocfilehash: 65cf8ef9a5dcba0165aad8522f91ff1eb2c963a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98918849"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Erros de resolução de problemas usando plantas Azure

Pode encontrar erros ao criar, atribuir ou remover plantas. Este artigo descreve vários erros que podem ocorrer e como resolvê-los.

## <a name="finding-error-details"></a>Encontrar detalhes de erro

Muitos erros serão o resultado da atribuição de uma planta a um âmbito. Quando uma atribuição falha, a planta fornece detalhes sobre a implementação falhada. Esta informação indica o problema para que possa ser corrigida e a próxima implantação seja bem sucedida.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione **as plantas atribuídas** a partir da página à esquerda e use a caixa de pesquisa para filtrar as atribuições de planta para encontrar a atribuição falhada. Também pode classificar a tabela de atribuições pela coluna **Provisioning State** para ver todas as atribuições falhadas agrupadas.

1. Selecione a planta com o estado _falhado_ ou clique à direita e selecione **Ver detalhes da atribuição**.

1. Um aviso vermelho de que a atribuição falhou está no topo da página de atribuição de plantas. Selecione qualquer lugar no banner para obter mais detalhes.

É comum que o erro seja causado por um artefacto e não pela planta como um todo. Se um artefacto criar um Cofre-Chave e a Política Azure impedir a criação do Key Vault, toda a atribuição falhará.

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-policy-violation"></a><a name="policy-violation"></a>Cenário: Violação de Políticas

#### <a name="issue"></a>Problema

A implementação do modelo falhou devido à violação de políticas.

#### <a name="cause"></a>Causa

Uma política pode entrar em conflito com a implantação por uma série de razões:

- O recurso criado é restringido pela política (geralmente as restrições de SKU ou de localização)
- A implantação é definir campos que são configurados por política (comum com tags)

#### <a name="resolution"></a>Resolução

Mude o plano para que não entre em conflito com as políticas nos detalhes de erro. Se esta mudança não for possível, uma opção alternativa é alterar o âmbito da atribuição da política para que o projeto deixe de estar em conflito com a política.

### <a name="scenario-blueprint-parameter-is-a-function"></a><a name="escape-function-parameter"></a>Cenário: O parâmetro da planta é uma função

#### <a name="issue"></a>Problema

Os parâmetros de planta que são funções são processados antes de serem passados para artefactos.

#### <a name="cause"></a>Causa

Passar um parâmetro de planta que usa uma função, `[resourceGroup().tags.myTag]` como, por exemplo, a um artefacto resulta no resultado processado da função que está a ser definida no artefacto em vez da função dinâmica.

#### <a name="resolution"></a>Resolução

Para passar uma função como parâmetro, escape toda a corda com `[` tal que o parâmetro da planta se pareça `[[resourceGroup().tags.myTag]` . O personagem de fuga faz com que as Plantas tratem o valor como uma corda ao processar a planta. O serviço Blueprints coloca então a função no artefacto permitindo que seja dinâmico como esperado. Para obter mais informações, consulte [Sintaxe e expressões nos modelos do Gestor de Recursos Azure](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="delete-errors"></a>Eliminar erros

### <a name="scenario-assignment-deletion-timeout"></a><a name="assign-delete-timeout"></a>Cenário: Tempo limite de eliminação de atribuição

#### <a name="issue"></a>Problema

A eliminação de uma missão de planta não está completa.

#### <a name="cause"></a>Causa

Uma atribuição de plantas pode ficar presa num estado não terminal quando eliminada. Este estado é causado quando os recursos criados pela atribuição do projeto ainda estão pendentes de eliminação ou não devolvem um código de estado à Azure Blueprints.

#### <a name="resolution"></a>Resolução

As atribuições de plantas num estado não terminal são automaticamente **marcadas Falha após** um intervalo _de seis horas._ Uma vez que o tempo limite tenha ajustado o estado da atribuição da planta, a eliminação pode ser novamente experimentada.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

- Obtenha respostas de especialistas da Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
- Se precisar de mais ajuda, pode apresentar um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Apoio**.