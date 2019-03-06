---
title: Resolver problemas comuns do serviço Kubernetes do Azure
description: Saiba como resolver problemas comuns ao utilizar o Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 5902ba86b51ca1998364e393ac02bbb0d0a23a28
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432639"
---
# <a name="aks-troubleshooting"></a>Resolução de problemas do AKS

Quando criar ou gerir os clusters do Azure Kubernetes Service (AKS), ocasionalmente, poderá ter problemas. Este artigo fornece detalhes sobre alguns problemas comuns e passos de resolução de problemas.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Em geral, onde posso encontrar informações sobre a depuração de problemas do Kubernetes?

Experimente o [guia oficial para resolução de problemas de clusters do Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Há também uma [guia de resolução de problemas](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md), publicados por um engenheiro da Microsoft para resolução de problemas de pods, nós, clusters e outros recursos.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Estou recebendo um erro de "quota excedida" durante a criação ou atualização. O que devo fazer? 

Precisa [pedir núcleos](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>O que é a configuração máxima de pods por nó para o AKS?

A configuração de pods por nó máxima é 30 por predefinição, se implementar um cluster do AKS no portal do Azure.
A configuração de pods por nó máxima é 110 por predefinição, se implementar um cluster do AKS na CLI do Azure. (Certifique-se de que está a utilizar a versão mais recente da CLI do Azure). Essa configuração padrão pode ser alterada utilizando o `–-max-pods` sinalizar no `az aks create` comando.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Estou recebendo um erro de insufficientSubnetSize durante a implementação de um cluster do AKS com a rede avançada. O que devo fazer?

Se for utilizado CNI do Azure (sistema de rede avançado), o AKS preallocates IP resolvido com base no "max-pods" por nó configurado. O número de nós num cluster do AKS pode ser qualquer número entre 1 e 110. Com base nos pods máximos configurados por nó, o tamanho da sub-rede deve ser superior a "produto do número de nós e o pod máximo por nó". A equação básica seguinte descreve isto:

Tamanho da sub-rede > número de nós do cluster (levando em consideração os requisitos de dimensionamento futuros) * max pods por nó.

Para obter mais informações, consulte [endereçamento IP planear para o seu cluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Meu pod está bloqueada no modo de CrashLoopBackOff. O que devo fazer?

Pode haver vários motivos para o pod bloqueados nesse modo. Procurar em:

* O pod em si, ao utilizar `kubectl describe pod <pod-name>`.
* Os registos, utilizando `kubectl log <pod-name>`.

Para obter mais informações sobre como solucionar problemas de pod, consulte [depurar aplicativos](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Estou a tentar ativar o RBAC num cluster existente. Como posso fazer isso?

Infelizmente, ativar o controlo de acesso baseado em funções (RBAC) em clusters existentes não é suportada neste momento. Tem de criar explicitamente novos clusters. Se utilizar a CLI, RBAC está ativado por predefinição. Se utilizar o portal do AKS, um botão de alternância para ativar o RBAC está disponível no fluxo de trabalho de criação.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Criei um cluster com RBAC ativado ao utilizar a CLI do Azure com as predefinições ou o portal do Azure e, agora posso ver muitos avisos no dashboard do Kubernetes. O dashboard utilizado para funcionar sem quaisquer avisos. O que devo fazer?

O motivo para os avisos no dashboard é que o cluster está agora ativado com RBAC e acesso a ele foi desabilitado por predefinição. Em geral, essa abordagem é uma prática recomendada, uma vez que a exposição de predefinição do dashboard para todos os utilizadores do cluster pode levar a ameaças de segurança. Se pretender continuar a ativar o dashboard, siga os passos em [nesta mensagem de blogue](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Não consigo ligar ao dashboard. O que devo fazer?

A maneira mais fácil acesso ao seu serviço fora do cluster é executar `kubectl proxy`, quais solicitações de proxies enviados para a localhost a porta 8001 para o servidor de API do Kubernetes. A partir daí, o servidor de API pode proxy ao seu serviço: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Se não vir o dashboard do Kubernetes, verifique se o `kube-proxy` pod está em execução `kube-system` espaço de nomes. Se não estiver num Estado de execução, elimine o pod e ele será reiniciado.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Eu não é possível obter os registos ao utilizar o kubectl registos ou não consigo ligar ao servidor de API. Estou a receber "erro do servidor: back-end de discagem de erro: marque o tcp...". O que devo fazer?

Certifique-se de que o grupo de segurança de rede predefinido não é modificado e que a porta 22 está aberta para ligação ao servidor de API. Verifique se o `tunnelfront` pod está em execução *kube system* usando o espaço de nomes a `kubectl get pods --namespace kube-system` comando. Se não estiver, force a eliminação do pod e ele será reiniciado.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Eu estou tentando atualizar ou dimensionar e estou recebendo uma "mensagem: Erro não é permitido alterar a propriedade 'imageReference' ". Como corrigir esse problema?

Poderá ser a obter este erro pois modificar as etiquetas em nós de agente dentro do cluster do AKS. Modificar e eliminar as etiquetas e outras propriedades de recursos no grupo de recursos MC_ * podem levar a resultados inesperados. Modificação de recursos sob o grupo MC_ * do AKS cluster divide o objetivo de nível de serviço (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Estou a receber erros que meu cluster está no Estado com falhas e atualizar ou dimensionamento não funcionará até ser corrigido

*Esse recurso de resolução de problemas é direcionado a partir de https://aka.ms/aks-cluster-failed*

Este erro ocorre quando clusters entrar num Estado com falhas por vários motivos. Siga os passos abaixo para resolver o seu estado de falha de cluster antes de repetir a operação que falhou anteriormente:

1. Até que o cluster é de `failed` Estado, `upgrade` e `scale` operações não ter êxito. Problemas de raiz comuns e resoluções incluem:
    * Dimensionar com **quota insuficiente de computação (CRP)**. Para resolver, primeiro a dimensionar o seu cluster para um estado estável objetivo dentro da quota. Em seguida, siga estes [aumentam de passos para pedir uma quota de computação](../azure-supportability/resource-manager-core-quotas-request.md) antes de tentar aumentar verticalmente novamente os limites de quota inicial do além deste.
    * Dimensionar um cluster com o advanced networking e **recursos de sub-rede insuficiente (redes)**. Para resolver, primeiro a dimensionar o seu cluster para um estado estável objetivo dentro da quota. Em seguida, siga [estas etapas para pedir uma quota de recursos aumentam](../azure-resource-manager/resource-manager-quota-errors.md#solution) antes de tentar aumentar verticalmente novamente os limites de quota inicial do além deste.
2. Uma vez resolvida a causa da falha de atualização, o cluster deve estar num Estado com êxito. Depois de verificar um Estado com êxito, repita a operação original.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Estou a receber erros ao tentar fazer a atualização ou de escala que meu cluster de estado está a ser atualmente sendo atualizado ou falha na atualização

*Esse recurso de resolução de problemas é direcionado a partir de https://aka.ms/aks-pending-upgrade*

Operações de cluster estão limitadas quando ocorrem operações de atualização de Active Directory ou uma atualização foi tentada, mas, em seguida, falhou. Para diagnosticar o problema, execute `az aks show -g myResourceGroup -n myAKSCluster -o table` para obter o estado detalhado no seu cluster. Com base no resultado:

* Se o cluster é atualizar de forma ativa, aguarde até concluir a operação. Se tiver êxito, tente a operação que falhou anteriormente novamente.
* Se a falha na atualização do cluster, siga os passos descritos acima
