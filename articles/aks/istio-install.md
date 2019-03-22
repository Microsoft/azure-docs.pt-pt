---
title: Instalar Istio no Azure Kubernetes Service (AKS)
description: Saiba como instalar e utilizar Istio para criar uma malha de serviço num cluster do Azure Kubernetes Service (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: d85b830b63e2d52f3eeb5df8645edccfccf43c76
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58138155"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalar e utilizar Istio no Azure Kubernetes Service (AKS)

[Istio] [ istio-github] é uma malha de serviço do código-fonte aberto que fornece um conjunto de chaves de funcionalidade entre os microsserviços num cluster do Kubernetes. Esses recursos incluem gerenciamento de tráfego, a identidade de serviço e segurança, imposição de políticas e observability. Para obter mais informações sobre Istio, consulte oficial [o que é Istio?] [ istio-docs-concepts] documentação.

Este artigo mostra-lhe como instalar Istio. O Istio `istioctl` binário de cliente é instalada no seu computador cliente, em seguida, os componentes de Istio estão instalados num cluster de Kubernetes no AKS. Estas instruções fazem referência a versão de Istio *1.0.4*. Pode encontrar versões de Istio adicionais em [GitHub - versões Istio][istio-github-releases].

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Transferir Istio
> * Instalar o cliente de Istio binário
> * Instalar os componentes de Istio Kubernetes
> * Validar a instalação

## <a name="before-you-begin"></a>Antes de começar

Os passos detalhados neste artigo partem do princípio de que criou um cluster do AKS (1.10 de Kubernetes e superior, com RBAC ativado) e estabeleceu uma `kubectl` ligação com o cluster. Se precisar de ajuda com qualquer um desses itens, em seguida, consulte a [início rápido do AKS][aks-quickstart].

Para instalar Istio, precisa [Helm] [ helm] versão *2.10.0* ou, mais tarde corretamente instalado e configurado no seu cluster. Se precisar de ajuda com a instalação Helm, consulte a [orientações de instalação do AKS Helm][helm-install]. Se não tiver, pelo menos, versão *2.10.0* do Helm a instalação, atualização ou consulte a [Istio - instalação com o manual do Helm] [ istio-install-helm] para outras opções de instalação.

Este artigo separa as diretrizes de instalação de Istio em várias etapas separadas. Cada um destes passos está descrita para que saiba como instalar Istio e saiba como Istio funciona com o Kubernetes. O resultado final é o mesmo na estrutura da instalação Istio oficial [orientações][istio-install-k8s-quickstart].

## <a name="download-istio"></a>Transferir Istio

Em primeiro lugar, transfira e extraia a versão mais recente do Istio. Os passos são um pouco diferentes para um shell de bash no MacOS, Linux ou subsistema Windows para Linux e para um shell do PowerShell. Escolha um de instalação seguinte passos para o seu ambiente preferido:

* [Bash no MacOS, Linux ou subsistema Windows para Linux](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

No MacOS, utilize `curl` para transferir a versão mais recente do Istio e, em seguida, extrair com `tar` da seguinte forma:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

No Linux ou subsistema Windows para Linux, utilize `curl` para transferir a versão mais recente do Istio e, em seguida, extrair com `tar` da seguinte forma:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

### <a name="powershell"></a>PowerShell

No PowerShell, utilize [Invoke-WebRequest] [ Invoke-WebRequest] para transferir a versão mais recente do Istio e, em seguida, extrair com [Expand-arquivo] [ Expand-Archive] como Segue-se:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.0.4"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

## <a name="install-the-istio-client-binary"></a>Instalar o cliente de Istio binário

O `istioctl` binário de cliente é executado no seu computador cliente e permite-lhe gerir regras de encaminhamento de Istio e políticas. Novamente, os passos de instalação são um pouco diferentes entre sistemas operativos cliente. Escolha um de instalação seguinte passos para o seu ambiente preferido.

> [!IMPORTANT]
> Certifique-se de que execute os passos nesta secção, a partir da pasta de nível superior da versão Istio que transferiu e extraiu.

### <a name="macos"></a>MacOS

Para instalar o Istio `istioctl` binário de cliente numa shell bash com base no MacOS, utilize os seguintes comandos. Copie estes comandos do `istioctl` cliente binário para a localização do programa de usuário padrão no seu `PATH`.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Se gostaria de conclusão da linha de comandos para o Istio `istioctl` cliente binário, em seguida, configurá-lo da seguinte forma:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Agora passar para a secção para [instale os componentes de Istio Kubernetes](#install-the-istio-kubernetes-components).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux ou subsistema Windows para Linux

Utilize os seguintes comandos para instalar o Istio `istioctl` cliente binário numa shell baseada no bash no Linux ou [subsistema Windows para Linux][install-wsl]. Copie estes comandos do `istioctl` cliente binário para a localização do programa de usuário padrão no seu `PATH`.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Se gostaria de conclusão da linha de comandos para o Istio `istioctl` cliente binário, em seguida, configurá-lo da seguinte forma:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Agora passar para a secção para [instale os componentes de Istio Kubernetes](#install-the-istio-kubernetes-components).

### <a name="windows"></a>Windows

Para instalar o Istio `istioctl` binário de cliente numa shell baseada no Powershell no Windows, utilize os seguintes comandos. Copie estes comandos do `istioctl` binário de cliente a um novo utilizador localização do programa e disponibilizá-la por meio de sua `PATH`.

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:/Program Files/Istio"
mv ./bin/istioctl.exe "C:/Program Files/Istio/"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Program Files\Istio\", "User")
```

## <a name="install-the-istio-kubernetes-components"></a>Instalar os componentes de Istio Kubernetes

> [!IMPORTANT]
> Certifique-se de que execute os passos nesta secção, a partir da pasta de nível superior da versão Istio que transferiu e extraiu.

> [!NOTE]
> Versão `1.0.6` e tem mais recente do gráfico Istio Helm quebras de código. Se optar por instalar esta versão, agora terá de criar manualmente um segredo para Kiali. Também terá de criar manualmente um segredo para o Grafana, se tiver definido `grafana.security.enabled=true`. Veja o gráfico do Helm Istio [README.md](https://github.com/istio/istio/tree/master/install/kubernetes/helm/istio#installing-the-chart) para obter mais detalhes sobre como criar estes segredos.

Para instalar os componentes de Istio no seu cluster do AKS, utilize o Helm. Instalar os recursos de Istio no `istio-system` espaço de nomes e ativar as opções adicionais para segurança e monitorização da seguinte forma:

```azurecli
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set grafana.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

O gráfico Helm implementa um grande número de objetos. A implementação pode demorar de 2 a 3 minutos a concluir, consoante o ambiente de cluster.

## <a name="validate-the-installation"></a>Validar a instalação

Para certificar-se de que tem uma implantação bem-sucedida do Istio, vamos validar a instalação.

Confirme que os serviços esperados foram criados. Utilize o [kubectl obter svc] [ kubectl-get] comando para ver os serviços em execução. Consulta a *istio sistema* espaço de nomes, onde os componentes de Istio e suplementos foram instalados, o gráfico Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

O resultado de exemplo seguinte mostra os serviços que devem agora estar a executar:

- *istio -** serviços
- * jaeger-* *, *rastreio*, e *zipkin* suplementos de rastreio
- *Prometheus* o serviço de métricas do suplemento
- *o grafana* análises de suplemento e o serviço de monitoramento do dashboard
- *kiali* suplemento malha dashboard do serviço

Se o `istio-ingressgateway` mostra um ip externo de `<pending>`, aguarde alguns minutos até que um endereço IP tenha sido atribuído pelo sistema de rede do Azure.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                   AGE       SELECTOR
grafana                  ClusterIP      10.0.26.60     <none>           3000/TCP                                                                                                                  3m        app=grafana
istio-citadel            ClusterIP      10.0.88.87     <none>           8060/TCP,9093/TCP                                                                                                         3m        istio=citadel
istio-egressgateway      ClusterIP      10.0.115.115   <none>           80/TCP,443/TCP                                                                                                            3m        app=istio-egressgateway,istio=egressgateway
istio-galley             ClusterIP      10.0.104.183   <none>           443/TCP,9093/TCP                                                                                                          3m        istio=galley
istio-ingressgateway     LoadBalancer   10.0.12.216    52.187.250.239   80:31380/TCP,443:31390/TCP,31400:31400/TCP,15011:30469/TCP,8060:31999/TCP,853:31235/TCP,15030:32000/TCP,15031:30293/TCP   3m        app=istio-ingressgateway,istio=ingressgateway
istio-pilot              ClusterIP      10.0.38.134    <none>           15010/TCP,15011/TCP,8080/TCP,9093/TCP                                                                                     3m        istio=pilot
istio-policy             ClusterIP      10.0.253.81    <none>           9091/TCP,15004/TCP,9093/TCP                                                                                               3m        istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.181.186   <none>           443/TCP                                                                                                                   3m        istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.177.113   <none>           9091/TCP,15004/TCP,9093/TCP,42422/TCP                                                                                     3m        istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                3m        app=jaeger
jaeger-collector         ClusterIP      10.0.112.81    <none>           14267/TCP,14268/TCP                                                                                                       3m        app=jaeger
jaeger-query             ClusterIP      10.0.179.193   <none>           16686/TCP                                                                                                                 3m        app=jaeger
kiali                    ClusterIP      10.0.211.63    <none>           20001/TCP                                                                                                                 3m        app=kiali
prometheus               ClusterIP      10.0.70.113    <none>           9090/TCP                                                                                                                  3m        app=prometheus
tracing                  ClusterIP      10.0.139.121   <none>           80/TCP                                                                                                                    3m        app=jaeger
zipkin                   ClusterIP      10.0.60.155    <none>           9411/TCP                                                                                                                  3m        app=jaeger
```

Em seguida, confirme que os pods necessários foram criados. Utilize o [kubectl obter pods] [ kubectl-get] de comandos e consultar novamente o *istio sistema* espaço de nomes:

```console
kubectl get pods --namespace istio-system
```

O resultado de exemplo seguinte mostra os pods em execução:

- o *istio -** pods
- o *prometheus -** pod de métricas do suplemento
- o *grafana -** pod do dashboard de monitorização e análise de suplemento
- o *kiali* pod do dashboard de malha de serviço de suplemento

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-59b787b9b-cr6d7                  1/1       Running     0          6m
istio-citadel-78df8c67d9-9lfpf           1/1       Running     0          6m
istio-egressgateway-6b96cd7f5-k848h      1/1       Running     0          6m
istio-galley-58f566cb66-8mhbv            1/1       Running     0          6m
istio-ingressgateway-6cbbf596f6-6jz8g    1/1       Running     0          6m
istio-pilot-8449d555fc-sl6kp             2/2       Running     0          6m
istio-policy-6b99d88bc5-55s52            2/2       Running     0          6m
istio-sidecar-injector-b88dfb954-8m86s   1/1       Running     0          6m
istio-telemetry-675cb4cb9d-8s7wd         2/2       Running     0          6m
istio-tracing-7596597bd7-sbnt9           1/1       Running     0          6m
kiali-5fbd6ffb-4qcxw                     1/1       Running     0          6m
prometheus-76db5fddd5-2tkxs              1/1       Running     0          6m
```

Todos os pods mostram o estado do `Running`. Se os seus pods não tem estes Estados terem, aguarde um ou dois minutos, até que eles fazem. Se qualquer pods comunicam um problema, utilize o [kubectl descrevem pod] [ kubectl-describe] comando para rever o seu estado e de saída.

## <a name="accessing-the-add-ons"></a>Acessando os complementos

Um número de suplementos foram instalado Istio em nossa configuração acima que fornecem funcionalidade adicional. As interfaces do usuário para os suplementos não são expostas publicamente por meio de um endereço ip externo. Para acessar as interfaces de usuário do suplemento, utilize o [kubectl porta-forward] [ kubectl-port-forward] comando. Este comando cria uma ligação segura entre uma porta local no computador cliente e o pod relevante no cluster do AKS.

### <a name="grafana"></a>Grafana

A análise e dashboards de monitorização para Istio é fornecida pela [Grafana][grafana]. Reencaminhar a porta local *3000* no seu computador cliente à porta *3000* no pod que está a executar o Grafana no cluster do AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

O resultado de exemplo seguinte mostra o reencaminhamento de porta a ser configurado para o Grafana:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Agora pode acessar o Grafana no seguinte URL no seu computador de cliente - [ http://localhost:3000 ](http://localhost:3000).

### <a name="prometheus"></a>Prometheus

O navegador de expressão para as métricas é fornecido pela [Prometheus][prometheus]. Reencaminhar a porta local *9090* no seu computador cliente à porta *9090* no pod que está a executar o Prometheus no cluster do AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

O resultado de exemplo seguinte mostra o reencaminhamento de porta a ser configurado para Prometheus:

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

Agora pode acessar o navegador de expressão Prometheus no seguinte URL no seu computador de cliente - [ http://localhost:9090 ](http://localhost:9090).

### <a name="jaeger"></a>Jaeger

Rastreio de interface do usuário é fornecida pela [Jaeger][jaeger]. Reencaminhar a porta local *16686* no seu computador cliente à porta *16686* no pod que está a executar o Jaeger no cluster do AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

O resultado de exemplo seguinte mostra o reencaminhamento de porta a ser configurado para Jaeger:

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

Agora pode acessar a interface do usuário de rastreamento Jaeger no seguinte URL no seu computador de cliente - [ http://localhost:16686 ](http://localhost:16686).

### <a name="kiali"></a>Kiali

Um dashboard do serviço malha observability é fornecido pela [Kiali][kiali]. Reencaminhar a porta local *20001* no seu computador cliente à porta *20001* no pod que está a executar o Kiali no cluster do AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

O resultado de exemplo seguinte mostra o reencaminhamento de porta a ser configurado para Kiali:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Agora pode aceder ao dashboard do Kiali serviço malha observability no seguinte URL no seu computador de cliente - [ http://localhost:20001 ](http://localhost:20001).

É o nome de utilizador predefinido e a palavra-passe para o dashboard de Kiali *username:admin / palavra-passe: administrador*. Estas credenciais podem ser configuradas através da *kiali.dashboard.username* e *kiali.dashboard.passphrase* executar Helm valores.

## <a name="next-steps"></a>Passos Seguintes

Para ver como pode usar Istio para fornecer inteligente de encaminhamento entre várias versões da sua aplicação e implementar uma versão canary, consulte a seguinte documentação:

> [!div class="nextstepaction"]
> [Cenário de encaminhamento inteligente Istio do AKS][istio-scenario-routing]

Para explorar mais opções de instalação e configuração para Istio, consulte os seguintes artigos Istio oficiais:

- [Istio - início rápido de instalação do Kubernetes][istio-install-k8s-quickstart]
- [Istio - guia de instalação do Helm][istio-install-helm]
- [Istio - opções de instalação do Helm][istio-install-helm-options]

Também pode seguir os cenários adicionais utilizando o [exemplo de aplicativo de Bookinfo Istio][istio-bookinfo-example].

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-k8s-quickstart]: https://istio.io/docs/setup/kubernetes/quick-start/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/helm-install/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10
[kubernetes-crd]: https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
[Invoke-WebRequest]: /powershell/module/microsoft.powershell.utility/invoke-webrequest
[Expand-Archive]: /powershell/module/Microsoft.PowerShell.Archive/Expand-Archive
