---
title: Instalar Linkerd no Serviço Azure Kubernetes (AKS)
description: Saiba como instalar e utilizar o Linkerd para criar uma malha de serviço num cluster Azure Kubernetes Service (AKS)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 419b61527b68299c82dec4f2f5da6b0220859cc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77593743"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>Instalar Linkerd no Serviço Azure Kubernetes (AKS)

[Linkerd][linkerd-github] é um projeto de incubação de malha de serviço de código aberto e [CNCF.][linkerd-cncf] Linkerd é uma malha de serviço ultraleve que fornece funcionalidades que incluem gestão de tráfego, identidade de serviço e segurança, fiabilidade e observabilidade. Para obter mais informações sobre linkerd, consulte a documentação oficial [da Linkerd FAQ][linkerd-faq] e [linkerd Architecture.][linkerd-architecture]

Este artigo mostra-lhe como instalar linkerd. O binário do cliente Linkerd `linkerd` está instalado na sua máquina cliente e os componentes Linkerd são instalados num cluster Kubernetes em AKS.

> [!NOTE]
> Estas instruções referem a versão Linkerd `stable-2.6.0` .
>
> O Linkerd `stable-2.6.x` pode ser executado contra as versões de Kubernetes `1.13+` . Pode encontrar versões linkerd mais estáveis e edge no [GitHub - Linkerd Releases][linkerd-github-releases].

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Descarregue e instale o binário de cliente linkerd Linkerd
> * Instalar Linkerd em AKS
> * Validar a instalação Linkerd
> * Aceda ao Painel de Instrumentos
> * Desinstalar Linkerd da AKS

## <a name="before-you-begin"></a>Antes de começar

Os passos detalhados neste artigo assumem que criou um cluster AKS (Kubernetes `1.13` e acima, com o RBAC habilitado) e estabeleceu uma `kubectl` ligação com o cluster. Se precisar de ajuda com algum destes itens, consulte o arranque rápido da [AKS][aks-quickstart].

Todas as cápsulas Linkerd devem ser programadas para funcionar em nós Linux - esta configuração é o padrão no método de instalação descrito abaixo e não requer nenhuma configuração adicional.

Este artigo separa a orientação de instalação linkerd em vários passos discretos. O resultado é o mesmo na estrutura que o Linkerd oficial começa a [orientar.][linkerd-getting-started]

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>Instalar Linkerd em AKS

Antes de instalarmos o Linkerd, vamos executar verificações de pré-instalação para determinar se o plano de controlo pode ser instalado no nosso cluster AKS:

```console
linkerd check --pre
```

Deve ver algo como o seguinte para indicar que o seu cluster AKS é um alvo de instalação válido para linkerd:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

pre-kubernetes-setup
--------------------
√ control plane namespace does not already exist
√ can create Namespaces
√ can create ClusterRoles
√ can create ClusterRoleBindings
√ can create CustomResourceDefinitions
√ can create PodSecurityPolicies
√ can create ServiceAccounts
√ can create Services
√ can create Deployments
√ can create CronJobs
√ can create ConfigMaps
√ no clock skew detected

pre-kubernetes-capability
-------------------------
√ has NET_ADMIN capability
√ has NET_RAW capability

pre-linkerd-global-resources
----------------------------
√ no ClusterRoles exist
√ no ClusterRoleBindings exist
√ no CustomResourceDefinitions exist
√ no MutatingWebhookConfigurations exist
√ no ValidatingWebhookConfigurations exist
√ no PodSecurityPolicies exist

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

Status check results are √
```

Agora é hora de instalar os componentes Linkerd. Utilize os `linkerd` binários e `kubectl` binários para instalar os componentes Linkerd no seu cluster AKS. Um `linkerd` espaço de nome será criado automaticamente e os componentes serão instalados neste espaço de nome.

```console
linkerd install | kubectl apply -f -
```

Linkerd implanta uma série de objetos. Verá a lista a partir da saída do seu `linkerd install` comando acima. A colocação dos componentes Linkerd deve demorar cerca de 1 minuto a ser concluída, dependendo do ambiente do seu cluster.

Neste momento, lançou o Linkerd para o seu cluster AKS. Para garantir que temos uma implementação bem sucedida do Linkerd, vamos passar para a secção seguinte para [validar a instalação Linkerd](#validate-the-linkerd-installation).

## <a name="validate-the-linkerd-installation"></a>Validar a instalação Linkerd

Confirme que os recursos foram criados com sucesso. Utilize o [kubectl get svc][kubectl-get] e [o kubectl obter][kubectl-get] comandos de pod para consultar o `linkerd` espaço de nomes, onde os componentes linkerd foram instalados pelo `linkerd install` comando:

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

A saída de exemplo a seguir mostra os serviços e cápsulas (programados nos nós Linux) que devem estar agora em execução:

```console
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE  SELECTOR
linkerd-controller-api   ClusterIP   10.0.110.67    <none>        8085/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-destination      ClusterIP   10.0.224.29    <none>        8086/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-dst              ClusterIP   10.0.225.148   <none>        8086/TCP            66s  linkerd.io/control-plane-component=destination
linkerd-grafana          ClusterIP   10.0.61.124    <none>        3000/TCP            65s  linkerd.io/control-plane-component=grafana
linkerd-identity         ClusterIP   10.0.6.104     <none>        8080/TCP            67s  linkerd.io/control-plane-component=identity
linkerd-prometheus       ClusterIP   10.0.27.168    <none>        9090/TCP            65s  linkerd.io/control-plane-component=prometheus
linkerd-proxy-injector   ClusterIP   10.0.100.133   <none>        443/TCP             64s  linkerd.io/control-plane-component=proxy-injector
linkerd-sp-validator     ClusterIP   10.0.221.5     <none>        443/TCP             64s  linkerd.io/control-plane-component=sp-validator
linkerd-tap              ClusterIP   10.0.18.14     <none>        8088/TCP,443/TCP    64s  linkerd.io/control-plane-component=tap
linkerd-web              ClusterIP   10.0.37.108    <none>        8084/TCP,9994/TCP   66s  linkerd.io/control-plane-component=web

NAME                                      READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
linkerd-controller-66ddc9f94f-cm9kt       3/3     Running   0          66s   10.240.0.50   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-destination-c94bc454-qpkng        2/2     Running   0          66s   10.240.0.78   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-grafana-6868fdcb66-4cmq2          2/2     Running   0          65s   10.240.0.69   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-identity-74d8df4b85-tqq8f         2/2     Running   0          66s   10.240.0.48   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-prometheus-699587cf8-k8ghg        2/2     Running   0          65s   10.240.0.41   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-proxy-injector-6556447f64-n29wr   2/2     Running   0          64s   10.240.0.32   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-sp-validator-56745cd567-v4x7h     2/2     Running   0          64s   10.240.0.6    aks-linux-16165125-vmss000000   <none>           <none>
linkerd-tap-5cd9fc566-ct988               2/2     Running   0          64s   10.240.0.15   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-web-774c79b6d5-dhhwf              2/2     Running   0          65s   10.240.0.70   aks-linux-16165125-vmss000002   <none>           <none>
```

Linkerd fornece um comando através do binário do `linkerd` cliente para validar que o plano de controlo Linkerd foi instalado e configurado com sucesso.

```console
linkerd check
```

Deve ver algo como o seguinte para indicar que a sua instalação foi bem sucedida:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

linkerd-config
--------------
√ control plane Namespace exists
√ control plane ClusterRoles exist
√ control plane ClusterRoleBindings exist
√ control plane ServiceAccounts exist
√ control plane CustomResourceDefinitions exist
√ control plane MutatingWebhookConfigurations exist
√ control plane ValidatingWebhookConfigurations exist
√ control plane PodSecurityPolicies exist

linkerd-existence
-----------------
√ 'linkerd-config' config map exists
√ heartbeat ServiceAccount exist
√ control plane replica sets are ready
√ no unschedulable pods
√ controller pod is running
√ can initialize the client
√ can query the control plane API

linkerd-api
-----------
√ control plane pods are ready
√ control plane self-check
√ [kubernetes] control plane can talk to Kubernetes
√ [prometheus] control plane can talk to Prometheus
√ no invalid service profiles

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

control-plane-version
---------------------
√ control plane is up-to-date
√ control plane and cli versions match

Status check results are √
```

## <a name="access-the-dashboard"></a>Aceda ao painel de instrumentos

Linkerd vem com um dashboard que fornece informações sobre a malha de serviço e cargas de trabalho. Para aceder ao painel, utilize o `linkerd dashboard` comando. Este comando aproveita [a porta de avanço do Kubectl][kubectl-port-forward] para criar uma ligação segura entre a sua máquina cliente e as cápsulas relevantes no seu cluster AKS. Em seguida, abrirá automaticamente o dashboard no seu navegador predefinido.

```console
linkerd dashboard
```

O comando também criará um porto-avançado e devolverá um link para os tabliers Grafana.

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>Desinstalar Linkerd da AKS

> [!WARNING]
> A eliminação do Linkerd de um sistema de funcionamento pode resultar em problemas relacionados com o tráfego entre os seus serviços. Certifique-se de que eviscrou para que o seu sistema continue a funcionar corretamente sem o Linkerd antes de prosseguir.

Primeiro, terá de remover os proxies do plano de dados. Remova quaisquer [anotações automáticas][linkerd-automatic-proxy-injection] de injeção de procuração dos espaços de nome da carga de trabalho e desenrolar as suas implementações de carga de trabalho. As suas cargas de trabalho não devem continuar a ter componentes de planos de dados associados.

Por último, retire o plano de controlo da seguinte forma:

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>Passos seguintes

Para explorar mais opções de instalação e configuração para Linkerd, consulte as seguintes orientações oficiais do Linkerd:

- [Linkerd - Instalação de leme][linkerd-install-with-helm]
- [Linkerd - Instalação em várias fases para atender privilégios de função][linkerd-multi-stage-installation]

Também pode seguir cenários adicionais utilizando:

- [Demonstração de emojivoto linkerd][linkerd-demo-emojivoto]
- [Demonstração de livros linkerd][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/selected=linkerd
[linkerd-faq]: https://linkerd.io/2/faq/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-getting-started]: https://linkerd.io/2/getting-started/
[linkerd-overview]: https://linkerd.io/2/overview/
[linkerd-github]: https://github.com/linkerd/linkerd2
[linkerd-github-releases]: https://github.com/linkerd/linkerd2/releases/

[linkerd-install-with-helm]: https://linkerd.io/2/tasks/install-helm/
[linkerd-multi-stage-installation]: https://linkerd.io/2/tasks/install/#multi-stage-install
[linkerd-automatic-proxy-injection]: https://linkerd.io/2/features/proxy-injection/

[linkerd-demo-emojivoto]: https://linkerd.io/2/getting-started/#step-5-install-the-demo-app
[linkerd-demo-books]: https://linkerd.io/2/tasks/books/

[helm]: https://helm.sh

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
