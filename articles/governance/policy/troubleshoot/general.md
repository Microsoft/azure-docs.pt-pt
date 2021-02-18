---
title: Resolver erros comuns
description: Aprenda a resolver problemas com a criação de definições políticas, os vários SDKs e o addon para Kubernetes.
ms.date: 01/26/2021
ms.topic: troubleshooting
ms.openlocfilehash: 6e0e4067f07266bae9c87fd4443d27314cc28c0b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592616"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>Erros de resolução de problemas com a utilização da Política Azure

Quando cria definições de política, trabalha com SDKs ou configura a Política Azure para o addon [Kubernetes,](../concepts/policy-for-kubernetes.md) poderá ter erros. Este artigo descreve vários erros gerais que podem ocorrer, e sugere formas de resolvê-los.

## <a name="find-error-details"></a>Encontrar detalhes de erro

A localização dos detalhes do erro depende do aspeto da Política Azure com que está a trabalhar.

- Se estiver a trabalhar com uma política personalizada, vá ao portal Azure para obter feedback de linagem sobre o esquema, ou reveja os dados de [conformidade resultantes](../how-to/get-compliance-data.md) para ver como os recursos foram avaliados.
- Se estiver a trabalhar com algum dos vários SDKs, o SDK fornece detalhes sobre o porquê da função ter falhado.
- Se estiver a trabalhar com o addon para Kubernetes, comece com o [registo no](../concepts/policy-for-kubernetes.md#logging) cluster.

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-alias-not-found"></a>Cenário: Pseudónimo não encontrado

#### <a name="issue"></a>Problema

Um pseudónimo incorreto ou inexistente é usado numa definição de política. A Azure Policy usa [pseudónimos](../concepts/definition-structure.md#aliases) para mapear propriedades do Gestor de Recursos Azure.

#### <a name="cause"></a>Causa

Um pseudónimo incorreto ou inexistente é usado numa definição de política.

#### <a name="resolution"></a>Resolução

Em primeiro lugar, valide que a propriedade do Gestor de Recursos tem um pseudónimo. Para procurar os pseudónimos disponíveis, vá à [extensão da Política Azure para o Código do Estúdio Visual](../how-to/extension-for-vscode.md) ou para o SDK.
Se o pseudónimo de uma propriedade do Gestor de Recursos não existir, crie um bilhete de apoio.

### <a name="scenario-evaluation-details-arent-up-to-date"></a>Cenário: Os detalhes da avaliação não estão atualizados

#### <a name="issue"></a>Problema

Um recurso está no estado _de Não Iniciado,_ ou os detalhes de conformidade não estão correntes.

#### <a name="cause"></a>Causa

Uma nova atribuição de políticas ou iniciativas leva cerca de 30 minutos a ser aplicada. Os recursos novos ou atualizados no âmbito de uma atribuição existente ficam disponíveis em cerca de 15 minutos. Uma verificação padrão de conformidade ocorre a cada 24 horas. Para obter mais informações, consulte [os gatilhos de avaliação.](../how-to/get-compliance-data.md#evaluation-triggers)

#### <a name="resolution"></a>Resolução

Em primeiro lugar, aguarde um tempo adequado para que uma avaliação termine e os resultados de conformidade fiquem disponíveis no portal Azure ou no SDK. Para iniciar uma nova avaliação com a Azure PowerShell ou a API REST, consulte a [avaliação a pedido](../how-to/get-compliance-data.md#on-demand-evaluation-scan).

### <a name="scenario-compliance-isnt-as-expected"></a>Cenário: A conformidade não é o esperado

#### <a name="issue"></a>Problema

Um recurso não está no estado de avaliação _compatível_ ou _não conforme_ que é esperado para o recurso.

#### <a name="cause"></a>Causa

O recurso não está no âmbito correto para a atribuição de políticas, ou a definição de política não funciona como pretendido.

#### <a name="resolution"></a>Resolução

Para resolver problemas na sua definição de política, faça o seguinte:

1. Em primeiro lugar, aguarde o tempo adequado para que uma avaliação termine e os resultados de conformidade fiquem disponíveis no portal Azure ou SDK. 

1. Para iniciar uma nova avaliação com a Azure PowerShell ou a API REST, consulte a [avaliação a pedido](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Certifique-se de que os parâmetros de atribuição e o âmbito de atribuição estão corretamente definidos.
1. Verifique o [modo de definição de política](../concepts/definition-structure.md#mode):
   - O modo deve ser `all` para todos os tipos de recursos.
   - O modo deve ser `indexed` se a definição de política verificar as etiquetas ou a localização.
1. Certifique-se de que o âmbito do recurso não está [excluído](../concepts/assignment-structure.md#excluded-scopes) ou [isento.](../concepts/exemption-structure.md)
1. Se o cumprimento de uma atribuição de políticas mostrar `0/0` recursos, nenhum recursos foi determinado ser aplicável dentro do âmbito de atribuição. Verifique tanto a definição de política como o âmbito de atribuição.
1. Para um recurso não conforme que se esperava que fosse conforme, consulte [as razões do incumprimento](../how-to/determine-non-compliance.md). A comparação da definição com o valor da propriedade avaliada indica por que razão um recurso não foi conforme.
   - Se o **valor-alvo** estiver errado, reveja a definição de política.
   - Se o **valor atual** estiver errado, valide a carga útil do recurso através `resources.azure.com` de .
1. Para outras questões e soluções comuns, consulte [Troubleshoot: Enforcement not as expected](#scenario-enforcement-not-as-expected).

Se ainda tiver algum problema com a definição de política incorporada duplicada e personalizada ou a definição personalizada, crie um bilhete de apoio ao abrigo **da Autoria de uma política** para encaminhar o problema corretamente.

### <a name="scenario-enforcement-not-as-expected"></a>Cenário: Execução não como esperado

#### <a name="issue"></a>Problema

Um recurso que espera que a Azure Policy atue não está a ser atuado, e não há entrada no diário de atividades do [Azure](../../../azure-monitor/essentials/platform-logs-overview.md).

#### <a name="cause"></a>Causa

A atribuição da política foi configurada para uma definição [**de medida de execução**](../concepts/assignment-structure.md#enforcement-mode) de _deficientes_.
Enquanto **a polícia Desativa** a mãe, o efeito da política não é aplicado, e não há entrada no registo de atividades.

#### <a name="resolution"></a>Resolução

Resolva os problemas da aplicação da sua missão política fazendo o seguinte:

1. Em primeiro lugar, aguarde o tempo adequado para que uma avaliação termine e os resultados de conformidade fiquem disponíveis no portal Azure ou no SDK. 

1. Para iniciar uma nova avaliação com a Azure PowerShell ou a API REST, consulte a [avaliação a pedido](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Certifique-se de que os parâmetros de atribuição e o âmbito de atribuição estão corretamente definidos e que **o modo de execução** está _ativado_.
1. Verifique o [modo de definição de política](../concepts/definition-structure.md#mode):
   - O modo deve ser `all` para todos os tipos de recursos.
   - O modo deve ser `indexed` se a definição de política verificar as etiquetas ou a localização.
1. Certifique-se de que o âmbito do recurso não está [excluído](../concepts/assignment-structure.md#excluded-scopes) ou [isento.](../concepts/exemption-structure.md)
1. Verifique se a carga útil do recurso corresponde à lógica da política. Isto pode ser feito [capturando um traço HTTP Archive (HAR)](../../../azure-portal/capture-browser-trace.md) ou revendo as propriedades do modelo do Gestor de Recursos Azure (modelo ARM).
1. Para outras questões e soluções comuns, consulte [Resolução de Problemas: Conformidade não como esperado](#scenario-compliance-isnt-as-expected).

Se ainda tiver algum problema com a definição de política incorporada duplicada e personalizada ou a definição personalizada, crie um bilhete de apoio ao abrigo **da Autoria de uma política** para encaminhar o problema corretamente.

### <a name="scenario-denied-by-azure-policy"></a>Cenário: Negado pela Política Azure

#### <a name="issue"></a>Problema

A criação ou atualização de um recurso é negada.

#### <a name="cause"></a>Causa

Uma atribuição de política ao âmbito do seu novo recurso ou atualizado satisfaz os critérios de uma definição de política com um efeito [Deny.](../concepts/effects.md#deny) Os recursos que satisfaçam estas definições estão impedidos de serem criados ou atualizados.

#### <a name="resolution"></a>Resolução

A mensagem de erro de uma atribuição de política de negação inclui a definição de política e iDs de atribuição de políticas. Se a informação de erro na mensagem for perdida, também está disponível no [registo de Atividades](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log). Utilize estas informações para obter mais detalhes para compreender as restrições de recursos e ajustar as propriedades de recursos no seu pedido para corresponder aos valores permitidos.

## <a name="template-errors"></a>Erros do modelo

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Cenário: Funções apoiadas por políticas processadas por modelo

#### <a name="issue"></a>Problema

A Azure Policy suporta uma série de funções e funções do modelo ARM que estão disponíveis apenas numa definição de política. O Gestor de Recursos processa estas funções como parte de uma implementação em vez de como parte de uma definição de política.

#### <a name="cause"></a>Causa

A utilização de funções suportadas, tais como `parameter()` `resourceGroup()` ou, resulta no resultado processado da função no tempo de implantação em vez de permitir o processo da função para a definição de política e o motor da Política Azure.

#### <a name="resolution"></a>Resolução

Para passar uma função como parte de uma definição de política, escapar de toda a cadeia com `[` tal que a propriedade se `[[resourceGroup().tags.myTag]` pareça. O personagem de fuga faz com que o Gestor de Recursos trate o valor como uma cadeia quando processa o modelo. A Política Azure coloca então a função na definição de política, o que permite que seja dinâmica como esperado. Para obter mais informações, consulte [Sintaxe e expressões nos modelos do Gestor de Recursos Azure](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-for-kubernetes-installation-errors"></a>Complemento para erros de instalação de Kubernetes

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>Cenário: A instalação utilizando um Gráfico de Leme falha devido a um erro de senha

#### <a name="issue"></a>Problema

O `helm install azure-policy-addon` comando falha e devolve um dos seguintes erros:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Causa

A palavra-passe gerada inclui uma vírgula ( `,` ), em que o Gráfico de Leme está dividindo-se.

#### <a name="resolution"></a>Resolução

Quando `helm install azure-policy-addon` correr, escape a vírgula `,` ( ) no valor da palavra-passe com um backslash `\` ().

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>Cenário: A instalação utilizando um Gráfico de Leme falha porque o nome já existe

#### <a name="issue"></a>Problema

O `helm install azure-policy-addon` comando falha e devolve o seguinte erro:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Causa

O Gráfico helm com o nome `azure-policy-addon` já foi instalado ou parcialmente instalado.

#### <a name="resolution"></a>Resolução

Siga as instruções para [remover a Política Azure para o add-on de Kubernetes](../concepts/policy-for-kubernetes.md#remove-the-add-on)e, em seguida, reexame o `helm install azure-policy-addon` comando.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Cenário: As identidades atribuídas pelo utilizador da máquina virtual Azure são substituídas por identidades geridas atribuídas pelo sistema

#### <a name="issue"></a>Problema

Depois de atribuir iniciativas de política de Configuração de Hóspedes a definições de auditoria dentro de uma máquina, as identidades geridas atribuídas pelo utilizador que foram atribuídas à máquina deixaram de ser atribuídas. Apenas uma identidade gerida atribuída pelo sistema é atribuída.

#### <a name="cause"></a>Causa

As definições de política anteriormente utilizadas nas definições de Implementação de Configuração de HóspedesIfNotExists garantiram que uma identidade atribuída ao sistema é atribuída à máquina, mas também removeram as atribuições de identidade atribuídas pelo utilizador.

#### <a name="resolution"></a>Resolução

As definições que anteriormente causaram este problema aparecem como _\[ Deprecadas \]_, e são substituídas por definições políticas que gerem pré-requisitos sem remover identidades geridas atribuídas pelo utilizador. É necessário um passo manual. Elimine quaisquer atribuições políticas existentes que sejam marcadas como _\[ Deprecadas \]_, e substitua-as pela iniciativa política pré-requisito atualizada e definições políticas que tenham o mesmo nome que o original.

Para obter uma narrativa detalhada, consulte o post de [blogSa alteração importante lançada para as políticas de auditoria de Configuração de Convidados](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316).

## <a name="add-on-for-kubernetes-general-errors"></a>Complemento para erros gerais de Kubernetes

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>Cenário: O addon é incapaz de chegar ao ponto final do serviço Azure Policy devido a restrições de saída

#### <a name="issue"></a>Problema

O addon não pode chegar ao ponto final do serviço Azure Policy, e devolve um dos seguintes erros:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Causa

Esta questão ocorre quando uma saída de cluster é bloqueada.

#### <a name="resolution"></a>Resolução

Certifique-se de que os domínios e portas mencionados nos seguintes artigos estão abertos:

- [Regras de rede de saída necessárias e nomes de domínio totalmente qualificados (FQDNs) para clusters AKS](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Instale o addon Azure Policy para Kubernetes ativados pelo Arco Azure (pré-visualização)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>Cenário: O addon é incapaz de chegar ao ponto final do serviço Azure Policy devido à configuração de identidade aad-pod

#### <a name="issue"></a>Problema

O addon não pode chegar ao ponto final do serviço Azure Policy, e devolve um dos seguintes erros:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Causa

Este erro ocorre quando _a identidade do add-pod_ é instalada no cluster e as cápsulas do _sistema kube_ não são excluídas na _identidade aad-pod_.

As cápsulas de identidade de _aad-pod -identidade_ (NMI) modificam os iptables dos nós para intercetar chamadas para o ponto final de metadados de instância Azure. Esta configuração significa que qualquer pedido feito para o ponto final dos metadados é intercetado pelo NMI, mesmo que a cápsula não utilize _a identidade aad-pod_.
O _AzurePodIdentityException_ CustomResourceDefinition (CRD) pode ser configurado para informar a _identidade aad-pod_ que quaisquer pedidos a um ponto final de metadados que tenham origem num pod que corresponda às etiquetas definidas no CRD devem ser proxiitizadas sem qualquer processamento no NMI.

#### <a name="resolution"></a>Resolução

Excluir as cápsulas de sistema que têm a `kubernetes.azure.com/managedby: aks` etiqueta no espaço de nome do sistema _kube_ em _identidade aad-pod,_ configurando o CRD de _ID AzurePodIdentityException._

Para obter mais informações, consulte [desativar a identidade do pod do Azure Ative (Azure AD) para uma cápsula/aplicação específica](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).

Para configurar uma exceção, siga este exemplo:

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

### <a name="scenario-the-resource-provider-isnt-registered"></a>Cenário: O fornecedor de recursos não está registado

#### <a name="issue"></a>Problema

O addon pode chegar ao ponto final do serviço Azure Policy, mas os registos adicionais apresentam um dos seguintes erros:

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
  https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
  StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
  Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>Causa

O fornecedor de recursos 'Microsoft.PolicyInsights' não está registado. Deve ser registado para o complemento para obter definições políticas e dados de conformidade de devolução.

#### <a name="resolution"></a>Resolução

Registe o fornecedor de recursos 'Microsoft.PolicyInsights' na subscrição do cluster. Para obter instruções, consulte [Registar um fornecedor de recursos.](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)

### <a name="scenario-the-subscription-is-disabled"></a>Cenário: A subscrição está desativada

#### <a name="issue"></a>Problema

O addon pode chegar ao ponto final do serviço Azure Policy, mas é apresentado o seguinte erro:

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>Causa

Este erro significa que a subscrição foi determinada como problemática, e a bandeira de recurso `Microsoft.PolicyInsights/DataPlaneBlocked` foi adicionada para bloquear a subscrição.

#### <a name="resolution"></a>Resolução

Para investigar e resolver este problema, [contacte a equipa de recurso.](mailto:azuredg@microsoft.com)

## <a name="next-steps"></a>Passos seguintes

Se o seu problema não estiver listado neste artigo ou não conseguir resolvê-lo, obtenha apoio visitando um dos seguintes canais:

- Obtenha respostas de especialistas através do [Microsoft Q&A](/answers/topics/azure-policy.html).
- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) . Este recurso oficial da Microsoft Azure no Twitter ajuda a melhorar a experiência do cliente ligando a comunidade Azure às respostas, suporte e especialistas certos.
- Se ainda precisar de ajuda, vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e selecione **Enviar um pedido de apoio.**
