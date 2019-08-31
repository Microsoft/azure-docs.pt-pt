---
title: Instalar o Hashicorp Consul no serviço kubernetes do Azure (AKS)
description: Saiba como instalar e usar o Consul para criar uma malha de serviço em um cluster do AKS (serviço kubernetes do Azure)
services: container-service
author: dstrebel
ms.service: container-service
ms.topic: article
ms.date: 06/19/2019
ms.author: dastrebe
ms.openlocfilehash: 7acd2533079499091427c7e63741b9c587ee29e5
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70189227"
---
# <a name="install-and-use-consul-connect-in-azure-kubernetes-service-aks"></a>Instalar e usar o Consul Connect no serviço kubernetes do Azure (AKS)

O [Consul][consul-github] é uma malha de serviço de software livre que fornece um conjunto de chaves de funcionalidade em todos os microserviços em um cluster kubernetes. Esses recursos incluem descoberta de serviço, verificação de integridade, segmentação de serviço e observação. Para obter mais informações sobre Consul, consulte a documentação oficial [o que é o Consul?][consul-docs-concepts] .

Este artigo mostra como instalar o Consul. Os componentes do Consul são instalados em um cluster kubernetes no AKS.

> [!NOTE]
> Estas instruções fazem referência à `1.5`versão do Consul.
>
> As versões `1.5.x` do Consul foram testadas pela equipe do Hashicorp em relação `1.12`às `1.14`versões `1.14`do kubernetes,,. Você pode encontrar versões adicionais do Consul em [versões do GitHub-Consul][consul-github-releases] e informações sobre cada uma das versões em [Consul-Release Notes][consul-release-notes].

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Instalar os componentes do Consul no AKS
> * Validar a instalação do Consul
> * Desinstalar o Consul do AKS

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste artigo pressupõem que você criou um cluster AKs (kubernetes `1.11` e superior, com o RBAC habilitado) e estabeleceu uma `kubectl` conexão com o cluster. Se precisar de ajuda com qualquer um desses itens, consulte o guia de [início rápido do AKS][aks-quickstart].

Você precisará de [Helm][helm] para seguir estas instruções e instalar o Consul. É recomendável que você tenha a `2.12.2` versão ou posterior instalada e configurada corretamente no cluster. Se precisar de ajuda com a instalação do Helm, consulte as [diretrizes de instalação do AKS Helm][helm-install]. Todos os pods de Consul também devem ser agendados para serem executados em nós do Linux.

Este artigo separa as diretrizes de instalação do Consul em várias etapas discretas. O resultado final é o mesmo na estrutura que as [diretrizes][consul-install-k8]de instalação oficial do Consul.

### <a name="install-the-consul-components-on-aks"></a>Instalar os componentes do Consul no AKS

Primeiro, Clonaremos o Consul oficial HashiCorp no repositório GitHub do kubernetes.

```bash
git clone https://github.com/hashicorp/consul-helm.git
cd consul-helm
```

Em seguida, precisaremos criar um arquivo de valores de Helm personalizado para a instalação do Consul. Crie o arquivo de valores personalizados a seguir antes de instalar o Consul.

```bash
vim consul-values.yaml
```

Em seguida, copie e cole os seguintes valores no arquivo Consul-Values. YAML.

```yaml
# Sets datacenter name and version Consul to use
global:
  datacenter: dc1
  image: "consul:1.5.2"

# Enables proxies to be injected into pods
connectInject:
  enabled: true

# Enables UI on ClusterIP
ui:
  service:
    type: "ClusterIP"

# Enables GRCP that is required for connectInject
client:
  enabled: true
  grpc: true

# Sets replicase to 3 for HA
server:
  replicas: 3
  bootstrapExpect: 1
  disruptionBudget:
    enabled: true
    maxUnavailable: 1

# Syncs Kubernetes service discovery with Consul
syncCatalog:
  enabled: true
```

Agora que temos o arquivo de valores personalizados, podemos instalar o Consul em nosso cluster AKS

Bash

```bash
helm install -f consul-values.yaml --name consul --namespace consul .
```
O `Consul` gráfico Helm implanta vários objetos. Você pode ver a lista da saída do `helm install` comando acima. A implantação dos componentes do Consul pode levar de 4 a 5 minutos para ser concluída, dependendo do seu ambiente de cluster.

> [!NOTE]
> Todos os pods Consul devem ser agendados para execução em nós do Linux. Se você tiver pools de nós do Windows Server além dos pools de nós do Linux em seu cluster, verifique se todos os pods Consul foram agendados para execução em nós do Linux.

Neste ponto, você implantou o Consul em seu cluster AKS. Para garantir que tenhamos uma implantação bem-sucedida do Consul, vamos passar para a próxima seção para validar a instalação do Consul.

## <a name="validate-the-consul-installation"></a>Validar a instalação do Consul

Primeiro, confirme que os serviços esperados foram criados. Use o comando [kubectl Get svc][kubectl-get] para exibir os serviços em execução. Consulte o `consul` namespace, onde os componentes do Consul foram instalados pelo `consul` gráfico do Helm:

```console
kubectl get svc --namespace consul
```

A saída de exemplo a seguir mostra os serviços que agora devem estar em execução:

```console
NAME                                 TYPE           CLUSTER-IP     EXTERNAL-IP             PORT(S)                                                                   AGE
consul                               ExternalName   <none>         consul.service.consul   <none>                                                                    38m
consul-consul-connect-injector-svc   ClusterIP      10.0.89.113    <none>                  443/TCP                                                                   40m
consul-consul-dns                    ClusterIP      10.0.166.82    <none>                  53/TCP,53/UDP                                                             40m
consul-consul-server                 ClusterIP      None           <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   40m
consul-consul-ui                     ClusterIP      10.0.117.164   <none>                  80/TCP                                                                    40m
```

Em seguida, confirme se os pods necessários foram criados. Use o comando [kubectl Get pods][kubectl-get] e consulte novamente o `consul` namespace:

```console
kubectl get pods --namespace consul
```

A saída de exemplo a seguir mostra os pods que estão em execução:

```console
NAME                                                              READY   STATUS    RESTARTS   AGE
consul-consul-7cc9v                                               1/1     Running   0          37m
consul-consul-7klg7                                               1/1     Running   0          37m
consul-consul-connect-injector-webhook-deployment-57f88df8hgmfs   1/1     Running   0          37m
consul-consul-lq8qb                                               1/1     Running   0          37m
consul-consul-server-0                                            1/1     Running   0          37m
consul-consul-server-1                                            1/1     Running   0          36m
consul-consul-server-2                                            1/1     Running   0          36m
consul-consul-sync-catalog-7cf7d5bfff-jjbjv                       1/1     Running   2          37m
```

 Todos os pods devem mostrar um status de `Running`. Se os pods não tiverem esses status, aguarde um ou dois minutos até que eles façam. Se qualquer pods relatar um problema, use o comando [kubectl para descrever o Pod][kubectl-describe] para revisar sua saída e status.

## <a name="accessing-the-consul-ui"></a>Acessando a interface do usuário do Consul

A interface do usuário do Consul foi instalada em nossa configuração acima, que fornece a configuração baseada na interface do usuário do Consul. A interface do usuário para Consul não é exposta publicamente por meio de um endereço IP externo. Para acessar as interfaces de usuário do complemento, use o comando [kubectl Port-Forward][kubectl-port-forward] . Esse comando cria uma conexão segura entre o computador cliente e o Pod relevante no cluster AKS.

```bash
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```
Agora você pode abrir um navegador e apontar para `http://localhost:8080/ui` para abrir a interface do usuário do amConsul. Você deve ver o seguinte ao abrir a interface do usuário:

![Interface do usuário do amConsul](./media/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Desinstalar o Consul do AKS

> [!WARNING]
> Excluir Consul de um sistema em execução pode resultar em problemas relacionados ao tráfego entre seus serviços. Verifique se você fez provisões para que o seu sistema ainda opere corretamente sem Consul antes de continuar.

### <a name="remove-consul-components-and-namespace"></a>Remover componentes e namespace do Consul

Para remover o Consul do cluster do AKS, use os comandos a seguir. Os `helm delete` comandos removerão o `consul` gráfico e o `kubectl delete ns` comando removerá o `consul` namespace.

```bash
helm delete --purge consul
kubectl delete ns consul
```

## <a name="next-steps"></a>Passos Seguintes

Para explorar mais opções de instalação e configuração para o Consul, consulte os seguintes artigos oficiais do Consul:

- [Guia de instalação do Consul-Helm][consul-install-k8]
- [Opções de instalação do Consul-Helm][consul-install-helm-options]

Você também pode seguir cenários adicionais usando o [aplicativo de exemplo Consul][consul-app-example].

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

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
