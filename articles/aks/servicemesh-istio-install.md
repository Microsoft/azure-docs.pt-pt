---
title: Instalar istio no serviço Azure Kubernetes (AKS)
description: Saiba como instalar e utilizar o Istio para criar uma malha de serviço num cluster Azure Kubernetes Service (AKS)
author: paulbouwer
ms.topic: article
ms.date: 10/02/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: c42e9c31397e9313898d7029366bc8de169d368e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94683833"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instale e utilize o Istio no Serviço Azure Kubernetes (AKS)

[A Istio][istio-github] é uma malha de serviço de código aberto que fornece um conjunto chave de funcionalidades em todos os microserviços de um cluster Kubernetes. Estas características incluem gestão de tráfego, identidade de serviço e segurança, aplicação de políticas e observabilidade. Para mais informações sobre Istio, consulte o oficial O que é a documentação [istio?][istio-docs-concepts]

Este artigo mostra-lhe como instalar o Istio. O binário do cliente Istio `istioctl` está instalado na sua máquina cliente e os componentes Istio são instalados num cluster Kubernetes em AKS.

> [!NOTE]
> As seguintes instruções referem a versão Istio `1.7.3` .
>
> Os lançamentos da Istio `1.7.x` foram testados pela equipa Istio contra a versão Kubernetes. `1.16+` Pode encontrar versões Istio adicionais no [GitHub - Istio Releases][istio-github-releases], informações sobre cada um dos lançamentos no [Istio News][istio-release-notes] e versões Kubernetes suportadas na [Istio General FAQ][istio-faq].

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Descarregue e instale o binário de cliente istioctl Istio
> * Instalar Istio em AKS
> * Validar a instalação istio
> * Aceda aos addons
> * Desinstalar Istio da AKS

## <a name="before-you-begin"></a>Antes de começar

Os passos detalhados neste artigo assumem que criou um cluster AKS (Kubernetes `1.16` e acima, com o RBAC de Kubernetes habilitado) e estabeleceu uma `kubectl` ligação com o cluster. Se precisar de ajuda com algum destes itens, consulte o arranque rápido da [AKS][aks-quickstart].

Certifique-se de que leu a documentação [Istio Performance e Scalability](https://istio.io/docs/concepts/performance-and-scalability/) para entender os requisitos adicionais de recursos para executar a Istio no seu cluster AKS. Os requisitos de núcleo e memória variarão em função da sua carga de trabalho específica. Escolha um número adequado de nós e tamanho VM para atender à sua configuração.

Este artigo separa a orientação de instalação istio em vários passos discretos. O resultado final é o mesmo em estrutura que a orientação oficial de [instalação][istio-install-istioctl]da Istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-operator-on-aks"></a>Instale o operador istio em AKS

A Istio fornece um [Operador][istio-install-operator] para gerir a instalação e atualizações dos componentes Istio dentro do seu cluster AKS. Vamos instalar o Operador Istio usando o binário do `istioctl` cliente.

```bash
istioctl operator init
```

Deverá ver algo como a seguinte saída para confirmar que o Operador Istio foi instalado.

```console
Using operator Deployment image: docker.io/istio/operator:1.7.3
✔ Istio operator installed
✔ Installation complete
```

O Operador Istio está instalado no espaço de `istio-operator` nomes. Consulta o espaço de nomes.

```bash
kubectl get all -n istio-operator
```

Deve ver os seguintes componentes implantados.

```console
NAME                                  READY   STATUS    RESTARTS   AGE
pod/istio-operator-6d7958b7bf-wxgdc   1/1     Running   0          2m43s

NAME                     TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
service/istio-operator   ClusterIP   10.0.8.57    <none>        8383/TCP   2m43s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istio-operator   1/1     1            1           2m43s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/istio-operator-6d7958b7bf   1         1         1       2m43s
```

Pode saber mais sobre o padrão do Operador e como pode ajudar a automatizar tarefas complexas através [de kubernetes.io][kubernetes-operator].


### <a name="install-istio-components"></a>Instalar componentes istio

Agora que instalámos com sucesso o Operador Istio no nosso cluster AKS, é hora de instalar os componentes istio. 

Alavancaremos o `default` [Perfil de Configuração Istio][istio-configuration-profiles] para construir a [Especificação do Operador Istio.][istio-control-plane]

Pode executar o `istioctl` seguinte comando para visualizar a configuração para o Perfil de `default` Configuração Istio.

```bash
istioctl profile dump default
```

> [!NOTE]
> A Istio deve estar programada para correr nos nós Linux. Se tiver nós do Windows Server no seu cluster, deve certificar-se de que as cápsulas Istio estão programadas apenas para funcionar nos nós Linux. Usaremos [os seletores de nó][kubernetes-node-selectors] para garantir que as cápsulas estão programadas para os nós corretos.

> [!CAUTION]
> As funcionalidades [Istio CNI][istio-feature-cni] Istio estão atualmente em [Alpha,][istio-feature-stages]pelo que o pensamento deve ser dado antes de permitir estes. 

Crie um ficheiro chamado `istio.aks.yaml` com o seguinte conteúdo. Este ficheiro irá deter a [Especificação do Operador Istio][istio-control-plane] para configurar o Istio.

```yaml
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  namespace: istio-system
  name: istio-control-plane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  # Enable the addons that we will want to use
  addonComponents:
    grafana:
      enabled: true
    prometheus:
      enabled: true
    tracing:
      enabled: true
    kiali:
      enabled: true
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
    kiali:
      dashboard:
        auth:
          strategy: anonymous 
```

Crie o `istio-system` espaço de nomes e implemente a Especificação do Operador Istio para esse espaço de nome. O Operador Istio estará atento à Especificação do Operador Istio e irá usá-lo para instalar e configurar a Istio no seu cluster AKS.

```bash
kubectl create ns istio-system

kubectl apply -f istio.aks.yaml 
```

Neste momento, enviaste o Istio para o teu aglomerado AKS. Para garantir que temos uma implantação bem sucedida da Istio, vamos passar à secção seguinte para [validar a instalação istio.](#validate-the-istio-installation)

## <a name="validate-the-istio-installation"></a>Validar a instalação istio

Consulta o `istio-system` espaço de nomes, onde os componentes Istio e add-on foram instalados pelo Operador Istio:

```bash
kubectl get all -n istio-system
```

Deve ver os seguintes componentes:

- `istio*` - os componentes istio
- `jaeger-*`, `tracing` e `zipkin` - addon de rastreio
- `prometheus` - addon métricas
- `grafana` - addon de painel de análise e monitorização
- `kiali` - addon de painel de malha de serviço

```console
NAME                                        READY   STATUS    RESTARTS   AGE
pod/grafana-7cf9794c74-mpfbp                1/1     Running   0          5m53s
pod/istio-ingressgateway-86b5dbdcb9-ndrp5   1/1     Running   0          5m57s
pod/istio-tracing-c98f4b8fc-zqklg           1/1     Running   0          82s
pod/istiod-6965c56995-4ph9h                 1/1     Running   0          6m15s
pod/kiali-7b44985d68-p87zh                  1/1     Running   0          81s
pod/prometheus-6868989549-5ghzz             1/1     Running   0          81s

NAME                                TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                                                      AGE
service/grafana                     ClusterIP      10.0.226.39    <none>         3000/TCP                                                     5m54s
service/istio-ingressgateway        LoadBalancer   10.0.143.56    20.53.72.254   15021:32166/TCP,80:31684/TCP,443:31302/TCP,15443:30863/TCP   5m57s
service/istiod                      ClusterIP      10.0.211.228   <none>         15010/TCP,15012/TCP,443/TCP,15014/TCP,853/TCP                6m16s
service/jaeger-agent                ClusterIP      None           <none>         5775/UDP,6831/UDP,6832/UDP                                   82s
service/jaeger-collector            ClusterIP      10.0.7.62      <none>         14267/TCP,14268/TCP,14250/TCP                                82s
service/jaeger-collector-headless   ClusterIP      None           <none>         14250/TCP                                                    82s
service/jaeger-query                ClusterIP      10.0.52.172    <none>         16686/TCP                                                    82s
service/kiali                       ClusterIP      10.0.71.179    <none>         20001/TCP                                                    82s
service/prometheus                  ClusterIP      10.0.171.151   <none>         9090/TCP                                                     82s
service/tracing                     ClusterIP      10.0.195.137   <none>         80/TCP                                                       82s
service/zipkin                      ClusterIP      10.0.136.111   <none>         9411/TCP                                                     82s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana                1/1     1            1           5m54s
deployment.apps/istio-ingressgateway   1/1     1            1           5m58s
deployment.apps/istio-tracing          1/1     1            1           83s
deployment.apps/istiod                 1/1     1            1           6m16s
deployment.apps/kiali                  1/1     1            1           83s
deployment.apps/prometheus             1/1     1            1           82s

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-7cf9794c74                1         1         1       5m54s
replicaset.apps/istio-ingressgateway-86b5dbdcb9   1         1         1       5m58s
replicaset.apps/istio-tracing-c98f4b8fc           1         1         1       83s
replicaset.apps/istiod-6965c56995                 1         1         1       6m16s
replicaset.apps/kiali-7b44985d68                  1         1         1       82s
replicaset.apps/prometheus-6868989549             1         1         1       82s

NAME                                                       REFERENCE                         TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istio-ingressgateway   Deployment/istio-ingressgateway   7%/80%    1         5         1          5m57s
horizontalpodautoscaler.autoscaling/istiod                 Deployment/istiod                 1%/80%    1         5         1          6m16s
```

Também pode obter informações adicionais sobre a instalação observando os registos do Operador Istio.

```bash
kubectl logs -n istio-operator -l name=istio-operator -f
```

Se o `istio-ingressgateway` programa mostrar uma ip externa de , `<pending>` aguarde alguns minutos até que um endereço IP tenha sido atribuído pela rede Azure.

Todas as cápsulas devem mostrar um estado de `Running` . Se as suas cápsulas não tiverem estes estatutos, espere um minuto ou dois até que tenham. Se alguma cápsula reportar um problema, utilize o comando [de pod de descrever kubectl][kubectl-describe] para rever a sua saída e estado.

## <a name="accessing-the-add-ons"></a>Aceder aos addons

Vários addons foram instalados pelo Operador Istio que fornecem funcionalidades adicionais. As aplicações web para os addons **não** são expostas publicamente através de um endereço ip externo. 

Para aceder às interfaces de utilizador adicionais, utilize o `istioctl dashboard` comando. Este comando utiliza [uma porta de porta kubectl][kubectl-port-forward] e uma porta aleatória para criar uma ligação segura entre a sua máquina cliente e a cápsula relevante no seu cluster AKS. Em seguida, abrirá automaticamente a aplicação web add-on no seu navegador padrão.

### <a name="grafana"></a>Grafana

Os painéis de análise e monitorização da Istio são fornecidos pela [Grafana.][grafana] Lembre-se de usar as credenciais que criou através do segredo de Grafana mais cedo quando solicitado. Abra o painel grafana com segurança da seguinte forma:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

As métricas de Istio são fornecidas por [Prometeu.][prometheus] Abra o painel Prometheus com segurança da seguinte forma:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Rio Jaeger

O rastreio dentro de Istio é fornecido por [Jaeger.][jaeger] Abra o painel Jaeger com segurança da seguinte forma:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Rio Kiali

Um painel de observação de malha de serviço é fornecido pela [Kiali.][kiali] Lembre-se de usar as credenciais que criou através do segredo kiali mais cedo quando solicitado. Abra o painel Kiali com segurança da seguinte forma:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Uma interface simples para os proxies [do Enviado][envoy] está disponível. Fornece informações e métricas de configuração para um representante do Enviado que está a funcionar numa cápsula especificada. Abra a interface do Enviado com segurança da seguinte forma:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Desinstalar Istio da AKS

> [!WARNING]
> A eliminação do Istio de um sistema de funcionamento pode resultar em problemas relacionados com o tráfego entre os seus serviços. Certifique-se de que esticou disposições para que o seu sistema continue a funcionar corretamente sem a Istio antes de prosseguir.

### <a name="remove-istio"></a>Remover Istio

Para remover o Istio do seu cluster AKS, elimine o `IstioOperator` recurso nomeado `istio-control-plane` que adicionamos anteriormente. O Operador Istio reconhecerá que a Especificação do Operador Istio foi removida e, em seguida, eliminará todos os componentes Istio associados.

```bash
kubectl delete istiooperator istio-control-plane -n istio-system
```

Pode executar o seguinte para verificar se todos os componentes Istio foram eliminados.

```bash
kubectl get all -n istio-system
```

### <a name="remove-istio-operator"></a>Remover Operador Istio

Uma vez que a Istio tenha sido desinstalada com sucesso, também pode remover o Operador Istio.

```bash
istioctl operator remove
```

E, finalmente, remova os `istio-` espaços de nome.

```bash
kubectl delete ns istio-system
kubectl delete ns istio-operator
```

## <a name="next-steps"></a>Passos seguintes

Para explorar mais opções de instalação e configuração para Istio, consulte as seguintes orientações oficiais da Istio:

- [Istio - guias de instalação][istio-installation-guides]

Também pode seguir cenários adicionais utilizando:

- [Exemplo da aplicação Istio Bookinfo][istio-bookinfo-example]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-install-operator]: https://istio.io/latest/docs/setup/install/operator/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[kubernetes-operator]: https://kubernetes.io/docs/concepts/extend-kubernetes/operator/
[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
