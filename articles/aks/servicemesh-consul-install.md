---
title: Instalar o Consul no serviço kubernetes do Azure (AKS)
description: Saiba como instalar e usar o Consul para criar uma malha de serviço em um cluster do AKS (serviço kubernetes do Azure)
author: dstrebel
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d5d0a575c3fb662df034b66a48135ac33393f95c
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885396"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Instalar e usar o Consul no serviço kubernetes do Azure (AKS)

O [Consul][consul-github] é uma malha de serviço de software livre que fornece um conjunto de chaves de funcionalidade em todos os microserviços em um cluster kubernetes. Esses recursos incluem descoberta de serviço, verificação de integridade, segmentação de serviço e observação. Para obter mais informações sobre Consul, consulte a documentação oficial [o que é o Consul?][consul-docs-concepts] .

Este artigo mostra como instalar o Consul. Os componentes do Consul são instalados em um cluster kubernetes no AKS.

> [!NOTE]
> Estas instruções fazem referência à versão do Consul `1.6.0`e usam pelo menos a versão do Helm `2.14.2`.
>
> As versões de `1.6.x` do Consul podem ser executadas em versões do kubernetes `1.13+`. Você pode encontrar versões adicionais do Consul em [versões do GitHub-Consul][consul-github-releases] e informações sobre cada uma das versões em [Consul-Release Notes][consul-release-notes].

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Instalar os componentes do Consul no AKS
> * Validar a instalação do Consul
> * Desinstalar o Consul do AKS

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste artigo pressupõem que você criou um cluster AKS (kubernetes `1.13` e superior, com o RBAC habilitado) e estabeleceu uma conexão de `kubectl` com o cluster. Se precisar de ajuda com qualquer um desses itens, consulte o guia de [início rápido do AKS][aks-quickstart]. Verifique se o cluster tem pelo menos três nós no pool de nós do Linux.

Você precisará de [Helm][helm] para seguir estas instruções e instalar o Consul. É recomendável que você tenha a versão estável mais recente corretamente instalada e configurada em seu cluster. Se precisar de ajuda com a instalação do Helm, consulte as [diretrizes de instalação do AKS Helm][helm-install]. Todos os pods de Consul também devem ser agendados para serem executados em nós do Linux.

Este artigo separa as diretrizes de instalação do Consul em várias etapas discretas. O resultado final é o mesmo na estrutura que as [diretrizes][consul-install-k8]de instalação oficial do Consul.

### <a name="install-the-consul-components-on-aks"></a>Instalar os componentes do Consul no AKS

Começaremos baixando a versão `v0.10.0` do gráfico Consul Helm. Esta versão do gráfico inclui a versão Consul `1.6.0`.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

Use Helm e o gráfico de `consul-helm` baixado para instalar os componentes do Consul no namespace `consul` em seu cluster AKS. 

> [!NOTE]
> **Opções de instalação**
> 
> Estamos usando as seguintes opções como parte da nossa instalação:
> - `connectInject.enabled=true`-permitir que os proxies sejam injetados em pods
> - `client.enabled=true`-permitir que os clientes Consul sejam executados em cada nó
> - `client.grpc=true`-habilitar ouvinte gRPC para connectInject
> - `syncCatalog.enabled=true`-sincronizar serviços kubernetes e Consul
>
> **Seletores de nó**
>
> No momento, o Consul deve ser agendado para ser executado em nós do Linux. Se você tiver nós do Windows Server em seu cluster, deverá garantir que os pods Consul sejam agendados apenas para execução em nós do Linux. Usaremos [seletores de nó][kubernetes-node-selectors] para garantir que os pods estejam agendados para os nós corretos.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

O `Consul` gráfico Helm implanta vários objetos. Você pode ver a lista da saída do comando `helm install` acima. A implantação dos componentes Consul pode levar cerca de 3 minutos para ser concluída, dependendo do seu ambiente de cluster.

Neste ponto, você implantou o Consul em seu cluster AKS. Para garantir que tenhamos uma implantação bem-sucedida do Consul, vamos passar para a próxima seção para validar a instalação do Consul.

## <a name="validate-the-consul-installation"></a>Validar a instalação do Consul

Confirme se os recursos foram criados com êxito. Use os comandos [kubectl Get svc][kubectl-get] e [kubectl Get Pod][kubectl-get] para consultar o namespace `consul`, em que os componentes do Consul foram instalados pelo comando `helm install`:

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

A saída de exemplo a seguir mostra os serviços e os pods (agendados em nós do Linux) que agora devem estar em execução:

```console
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

Todos os pods devem mostrar um status de `Running`. Se os pods não tiverem esses status, aguarde um ou dois minutos até que eles façam. Se qualquer pods relatar um problema, use o comando [kubectl para descrever o Pod][kubectl-describe] para revisar sua saída e status.

## <a name="accessing-the-consul-ui"></a>Acessando a interface do usuário do Consul

A interface do usuário do Consul foi instalada em nossa configuração acima e fornece a configuração baseada na interface do usuário do Consul. A interface do usuário para Consul não é exposta publicamente por meio de um endereço IP externo. Para acessar a interface do usuário do Consul, use o comando [kubectl Port-Forward][kubectl-port-forward] . Esse comando cria uma conexão segura entre o computador cliente e o Pod relevante no cluster AKS.

```azurecli
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

Agora você pode abrir um navegador e apontar para `http://localhost:8080/ui` para abrir a interface do usuário do Consul. Você deve ver o seguinte ao abrir a interface do usuário:

![Interface do usuário do amConsul](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Desinstalar o Consul do AKS

> [!WARNING]
> Excluir Consul de um sistema em execução pode resultar em problemas relacionados ao tráfego entre seus serviços. Verifique se você fez provisões para que o seu sistema ainda opere corretamente sem Consul antes de continuar.

### <a name="remove-consul-components-and-namespace"></a>Remover componentes e namespace do Consul

Para remover o Consul do cluster do AKS, use os comandos a seguir. Os comandos de `helm delete` removerão o gráfico de `consul` e o comando `kubectl delete namespace` removerá o namespace `consul`.

```azurecli
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>Passos seguintes

Para explorar mais opções de instalação e configuração para o Consul, consulte os seguintes artigos oficiais do Consul:

- [Guia de instalação do Consul-Helm][consul-install-k8]
- [Opções de instalação do Consul-Helm][consul-install-helm-options]

Você também pode seguir cenários adicionais usando:

- [Aplicativo de exemplo Consul][consul-app-example]

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
