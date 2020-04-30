---
title: Manuseie o nó Linux reinicia com kured
titleSuffix: Azure Kubernetes Service
description: Saiba como atualizar os nós linux e reinicie-os automaticamente com kured no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 02/28/2019
ms.openlocfilehash: 955e5323769a7b9bf80413c045aaa3d55547eb02
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82208079"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Aplicar atualizações de segurança e kernel aos nós Linux no Serviço Azure Kubernetes (AKS)

Para proteger os seus clusters, as atualizações de segurança são automaticamente aplicadas aos nós Linux no AKS. Estas atualizações incluem correções de segurança do OS ou atualizações de kernel. Algumas destas atualizações requerem um reboot do nó para completar o processo. A AKS não reinicia automaticamente estes nódosos Linux para completar o processo de atualização.

O processo para manter os nós do Windows Server atualizados é um pouco diferente. Os nós do Windows Server não recebem atualizações diárias. Em vez disso, executa uma atualização AKS que implementa novos nós com a mais recente imagem e patches do Window Server. Para os clusters AKS que utilizam os nós do Windows Server, consulte [a atualização de um conjunto][nodepool-upgrade]de nós em AKS .

Este artigo mostra-lhe como usar o kured de código aberto [(KUbernetes REboot Daemon)][kured] para vigiar os nós linux que requerem um reboot, em seguida, lidar automaticamente com o reagendamento de cápsulas de corrida e processo de reinicialização do nó.

> [!NOTE]
> `Kured`é um projeto de código aberto da Weaveworks. O apoio a este projeto no AKS é prestado com base no melhor esforço. O apoio adicional pode ser encontrado no canal Slack da comunidade #weave.

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que você tem um aglomerado AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Também precisa da versão 2.0.59 do Azure CLI ou posteriormente instalada e configurada. Corra `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Compreenda a experiência de atualização do nó AKS

Num cluster AKS, os seus nós kubernetes funcionam como máquinas virtuais Azure (VMs). Estes VMs baseados em Linux usam uma imagem Ubuntu, com o SISTEMA configurado para verificar automaticamente as atualizações todas as noites. Se estiverem disponíveis atualizações de segurança ou kernel, são automaticamente descarregados e instalados.

![Atualização do nó AKS e processo de reinicialização com kured](media/node-updates-kured/node-reboot-process.png)

Algumas atualizações de segurança, como atualizações de kernel, requerem um reboot do nó para finalizar o processo. Um nó Linux que requer um reboot cria um ficheiro chamado */var/run/reboot-required*. Este processo de reinicialização não acontece automaticamente.

Pode utilizar os seus próprios fluxos de trabalho e `kured` processos para lidar com reboots de nós, ou usar para orquestrar o processo. Com `kured`, um [DaemonSet][DaemonSet] é implantado que executa uma cápsula em cada nó Linux no cluster. Estas cápsulas no relógio DaemonSet para a existência do ficheiro */var/run/reboot-required* e, em seguida, iniciar um processo para reiniciar os nós.

### <a name="node-upgrades"></a>Upgrades de nó

Há um processo adicional no AKS que permite *atualizar* um cluster. Normalmente, uma atualização é para passar para uma versão mais recente da Kubernetes, e não apenas aplicar atualizações de segurança do nó. Uma atualização AKS executa as seguintes ações:

* Um novo nó é implantado com as últimas atualizações de segurança e versão Kubernetes aplicada.
* Um nó velho é isolado e drenado.
* As cápsulas estão marcadas para o novo nó.
* O nó velho é apagado.

Não pode ficar na mesma versão Kubernetes durante um evento de upgrade. Deve especificar uma versão mais recente da Kubernetes. Para atualizar para a versão mais recente da Kubernetes, pode [atualizar o seu cluster AKS][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Implementar kured em um cluster AKS

Para implementar `kured` o DaemonSet, instale o seguinte gráfico oficial de Kured Helm. Isto cria uma função de papel e cluster, encadernações e `kured`uma conta de serviço, em seguida, implementa o DaemonSet usando .

```console
# Add the stable Helm repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Update your local Helm chart repository cache
helm repo update

# Create a dedicated namespace where you would like to deploy kured into
kubectl create namespace kured

# Install kured in that namespace with Helm 3 (only on Linux nodes, kured is not working on Windows nodes)
helm install kured stable/kured --namespace kured --set nodeSelector."beta\.kubernetes\.io/os"=linux
```

Também pode configurar parâmetros `kured`adicionais para, como a integração com Prometeu ou Slack. Para obter mais informações sobre parâmetros de configuração adicionais, consulte o gráfico de [Helm kured][kured-install].

## <a name="update-cluster-nodes"></a>Atualizar nós de cluster

Por padrão, os nós linux no AKS verificam as atualizações todas as noites. Se não quiser esperar, pode efetuar manualmente `kured` uma atualização para verificar se corre corretamente. Primeiro, siga os passos até [sSH até um dos seus nós AKS][aks-ssh]. Uma vez que tenha uma ligação SSH ao nó Linux, verifique se há atualizações e aplique-as da seguinte forma:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Se forem aplicadas atualizações que exijam um reboot do nó, um ficheiro é escrito para */var/run/reboot-required*. `Kured`verifica os nódosos que requerem um reboot a cada 60 minutos por padrão.

## <a name="monitor-and-review-reboot-process"></a>Monitorizar e rever processo de reinicialização

Quando uma das réplicas do DaemonSet detetou que é necessário um reboot do nó, um cadeado é colocado no nó através da API kubernetes. Esta fechadura impede que cápsulas adicionais sejam programadas no nó. O cadeado também indica que apenas um nó deve ser reiniciado de cada vez. Com o nó isolado, as cápsulas de funcionamento são drenadas do nó, e o nó é reiniciado.

Pode monitorizar o estado dos nódosos usando o comando [kubectl get nódosos.][kubectl-get-nodes] A saída de exemplo seguinte mostra um nó com um estado de *AgendamentoDeficiente à* medida que o nó se prepara para o processo de reinicialização:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

Uma vez concluído o processo de atualização, pode visualizar o estado dos nós usando `--output wide` o [kubectl obter][kubectl-get-nodes] comando de nós com o parâmetro. Esta saída adicional permite-lhe ver uma diferença na *VERSÃO KERNEL* dos nós subjacentes, como mostra a seguinte saída de exemplo. O *aks-nodepool1-28993262-0* foi atualizado num passo anterior e mostra a versão *kernel 4.15.0-1039-azure*. O nó *aks-nodepool1-28993262-1* que não foi atualizado mostra a versão *kernel 4.15.0-1037-azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Passos seguintes

Este artigo detalhou `kured` como usar para reiniciar os nós do Linux automaticamente como parte do processo de atualização de segurança. Para atualizar para a versão mais recente da Kubernetes, pode [atualizar o seu cluster AKS][aks-upgrade].

Para os clusters AKS que utilizam os nós do Windows Server, consulte [a atualização de um conjunto][nodepool-upgrade]de nós em AKS .

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://hub.helm.sh/charts/stable/kured
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
