---
title: Atualizar e reinicializar nós do Linux com o kured no serviço kubernetes do Azure (AKS)
description: Saiba como atualizar nós do Linux e reinicializá-los automaticamente com o kured no serviço kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 02/28/2019
ms.author: mlearned
ms.openlocfilehash: c9e7c23806d4a0a0e2c0b36122d9eb087c986556
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549177"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Aplicar atualizações de segurança e kernel a nós do Linux no serviço kubernetes do Azure (AKS)

Para proteger seus clusters, as atualizações de segurança são aplicadas automaticamente aos nós do Linux no AKS. Essas atualizações incluem correções de segurança do so ou atualizações do kernel. Algumas dessas atualizações exigem uma reinicialização do nó para concluir o processo. O AKS não reinicializa automaticamente esses nós do Linux para concluir o processo de atualização.

O processo para manter os nós do Windows Server (atualmente em visualização no AKS) atualizado é um pouco diferente. Os nós do Windows Server não recebem atualizações diárias. Em vez disso, você executa uma atualização do AKS que implanta novos nós com a mais recente imagem do servidor de janela base e patches. Para clusters AKS que usam nós do Windows Server, consulte [atualizar um pool de nós em AKs][nodepool-upgrade].

Este artigo mostra como usar o kured de código-fonte aberto [(KUbernetes reboot daemon)][kured] para observar os nós do Linux que exigem uma reinicialização e, em seguida, manipular automaticamente o reagendamento da execução do processo de reinicialização e do nó.

> [!NOTE]
> `Kured` é um projeto de código-fonte aberto por Weaveworks. O suporte para esse projeto no AKS é fornecido em uma base de melhor esforço. O suporte adicional pode ser encontrado no canal de margem de atraso #weave-Community.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS existente. Se você precisar de um cluster AKS, consulte o guia de início rápido do AKS [usando o CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Você também precisa do CLI do Azure versão 2.0.59 ou posterior instalada e configurada. Execute `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Entender a experiência de atualização de nó do AKS

Em um cluster AKS, seus nós do kubernetes são executados como VMs (máquinas virtuais) do Azure. Essas VMs baseadas em Linux usam uma imagem do Ubuntu, com o sistema operacional configurado para verificar automaticamente se há atualizações todas as noites. Se as atualizações de segurança ou kernel estiverem disponíveis, elas serão baixadas e instaladas automaticamente.

![Processo de atualização e reinicialização do nó AKS com kured](media/node-updates-kured/node-reboot-process.png)

Algumas atualizações de segurança, como atualizações de kernel, exigem uma reinicialização do nó para finalizar o processo. Um nó do Linux que requer uma reinicialização cria um arquivo chamado */var/run/reboot-Required*. Esse processo de reinicialização não ocorre automaticamente.

Você pode usar seus próprios fluxos de trabalho e processos para lidar com reinicializações de nó ou usar `kured` para orquestrar o processo. Com o `kured`, um [daemonset][DaemonSet] é implantado que executa um pod em cada nó do Linux no cluster. Esses pods no Daemonset observam a existência do arquivo */var/run/reboot-Required* e, em seguida, iniciam um processo para reinicializar os nós.

### <a name="node-upgrades"></a>Atualizações de nó

Há um processo adicional no AKS que permite *Atualizar* um cluster. Uma atualização é normalmente para mover para uma versão mais recente do kubernetes, não apenas aplicar atualizações de segurança de nó. Uma atualização do AKS executa as seguintes ações:

* Um novo nó é implantado com as últimas atualizações de segurança e a versão kubernetes aplicada.
* Um nó antigo é isolados e drenado.
* Os pods estão agendados no novo nó.
* O nó antigo é excluído.

Não é possível permanecer na mesma versão do kubernetes durante um evento de atualização. Você deve especificar uma versão mais recente do kubernetes. Para atualizar para a versão mais recente do kubernetes, você pode [atualizar o cluster do AKS][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Implantar kured em um cluster AKS

Para implantar o `kured` Daemonset, aplique o seguinte manifesto YAML de exemplo a partir de sua página de projeto do GitHub. Esse manifesto cria uma função e uma função de cluster, associações e uma conta de serviço e, em seguida, implanta o Daemonset usando `kured` versão 1.1.0 que dá suporte a clusters AKS 1,9 ou posterior.

```console
kubectl apply -f https://github.com/weaveworks/kured/releases/download/1.2.0/kured-1.2.0-dockerhub.yaml
```

Você também pode configurar parâmetros adicionais para `kured`, como integração com Prometheus ou margem de atraso. Para obter mais informações sobre parâmetros de configuração adicionais, consulte os [documentos de instalação do kured][kured-install].

## <a name="update-cluster-nodes"></a>Atualizar nós de cluster

Por padrão, os nós do Linux no AKS verificam se há atualizações todas as noites. Se você não quiser esperar, poderá executar manualmente uma atualização para verificar se `kured` executado corretamente. Primeiro, siga as etapas para executar o [SSH em um dos nós do AKS][aks-ssh]. Depois de ter uma conexão SSH com o nó do Linux, verifique se há atualizações e aplique-as da seguinte maneira:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Se foram aplicadas atualizações que exigem uma reinicialização de nó, um arquivo será gravado em */var/run/reboot-Required*. o `Kured` verifica se há nós que exigem uma reinicialização a cada 60 minutos por padrão.

## <a name="monitor-and-review-reboot-process"></a>Monitorar e examinar o processo de reinicialização

Quando uma das réplicas no Daemonset detectou que uma reinicialização de nó é necessária, um bloqueio é colocado no nó por meio da API kubernetes. Esse bloqueio impede que os pods adicionais sejam agendados no nó. O bloqueio também indica que apenas um nó deve ser reinicializado por vez. Com o nó isolados desativado, os pods em execução são esgotados do nó e o nó é reinicializado.

Você pode monitorar o status dos nós usando o comando [kubectl Get Nodes][kubectl-get-nodes] . A saída de exemplo a seguir mostra um nó com um status de *SchedulingDisabled* conforme o nó se prepara para o processo de reinicialização:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

Depois que o processo de atualização for concluído, você poderá exibir o status dos nós usando o comando [kubectl Get Nodes][kubectl-get-nodes] com o parâmetro `--output wide`. Essa saída adicional permite que você veja uma diferença na *versão kernel* dos nós subjacentes, conforme mostrado na saída de exemplo a seguir. O *AKs-nodepool1-28993262-0* foi atualizado em uma etapa anterior e mostra a versão *do kernel 4.15.0-1039-Azure*. O nó *AKs-nodepool1-28993262-1* que não foi atualizado mostra a versão *do kernel 4.15.0-1037-Azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Passos seguintes

Este artigo detalha como usar `kured` para reinicializar os nós do Linux automaticamente como parte do processo de atualização de segurança. Para atualizar para a versão mais recente do kubernetes, você pode [atualizar o cluster do AKS][aks-upgrade].

Para clusters AKS que usam nós do Windows Server, consulte [atualizar um pool de nós em AKs][nodepool-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured#installation
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
