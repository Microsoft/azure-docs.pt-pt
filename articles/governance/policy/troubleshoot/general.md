---
title: Resolver erros comuns
description: Aprenda a resolver problemas com a criação de definições políticas, os vários SDK e o addon para Kubernetes.
ms.date: 05/22/2020
ms.topic: troubleshooting
ms.openlocfilehash: 6d23a148521506adf0c0fc16913a32aab5eb7a30
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135576"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>Erros de resolução de problemas usando a Política Azure

Pode ter erros ao criar definições de política, trabalhar com a SDK ou configurar a Política Azure para o addon [Kubernetes.](../concepts/policy-for-kubernetes.md) Este artigo descreve vários erros que podem ocorrer e como resolvê-los.

## <a name="finding-error-details"></a>Encontrar detalhes de erro

A localização dos detalhes de erro depende da ação que causa o erro.

- Ao trabalhar com uma política personalizada, experimente-a no portal Azure para obter feedback de linagem sobre o esquema ou rever os dados de [conformidade resultantes](../how-to/get-compliance-data.md) para ver como os recursos foram avaliados.
- Ao trabalhar com vários SDK, o SDK fornece detalhes sobre o porquê da função ter falhado.
- Ao trabalhar com o addon para Kubernetes, comece com a [exploração madeireira](../concepts/policy-for-kubernetes.md#logging) no cluster.

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-alias-not-found"></a>Cenário: Pseudónimo não encontrado

#### <a name="issue"></a>Problema

A Azure Policy usa [pseudónimos](../concepts/definition-structure.md#aliases) para mapear propriedades do Gestor de Recursos Azure.

#### <a name="cause"></a>Causa

Um pseudónimo incorreto ou inexistente é utilizado numa definição de política.

#### <a name="resolution"></a>Resolução

Em primeiro lugar, valide que a propriedade do Gestor de Recursos tem um pseudónimo. Utilize [a extensão da Política Azure para o Código do Estúdio Visual](../how-to/extension-for-vscode.md), Gráfico de Recursos [Azure](../../resource-graph/samples/starter.md#distinct-alias-values)ou SDK para procurar pseudónimos disponíveis. Se o pseudónimo de uma propriedade do Gestor de Recursos não existir, crie um bilhete de apoio.

### <a name="scenario-evaluation-details-not-up-to-date"></a>Cenário: Detalhes da avaliação não atualizados

#### <a name="issue"></a>Problema

Um recurso está no estado "Não Iniciado" ou os detalhes de conformidade não estão em vigor.

#### <a name="cause"></a>Causa

Uma nova atribuição de políticas ou iniciativas leva cerca de 30 minutos a ser aplicada. Os recursos novos ou atualizados no âmbito de uma atribuição existente ficam disponíveis cerca de 15 minutos depois. Um exame de conformidade padrão acontece a cada 24 horas. Para obter mais informações, consulte [os gatilhos de avaliação.](../how-to/get-compliance-data.md#evaluation-triggers)

#### <a name="resolution"></a>Resolução

Em primeiro lugar, aguarde o tempo adequado para que uma avaliação complete e os resultados de conformidade fiquem disponíveis no portal Azure ou SDK. Para iniciar uma nova avaliação com Azure PowerShell ou REST API, consulte a [avaliação a pedido](../how-to/get-compliance-data.md#on-demand-evaluation-scan).

### <a name="scenario-evaluation-not-as-expected"></a>Cenário: Avaliação não como esperado

#### <a name="issue"></a>Problema

Um recurso não está no estado de avaliação, seja _complacente_ ou _não conforme,_ que é esperado para esse recurso.

#### <a name="cause"></a>Causa

O recurso não está no âmbito correto para a atribuição de políticas ou a definição de política não funciona como pretendido.

#### <a name="resolution"></a>Resolução

- Para um recurso não conforme que se esperava que fosse conforme, comece por [determinar as razões do incumprimento](../how-to/determine-non-compliance.md). A comparação da definição com o valor patrimonial avaliado indica por que razão um recurso não foi conforme.
- Para um recurso conforme que se esperava não conforme, leia a condição de definição de política por condição e avalie contra as propriedades dos recursos. Valide que os operadores lógicos estão a agrupar as condições certas e que as suas condições não estão invertidas.

Se o cumprimento de uma atribuição de políticas mostrar `0/0` recursos, nenhum recursos foi determinado ser aplicável dentro do âmbito de atribuição. Verifique tanto a definição de política como o âmbito de atribuição.

### <a name="scenario-enforcement-not-as-expected"></a>Cenário: Execução não como esperado

#### <a name="issue"></a>Problema

Um recurso que se espera que seja atuado pela Azure Policy não é e não há entrada no diário de atividades do [Azure](../../../azure-monitor/platform/platform-logs-overview.md).

#### <a name="cause"></a>Causa

A atribuição da política foi configurada para [a aplicação da Medida](../concepts/assignment-structure.md#enforcement-mode) de _Incapacidade_. Enquanto o modo de execução é desativado, o efeito de política não é aplicado e não há entrada no registo de Atividade.

#### <a name="resolution"></a>Resolução

Atualizar **execuçãoMode** para _Ativar_. Esta alteração permite que a Azure Policy atue sobre os recursos desta atribuição de políticas e envie entradas para o registo de atividades. Se **a aplicação da leiMode** já estiver ativada, consulte [a Avaliação não como esperado](#scenario-evaluation-not-as-expected) para os cursos de ação.

### <a name="scenario-denied-by-azure-policy"></a>Cenário: Negado pela Política Azure

#### <a name="issue"></a>Problema

A criação ou atualização de um recurso é negada.

#### <a name="cause"></a>Causa

Uma atribuição de política ao âmbito do seu novo ou atualizado recurso está em cumprimento dos critérios de uma definição de política com um efeito [Deny.](../concepts/effects.md#deny) Os recursos são impedidos de criar ou atualizar estas definições.

#### <a name="resolution"></a>Resolução

A mensagem de erro de uma atribuição de política de negação inclui a definição de política e iDs de atribuição de políticas. Se a informação de erro na mensagem for perdida, também está disponível no [registo de Atividades](../../../azure-monitor/platform/activity-log.md#view-the-activity-log). Utilize estas informações para obter mais detalhes para compreender as restrições de recursos e ajustar as propriedades de recursos no seu pedido para corresponder aos valores permitidos.

## <a name="template-errors"></a>Erros do modelo

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Cenário: Funções apoiadas por políticas processadas por modelo

#### <a name="issue"></a>Problema

A Azure Policy suporta uma série de funções e funções do Azure Resource Manager (modelo ARM) que só estão disponíveis numa definição de política. O Gestor de Recursos processa estas funções como parte de uma implementação em vez de como parte de uma definição de política.

#### <a name="cause"></a>Causa

A utilização de funções suportadas, tais como `parameter()` `resourceGroup()` ou, resulta no resultado processado da função no tempo de implantação em vez de deixar a função para a definição de política e motor Azure Policy para processar.

#### <a name="resolution"></a>Resolução

Para passar uma função para fazer parte de uma definição de política, escapar de toda a cadeia com `[` tal que a propriedade se `[[resourceGroup().tags.myTag]` pareça. O personagem de fuga faz com que o Gestor de Recursos trate o valor como uma cadeia ao processar o modelo. A Política Azure coloca então a função na definição de política, permitindo que seja dinâmica como esperado. Para obter mais informações, consulte [Sintaxe e expressões nos modelos do Gestor de Recursos Azure](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-installation-errors"></a>Erros de instalação de complemento

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>Cenário: Instalar usando o Gráfico de Leme falha na palavra-passe

#### <a name="issue"></a>Problema

O `helm install azure-policy-addon` comando falha com uma das seguintes mensagens:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Causa

A palavra-passe gerada inclui uma vírgula em `,` que o Helm Chart está a dividir-se.

#### <a name="resolution"></a>Resolução

Escape à vírgula `,` no valor da palavra-passe quando correr `helm install azure-policy-addon` com uma pestana traseira ( `\` ).

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>Cenário: Instalar usando o Gráfico helm falha como o nome já existe

#### <a name="issue"></a>Problema

O `helm install azure-policy-addon` comando falha com a seguinte mensagem:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Causa

O Gráfico helm com o nome `azure-policy-addon` já foi instalado ou parcialmente instalado.

#### <a name="resolution"></a>Resolução

Siga as instruções para [remover a Política de Azure para o add-on de Kubernetes](../concepts/policy-for-kubernetes.md#remove-the-add-on)e, em seguida, repercuta o `helm install azure-policy-addon` comando.

## <a name="next-steps"></a>Próximos passos

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

- Obtenha respostas de especialistas através do [Microsoft Q&A](/answers/topics/azure-policy.html).
- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
- Se precisar de mais ajuda, pode apresentar um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Apoio**.
