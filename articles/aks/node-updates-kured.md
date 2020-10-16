---
title: Handle Linux nó reboots com kured
titleSuffix: Azure Kubernetes Service
description: Saiba como atualizar os nós Linux e reinicie-os automaticamente com o Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 02/28/2019
ms.openlocfilehash: 35c9e76c234e4b09fbb090eda363506ee3e11130
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88164245"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Aplicar atualizações de segurança e kernel aos nós Linux no Serviço Azure Kubernetes (AKS)

Para proteger os seus clusters, as atualizações de segurança são automaticamente aplicadas aos nós Linux em AKS. Estas atualizações incluem correções de segurança de SO ou atualizações de kernel. Algumas destas atualizações requerem um reboot de nó para completar o processo. A AKS não reinicia automaticamente estes nós Linux para completar o processo de atualização.

O processo para manter os nós do Windows Server atualizados é um pouco diferente. Os nós do Windows Server não recebem atualizações diárias. Em vez disso, executa uma atualização AKS que implementa novos nós com a mais recente imagem e patches do Servidor de Janelas de base. Para clusters AKS que usam nós do Windows Server, consulte [a atualização de uma piscina de nó em AKS][nodepool-upgrade].

Este artigo mostra-lhe como usar o kured de código aberto [(KUbernetes REboot Daemon)][kured] para observar os nós Linux que requerem um reboot, em seguida, lidar automaticamente com o reagendamento de cápsulas de corrida e processo de reinicialização do nó.

> [!NOTE]
> `Kured` é um projeto de código aberto da Weaveworks. O apoio a este projeto na AKS é prestado com o melhor esforço. Suporte adicional pode ser encontrado no canal slack #weave-comunidade.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Também precisa da versão Azure CLI 2.0.59 ou posteriormente instalada e configurada. Corre  `az --version` para encontrar a versão. Se necessitar de instalar ou atualizar, consulte [instalar o Azure CLI][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Compreenda a experiência de atualização do nó AKS

Num cluster AKS, os seus nóns Kubernetes funcionam como máquinas virtuais Azure (VMs). Estes VMs baseados em Linux usam uma imagem Ubuntu, com o SISTEMA configurado para verificar automaticamente se há atualizações todas as noites. Se estiverem disponíveis atualizações de segurança ou kernel, são automaticamente descarregadas e instaladas.

![Atualização do nó AKS e processo de reinicialização com kured](media/node-updates-kured/node-reboot-process.png)

Algumas atualizações de segurança, como atualizações de kernel, requerem um reboot de nó para finalizar o processo. Um nó Linux que requer um reboot cria um ficheiro chamado */var/run/reboot-required*. Este processo de reinicialização não acontece automaticamente.

Pode utilizar os seus próprios fluxos de trabalho e processos para lidar com reinicializações de nó, ou usar `kured` para orquestrar o processo. Com `kured` , é implantado um [DaemonSet][DaemonSet] que executa uma cápsula em cada nó Linux no cluster. Estas cápsulas no Relógio DaemonSet para a existência do ficheiro */var/run/reboot-required* e, em seguida, iniciar um processo para reiniciar os nós.

### <a name="node-upgrades"></a>Atualizações de nó

Há um processo adicional na AKS que permite *atualizar* um cluster. Uma atualização é normalmente para mover-se para uma versão mais recente de Kubernetes, e não apenas aplicar atualizações de segurança de nó. Uma atualização AKS executa as seguintes ações:

* Um novo nó é implementado com as mais recentes atualizações de segurança e a versão Kubernetes aplicada.
* Um nó velho é isolado e drenado.
* As cápsulas estão agendadas para o novo nó.
* O velho nó é apagado.

Não podes permanecer na mesma versão Kubernetes durante um evento de atualização. Tem de especificar uma versão mais recente de Kubernetes. Para atualizar para a versão mais recente do Kubernetes, pode [atualizar o seu cluster AKS.][aks-upgrade]

## <a name="deploy-kured-in-an-aks-cluster"></a>Implantar num cluster AKS

Para implementar o `kured` DaemonSet, instale o seguinte gráfico oficial de Kured Helm. Isto cria um papel de papel e cluster, encadernações e uma conta de serviço, em seguida, implementa o DaemonSet usando `kured` .

```console
# Add the Kured Helm repository
helm repo add kured https://weaveworks.github.io/kured

# Update your local Helm chart repository cache
helm repo update

# Create a dedicated namespace where you would like to deploy kured into
kubectl create namespace kured

# Install kured in that namespace with Helm 3 (only on Linux nodes, kured is not working on Windows nodes)
helm install kured kured/kured --namespace kured --set nodeSelector."beta\.kubernetes\.io/os"=linux
```

Também pode configurar parâmetros adicionais `kured` para, como a integração com Prometheus ou Slack. Para obter mais informações sobre parâmetros de configuração adicionais, consulte o [gráfico kured Helm][kured-install].

## <a name="update-cluster-nodes"></a>Atualizar os nosdes de cluster

Por padrão, os nós Linux na AKS verificam as atualizações todas as noites. Se não quiser esperar, pode realizar manualmente uma atualização para verificar se `kured` funciona corretamente. Primeiro, siga os passos para [sSH até um dos seus nós AKS][aks-ssh]. Assim que tiver uma ligação SSH com o nó Linux, verifique se há atualizações e aplique-as da seguinte forma:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Se forem aplicadas atualizações que exijam um reinício do nó, um ficheiro é escrito para */var/run/reboot-required*. `Kured` verifica os nós que requerem um reboot a cada 60 minutos por defeito.

## <a name="monitor-and-review-reboot-process"></a>Monitore e reveja o processo de reinicialização

Quando uma das réplicas do DaemonSet detetou a necessidade de um reboot de nó, é colocada uma fechadura no nó através da API de Kubernetes. Esta fechadura evita que sejam programadas cápsulas adicionais no nó. O bloqueio também indica que apenas um nó deve ser reiniciado de cada vez. Com o nó isolado, as cápsulas de corrida são drenadas do nó, e o nó é reiniciado.

Pode monitorizar o estado dos nós utilizando o comando [kubectl Get Nodes.][kubectl-get-nodes] A saída de exemplo a seguir mostra um nó com um estado de *AgendamentoDisabled* à medida que o nó se prepara para o processo de reinicialização:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

Uma vez concluído o processo de atualização, pode visualizar o estado dos nós utilizando o comando [kubectl get nodes][kubectl-get-nodes] com o `--output wide` parâmetro. Esta saída adicional *permite-lhe* ver uma diferença na VERSÃO KERNEL dos nós subjacentes, como mostra a saída do exemplo seguinte. O *aks-nodepool1-28993262-0* foi atualizado num passo anterior e mostra a versão kernel *4.15.0-1039-azure*. O nó *aks-nodepool1-28993262-1* que não foi atualizado mostra a versão *kernel 4.15.0-1037-azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Passos seguintes

Este artigo detalhou como usar `kured` para reiniciar os nós Linux automaticamente como parte do processo de atualização de segurança. Para atualizar para a versão mais recente do Kubernetes, pode [atualizar o seu cluster AKS.][aks-upgrade]

Para clusters AKS que usam nós do Windows Server, consulte [a atualização de uma piscina de nó em AKS][nodepool-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured/tree/master/charts/kured
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
