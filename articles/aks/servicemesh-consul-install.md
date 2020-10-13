---
title: Instalar cônsul no Serviço Azure Kubernetes (AKS)
description: Saiba como instalar e utilizar o Cônsul para criar uma malha de serviço num cluster Azure Kubernetes Service (AKS)
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 2f47ded22209a9d53510c7d0a2f8270096161354
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86244140"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Instale e utilize o Cônsul no Serviço Azure Kubernetes (AKS)

[O Cônsul][consul-github] é uma malha de serviço de código aberto que fornece um conjunto chave de funcionalidades através dos microserviços num cluster Kubernetes. Estas funcionalidades incluem a descoberta do serviço, verificação de saúde, segmentação de serviços e observabilidade. Para mais informações sobre o Cônsul, consulte o oficial O que é a documentação [do Cônsul?][consul-docs-concepts]

Este artigo mostra-lhe como instalar o Cônsul. Os componentes do Cônsul são instalados num cluster Kubernetes em AKS.

> [!NOTE]
> Estas instruções referem a versão do Cônsul, `1.6.0` e utilizam pelo menos a versão Helm `2.14.2` .
>
> As versões do Cônsul `1.6.x` podem ser executadas contra as versões de Kubernetes `1.13+` . Pode encontrar versões cônsul adicionais no [GitHub - Cônsul e][consul-github-releases] informações sobre cada um dos lançamentos no [Cônsul- Notas de Lançamento][consul-release-notes].

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Instale os componentes do Cônsul em AKS
> * Validar a instalação do Cônsul
> * Cônsul desinstalação da AKS

## <a name="before-you-begin"></a>Antes de começar

Os passos detalhados neste artigo assumem que criou um cluster AKS (Kubernetes `1.13` e acima, com o RBAC habilitado) e estabeleceu uma `kubectl` ligação com o cluster. Se precisar de ajuda com algum destes itens, consulte o arranque rápido da [AKS][aks-quickstart]. Certifique-se de que o seu cluster tem pelo menos 3 nós na piscina de nólinho Linux.

Vai precisar do [Helm][helm] para seguir estas instruções e instalar o Cônsul. Recomenda-se que tenha a versão mais recente estável corretamente instalada e configurada no seu cluster. Se precisar de ajuda para instalar o Helm, consulte a orientação de [instalação do AKS Helm][helm-install]. Todas as cápsulas de cônsul também devem ser programadas para correr em nós Linux.

Este artigo separa a orientação de instalação do Cônsul em vários passos discretos. O resultado final é o mesmo em estrutura que a [orientação][consul-install-k8]oficial de instalação do Cônsul .

### <a name="install-the-consul-components-on-aks"></a>Instale os componentes do Cônsul em AKS

Começaremos por descarregar a versão `v0.10.0` da tabela do Cônsul Helm. Esta versão do gráfico inclui a versão Cônsul. `1.6.0`

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

Utilize o Helm e o gráfico descarregado `consul-helm` para instalar os componentes do Cônsul no espaço de `consul` nomes no seu cluster AKS. 

> [!NOTE]
> **Opções de instalação**
> 
> Utilizamos as seguintes opções como parte da nossa instalação:
> - `connectInject.enabled=true` - permitir que os proxies sejam injetados em cápsulas
> - `client.enabled=true` - permitir que os clientes do Cônsul corram em todos os nóns
> - `client.grpc=true` - permitir que o ouvinte gRPC se ligue
> - `syncCatalog.enabled=true` - sincronizar os serviços kubernetes e cônsul
>
> **Seletores de nó**
>
> O cônsul deve estar programado para correr em nós Linux. Se tiver nós do Windows Server no seu cluster, deve certificar-se de que as cápsulas do Cônsul estão programadas apenas para funcionar nos nós Linux. Usaremos [os seletores de nó][kubernetes-node-selectors] para garantir que as cápsulas estão programadas para os nós corretos.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

O `Consul` gráfico Helm implanta uma série de objetos. Pode ver a lista a partir da saída do seu `helm install` comando acima. A colocação dos componentes do Cônsul pode demorar cerca de 3 minutos a ser concluída, dependendo do ambiente do seu cluster.

Neste momento, enviaste o Cônsul para o teu agrupamento AKS. Para garantir que temos uma implantação bem sucedida do Cônsul, passemos à secção seguinte para validar a instalação do Cônsul.

## <a name="validate-the-consul-installation"></a>Validar a instalação do Cônsul

Confirme que os recursos foram criados com sucesso. Utilize o [kubectl get svc][kubectl-get] e [kubectl obter][kubectl-get] comandos de pod para consultar o `consul` espaço de nomes, onde os componentes do Cônsul foram instalados pelo `helm install` comando:

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

A saída de exemplo a seguir mostra os serviços e cápsulas (programados nos nós Linux) que devem estar agora em execução:

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

Todas as cápsulas devem mostrar um estado de `Running` . Se as suas cápsulas não tiverem estes estatutos, espere um minuto ou dois até que tenham. Se alguma cápsula reportar um problema, utilize o comando [de pod de descrever kubectl][kubectl-describe] para rever a sua saída e estado.

## <a name="accessing-the-consul-ui"></a>Acesso ao UI Cônsul

O Cônsul UI foi instalado na nossa configuração acima e fornece configuração baseada em UI para cônsul. A UI para Cônsul não é exposta publicamente através de um endereço IP externo. Para aceder à interface do utilizador do Cônsul, utilize o comando [porta-frente kubectl.][kubectl-port-forward] Este comando cria uma ligação segura entre a sua máquina cliente e a cápsula relevante no seu cluster AKS.

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

Agora pode abrir um navegador e apontá-lo `http://localhost:8080/ui` para abrir o UI do Cônsul. Deve ver o seguinte quando abrir a UI:

![UI Cônsul](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Cônsul desinstalação da AKS

> [!WARNING]
> A eliminação do Cônsul de um sistema de funcionamento pode resultar em problemas relacionados com o tráfego entre os seus serviços. Certifique-se de que escamou as disposições para que o seu sistema continue a funcionar corretamente sem cônsul antes de prosseguir.

### <a name="remove-consul-components-and-namespace"></a>Remover componentes do Cônsul e espaço de nome

Para remover o Cônsul do seu cluster AKS, utilize os seguintes comandos. Os `helm delete` comandos removerão o `consul` gráfico, e o `kubectl delete namespace` comando removerá o espaço de `consul` nome.

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>Passos seguintes

Para explorar mais opções de instalação e configuração para cônsul, consulte os seguintes artigos oficiais do Cônsul:

- [Cônsul - Guia de instalação helm][consul-install-k8]
- [Cônsul - Opções de instalação de leme][consul-install-helm-options]

Também pode seguir cenários adicionais utilizando:

- [Aplicação de exemplo de cônsul][consul-app-example]
- [Arquitetura de Referência de Consul Kubernetes][consul-reference]
- [Cônsul Mesh Gateways][consul-mesh-gateways]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-deployment-guide
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-mesh-gateways]: https://learn.hashicorp.com/consul/kubernetes/mesh-gateways
[consul-reference]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-reference
[consul-app-example]: https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh
[install-wsl]: /windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
