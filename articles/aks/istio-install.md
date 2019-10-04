---
title: Instalar o İSTİO no serviço kubernetes do Azure (AKS)
description: Saiba como instalar e usar o İSTİO para criar uma malha de serviço em um cluster do AKS (serviço kubernetes do Azure)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 9344d2832c37c34d5690dc8f3aae7394ca644276
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827330"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalar e usar o İSTİO no serviço kubernetes do Azure (AKS)

O [İSTİO][istio-github] é uma malha de serviço de software livre que fornece um conjunto de chaves de funcionalidade em todos os microserviços em um cluster kubernetes. Esses recursos incluem gerenciamento de tráfego, identidade de serviço e segurança, imposição de política e observação. Para obter mais informações sobre İSTİO, consulte a documentação oficial [o que é o İSTİO?][istio-docs-concepts] .

Este artigo mostra como instalar o İSTİO. O binário do cliente İSTİO `istioctl` é instalado no computador cliente e os componentes do İSTİO são instalados em um cluster kubernetes no AKS.

> [!NOTE]
> Estas instruções fazem referência à versão do İSTİO `1.1.3`.
>
> As versões do İSTİO `1.1.x` foram testadas pela equipe do İSTİO em relação às versões do kubernetes `1.11`, `1.12`, `1.13`. Você pode encontrar versões adicionais do İSTİO em [versões do GitHub-İSTİO][istio-github-releases] e informações sobre cada uma das versões em [İSTİO News][istio-release-notes].

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Baixar o İSTİO
> * Instalar o binário do cliente İSTİO istioctl
> * Instalar o İSTİO CRDs no AKS
> * Instalar os componentes do İSTİO no AKS
> * Validar a instalação do İSTİO
> * Acessando os Complementos
> * Desinstalar o İSTİO do AKS

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste artigo pressupõem que você criou um cluster AKS (kubernetes `1.11` e acima, com o RBAC habilitado) e estabeleceu uma conexão de `kubectl` com o cluster. Se precisar de ajuda com qualquer um desses itens, consulte o guia de [início rápido do AKS][aks-quickstart].

Você precisará de [Helm][helm] para seguir estas instruções e instalar o İSTİO. É recomendável que você tenha a versão `2.12.2` ou posterior instalada e configurada corretamente no cluster. Se precisar de ajuda com a instalação do Helm, consulte as [diretrizes de instalação do AKS Helm][helm-install]. Todos os pods de İSTİO também devem ser agendados para serem executados em nós do Linux.

Verifique se você leu a documentação de [desempenho e escalabilidade do İSTİO](https://istio.io/docs/concepts/performance-and-scalability/) para entender os requisitos de recursos adicionais para executar o İSTİO em seu cluster AKs. Os requisitos de núcleo e memória variam de acordo com sua carga de trabalho específica. Escolha um número apropriado de nós e o tamanho da VM para atender à sua configuração.

Este artigo separa as diretrizes de instalação do İSTİO em várias etapas discretas. O resultado final é o mesmo na estrutura que as [diretrizes][istio-install-helm]de instalação oficial do İSTİO.

## <a name="download-istio"></a>Baixar o İSTİO

Primeiro, baixe e extraia a versão mais recente do İSTİO. As etapas são um pouco diferentes para um shell bash no MacOS, Linux ou no subsistema Windows para Linux e para um shell do PowerShell. Escolha uma das seguintes etapas de instalação que corresponde ao seu ambiente preferido:

* [Bash no MacOS, Linux ou subsistema Windows para Linux](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

No MacOS, use `curl` para baixar a versão mais recente do İSTİO e, em seguida, extrair com `tar` da seguinte maneira:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

No subsistema Linux ou Windows para Linux, use `curl` para baixar a versão mais recente do İSTİO e, em seguida, extrair com `tar` da seguinte maneira:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Agora, vá para a seção para [instalar o binário do cliente İSTİO istioctl](#install-the-istio-istioctl-client-binary).

### <a name="powershell"></a>PowerShell

No PowerShell, use `Invoke-WebRequest` para baixar a versão mais recente do İSTİO e, em seguida, extrair com `Expand-Archive` da seguinte maneira:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
# Use TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Agora, vá para a seção para [instalar o binário do cliente İSTİO istioctl](#install-the-istio-istioctl-client-binary).

## <a name="install-the-istio-istioctl-client-binary"></a>Instalar o binário do cliente İSTİO istioctl

> [!IMPORTANT]
> Certifique-se de executar as etapas nesta seção, da pasta de nível superior da versão İSTİO que você baixou e extraiu.

O binário do cliente `istioctl` é executado no computador cliente e permite que você interaja com a malha do serviço İSTİO. As etapas de instalação são um pouco diferentes entre os sistemas operacionais cliente. Escolha uma das seguintes etapas de instalação que corresponde ao seu ambiente preferido:

* [MacOS](#macos)
* [Subsistema Linux ou Windows para Linux](#linux-or-windows-subsystem-for-linux)
* [Windows](#windows)

### <a name="macos"></a>MacOS

Para instalar o binário do cliente İSTİO `istioctl` em um shell baseado em bash no MacOS, use os comandos a seguir. Esses comandos copiam o binário do cliente `istioctl` para o local do programa de usuário padrão em seu `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Se você quiser a conclusão da linha de comando para o binário do cliente İSTİO `istioctl`, configure-o da seguinte maneira:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Agora, vá para a próxima seção para [instalar o İSTİO crds no AKs](#install-the-istio-crds-on-aks).

### <a name="linux-or-windows-subsystem-for-linux"></a>Subsistema Linux ou Windows para Linux

Use os comandos a seguir para instalar o binário do cliente İSTİO `istioctl` em um shell baseado em bash no [subsistema Linux ou Windows para Linux][install-wsl]. Esses comandos copiam o binário do cliente `istioctl` para o local do programa de usuário padrão em seu `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Se você quiser a conclusão da linha de comando para o binário do cliente İSTİO `istioctl`, configure-o da seguinte maneira:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Agora, vá para a próxima seção para [instalar o İSTİO crds no AKs](#install-the-istio-crds-on-aks).

### <a name="windows"></a>Windows

Para instalar o binário do cliente İSTİO `istioctl` em um shell baseado no **PowerShell**no Windows, use os comandos a seguir. Esses comandos copiam o binário do cliente `istioctl` para uma pasta İSTİO e, em seguida, o disponibilizam imediatamente (no shell atual) e permanentemente (através de reinicializações do Shell) por meio de seu `PATH`. Você não precisa de privilégios elevados (administrador) para executar esses comandos e não precisa reiniciar o Shell.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```

Agora, vá para a próxima seção para [instalar o İSTİO crds no AKs](#install-the-istio-crds-on-aks).

## <a name="install-the-istio-crds-on-aks"></a>Instalar o İSTİO CRDs no AKS

> [!IMPORTANT]
> Certifique-se de executar as etapas nesta seção, da pasta de nível superior da versão İSTİO que você baixou e extraiu.

O İSTİO usa [definições de recursos personalizados (crds)][kubernetes-crd] para gerenciar sua configuração de tempo de execução. Precisamos instalar o İSTİO CRDs primeiro, já que os componentes do İSTİO têm uma dependência neles. Use Helm e o gráfico `istio-init` para instalar o İSTİO CRDs no namespace `istio-system` no cluster AKS:

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

Os [trabalhos][kubernetes-jobs] são implantados como parte do gráfico `istio-init` Helm para instalar o crds. Esses trabalhos devem levar de 1 a 2 minutos para serem concluídos, dependendo do seu ambiente de cluster. Você pode verificar se os trabalhos foram concluídos com êxito da seguinte maneira:

```azurecli
kubectl get jobs -n istio-system
```

A saída de exemplo a seguir mostra os trabalhos concluídos com êxito.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

Agora que confirmamos a conclusão bem-sucedida dos trabalhos, vamos verificar se temos o número correto de İSTİO CRDs instalado. Você pode verificar se todos os 53 İSTİO CRDs foram instalados executando o comando apropriado para seu ambiente. O comando deve retornar o número `53`.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

Powershell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

Se você tiver até este ponto, isso significa que você instalou com êxito o İSTİO CRDs. Agora, vá para a próxima seção para [instalar os componentes do İSTİO no AKs](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Instalar os componentes do İSTİO no AKS

> [!IMPORTANT]
> Certifique-se de executar as etapas nesta seção, da pasta de nível superior da versão İSTİO que você baixou e extraiu.

Instalaremos o [Grafana][grafana] e o [Kiali][kiali] como parte da nossa instalação do İSTİO. O Grafana fornece painéis de análise e monitoramento e o Kiali fornece um painel de observação de malha de serviço. Em nossa configuração, cada um desses componentes requer credenciais que devem ser fornecidas como um [segredo][kubernetes-secrets].

Antes que possamos instalar os componentes do İSTİO, devemos criar os segredos para Grafana e Kiali. Crie esses segredos executando os comandos apropriados para o seu ambiente.

### <a name="add-grafana-secret"></a>Adicionar segredo Grafana

Substitua o token `REPLACE_WITH_YOUR_SECURE_PASSWORD` pela sua senha e execute os seguintes comandos:

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

### <a name="add-kiali-secret"></a>Adicionar segredo Kiali

Substitua o token `REPLACE_WITH_YOUR_SECURE_PASSWORD` pela sua senha e execute os seguintes comandos:

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

### <a name="install-istio-components"></a>Instalar componentes do İSTİO

Agora que criamos com êxito os segredos Grafana e Kiali em nosso cluster AKS, é hora de instalar os componentes do İSTİO. Use Helm e o gráfico `istio` para instalar os componentes do İSTİO no namespace `istio-system` em seu cluster AKS. Use os comandos apropriados para o seu ambiente.

> [!NOTE]
> Estamos usando as seguintes opções como parte da nossa instalação:
> - `global.controlPlaneSecurityEnabled=true`-TLS mútuo habilitado para o plano de controle
> - `mixer.adapters.useAdapterCRDs=false`-remover as inspeções de CRDs do adaptador do mixer que serão preteridas e isso melhorará o desempenho
> - `grafana.enabled=true`-habilitar a implantação do Grafana para os painéis de análise e monitoramento
> - `grafana.security.enabled=true`-habilitar autenticação para Grafana
> - `tracing.enabled=true`-habilitar a implantação do Jaeger para rastreamento
> - `kiali.enabled=true`-habilitar a implantação do Kiali para um painel de observação de malha de serviço

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

O gráfico `istio` Helm implanta um grande número de objetos. Você pode ver a lista da saída do comando `helm install` acima. A implantação dos componentes do İSTİO pode levar de 4 a 5 minutos para ser concluída, dependendo do seu ambiente de cluster.

> [!NOTE]
> Todos os pods İSTİO devem ser agendados para execução em nós do Linux. Se você tiver pools de nós do Windows Server além dos pools de nós do Linux em seu cluster, verifique se todos os pods İSTİO foram agendados para execução em nós do Linux.

Neste ponto, você implantou o İSTİO em seu cluster AKS. Para garantir que tenhamos uma implantação bem-sucedida do İSTİO, vamos passar para a próxima seção para [validar a instalação do İSTİO](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Validar a instalação do İSTİO

Primeiro, confirme que os serviços esperados foram criados. Use o comando [kubectl Get svc][kubectl-get] para exibir os serviços em execução. Consulte o namespace `istio-system`, no qual os componentes İSTİO e complemento foram instalados pelo gráfico de `istio` Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

A saída de exemplo a seguir mostra os serviços que agora devem estar em execução:

- serviços `istio-*`
- serviços de rastreamento de Complementos `jaeger-*`, `tracing` e `zipkin`
- serviço de métricas de complemento `prometheus`
- serviço de painel de análise e monitoramento do `grafana`
- serviço de painel de malha do serviço de complemento `kiali`

Se o `istio-ingressgateway` mostrar um IP externo de `<pending>`, aguarde alguns minutos até que um endereço IP tenha sido atribuído pela rede do Azure.

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

Em seguida, confirme se os pods necessários foram criados. Use o comando [kubectl Get pods][kubectl-get] e consulte novamente o namespace `istio-system`:

```console
kubectl get pods --namespace istio-system
```

A saída de exemplo a seguir mostra os pods que estão em execução:

- o pods `istio-*`
- o pod de métricas de complemento `prometheus-*`
- o Pod do painel de análise e monitoramento do `grafana-*`
- o Pod do painel de malha do serviço de complemento `kiali`

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

Deve haver dois pods `istio-init-crd-*` com status de `Completed`. Esses pods eram responsáveis por executar os trabalhos que criaram o CRDs em uma etapa anterior. Todos os outros pods devem mostrar um status de `Running`. Se os pods não tiverem esses status, aguarde um ou dois minutos até que eles façam. Se qualquer pods relatar um problema, use o comando [kubectl para descrever o Pod][kubectl-describe] para revisar sua saída e status.

## <a name="accessing-the-add-ons"></a>Acessando os Complementos

Um número de Complementos foi instalado İSTİO em nossa configuração acima que fornece funcionalidade adicional. As interfaces de usuário para os complementos não são expostas publicamente por meio de um endereço IP externo. Para acessar as interfaces de usuário do complemento, use o comando [kubectl Port-Forward][kubectl-port-forward] . Esse comando cria uma conexão segura entre o computador cliente e o Pod relevante no cluster AKS.

Adicionamos uma camada adicional de segurança para Grafana e Kiali especificando as credenciais para elas anteriormente neste artigo.

### <a name="grafana"></a>Grafana

Os painéis de análise e monitoramento para İSTİO são fornecidos pelo [Grafana][grafana]. Encaminhe a porta local `3000` no computador cliente para a porta `3000` no pod que está executando Grafana em seu cluster AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

A saída de exemplo a seguir mostra o encaminhamento de porta que está sendo configurado para Grafana:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Agora você pode acessar o Grafana na URL a seguir no computador cliente- [http://localhost:3000](http://localhost:3000). Lembre-se de usar as credenciais criadas por meio do segredo Grafana anteriormente quando solicitado.

### <a name="prometheus"></a>Prometheus

As métricas para İSTİO são fornecidas pelo [Prometheus][prometheus]. Encaminhe a porta local `9090` no computador cliente para a porta `9090` no pod que está executando Prometheus em seu cluster AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

A saída de exemplo a seguir mostra o encaminhamento de porta que está sendo configurado para Prometheus:

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

Agora você pode acessar o navegador de expressões Prometheus na URL a seguir no computador cliente- [http://localhost:9090](http://localhost:9090).

### <a name="jaeger"></a>Jaeger

O rastreamento dentro de İSTİO é fornecido pelo [Jaeger][jaeger]. Encaminhe a porta local `16686` no computador cliente para a porta `16686` no pod que está executando Jaeger em seu cluster AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

A saída de exemplo a seguir mostra o encaminhamento de porta que está sendo configurado para Jaeger:

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

Agora você pode acessar a interface do usuário de rastreamento do Jaeger na URL a seguir no computador cliente- [http://localhost:16686](http://localhost:16686).

### <a name="kiali"></a>Kiali

Um painel de observação de malha de serviço é fornecido pelo [Kiali][kiali]. Encaminhe a porta local `20001` no computador cliente para a porta `20001` no pod que está executando Kiali em seu cluster AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

A saída de exemplo a seguir mostra o encaminhamento de porta que está sendo configurado para Kiali:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Agora você pode acessar o painel de observação de malha de serviço do Kiali na seguinte URL no computador cliente- [http://localhost:20001/kiali/console/](http://localhost:20001/kiali/console/). Lembre-se de usar as credenciais criadas por meio do segredo Kiali anteriormente quando solicitado.

## <a name="uninstall-istio-from-aks"></a>Desinstalar o İSTİO do AKS

> [!WARNING]
> Excluir İSTİO de um sistema em execução pode resultar em problemas relacionados ao tráfego entre seus serviços. Verifique se você fez provisões para que o seu sistema ainda opere corretamente sem İSTİO antes de continuar.

### <a name="remove-istio-components-and-namespace"></a>Remover componentes e namespace do İSTİO

Para remover o İSTİO do cluster do AKS, use os comandos a seguir. Os comandos `helm delete` removerão os gráficos `istio` e `istio-init`, e o comando `kubectl delete ns` removerá o namespace `istio-system`.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Remover İSTİO CRDs

Os comandos acima excluem todos os componentes e o namespace İSTİO, mas ainda continuamos com o İSTİO CRDs. Para excluir o CRDs, você pode usar uma das abordagens a seguir.

#1 de abordagem-esse comando pressupõe que você está executando essa etapa da pasta de nível superior da versão baixada e extraída do İSTİO que você usou para instalar o İSTİO com.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Abordagem #2-Use um desses comandos se você não tiver mais acesso à versão baixada e extraída do İSTİO que você usou para instalar o İSTİO com. Esse comando levará um pouco mais de tempo que levará alguns minutos para ser concluído.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

Powershell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>Passos seguintes

A documentação a seguir descreve como você pode usar o İSTİO para fornecer roteamento inteligente para distribuir uma versão do canário:

> [!div class="nextstepaction"]
> [Cenário de roteamento inteligente do AKS İSTİO][istio-scenario-routing]

Para explorar mais opções de instalação e configuração para o İSTİO, consulte os seguintes artigos oficiais do İSTİO:

- [Guia de instalação do İSTİO-Helm][istio-install-helm]
- [Opções de instalação do İSTİO-Helm][istio-install-helm-options]

Você também pode seguir cenários adicionais usando o [exemplo de aplicativo İSTİO BookInfo][istio-bookinfo-example].

Para saber como monitorar seu aplicativo AKS usando o Application Insights e o İSTİO, consulte a seguinte documentação de Azure Monitor:
- [Monitoramento de aplicativo de instrumentação zero para aplicativos hospedados kubernetes][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
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
