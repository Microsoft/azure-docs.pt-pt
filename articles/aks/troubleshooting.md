---
title: Solucionar problemas comuns do serviço kubernetes do Azure
description: Saiba como solucionar problemas comuns ao usar o serviço de kubernetes do Azure (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 00fadd8a98ec4f58783ed8b407e2621a7c107149
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533533"
---
# <a name="aks-troubleshooting"></a>Solução de problemas do AKS

Ao criar ou gerenciar clusters do AKS (serviço kubernetes do Azure), ocasionalmente, você pode encontrar problemas. Este artigo fornece detalhes sobre alguns problemas comuns e etapas de solução de problemas.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Em geral, onde encontro informações sobre a depuração de problemas do kubernetes?

Experimente o [guia oficial para solucionar problemas](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)de clusters do kubernetes.
Há também um [Guia de solução de problemas](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md), publicado por um engenheiro da Microsoft para solucionar problemas de pods, nós, clusters e outros recursos.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Estou recebendo um erro de "cota excedido" durante a criação ou a atualização. O que devo fazer? 

Você precisa [solicitar núcleos](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Qual é a configuração máxima de pods por nó para AKS?

A configuração de pods máxima por nó será de 30 por padrão se você implantar um cluster AKS no portal do Azure.
A configuração de pods máxima por nó será 110 por padrão se você implantar um cluster AKS no CLI do Azure. (Verifique se você está usando a versão mais recente do CLI do Azure). Essa configuração padrão pode ser alterada usando o `–-max-pods` sinalizador `az aks create` no comando.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Estou recebendo um erro de insufficientSubnetSize ao implantar um cluster AKS com rede avançada. O que devo fazer?

Se o Azure CNI (rede avançada) for usado, o AKS prefixará o IP endereçado com base no "Max-pods" por nó configurado. O número de nós em um cluster AKS pode ser de qualquer lugar entre 1 e 110. Com base no máximo de pods configurado por nó, o tamanho da sub-rede deve ser maior que o "produto do número de nós e o Pod máximo por nó". A equação básica a seguir descreve isso:

O tamanho da sub-rede > número de nós no cluster (levando em consideração os requisitos de dimensionamento futuros) * máximo de pods por nó.

Para obter mais informações, consulte [planejar o endereçamento de IP para o cluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Meu pod está preso no modo CrashLoopBackOff. O que devo fazer?

Pode haver vários motivos para o Pod estar preso nesse modo. Você pode examinar:

* O próprio Pod, usando `kubectl describe pod <pod-name>`.
* Os logs, usando `kubectl log <pod-name>`.

Para obter mais informações sobre como solucionar problemas de Pod, consulte [depurar aplicativos](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Estou tentando habilitar o RBAC em um cluster existente. Como posso fazer isso?

Infelizmente, não há suporte para habilitar o controle de acesso baseado em função (RBAC) em clusters existentes no momento. Você deve criar explicitamente novos clusters. Se você usar a CLI, o RBAC será habilitado por padrão. Se você usar o portal do AKS, um botão de alternância para habilitar o RBAC estará disponível no fluxo de trabalho de criação.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Criei um cluster com o RBAC habilitado usando o CLI do Azure com os padrões ou o portal do Azure, e agora vejo muitos avisos no painel do kubernetes. O painel usado para funcionar sem avisos. O que devo fazer?

O motivo para os avisos no painel é que o cluster agora está habilitado com RBAC e o acesso a ele foi desabilitado por padrão. Em geral, essa abordagem é uma boa prática porque a exposição padrão do painel a todos os usuários do cluster pode levar a ameaças de segurança. Se você ainda quiser habilitar o painel, siga as etapas nesta postagem no [blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Não consigo me conectar ao painel. O que devo fazer?

A maneira mais fácil de acessar o serviço fora do cluster é executar `kubectl proxy`, quais proxies são enviados para a porta localhost 8001 para o servidor de API kubernetes. A partir daí, o servidor de API pode fazer proxy para `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`seu serviço:.

Se você não vir o painel kubernetes, verifique se o `kube-proxy` Pod está em execução `kube-system` no namespace. Se não estiver em um estado de execução, exclua o pod e ele será reiniciado.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Não consigo obter logs usando logs do kubectl ou não consigo me conectar ao servidor de API. Estou recebendo "erro do servidor: erro ao discar back-end: discar TCP...". O que devo fazer?

Verifique se o grupo de segurança de rede padrão não foi modificado e se a porta 22 e 9000 estão abertas para conexão com o servidor de API. Verifique se o `tunnelfront` Pod está em execução no namespace *Kube-System* usando o `kubectl get pods --namespace kube-system` comando. Se não estiver, force a exclusão do pod e ela será reiniciada.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Estou tentando atualizar ou dimensionar e estou recebendo uma mensagem: Erro ao alterar a propriedade ' imageReference ' não é permitido. Como fazer corrigir esse problema?

Você pode estar recebendo esse erro porque modificou as marcas nos nós de agente dentro do cluster AKS. Modificar e excluir marcas e outras propriedades de recursos no grupo de recursos MC_ * pode levar a resultados inesperados. Modificar os recursos no grupo MC_ * no cluster AKS interrompe o SLO (objetivo de nível de serviço).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Estou recebendo erros de que meu cluster está em estado de falha e a atualização ou o dimensionamento não funcionará até que seja corrigido

*Esta assistência para solução de problemas é direcionada de https://aka.ms/aks-cluster-failed*

Esse erro ocorre quando os clusters entram em um estado de falha por vários motivos. Siga as etapas abaixo para resolver o estado de falha do cluster antes de repetir a operação que falhou anteriormente:

1. Até que o cluster esteja fora `failed` do `upgrade` estado e `scale` as operações não tenham sucesso. As resoluções e problemas de raiz comuns incluem:
    * Dimensionamento com **cota de computação insuficiente (CRP)** . Para resolver, primeiro dimensione o cluster de volta para um estado de meta estável dentro da cota. Em seguida, siga estas [etapas para solicitar um aumento de cota de computação](../azure-supportability/resource-manager-core-quotas-request.md) antes de tentar escalar verticalmente novamente além dos limites de cota iniciais.
    * Dimensionamento de um cluster com rede avançada e **recursos de sub-rede (rede)** insuficientes. Para resolver, primeiro dimensione o cluster de volta para um estado de meta estável dentro da cota. Em seguida, siga [estas etapas para solicitar um aumento de cota de recursos](../azure-resource-manager/resource-manager-quota-errors.md#solution) antes de tentar escalar verticalmente novamente além dos limites de cota iniciais.
2. Depois que a causa subjacente da falha de atualização for resolvida, o cluster deverá estar em um estado com êxito. Quando um estado bem-sucedido for verificado, repita a operação original.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Estou recebendo erros ao tentar atualizar ou dimensionar o estado em que meu cluster está sendo atualizado no momento ou com falha na atualização

*Esta assistência para solução de problemas é direcionada de https://aka.ms/aks-pending-upgrade*

As operações de atualização e dimensionamento em um cluster com um único pool de nós ou um cluster com [vários pools de nós](use-multiple-node-pools.md) são mutuamente exclusivas. Você não pode ter um cluster ou pool de nós simultaneamente para atualizar e dimensionar. Em vez disso, cada tipo de operação deve ser concluído no recurso de destino antes da próxima solicitação no mesmo recurso. Como resultado, as operações são limitadas quando as operações de atualização ativa ou de escala estão ocorrendo ou tentadas e subsequentemente falharam. 

Para ajudar a diagnosticar a `az aks show -g myResourceGroup -n myAKSCluster -o table` execução do problema para recuperar o status detalhado no cluster. Com base no resultado:

* Se o cluster estiver sendo atualizado ativamente, aguarde até que a operação seja encerrada. Se tiver êxito, repita a operação anterior com falha novamente.
* Se o cluster tiver falhado na atualização, siga as etapas descritas na seção anterior.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Posso mover meu cluster para uma assinatura diferente ou minha assinatura com meu cluster para um novo locatário?

Se você moveu o cluster AKS para uma assinatura diferente ou o cluster que possui a assinatura para um novo locatário, o cluster perderá a funcionalidade devido à perda de atribuições de função e aos direitos de entidades de serviço. **AKs não dá suporte à movimentação de clusters entre assinaturas ou locatários** devido a essa restrição.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Estou recebendo erros ao tentar usar recursos que exigem conjuntos de dimensionamento de máquinas virtuais

*Esta assistência para solução de problemas é direcionada do aka.ms/aks-vmss-enablement*

Você pode receber erros que indicam que o cluster AKS não está em um conjunto de dimensionamento de máquinas virtuais, como o exemplo a seguir:

**O AgentPool ' AgentPool ' definiu o dimensionamento automático como habilitado, mas não está em conjuntos de dimensionamento de máquinas virtuais**

Para usar recursos como os pools de dimensionamento de vários nós ou do cluster, os clusters AKS devem ser criados para usar conjuntos de dimensionamento de máquinas virtuais. Os erros são retornados se você tentar usar recursos que dependem de conjuntos de dimensionamento de máquinas virtuais e você tiver como alvo um cluster AKS de conjunto de dimensionamento de máquinas não virtuais regular. O suporte ao conjunto de dimensionamento de máquinas virtuais está atualmente em visualização no AKS.

Siga as etapas *antes de começar* no documento apropriado para se registrar corretamente na visualização do recurso do conjunto de dimensionamento de máquinas virtuais e criar um cluster AKs:

* [Usar o dimensionamento de cluster](cluster-autoscaler.md)
* [Criar e usar vários pools de nós](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Quais restrições de nomenclatura são impostas para recursos e parâmetros AKS?

*Esta assistência para solução de problemas é direcionada do aka.ms/aks-naming-rules*

As restrições de nomenclatura são implementadas pela plataforma do Azure e AKS. Se um nome de recurso ou parâmetro quebrar uma dessas restrições, será retornado um erro solicitando que você forneça uma entrada diferente. As seguintes diretrizes de nomenclatura comuns se aplicam:

* O nome do grupo de recursos AKS *MC_* combina o nome do grupo de recursos e o nome do recurso. A sintaxe gerada automaticamente de `MC_resourceGroupName_resourceName_AzureRegion` não deve ser maior que 80 caracteres. Se necessário, reduza o tamanho do nome do grupo de recursos ou do nome do cluster AKS.
* O *dnsPrefix* deve começar e terminar com valores alfanuméricos. Os caracteres válidos incluem valores alfanuméricos e hifens (-). O *dnsPrefix* não pode incluir caracteres especiais, como um ponto (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Estou recebendo erros ao tentar criar, atualizar, dimensionar, excluir ou atualizar o cluster. essa operação não é permitida porque outra operação está em andamento.

*Esta assistência para solução de problemas é direcionada do aka.ms/aks-pending-operation*

As operações de cluster são limitadas quando uma operação anterior ainda está em andamento. Para recuperar um status detalhado do cluster, use o `az aks show -g myResourceGroup -n myAKSCluster -o table` comando. Use seu próprio grupo de recursos e o nome do cluster AKS, conforme necessário.

Com base na saída do status do cluster:

* Se o cluster estiver em qualquer estado de provisionamento diferente de *êxito* ou *falha*, aguarde até que a operação (*atualização/atualização/criação/dimensionamento/exclusão/migração*) seja encerrada. Quando a operação anterior for concluída, tente novamente a operação de cluster mais recente.

* Se o cluster tiver uma falha de atualização, siga as etapas descritas estou [recebendo erros de que meu cluster está em estado de falha e a atualização ou o dimensionamento não funcionará até que seja corrigido](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).
