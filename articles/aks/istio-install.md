---
title: Instalar Istio no Azure Kubernetes Service (AKS)
description: Saiba como instalar e utilizar Istio para criar uma malha de serviço num cluster do Azure Kubernetes Service (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 33d86ab8c88b45c7787620773f0df6e7fe888cf3
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65850403"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalar e utilizar Istio no Azure Kubernetes Service (AKS)

[Istio] [ istio-github] é uma malha de serviço do código-fonte aberto que fornece um conjunto de chaves de funcionalidade entre os microsserviços num cluster do Kubernetes. Esses recursos incluem gerenciamento de tráfego, a identidade de serviço e segurança, imposição de políticas e observability. Para obter mais informações sobre Istio, consulte oficial [o que é Istio?] [ istio-docs-concepts] documentação.

Este artigo mostra-lhe como instalar Istio. O Istio `istioctl` binário de cliente é instalada no seu computador cliente e os componentes de Istio estão instalados num cluster de Kubernetes no AKS.

> [!NOTE]
> Estas instruções fazem referência a versão de Istio `1.1.3`.
>
> O Istio `1.1.x` versões foram testadas pela equipe do Istio em relação a versões do Kubernetes `1.11`, `1.12`, `1.13`. Pode encontrar versões de Istio adicionais em [GitHub - Istio libera] [ istio-github-releases] e informações sobre todas as versões no [Istio - notas de versão] [ istio-release-notes].

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Transferir Istio
> * Instalar o cliente de istioctl Istio binário
> * Instalar os CRDs Istio no AKS
> * Instale os componentes de Istio no AKS
> * Validar a instalação de Istio
> * Acessando os complementos
> * Desinstalar Istio do AKS

## <a name="before-you-begin"></a>Antes de começar

Os passos detalhados neste artigo partem do princípio de que criou um cluster do AKS (Kubernetes `1.11` e superior, com RBAC ativado) e estabeleceu uma `kubectl` ligação com o cluster. Se precisar de ajuda com qualquer um desses itens, em seguida, consulte a [início rápido do AKS][aks-quickstart].

Precisará [Helm] [ helm] siga estas instruções e instalar Istio. Recomenda-se que tem a versão `2.12.2` ou, mais tarde corretamente instalado e configurado no seu cluster. Se precisar de ajuda com a instalação Helm, em seguida, consulte a [orientações de instalação do AKS Helm][helm-install]. Todos os pods de Istio também deverá ser programadas para ser executado em nós do Linux.

Este artigo separa as diretrizes de instalação de Istio em várias etapas separadas. O resultado final é o mesmo na estrutura da instalação Istio oficial [orientações][istio-install-helm].

## <a name="download-istio"></a>Transferir Istio

Em primeiro lugar, transfira e extraia a versão mais recente do Istio. Os passos são um pouco diferentes para um shell de bash no MacOS, Linux ou subsistema Windows para Linux e para um shell do PowerShell. Escolha um dos seguintes passos de instalação que corresponde ao seu ambiente preferido:

* [Bash no MacOS, Linux ou subsistema Windows para Linux](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

No MacOS, utilize `curl` para transferir a versão mais recente do Istio e, em seguida, extrair com `tar` da seguinte forma:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

No Linux ou subsistema Windows para Linux, utilize `curl` para transferir a versão mais recente do Istio e, em seguida, extrair com `tar` da seguinte forma:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Agora passar para a secção para [instalar o cliente de istioctl Istio binário](#install-the-istio-istioctl-client-binary).

### <a name="powershell"></a>PowerShell

No PowerShell, utilize `Invoke-WebRequest` para transferir a versão mais recente do Istio e, em seguida, extrair com `Expand-Archive` da seguinte forma:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Agora passar para a secção para [instalar o cliente de istioctl Istio binário](#install-the-istio-istioctl-client-binary).

## <a name="install-the-istio-istioctl-client-binary"></a>Instalar o cliente de istioctl Istio binário

> [!IMPORTANT]
> Certifique-se de que execute os passos nesta secção, a partir da pasta de nível superior da versão Istio que transferiu e extraiu.

O `istioctl` binário de cliente é executado no seu computador cliente e permite que interaja com a malha de serviço Istio. Os passos de instalação são um pouco diferentes entre sistemas operativos cliente. Escolha um dos seguintes passos de instalação que corresponde ao seu ambiente preferido:

* [MacOS](#macos)
* [Linux ou subsistema Windows para Linux](#linux-or-windows-subsystem-for-linux)
* [Windows](#windows)

### <a name="macos"></a>MacOS

Para instalar o Istio `istioctl` binário de cliente numa shell bash com base no MacOS, utilize os seguintes comandos. Copie estes comandos do `istioctl` cliente binário para a localização do programa de usuário padrão no seu `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
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

Agora passar para a secção seguinte para [instalar o CRDs Istio no AKS](#install-the-istio-crds-on-aks).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux ou subsistema Windows para Linux

Utilize os seguintes comandos para instalar o Istio `istioctl` cliente binário numa shell baseada no bash no Linux ou [subsistema Windows para Linux][install-wsl]. Copie estes comandos do `istioctl` cliente binário para a localização do programa de usuário padrão no seu `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
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

Agora passar para a secção seguinte para [instalar o CRDs Istio no AKS](#install-the-istio-crds-on-aks).

### <a name="windows"></a>Windows

Para instalar o Istio `istioctl` cliente binário num **Powershell**-shell com base no Windows, utilize os seguintes comandos. Estes comandos copiar o `istioctl` binário para uma pasta de Istio de cliente e torná-la permanentemente disponível por meio de sua `PATH`. Não tem privilégios elevados de (administrador) para executar esses comandos.

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Istio\", "User")
```

Agora passar para a secção seguinte para [instalar o CRDs Istio no AKS](#install-the-istio-crds-on-aks).

## <a name="install-the-istio-crds-on-aks"></a>Instalar os CRDs Istio no AKS

> [!IMPORTANT]
> Certifique-se de que execute os passos nesta secção, a partir da pasta de nível superior da versão Istio que transferiu e extraiu.

Utiliza Istio [definições de recursos personalizado (CRDs)] [ kubernetes-crd] para gerir a sua configuração de tempo de execução. É necessário instalar o CRDs Istio em primeiro lugar, uma vez que os componentes de Istio têm uma dependência nos mesmos. Utilizar o Helm e o `istio-init` gráfico para instalar o CRDs Istio no `istio-system` espaço de nomes no seu cluster do AKS:

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[Trabalhos] [ kubernetes-jobs] são implementadas como parte do `istio-init` gráfico Helm para instalar os CRDs. Estas tarefas devem ter entre 1 a 2 minutos a concluir, consoante o seu ambiente de cluster. Pode verificar que as tarefas foram concluídas com êxito da seguinte forma:

```azurecli
kubectl get jobs -n istio-system
```

O resultado de exemplo seguinte mostra as tarefas concluídas com êxito.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

Agora que temos confirmado a conclusão com êxito dos trabalhos, vamos ver se de que temos o número correto de CRDs Istio instalado. Pode verificar que todos os 53 CRDs de Istio foram instalados ao executar o comando adequado para o seu ambiente. O comando deverá devolver o número `53`.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

Powershell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

Se tem a este ponto, em seguida, isso significa que instalou com êxito o CRDs Istio. Agora passar para a secção seguinte para [instale os componentes de Istio no AKS](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Instale os componentes de Istio no AKS

> [!IMPORTANT]
> Certifique-se de que execute os passos nesta secção, a partir da pasta de nível superior da versão Istio que transferiu e extraiu.

Podemos estar a instalar [Grafana] [ grafana] e [Kiali] [ kiali] como parte da nossa instalação Istio. O Grafana fornece análises e dashboards de monitorização e Kiali fornece um dashboard de observability de malha do serviço. Em nossa configuração, cada um desses componentes requer credenciais que devem ser fornecidas como um [segredo][kubernetes-secrets].

Antes, pode instalar os componentes de Istio, devemos criar os segredos para o Grafana e Kiali. Crie estes segredos ao executar os comandos apropriados para o seu ambiente.

### <a name="add-grafana-secret"></a>Adicionar o Grafana segredo

Substitua o `REPLACE_WITH_YOUR_SECURE_PASSWORD` token com a palavra-passe e execute os seguintes comandos:

#### <a name="macos-linux"></a>MacOS, Linux

```bash
GRAFANA_USERNAME=$(echo -n "grafana" | base64)
GRAFANA_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$GRAFANA_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("grafana"))
$GRAFANA_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE" | kubectl apply -f -
```

### <a name="add-kiali-secret"></a>Adicionar Kiali segredo

Substitua o `REPLACE_WITH_YOUR_SECURE_PASSWORD` token com a palavra-passe e execute os seguintes comandos:

#### <a name="macos-linux"></a>MacOS, Linux

```bash
KIALI_USERNAME=$(echo -n "kiali" | base64)
KIALI_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$KIALI_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("kiali"))
$KIALI_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE" | kubectl apply -f -
```

### <a name="install-istio-components"></a>Instalar componentes de Istio

Agora que criámos com êxito os segredos da Grafana e Kiali no nosso cluster do AKS, chegou a hora para instalar os componentes de Istio. Utilizar o Helm e o `istio` gráfico de instalar os componentes de Istio no `istio-system` espaço de nomes no seu cluster do AKS. Utilize os comandos apropriados para o seu ambiente.

> [!NOTE]
> Estamos a utilizar as seguintes opções como parte da nossa instalação:
> - `global.controlPlaneSecurityEnabled=true` -TLS mútua ativado para o plano de controlo
> - `mixer.adapters.useAdapterCRDs=false` -remover watches no adaptador de mixer CRDs à medida que será preterido e isso irão melhorar o desempenho
> - `grafana.enabled=true` -Ativar o Grafana a implementação para análises e dashboards de monitorização
> - `grafana.security.enabled=true` -Ativar a autenticação para o Grafana
> - `tracing.enabled=true` -Ativar a implementação de Jaeger para o rastreio
> - `kiali.enabled=true` -Ativar a implementação de Kiali para um dashboard de observability de malha do serviço

Bash

```bash
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set mixer.adapters.useAdapterCRDs=false \
  --set grafana.enabled=true --set grafana.security.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

Powershell

```powershell
helm install install/kubernetes/helm/istio --name istio --namespace istio-system `
  --set global.controlPlaneSecurityEnabled=true `
  --set mixer.adapters.useAdapterCRDs=false `
  --set grafana.enabled=true --set grafana.security.enabled=true `
  --set tracing.enabled=true `
  --set kiali.enabled=true
```

O `istio` gráfico Helm implementa um grande número de objetos. Pode ver a lista da saída do seu `helm install` comando acima. A implementação dos componentes Istio pode demorar 4 a 5 minutos para concluir, dependendo do seu ambiente de cluster.

> [!NOTE]
> Todos os pods de Istio tem de ser agendadas para ser executada em nós do Linux. Se tiver conjuntos de nós do Windows Server, além de conjuntos de nós do Linux no seu cluster, certifique-se de que todos os pods de Istio tiverem sido agendadas para serem executadas em nós do Linux.

Neste momento, implementou Istio ao seu cluster do AKS. Para garantir que temos uma implantação bem-sucedida do Istio, vamos passar para a secção seguinte para [validar a instalação de Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Validar a instalação de Istio

Confirme que os serviços esperados foram criados. Utilize o [kubectl obter svc] [ kubectl-get] comando para ver os serviços em execução. Consulta a `istio-system` espaço de nomes, onde os componentes Istio e suplementos foram instalados pelo `istio` gráfico Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

O resultado de exemplo seguinte mostra os serviços que devem agora estar a executar:

- `istio-*` Serviços
- `jaeger-*`, `tracing`, e `zipkin` suplementos de rastreio
- `prometheus` serviço de métricas do suplemento
- `grafana` análise de suplemento e o serviço de monitoramento do dashboard
- `kiali` serviço de dashboard de malha de serviço de suplemento

Se o `istio-ingressgateway` mostra um ip externo de `<pending>`, aguarde alguns minutos até que um endereço IP tenha sido atribuído pelo sistema de rede do Azure.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                                                                                                                      AGE       SELECTOR
grafana                  ClusterIP      10.0.81.182    <none>          3000/TCP                                                                                                                                     119s      app=grafana
istio-citadel            ClusterIP      10.0.96.33     <none>          8060/TCP,15014/TCP                                                                                                                           119s      istio=citadel
istio-galley             ClusterIP      10.0.237.158   <none>          443/TCP,15014/TCP,9901/TCP                                                                                                                   119s      istio=galley
istio-ingressgateway     LoadBalancer   10.0.154.12    20.188.211.19   15020:30603/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:31198/TCP,15030:30610/TCP,15031:30937/TCP,15032:31344/TCP,15443:31499/TCP   119s      app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.178.56    <none>          15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       119s      istio=pilot
istio-policy             ClusterIP      10.0.116.118   <none>          9091/TCP,15004/TCP,15014/TCP                                                                                                                 119s      istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.31.160    <none>          443/TCP                                                                                                                                      119s      istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.187.246   <none>          9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       119s      istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>          5775/UDP,6831/UDP,6832/UDP                                                                                                                   119s      app=jaeger
jaeger-collector         ClusterIP      10.0.116.63    <none>          14267/TCP,14268/TCP                                                                                                                          119s      app=jaeger
jaeger-query             ClusterIP      10.0.22.108    <none>          16686/TCP                                                                                                                                    119s      app=jaeger
kiali                    ClusterIP      10.0.142.50    <none>          20001/TCP                                                                                                                                    119s      app=kiali
prometheus               ClusterIP      10.0.138.134   <none>          9090/TCP                                                                                                                                     119s      app=prometheus
tracing                  ClusterIP      10.0.165.210   <none>          80/TCP                                                                                                                                       118s      app=jaeger
zipkin                   ClusterIP      10.0.126.211   <none>          9411/TCP                                                                                                                                     118s      app=jaeger
```

Em seguida, confirme que os pods necessários foram criados. Utilize o [kubectl obter pods] [ kubectl-get] de comandos e consultar novamente o `istio-system` espaço de nomes:

```console
kubectl get pods --namespace istio-system
```

O resultado de exemplo seguinte mostra os pods em execução:

- o `istio-*` pods
- o `prometheus-*` pod de métricas do suplemento
- o `grafana-*` pod do dashboard de monitorização e análise de suplemento
- o `kiali` pod do dashboard de malha de serviço de suplemento

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-88779954d-nzpm7                  1/1       Running     0          6m26s
istio-citadel-7f699dc8c8-n7q8g           1/1       Running     0          6m26s
istio-galley-649bc8cd97-wfjzm            1/1       Running     0          6m26s
istio-ingressgateway-65dfbd566-42wkn     1/1       Running     0          6m26s
istio-init-crd-10-tmtw5                  0/1       Completed   0          20m38s
istio-init-crd-11-ql25l                  0/1       Completed   0          20m38s
istio-pilot-958dd8cc4-4ckf9              2/2       Running     0          6m26s
istio-policy-86b4b7cf9-zf7v7             2/2       Running     4          6m26s
istio-sidecar-injector-d48786c5c-pmrj9   1/1       Running     0          6m26s
istio-telemetry-7f6996fdcc-84w94         2/2       Running     3          6m26s
istio-tracing-79db5954f-h7hmz            1/1       Running     0          6m26s
kiali-5c4cdbb869-s28dv                   1/1       Running     0          6m26s
prometheus-67599bf55b-pgxd8              1/1       Running     0          6m26s
```

Deve haver dois `istio-init-crd-*` pods com um `Completed` estado. Estes pods foram responsáveis por executar as tarefas que criou os CRDs num passo anterior. Todos os outros pods devem mostrar estado `Running`. Se os seus pods não tem estes Estados terem, aguarde um ou dois minutos, até que eles fazem. Se qualquer pods comunicam um problema, utilize o [kubectl descrevem pod] [ kubectl-describe] comando para rever o seu estado e de saída.

## <a name="accessing-the-add-ons"></a>Acessando os complementos

Um número de suplementos foram instalado Istio em nossa configuração acima que fornecem funcionalidade adicional. As interfaces do usuário para os suplementos não são expostas publicamente por meio de um endereço ip externo. Para acessar as interfaces de usuário do suplemento, utilize o [kubectl porta-forward] [ kubectl-port-forward] comando. Este comando cria uma ligação segura entre o seu computador cliente e o pod relevante no cluster do AKS.

Adicionamos uma camada adicional de segurança para o Grafana e Kiali ao especificar credenciais para as mesmas anteriormente no artigo.

### <a name="grafana"></a>Grafana

A análise e dashboards de monitorização para Istio são fornecidos pela [Grafana][grafana]. Reencaminhar a porta local `3000` no seu computador cliente à porta `3000` no pod que está a executar o Grafana no cluster do AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

O resultado de exemplo seguinte mostra o reencaminhamento de porta a ser configurado para o Grafana:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Agora pode acessar o Grafana no seguinte URL no seu computador de cliente - [ http://localhost:3000 ](http://localhost:3000). Não se esqueça de utilizar as credenciais que criou através do Grafana secreta anteriormente quando lhe for pedido.

### <a name="prometheus"></a>Prometheus

As métricas para Istio são fornecidas pela [Prometheus][prometheus]. Reencaminhar a porta local `9090` no seu computador cliente à porta `9090` no pod que está a executar o Prometheus no cluster do AKS:

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

Rastreio dentro Istio é fornecido pela [Jaeger][jaeger]. Reencaminhar a porta local `16686` no seu computador cliente à porta `16686` no pod que está a executar o Jaeger no cluster do AKS:

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

Um dashboard do serviço malha observability é fornecido pela [Kiali][kiali]. Reencaminhar a porta local `20001` no seu computador cliente à porta `20001` no pod que está a executar o Kiali no cluster do AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

O resultado de exemplo seguinte mostra o reencaminhamento de porta a ser configurado para Kiali:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Agora pode aceder ao dashboard do Kiali serviço malha observability no seguinte URL no seu computador de cliente - [ http://localhost:20001/kiali/console/ ](http://localhost:20001/kiali/console/). Não se esqueça de utilizar as credenciais que criou através do Kiali secreta anteriormente quando lhe for pedido.

## <a name="uninstall-istio-from-aks"></a>Desinstalar Istio do AKS

> [!WARNING]
> A eliminar Istio de um sistema em execução pode resultar em tráfego relacionado com problemas entre seus serviços. Certifique-se de que efetuou provisões para que seu sistema continuará a funcionar corretamente sem Istio antes de continuar.

### <a name="remove-istio-components-and-namespace"></a>Remover componentes do Istio e espaço de nomes

Para remover Istio do seu cluster do AKS, utilize os seguintes comandos. O `helm delete` comandos removerá o `istio` e `istio-init` gráficos e o `kubectl delete ns` comando irá remover o `istio-system` espaço de nomes.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Remover Istio CRDs

Os comandos acima eliminar todos os componentes de Istio e espaço de nomes, mas ainda Continuamos com o CRDs Istio. Para eliminar os CRDs, pode utilizar um seguintes abordagens.

Abordagem #1 - Este comando assume que está a executar este passo da pasta de nível superior da versão transferida e extraída do Istio que utilizou para instalar Istio com.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Abordagem #2 – utilize um dos seguintes comandos, se já não tiver acesso à versão transferido e extraído do Istio que utilizou para instalar Istio com. Este comando irá demorar um pouco mais - esperava demorar alguns minutos a concluir.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

Powershell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>Passos Seguintes

A seguinte documentação descreve como pode utilizar Istio para fornecer encaminhamento inteligente para implementar uma versão canary:

> [!div class="nextstepaction"]
> [Cenário de encaminhamento inteligente Istio do AKS][istio-scenario-routing]

Para explorar mais opções de instalação e configuração para Istio, consulte os seguintes artigos Istio oficiais:

- [Istio - guia de instalação do Helm][istio-install-helm]
- [Istio - opções de instalação do Helm][istio-install-helm-options]

Também pode seguir os cenários adicionais utilizando o [exemplo de aplicativo de Bookinfo Istio][istio-bookinfo-example].

Para saber como monitorizar a sua aplicação de AKS com o Application Insights e Istio, consulte a seguinte documentação do Azure Monitor:
- [Sem monitorização de aplicações de instrumentação para Kubernetes alojado aplicativos][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/about/notes/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md