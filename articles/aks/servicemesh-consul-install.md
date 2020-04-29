---
title: Instalar cônsul no Serviço Azure Kubernetes (AKS)
description: Aprenda a instalar e utilizar o Cônsul para criar uma malha de serviço num cluster do Serviço Azure Kubernetes (AKS)
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 1601ab6d81b888fd2247e95f22c58e1fc91df698
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78273726"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Instalar e utilizar cônsul no Serviço Azure Kubernetes (AKS)

[O cônsul][consul-github] é uma malha de serviço de código aberto que fornece um conjunto chave de funcionalidades em todos os microserviços em um cluster Kubernetes. Estas funcionalidades incluem a descoberta do serviço, verificação de saúde, segmentação de serviços e observabilidade. Para mais informações sobre o Cônsul, consulte a documentação oficial [do Cônsul?][consul-docs-concepts]

Este artigo mostra-lhe como instalar o Cônsul. Os componentes do Cônsul são instalados num aglomerado de Kubernetes no AKS.

> [!NOTE]
> Estas instruções referem `1.6.0`a versão do `2.14.2`Cônsul, e utilizam pelo menos a versão Helm .
>
> Os lançamentos do Cônsul `1.6.x` podem ser `1.13+`executados contra versões Kubernetes. Pode encontrar versões adicionais do Cônsul no [GitHub - Lançamentos do Cônsul][consul-github-releases] e informações sobre cada um dos lançamentos em [Notas de Lançamento do Cônsul][consul-release-notes].

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Instale os componentes do Cônsul no AKS
> * Validar a instalação do Cônsul
> * Desinstalar cônsul da AKS

## <a name="before-you-begin"></a>Antes de começar

Os passos detalhados neste artigo assumem que criou um `1.13` cluster AKS (Kubernetes ou `kubectl` acima, com RBAC habilitado) e estabeleceu uma ligação com o cluster. Se precisar de ajuda com algum destes itens, consulte o arranque rápido do [AKS.][aks-quickstart] Certifique-se de que o seu cluster tem pelo menos 3 nós na piscina do nó Linux.

Vai precisar do [Helm][helm] para seguir estas instruções e instalar o Cônsul. Recomenda-se que tenha a versão mais recente estável corretamente instalada e configurada no seu cluster. Se precisar de ajuda para instalar o Helm, consulte a orientação de [instalação AKS Helm][helm-install]. Todas as cápsulas de cônsul também devem ser programadas para correr em nós linux.

Este artigo separa a orientação de instalação do Cônsul em vários passos discretos. O resultado final é o mesmo na estrutura que a orientação oficial de [instalação][consul-install-k8]do Cônsul.

### <a name="install-the-consul-components-on-aks"></a>Instale os componentes do Cônsul no AKS

Começaremos por descarregar a `v0.10.0` versão da tabela do Cônsul Helm. Esta versão do gráfico inclui `1.6.0`a versão do Cônsul.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

Utilize o Helm `consul-helm` e o gráfico descarregado para `consul` instalar os componentes do Cônsul no espaço de nome no seu cluster AKS. 

> [!NOTE]
> **Opções de instalação**
> 
> Estamos a usar as seguintes opções como parte da nossa instalação:
> - `connectInject.enabled=true`- permitir que os proxies sejam injetados em cápsulas
> - `client.enabled=true`- permitir que os clientes do Cônsul corram em cada nó
> - `client.grpc=true`- ativar o ouvinte gRPC para ligarInject
> - `syncCatalog.enabled=true`- sincronizar os serviços kubernetes e cônsul
>
> **Selecionadores de nó**
>
> Cônsul deve estar programado para correr em nódoslinos Linux. Se tiver nós do Windows Server no seu cluster, tem de garantir que as cápsulas do Cônsul só estão programadas para serem executadas nos nós linux. Usaremos [selecionadores][kubernetes-node-selectors] de nós para garantir que as cápsulas estão programadas para os nós corretos.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

O `Consul` gráfico helm implementa uma série de objetos. Pode ver a lista a `helm install` partir da saída do seu comando acima. A implantação dos componentes do Cônsul pode demorar cerca de 3 minutos a ser concluída, dependendo do ambiente do cluster.

Neste momento, destacou o Cônsul para o seu aglomerado AKS. Para garantir que temos uma implantação bem sucedida do Cônsul, vamos passar à secção seguinte para validar a instalação do Cônsul.

## <a name="validate-the-consul-installation"></a>Validar a instalação do Cônsul

Confirme que os recursos foram criados com sucesso. Utilize o [kubectl get svc][kubectl-get] e [kubectl][kubectl-get] obtenha `consul` comandos de casul para consultar o `helm install` espaço de nome, onde os componentes do Cônsul foram instalados pelo comando:

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

A saída de exemplo seguinte mostra os serviços e cápsulas (programados nos nós linux) que devem estar agora a funcionar:

```output
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

Todas as cápsulas devem mostrar `Running`um estado de . Se as suas cápsulas não tiverem estes estatutos, aguarde um minuto ou dois até que o façam. Se alguma cápsula reportar um problema, use o comando de cápsula de [utilização kubectl][kubectl-describe] para rever a sua saída e estado.

## <a name="accessing-the-consul-ui"></a>Acesso ao Cônsul UI

O Cônsul UI foi instalado na nossa configuração acima e fornece configuração baseada em UI para cônsul. A UI para Cônsul não é exposta publicamente através de um endereço ip externo. Para aceder à interface de utilizador do Cônsul, utilize o comando [porta-frente kubectl.][kubectl-port-forward] Este comando cria uma ligação segura entre a sua máquina cliente e a cápsula relevante no seu cluster AKS.

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

Agora pode abrir um navegador `http://localhost:8080/ui` e apontá-lo para abrir o Cônsul UI. Deve ver o seguinte quando abrir a UI:

![Cônsul UI](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Desinstalar cônsul da AKS

> [!WARNING]
> A desviação do Cônsul de um sistema de funcionamento pode resultar em problemas relacionados com o tráfego entre os seus serviços. Certifique-se de que fez provisões para que o seu sistema continue a funcionar corretamente sem cônsul antes de prosseguir.

### <a name="remove-consul-components-and-namespace"></a>Remover componentes do Cônsul e espaço de nome

Para remover o Cônsul do seu cluster AKS, utilize os seguintes comandos. Os `helm delete` comandos removerão o `consul` `kubectl delete namespace` gráfico, e `consul` o comando removerá o espaço de nome.

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>Passos seguintes

Para explorar mais opções de instalação e configuração para cônsul, consulte os seguintes artigos oficiais do Cônsul:

- [Cônsul - Guia de instalação helm][consul-install-k8]
- [Cônsul - Opções de instalação helm][consul-install-helm-options]

Também pode seguir cenários adicionais usando:

- [Pedido de exemplo do cônsul][consul-app-example]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://www.consul.io/docs/platform/k8s/run.html
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-app-example]: https://github.com/hashicorp/demo-consul-101/tree/master/k8s
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
