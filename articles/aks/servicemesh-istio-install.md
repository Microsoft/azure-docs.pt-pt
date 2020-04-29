---
title: Instalar istio no Serviço Azure Kubernetes (AKS)
description: Aprenda a instalar e utilizar a Istio para criar uma malha de serviço num cluster do Serviço Azure Kubernetes (AKS)
author: paulbouwer
ms.topic: article
ms.date: 02/19/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d1d02cb42a86023e5c341daab678c39f22f75dda
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80877699"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalar e utilizar o Istio no Serviço Azure Kubernetes (AKS)

[Istio][istio-github] é uma malha de serviço de código aberto que fornece um conjunto chave de funcionalidades em todos os microserviços em um cluster Kubernetes. Estas características incluem gestão de tráfego, identidade de serviço e segurança, aplicação de políticas e observabilidade. Para mais informações sobre Istio, consulte a documentação oficial [do Istio?][istio-docs-concepts]

Este artigo mostra-lhe como instalar istio. O binário `istioctl` do cliente Istio está instalado na sua máquina cliente e os componentes Istio estão instalados num cluster Kubernetes no AKS.

> [!NOTE]
> As seguintes instruções `1.4.0`referem a versão Istio .
>
> Os lançamentos da `1.4.x` Istio foram testados pela equipa Istio contra as versões `1.13`Kubernetes, `1.14`. `1.15` Pode encontrar versões istio adicionais no [GitHub - Istio Releases,][istio-github-releases]informações sobre cada um dos lançamentos na [Istio News][istio-release-notes] e versões kubernetes suportadas na [Istio General FAQ.][istio-faq]

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Descarregue e instale o binário do cliente istioctl istioctl
> * Instale istio na AKS
> * Validar a instalação Istio
> * Aceda aos addons
> * Desinstalar Istio da AKS

## <a name="before-you-begin"></a>Antes de começar

Os passos detalhados neste artigo assumem que criou um `1.13` cluster AKS (Kubernetes ou `kubectl` acima, com RBAC habilitado) e estabeleceu uma ligação com o cluster. Se precisar de ajuda com algum destes itens, consulte o arranque rápido do [AKS.][aks-quickstart]

Certifique-se de que leu a documentação [istio performance e escalabilidade](https://istio.io/docs/concepts/performance-and-scalability/) para entender os requisitos adicionais de recursos para executar istio no seu cluster AKS. Os requisitos de núcleo e memória variarão em função da sua carga de trabalho específica. Escolha um número adequado de nós e tamanho VM para atender a sua configuração.

Este artigo separa a orientação de instalação istio em vários passos discretos. O resultado final é o mesmo na estrutura que a [orientação][istio-install-istioctl]oficial de instalação istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>Instale os componentes Istio no AKS

Vamos instalar [grafana][grafana] e [Kiali][kiali] como parte da nossa instalação istio. Grafana fornece análise e monitorização de dashboards, e Kiali fornece um painel de observação de malha de serviço. Na nossa configuração, cada um destes componentes requer credenciais que devem ser fornecidas como [segredo.][kubernetes-secrets]

Antes de podermos instalar os componentes istio, temos de criar os segredos tanto para grafana como para o Kiali. Estes segredos precisam de `istio-system` ser instalados no espaço de nome que será usado pela Istio, por isso também teremos de criar o espaço de nome. Precisamos de `--save-config` usar a opção `kubectl create` ao criar o espaço de `kubectl apply` nome sem que o instalador Istio possa funcionar neste objeto no futuro.

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

### <a name="install-istio-components"></a>Instalar componentes Istio

Agora que criámos com sucesso os segredos de Grafana e Kiali no nosso cluster AKS, está na hora de instalar os componentes istio. 

A abordagem de instalação [helm][helm] para Istio será depreciada no futuro. A nova abordagem de instalação da Istio aproveita o binário do `istioctl` cliente, os perfis de [configuração Istio,][istio-configuration-profiles]e a nova especificação e api do novo plano de controlo [istio.][istio-control-plane] Esta nova abordagem é o que vamos usar para instalar o Istio.

> [!NOTE]
> Istio deve estar programado para correr em nódeos Linux. Se tiver nós do Windows Server no seu cluster, tem de garantir que as cápsulas Istio só estão programadas para serem executadas nos nós linux. Usaremos [selecionadores][kubernetes-node-selectors] de nós para garantir que as cápsulas estão programadas para os nós corretos.

> [!CAUTION]
> As [funcionalidades SDS (serviço][istio-feature-sds] de descoberta secreta) e [Istio CNI][istio-feature-cni] Istio estão atualmente em [Alpha,][istio-feature-stages]pelo que o pensamento deve ser dado antes de permitir estes. 
>
> Note que a funcionalidade De Projeção de Volume de [Token da Conta de Serviço][kubernetes-feature-sa-projected-volume] (um requisito para SDS) está agora **ativada** para todas as versões Kubernetes 1.13 e mais altas no AKS.

Crie um `istio.aks.yaml` ficheiro chamado com o seguinte conteúdo. Este ficheiro irá conter os detalhes de especificações do plano de [controlo istio][istio-control-plane] para configurar istio.

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

Instale o `istioctl apply` istio utilizando `istio.aks.yaml` o comando e o ficheiro de especificação de plano de controlo istio acima da seguinte forma:

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

O instalador irá implantar uma série de [CRDs][kubernetes-crd] e, em seguida, gerir dependências para instalar todos os objetos relevantes definidos para esta configuração de Istio. Deviaver algo como o seguinte corte de saída.

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

Neste momento, implantaste o Istio no teu aglomerado AKS. Para garantir que temos uma implementação bem sucedida da Istio, vamos passar à secção seguinte para [Validar a instalação istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Validar a instalação Istio

Primeiro, confirme que os serviços esperados foram criados. Use o [kubectl obter][kubectl-get] comando svc para ver os serviços de funcionamento. Consulta do `istio-system` espaço de nome, onde os componentes Istio e `istio` add-on foram instalados pela tabela Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

A saída de exemplo a seguir mostra os serviços que devem estar agora a funcionar:

- `istio-*`serviços
- `jaeger-*`, `tracing`e `zipkin` serviços de rastreio adicionais
- `prometheus`serviço de métricas adicionais
- `grafana`análise adicionais e serviço de monitorização do dashboard
- `kiali`serviço de painel de instrumentos de malha de serviço adicionais

Se `istio-ingressgateway` os programas mostrarem um ip externo de `<pending>`, aguarde alguns minutos até que um endereço IP tenha sido atribuído por rede Azure.

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

Em seguida, confirme que as cápsulas necessárias foram criadas. Use o [kubectl obter comando de casulos,][kubectl-get] e novamente consultar o espaço de `istio-system` nome:

```console
kubectl get pods --namespace istio-system
```

A saída de exemplo a seguir mostra as cápsulas que estão em execução:

- as `istio-*` cápsulas
- o `prometheus-*` casulo de métricas adicionais
- a `grafana-*` análise adicionais e monitorização do painel de instrumentos
- o `kiali` painel de instrumentos de malha de serviço add-on

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

Todas as cápsulas devem mostrar `Running`um estado de . Se as suas cápsulas não tiverem estes estatutos, aguarde um minuto ou dois até que o façam. Se alguma cápsula reportar um problema, use o comando de cápsula de [utilização kubectl][kubectl-describe] para rever a sua saída e estado.

## <a name="accessing-the-add-ons"></a>Acesso aos addons

Vários addons foram instalados pela Istio na nossa configuração acima que fornecem funcionalidadeadicional. As aplicações web para os addons **não** são expostas publicamente através de um endereço ip externo. 

Para aceder às interfaces adicionais `istioctl dashboard` do utilizador, utilize o comando. Este comando alavanca [a porta-frente kubectl][kubectl-port-forward] e uma porta aleatória para criar uma ligação segura entre a sua máquina cliente e a cápsula relevante no seu cluster AKS. Em seguida, abrirá automaticamente a aplicação web add-on no seu navegador predefinido.

Adicionámos uma camada adicional de segurança para Grafana e Kiali, especificando credenciais para eles no início deste artigo.

### <a name="grafana"></a>Grafana

Os painéis de análise e monitorização de Istio são fornecidos pela [Grafana.][grafana] Lembre-se de usar as credenciais que criou através do segredo de Grafana mais cedo quando solicitado. Abra o painel grafana de forma segura da seguinte forma:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

As métricas para Istio são fornecidas por [Prometeu.][prometheus] Abra o painel prometheus de forma segura da seguinte forma:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Rio Jaeger

O rastreio dentro de Istio é fornecido por [Jaeger.][jaeger] Abra o painel jaeger de forma segura da seguinte forma:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Rio Kiali

Um painel de observação de malha de serviço é fornecido pela [Kiali.][kiali] Lembre-se de usar as credenciais que criou através do segredo de Kiali mais cedo quando solicitado. Abra o tablier Kiali de forma segura da seguinte forma:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Uma interface simples para os proxies do [Enviado][envoy] está disponível. Fornece informações e métricas de configuração para um representante do enviado que funciona numa cápsula especificada. Abra a interface do Enviado de forma segura:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Desinstalar Istio da AKS

> [!WARNING]
> A descontinuação da Istio de um sistema de funcionamento pode resultar em problemas relacionados com o tráfego entre os seus serviços. Certifique-se de que fez provisões para que o seu sistema continue a funcionar corretamente sem a Istio antes de prosseguir.

### <a name="remove-istio-components-and-namespace"></a>Remover componentes Istio e espaço de nome

Para remover o Istio do seu `istioctl manifest generate` cluster `istio.aks.yaml` AKS, use o comando com o ficheiro de especificação de plano de controlo istio. Isto gerará o manifesto implantado, ao `kubectl delete` qual iremos canalizar para remover `istio-system` todos os componentes instalados e o espaço de nome.

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>Remover IStio CRDs e Segredos

Os comandos acima eliminam todos os componentes istio e espaço de nome, mas ainda nos resta com segredos istio gerados. 

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

A seguinte documentação descreve como pode usar istio para fornecer encaminhamento inteligente para lançar uma libertação canária:

> [!div class="nextstepaction"]
> [Cenário inteligente de encaminhamento da AKS Istio][istio-scenario-routing]

Para explorar mais opções de instalação e configuração para istio, consulte a seguinte orientação oficial istio:

- [Istio - guias de instalação][istio-installation-guides]

Também pode seguir cenários adicionais usando:

- [Exemplo de aplicação Istio Bookinfo][istio-bookinfo-example]

Para saber como monitorizar a sua aplicação AKS utilizando insights de aplicação e istio, consulte a seguinte documentação do Monitor Do Azure:

- [Monitorização de aplicações de instrumentação zero para aplicações hospedadas pela Kubernetes][app-insights]

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
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
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
