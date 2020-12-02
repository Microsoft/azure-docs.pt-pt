---
title: Resolver erros comuns
description: Aprenda a resolver problemas com a criação de definições políticas, os vários SDK e o addon para Kubernetes.
ms.date: 12/01/2020
ms.topic: troubleshooting
ms.openlocfilehash: f3667988d527100507d308887338278e1200d454
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511003"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>Erros de resolução de problemas usando a Política Azure

Pode ter erros ao criar definições de política, trabalhar com a SDK ou configurar a Política Azure para o addon [Kubernetes.](../concepts/policy-for-kubernetes.md) Este artigo descreve vários erros gerais que podem ocorrer e como resolvê-los.

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

### <a name="scenario-compliance-not-as-expected"></a>Cenário: Conformidade não como esperado

#### <a name="issue"></a>Problema

Um recurso não está no estado de avaliação, seja _complacente_ ou _não conforme,_ que é esperado para esse recurso.

#### <a name="cause"></a>Causa

O recurso não está no âmbito correto para a atribuição de políticas ou a definição de política não funciona como pretendido.

#### <a name="resolution"></a>Resolução

Siga estes passos para resolver os problemas da sua definição política:

1. Em primeiro lugar, aguarde o tempo adequado para que uma avaliação complete e os resultados de conformidade fiquem disponíveis no portal Azure ou SDK. Para iniciar uma nova avaliação com Azure PowerShell ou REST API, consulte a [avaliação a pedido](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Verifique se os parâmetros de atribuição e o âmbito de atribuição estão corretamente definidos.
1. Verifique o [modo de definição de política](../concepts/definition-structure.md#mode):
   - Modo 'todos' para todos os tipos de recursos.
   - Modo 'indexado' se a definição de política verificar as etiquetas ou a localização.
1. Verifique se o âmbito do recurso não está [excluído](../concepts/assignment-structure.md#excluded-scopes) ou [isento.](../concepts/exemption-structure.md)
1. Se o cumprimento de uma atribuição de políticas mostrar `0/0` recursos, nenhum recursos foi determinado ser aplicável dentro do âmbito de atribuição. Verifique tanto a definição de política como o âmbito de atribuição.
1. Para um recurso não conforme que se esperava que fosse conforme, verifique [as razões determinantes para o incumprimento](../how-to/determine-non-compliance.md). A comparação da definição com o valor patrimonial avaliado indica por que razão um recurso não foi conforme.
   - Se o **valor-alvo** estiver errado, reveja a definição de política.
   - Se o **valor atual** estiver errado, valide a carga útil do recurso através `resources.azure.com` de .
1. Verifique [resolução de problemas: A aplicação não é tão esperada para](#scenario-enforcement-not-as-expected) outras questões e soluções comuns.

Se ainda tiver algum problema com a definição de política incorporada duplicada e personalizada ou a definição personalizada, crie um bilhete de apoio ao abrigo **da Autoria de uma política** para encaminhar o problema corretamente.

### <a name="scenario-enforcement-not-as-expected"></a>Cenário: Execução não como esperado

#### <a name="issue"></a>Problema

Um recurso que se espera que seja atuado pela Azure Policy não é e não há entrada no diário de atividades do [Azure](../../../azure-monitor/platform/platform-logs-overview.md).

#### <a name="cause"></a>Causa

A atribuição da política foi configurada para [a aplicação da Medida](../concepts/assignment-structure.md#enforcement-mode) de _Incapacidade_. Enquanto o modo de execução é desativado, o efeito de política não é aplicado e não há entrada no registo de Atividade.

#### <a name="resolution"></a>Resolução

Siga estes passos para resolver os problemas da aplicação da sua missão política:

1. Em primeiro lugar, aguarde o tempo adequado para que uma avaliação complete e os resultados de conformidade fiquem disponíveis no portal Azure ou SDK. Para iniciar uma nova avaliação com Azure PowerShell ou REST API, consulte a [avaliação a pedido](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Verifique se os parâmetros de atribuição e o âmbito de atribuição estão corretamente definidos e se **o modo de execução** está _ativado_.
1. Verifique o [modo de definição de política](../concepts/definition-structure.md#mode):
   - Modo 'todos' para todos os tipos de recursos.
   - Modo 'indexado' se a definição de política verificar as etiquetas ou a localização.
1. Verifique se o âmbito do recurso não está [excluído](../concepts/assignment-structure.md#excluded-scopes) ou [isento.](../concepts/exemption-structure.md)
1. Verifique se o payload do recurso corresponde à lógica da política. Isto pode ser feito [capturando um traço HAR](../../../azure-portal/capture-browser-trace.md) ou revendo as propriedades do modelo ARM.
1. Verifique [resolução de problemas: Conformidade não como esperado](#scenario-compliance-not-as-expected) para outras questões e soluções comuns.

Se ainda tiver algum problema com a definição de política incorporada duplicada e personalizada ou a definição personalizada, crie um bilhete de apoio ao abrigo **da Autoria de uma política** para encaminhar o problema corretamente.

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

## <a name="add-on-for-kubernetes-installation-errors"></a>Complemento para erros de instalação de Kubernetes

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

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Cenário: As identidades atribuídas pelo utilizador da máquina virtual Azure são substituídas por identidades geridas atribuídas pelo sistema

#### <a name="issue"></a>Problema

Após a atribuição de iniciativas de política de Configuração de Convidados para configurações de auditoria dentro de máquinas, as identidades geridas atribuídas pelo utilizador que foram atribuídas à máquina deixaram de ser atribuídas. Apenas uma identidade gerida atribuída pelo sistema é atribuída.

#### <a name="cause"></a>Causa

As definições de política anteriormente utilizadas nas definições de Implementação de Configuração de HóspedesIfNotExists garantiram que uma identidade atribuída ao sistema é atribuída à máquina, mas também removidas as atribuições de identidade atribuídas pelo utilizador.

#### <a name="resolution"></a>Resolução

As definições que anteriormente causaram este problema aparecem como \[ Deprecadas \] e são substituídas por definições políticas que gerem pré-requisitos sem remover a identidade gerida atribuída pelo utilizador. É necessário um passo manual. Eliminar quaisquer atribuições políticas existentes que estejam marcadas \[ Deprecadas \] e substituí-las pela iniciativa política pré-requisito atualizada e definições políticas que tenham o mesmo nome que o original.

Para obter uma narrativa detalhada, consulte a seguinte publicação de blog:

[Mudança importante lançada para as políticas de auditoria de Configuração de Hóspedes](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)

## <a name="add-on-for-kubernetes-general-errors"></a>Complemento para erros gerais de Kubernetes

### <a name="scenario-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-due-to-egress-restrictions"></a>Cenário: O add-on não consegue chegar ao ponto final do serviço Azure Policy devido a restrições de saída

#### <a name="issue"></a>Problema

O addon não pode chegar ao ponto final do serviço Azure Policy e devolve um dos seguintes erros:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Causa

Estas questões acontecem quando uma saída de fragmentação é bloqueada.

#### <a name="resolution"></a>Resolução

Certifique-se de que os domínios e portas dos seguintes artigos estão abertos:

- [Regras de rede de saída exigidas e FQDNs para clusters AKS](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Instalar Add-on de política Azure para Azure Arc ativado Kubernetes (pré-visualização)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-due-to-aad-pod-identity-configuration"></a>Cenário: O add-on é incapaz de chegar ao ponto final do serviço Azure Policy devido à configuração de identidade aad-pod

#### <a name="issue"></a>Problema

O addon não pode chegar ao ponto final do serviço Azure Policy e devolve um dos seguintes erros:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Causa

Este erro ocorre quando _a identidade do add-pod_ é instalada no cluster e as cápsulas _do sistema kube_ não são excluídas na _identidade aad-pod_.

As cápsulas de identidade de _aad-pod-identidade_ (NMI) modificam os iptables dos nós para intercetar chamadas para o ponto final dos metadados de exemplo de Azure. Esta configuração significa que qualquer pedido feito ao ponto final dos metadados é intercetado pelo NMI mesmo que a cápsula não utilize _a identidade aad-pod_.
**AzurePodIdentityExcepção** O CRD pode ser configurado para informar a _identidade aad-pod_ que quaisquer pedidos para o ponto final de metadados originários de uma cápsula que corresponda às etiquetas definidas em CRD devem ser proxiited sem qualquer processamento em NMI.

#### <a name="resolution"></a>Resolução

Excluir as cápsulas do sistema com `kubernetes.azure.com/managedby: aks` etiqueta no espaço de _nomes do sistema kube_ em _identidade aad-pod,_ configurando o **CRD de ID AzurePodIdentityException.**

Para obter mais informações, consulte [desativar a identidade do pod AAD para um pod/aplicação específico.](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)

Para configurar uma exceção, consulte este exemplo:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: mic-exception
  namespace: default
spec:
  podLabels:
    app: mic
    component: mic
---
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: aks-addon-exception
  namespace: kube-system
spec:
  podLabels:
    kubernetes.azure.com/managedby: aks
```

### <a name="scenario-the-resource-provider-isnt-registered"></a>Cenário: O Fornecedor de Recursos não está registado

#### <a name="issue"></a>Problema

O addon pode chegar ao ponto final do serviço Azure Policy, mas vê um dos seguintes erros em registos adicionais:

```
The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
https://aka.ms/policy-register-subscription for how to register subscriptions.
```

ou

```
policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
Code="InternalServerError" Message="Encountered an internal server error."
```

#### <a name="cause"></a>Causa

O `Microsoft.PolicyInsights` fornecedor de recursos não está registado e deve ser registado para o addon para obter definições de política e dados de conformidade de devolução.

#### <a name="resolution"></a>Resolução

Registe o `Microsoft.PolicyInsights` fornecedor de recursos na subscrição do cluster. Para obter instruções, consulte [Registar um fornecedor de recursos.](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)

### <a name="scenario-the-subscription-is-disabled"></a>Cenário: A subscrição está desativada

#### <a name="issue"></a>Problema

O addon pode chegar ao ponto final do serviço Azure Policy, mas vê o seguinte erro:

```
The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.
```

#### <a name="cause"></a>Causa

Este erro significa que a subscrição foi determinada como problemática e a bandeira de recurso `Microsoft.PolicyInsights/DataPlaneBlocked` foi adicionada para bloquear a subscrição.

#### <a name="resolution"></a>Resolução

Contacte a equipa de recurso `azuredg@microsoft.com` para investigar e resolver este problema.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

- Obtenha respostas de especialistas através do [Microsoft Q&A](/answers/topics/azure-policy.html).
- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
- Se precisar de mais ajuda, pode apresentar um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Apoio**.
