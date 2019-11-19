---
title: Instalar o İSTİO no serviço kubernetes do Azure (AKS)
description: Saiba como instalar e usar o İSTİO para criar uma malha de serviço em um cluster do AKS (serviço kubernetes do Azure)
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 11/15/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 2768c2d4cef68dcf25e25c047aaa69653af5e0b6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170808"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalar e usar o İSTİO no serviço kubernetes do Azure (AKS)

O [İSTİO][istio-github] é uma malha de serviço de software livre que fornece um conjunto de chaves de funcionalidade em todos os microserviços em um cluster kubernetes. Esses recursos incluem gerenciamento de tráfego, identidade de serviço e segurança, imposição de política e observação. Para obter mais informações sobre İSTİO, consulte a documentação oficial [o que é o İSTİO?][istio-docs-concepts] .

Este artigo mostra como instalar o İSTİO. O binário do İSTİO `istioctl` Client é instalado no computador cliente e os componentes do İSTİO são instalados em um cluster kubernetes no AKS.

> [!NOTE]
> As instruções a seguir fazem referência à versão İSTİO `1.4.0`.
>
> As versões de `1.4.x` do İSTİO foram testadas pela equipe do İSTİO em relação às versões do kubernetes `1.13`, `1.14``1.15`. Você pode encontrar versões adicionais do İSTİO em [versões do GitHub-İSTİO][istio-github-releases], informações sobre cada uma das versões em [İSTİO News][istio-release-notes] e versões de kubernetes com suporte em [perguntas frequentes][istio-faq]sobre o İSTİO.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Baixar e instalar o binário do cliente İSTİO istioctl
> * Instalar o İSTİO no AKS
> * Validar a instalação do İSTİO
> * Acessar os Complementos
> * Desinstalar o İSTİO do AKS

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste artigo pressupõem que você criou um cluster AKS (kubernetes `1.13` e superior, com o RBAC habilitado) e estabeleceu uma conexão de `kubectl` com o cluster. Se precisar de ajuda com qualquer um desses itens, consulte o guia de [início rápido do AKS][aks-quickstart].

Verifique se você leu a documentação de [desempenho e escalabilidade do İSTİO](https://istio.io/docs/concepts/performance-and-scalability/) para entender os requisitos de recursos adicionais para executar o İSTİO em seu cluster AKs. Os requisitos de núcleo e memória variam de acordo com sua carga de trabalho específica. Escolha um número apropriado de nós e o tamanho da VM para atender à sua configuração.

Este artigo separa as diretrizes de instalação do İSTİO em várias etapas discretas. O resultado final é o mesmo na estrutura que as [diretrizes][istio-install-istioctl]de instalação oficial do İSTİO.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>Instalar os componentes do İSTİO no AKS

Instalaremos o [Grafana][grafana] e o [Kiali][kiali] como parte da nossa instalação do İSTİO. O Grafana fornece painéis de análise e monitoramento e o Kiali fornece um painel de observação de malha de serviço. Em nossa configuração, cada um desses componentes requer credenciais que devem ser fornecidas como um [segredo][kubernetes-secrets].

Antes que possamos instalar os componentes do İSTİO, devemos criar os segredos para Grafana e Kiali. Esses segredos precisam ser instalados no namespace `istio-system` que serão usados pelo İSTİO, portanto, precisaremos criar o namespace também. Precisamos usar a opção `--save-config` ao criar o namespace via `kubectl create` para que o instalador do İSTİO possa executar `kubectl apply` nesse objeto no futuro.

```console
kubectl create namespace istio-system --save-config
```

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>Instalar componentes do İSTİO

Agora que criamos com êxito os segredos Grafana e Kiali em nosso cluster AKS, é hora de instalar os componentes do İSTİO. 

A abordagem de instalação do [Helm][helm] para İSTİO será preterida no futuro. A nova abordagem de instalação para İSTİO aproveita o binário do cliente `istioctl`, os [perfis de configuração do İSTİO][istio-configuration-profiles]e a nova especificação do plano de [controle İSTİO e a API][istio-control-plane]. Essa nova abordagem é o que usaremos para instalar o İSTİO.

> [!NOTE]
> No momento, o İSTİO deve ser agendado para ser executado em nós do Linux. Se você tiver nós do Windows Server em seu cluster, deverá garantir que os pods İSTİO sejam agendados apenas para execução em nós do Linux. Usaremos [seletores de nó][kubernetes-node-selectors] para garantir que os pods estejam agendados para os nós corretos.

> [!CAUTION]
> Os recursos [SDS (serviço de descoberta de segredo)][istio-feature-sds] e [İSTİO CNI][istio-feature-cni] İSTİO estão atualmente em [Alpha][istio-feature-stages], portanto, deve-se considerar antes de habilitá-los. Além disso, o recurso de kubernetes de [projeção do volume de token da conta de serviço][kubernetes-feature-sa-projected-volume] (um requisito para o SDS) não está habilitado nas versões atuais do AKS.

Crie um arquivo chamado `istio.aks.yaml` com o conteúdo a seguir. Este arquivo conterá os detalhes de [especificação do plano de controle İSTİO][istio-control-plane] para configurar o İSTİO.

```yaml
apiVersion: install.istio.io/v1alpha2
kind: IstioControlPlane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
      # Enable mutual TLS for the control plane
      controlPlaneSecurityEnabled: true
      mtls:
        # Require all service to service communication to have mtls
        enabled: false
    grafana:
      # Enable Grafana deployment for analytics and monitoring dashboards
      enabled: true
      security:
        # Enable authentication for Grafana
        enabled: true
    kiali:
      # Enable the Kiali deployment for a service mesh observability dashboard
      enabled: true
    tracing:
      # Enable the Jaeger deployment for tracing
      enabled: true
```

Instale o İSTİO usando o comando `istioctl apply` e o `istio.aks.yaml` arquivo de especificação do plano de controle do İSTİO acima da seguinte maneira:

```console
istioctl manifest apply -f istio.aks.yaml
```

O instalador implantará uma série de [crds][kubernetes-crd] e, em seguida, gerenciará as dependências para instalar todos os objetos relevantes definidos para essa configuração de İSTİO. Você deverá ver algo semelhante ao trecho de saída a seguir.

```console
Applying manifests for these components:
- Tracing
- EgressGateway
- NodeAgent
- Grafana
- Policy
- Citadel
- CertManager
- IngressGateway
- Injector
- Prometheus
- PrometheusOperator
- Kiali
- Telemetry
- Galley
- Cni
- Pilot
- Base
- CoreDNS
NodeAgent is waiting on a prerequisite...
Telemetry is waiting on a prerequisite...
Galley is waiting on a prerequisite...
Cni is waiting on a prerequisite...
Grafana is waiting on a prerequisite...
Policy is waiting on a prerequisite...
Citadel is waiting on a prerequisite...
EgressGateway is waiting on a prerequisite...
Tracing is waiting on a prerequisite...
Kiali is waiting on a prerequisite...
PrometheusOperator is waiting on a prerequisite...
IngressGateway is waiting on a prerequisite...
Prometheus is waiting on a prerequisite...
CertManager is waiting on a prerequisite...
Injector is waiting on a prerequisite...
Pilot is waiting on a prerequisite...
Applying manifest for component Base
Waiting for CRDs to be applied.
CRDs applied.
Finished applying manifest for component Base
Prerequisite for Tracing has completed, proceeding with install.
Prerequisite for Injector has completed, proceeding with install.
Prerequisite for Telemetry has completed, proceeding with install.
Prerequisite for Policy has completed, proceeding with install.
Prerequisite for PrometheusOperator has completed, proceeding with install.
Prerequisite for NodeAgent has completed, proceeding with install.
Prerequisite for IngressGateway has completed, proceeding with install.
Prerequisite for Kiali has completed, proceeding with install.
Prerequisite for EgressGateway has completed, proceeding with install.
Prerequisite for Galley has completed, proceeding with install.
Prerequisite for Grafana has completed, proceeding with install.
Prerequisite for Cni has completed, proceeding with install.
Prerequisite for Citadel has completed, proceeding with install.
Applying manifest for component Tracing
Prerequisite for Prometheus has completed, proceeding with install.
Prerequisite for Pilot has completed, proceeding with install.
Prerequisite for CertManager has completed, proceeding with install.
Applying manifest for component Kiali
Applying manifest for component Prometheus
Applying manifest for component IngressGateway
Applying manifest for component Policy
Applying manifest for component Telemetry
Applying manifest for component Citadel
Applying manifest for component Galley
Applying manifest for component Pilot
Applying manifest for component Injector
Applying manifest for component Grafana
Finished applying manifest for component Kiali
Finished applying manifest for component Tracing
Finished applying manifest for component Prometheus
Finished applying manifest for component Citadel
Finished applying manifest for component Policy
Finished applying manifest for component IngressGateway
Finished applying manifest for component Injector
Finished applying manifest for component Galley
Finished applying manifest for component Pilot
Finished applying manifest for component Grafana
Finished applying manifest for component Telemetry

Component IngressGateway installed successfully:
================================================

serviceaccount/istio-ingressgateway-service-account created
deployment.apps/istio-ingressgateway created
gateway.networking.istio.io/ingressgateway created
sidecar.networking.istio.io/default created
poddisruptionbudget.policy/ingressgateway created
horizontalpodautoscaler.autoscaling/istio-ingressgateway created
service/istio-ingressgateway created

...
```

Neste ponto, você implantou o İSTİO em seu cluster AKS. Para garantir que tenhamos uma implantação bem-sucedida do İSTİO, vamos passar para a próxima seção para [validar a instalação do İSTİO](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Validar a instalação do İSTİO

Primeiro, confirme que os serviços esperados foram criados. Use o comando [kubectl Get svc][kubectl-get] para exibir os serviços em execução. Consulte o namespace `istio-system`, em que os componentes İSTİO e complemento foram instalados pelo gráfico `istio` Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

A saída de exemplo a seguir mostra os serviços que agora devem estar em execução:

- serviços `istio-*`s
- `jaeger-*`, `tracing`e `zipkin` serviços de rastreamento de complemento
- `prometheus` serviço de métricas complementares
- `grafana` o serviço de painel de análise e monitoramento do complemento
- serviço de painel de malha do serviço de `kiali` complementar

Se o `istio-ingressgateway` mostrar um IP externo de `<pending>`, aguarde alguns minutos até que um endereço IP tenha sido atribuído pela rede do Azure.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.116.147   <none>           3000/TCP                                                                                                                     92s   app=grafana
istio-citadel            ClusterIP      10.0.248.152   <none>           8060/TCP,15014/TCP                                                                                                           94s   app=citadel
istio-galley             ClusterIP      10.0.50.100    <none>           443/TCP,15014/TCP,9901/TCP,15019/TCP                                                                                         93s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.36.213    20.188.221.111   15020:30369/TCP,80:31368/TCP,443:30045/TCP,15029:32011/TCP,15030:31212/TCP,15031:32411/TCP,15032:30009/TCP,15443:30010/TCP   93s   app=istio-ingressgateway
istio-pilot              ClusterIP      10.0.23.222    <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                       93s   istio=pilot
istio-policy             ClusterIP      10.0.59.250    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                 93s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.123.219   <none>           443/TCP                                                                                                                      93s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.216.9     <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                       89s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                   96s   app=jaeger
jaeger-collector         ClusterIP      10.0.221.24    <none>           14267/TCP,14268/TCP,14250/TCP                                                                                                95s   app=jaeger
jaeger-query             ClusterIP      10.0.46.154    <none>           16686/TCP                                                                                                                    95s   app=jaeger
kiali                    ClusterIP      10.0.174.97    <none>           20001/TCP                                                                                                                    94s   app=kiali
prometheus               ClusterIP      10.0.245.226   <none>           9090/TCP                                                                                                                     94s   app=prometheus
tracing                  ClusterIP      10.0.249.95    <none>           9411/TCP                                                                                                                     95s   app=jaeger
zipkin                   ClusterIP      10.0.154.89    <none>           9411/TCP                                                                                                                     94s   app=jaeger
```

Em seguida, confirme se os pods necessários foram criados. Use o comando [kubectl Get pods][kubectl-get] e consulte novamente o namespace `istio-system`:

```console
kubectl get pods --namespace istio-system
```

A saída de exemplo a seguir mostra os pods que estão em execução:

- o pods de `istio-*`
- o pod de métricas de complemento `prometheus-*`
- o `grafana-*` o pod de painel de análise e monitoramento do complemento
- o Pod do painel de malha do serviço de `kiali` complementar

```console
NAME                                          READY   STATUS    RESTARTS   AGE
grafana-6bc97ff99-k9sk4                       1/1     Running   0          92s
istio-citadel-6b5c754454-tb8nf                1/1     Running   0          94s
istio-galley-7d6d78d7c5-zshsd                 2/2     Running   0          94s
istio-ingressgateway-85869c5cc7-x5d76         1/1     Running   0          95s
istio-pilot-787d6995b5-n5vrj                  2/2     Running   0          94s
istio-policy-6cf4fbc8dc-sdsg5                 2/2     Running   2          94s
istio-sidecar-injector-5d5b978668-wrz2s       1/1     Running   0          94s
istio-telemetry-5498db684-6kdnw               2/2     Running   1          94s
istio-tracing-78548677bc-74tx6                1/1     Running   0          96s
kiali-59b7fd7f68-92zrh                        1/1     Running   0          95s
prometheus-7c7cf9dbd6-rjxcv                   1/1     Running   0          94s
```

Todos os pods devem mostrar um status de `Running`. Se os pods não tiverem esses status, aguarde um ou dois minutos até que eles façam. Se qualquer pods relatar um problema, use o comando [kubectl para descrever o Pod][kubectl-describe] para revisar sua saída e status.

## <a name="accessing-the-add-ons"></a>Acessando os Complementos

Vários Complementos foram instalados pelo İSTİO em nossa configuração acima que fornecem funcionalidade adicional. Os aplicativos Web para os complementos **não** são expostos publicamente por meio de um endereço IP externo. 

Para acessar as interfaces de usuário do complemento, use o comando `istioctl dashboard`. Esse comando aproveita a porta [kubectl][kubectl-port-forward] e a porta aleatória para criar uma conexão segura entre o computador cliente e o Pod relevante no cluster do AKS. Em seguida, ele abrirá automaticamente o aplicativo Web complementar no navegador padrão.

Adicionamos uma camada adicional de segurança para Grafana e Kiali especificando as credenciais para elas anteriormente neste artigo.

### <a name="grafana"></a>Grafana

Os painéis de análise e monitoramento para İSTİO são fornecidos pelo [Grafana][grafana]. Lembre-se de usar as credenciais criadas por meio do segredo Grafana anteriormente quando solicitado. Abra o painel do Grafana com segurança da seguinte maneira:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

As métricas para İSTİO são fornecidas pelo [Prometheus][prometheus]. Abra o painel do Prometheus com segurança da seguinte maneira:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

O rastreamento dentro de İSTİO é fornecido pelo [Jaeger][jaeger]. Abra o painel do Jaeger com segurança da seguinte maneira:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

Um painel de observação de malha de serviço é fornecido pelo [Kiali][kiali]. Lembre-se de usar as credenciais criadas por meio do segredo Kiali anteriormente quando solicitado. Abra o painel do Kiali com segurança da seguinte maneira:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Uma interface simples para os proxies [Envoy][envoy] está disponível. Ele fornece informações de configuração e métricas para um proxy Envoy em execução em um pod especificado. Abra a interface Envoy com segurança da seguinte maneira:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Desinstalar o İSTİO do AKS

> [!WARNING]
> Excluir İSTİO de um sistema em execução pode resultar em problemas relacionados ao tráfego entre seus serviços. Verifique se você fez provisões para que o seu sistema ainda opere corretamente sem İSTİO antes de continuar.

### <a name="remove-istio-components-and-namespace"></a>Remover componentes e namespace do İSTİO

Para remover o İSTİO do cluster do AKS, use o comando `istioctl manifest generate` com o arquivo de especificação `istio.aks.yaml` plano de controle İSTİO. Isso gerará o manifesto implantado, que será redirecionado para `kubectl delete` a fim de remover todos os componentes instalados e o namespace `istio-system`.

```console
istioctl manifest generate -f istio.aks.yaml | kubectl delete -f -
```

### <a name="remove-istio-crds-and-secrets"></a>Remover İSTİO CRDs e segredos

Os comandos acima excluem todos os componentes e namespace İSTİO, mas ainda continuamos com segredos İSTİO gerados. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Passos seguintes

A documentação a seguir descreve como você pode usar o İSTİO para fornecer roteamento inteligente para distribuir uma versão do canário:

> [!div class="nextstepaction"]
> [Cenário de roteamento inteligente do AKS İSTİO][istio-scenario-routing]

Para explorar mais opções de instalação e configuração para o İSTİO, consulte as seguintes diretrizes oficiais de İSTİO:

- [İSTİO-guias de instalação][istio-installation-guides]

Você também pode seguir cenários adicionais usando:

- [Exemplo de aplicativo İSTİO BookInfo][istio-bookinfo-example]

Para saber como monitorar seu aplicativo AKS usando o Application Insights e o İSTİO, consulte a seguinte documentação de Azure Monitor:

- [Monitoramento de aplicativo de instrumentação zero para aplicativos hospedados kubernetes][app-insights]

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
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha12.pb/#IstioControlPlane
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./servicemesh-istio-scenario-routing.md
