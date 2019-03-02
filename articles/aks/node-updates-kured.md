---
title: Atualizar e reiniciar os nós com kured no Azure Kubernetes Service (AKS)
description: Saiba como atualizar os nós e reinicie-os automaticamente com kured no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: 75057f6bd92fbdc805da2e0e36dc2bff7b069f26
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243334"
---
# <a name="apply-security-and-kernel-updates-to-nodes-in-azure-kubernetes-service-aks"></a>Aplicar atualizações de kernel e de segurança a nós no Azure Kubernetes Service (AKS)

Para proteger os seus clusters, atualizações de segurança são aplicadas automaticamente a nós no AKS. Estas atualizações incluem correções de segurança do sistema operacional ou atualizações de kernel. Algumas destas atualizações requerem um reinício do nó para concluir o processo. AKS automaticamente não reiniciar os nós para concluir o processo de atualização.

Este artigo mostra-lhe como utilizar o código-fonte aberto [kured (KUbernetes reiniciar Daemon)] [ kured] para ver para nós que requerem um reinício, em seguida, automaticamente, lidar com o valor do reagendamento de execução pods e reinício do nó processo.

> [!NOTE]
> `Kured` é um projeto de código-fonte aberto por Weaveworks. Suporte para esse projeto no AKS é fornecido numa base de melhor esforço. Suporte adicional pode ser encontrado no canal slack #weave-Comunidade,

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem um cluster do AKS existente. Se precisar de um cluster do AKS, consulte o guia de introdução do AKS [com a CLI do Azure] [ aks-quickstart-cli] ou [no portal do Azure][aks-quickstart-portal].

Também precisa da versão 2.0.59 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Compreender a experiência de atualização de nós do AKS

Num cluster do AKS, os nós do Kubernetes executam como máquinas de virtuais (VMs) do Azure. Estas VMs baseadas em Linux utilizam uma imagem de Ubuntu, com o sistema operacional configurado para verificar automaticamente as atualizações de todas as noites. Se as atualizações de kernel ou de segurança disponíveis, são automaticamente transferidos e instalados.

![Nó AKS atualizar e reiniciar o processo com kured](media/node-updates-kured/node-reboot-process.png)

Algumas atualizações de segurança, tais como atualizações de kernel, exigem um reinício do nó para finalizar o processo. Um nó que requer uma reinicialização cria um ficheiro denominado */var/run/reboot-required*. Este processo de reinício não acontece automaticamente.

Pode utilizar os seus próprios processos e fluxos de trabalho para manipular os reinícios de nó ou utilize `kured` para orquestrar o processo. Com o `kured`, um [DaemonSet] [ DaemonSet] é implementado que executa um pod em cada nó do cluster. Assista a estes pods no DaemonSet existência do */var/run/reboot-required* de ficheiros e, em seguida, inicia um processo para reiniciar os nós.

### <a name="node-upgrades"></a>Atualizações de nós

Há um processo adicional no AKS que lhe permita *atualizar* um cluster. Uma atualização é, normalmente, mover para uma versão mais recente do Kubernetes, não apenas aplicar atualizações de segurança do nó. Uma atualização do AKS executa as seguintes ações:

* Um novo nó é implementado com as mais recentes atualizações de segurança e a versão do Kubernetes aplicada.
* Um nó antigo é isolado e drenado.
* Pods são agendadas no novo nó.
* O nó antigo é eliminado.

Não é possível permanecer na mesma versão do Kubernetes durante um evento de atualização. Tem de especificar uma versão mais recente do Kubernetes. Para atualizar para a versão mais recente do Kubernetes, pode [atualizar o seu cluster do AKS][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Implementar kured num cluster do AKS

Para implementar o `kured` DaemonSet, aplicam-se o exemplo a seguir YAML de manifesto da sua página de projeto do GitHub. Esse manifesto cria uma função e função de cluster, enlaces e uma conta de serviço, em seguida, implementa o DaemonSet utilizando `kured` versão 1.1.0, que oferece suporte a clusters do AKS 1.9 ou posteriores.

```console
kubectl apply -f https://github.com/weaveworks/kured/releases/download/1.1.0/kured-1.1.0.yaml
```

Também pode configurar parâmetros adicionais para `kured`, como integração com Prometheus ou Slack. Para obter mais informações sobre parâmetros de configuração adicionais, consulte a [docs de instalação kured][kured-install].

## <a name="update-cluster-nodes"></a>Atualizar os nós de cluster

Por predefinição, nós do AKS procurar atualizações todas as noites. Se não quiser esperar, pode efetuar manualmente uma atualização para verificar se `kured` é executada corretamente. Em primeiro lugar, siga os passos para [SSH a um de nós do AKS][aks-ssh]. Assim que tiver uma ligação SSH ao nó, verifique a existência de atualizações e aplicá-los da seguinte forma:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Se as atualizações foram aplicadas que requerem um reinício do nó, um ficheiro é escrito */var/run/reboot-required*. `Kured` verifica a existência de nós que requerem um reinício a cada 60 minutos por predefinição.

## <a name="monitor-and-review-reboot-process"></a>Monitorizar e rever o processo de reinício

Quando uma das réplicas no DaemonSet detetou que é necessário um reinício de nó, um bloqueio é colocado no nó por meio da API do Kubernetes. Este bloqueio impede que os pods adicionais a ser agendadas no nó. O bloqueio também indica que apenas um nó deve ser reiniciado de cada vez. Com o nó isolado, pods em execução são drenados a partir do nó e o nó for reiniciado.

Pode monitorizar o estado de nós com o [kubectl obter nós] [ kubectl-get-nodes] comando. O resultado de exemplo seguinte mostra um nó com o estado *SchedulingDisabled* como o nó se prepara para o processo de reinício:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

Quando o processo de atualização estiver concluído, pode ver o estado de nós com o [kubectl obter nós] [ kubectl-get-nodes] comando com o `--output wide` parâmetro. Este resultado adicional permite que veja uma diferença *versão de KERNEL* de nós subjacentes, conforme mostrado na seguinte saída de exemplo. O *aks-nodepool1-28993262-0* foi atualizado num passo anterior e mostra a versão do kernel *4.15.0-1039-azure*. O nó *aks-nodepool1-28993262-1* que não tem sido atualizada mostra a versão do kernel *4.15.0-1037-azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Passos Seguintes

Este artigo obter detalhadas sobre como utilizar `kured` para reiniciar os nós automaticamente como parte do processo de atualização de segurança. Para atualizar para a versão mais recente do Kubernetes, pode [atualizar o seu cluster do AKS][aks-upgrade].

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
