---
title: Instalar o İSTİO no serviço kubernetes do Azure (AKS)
description: Saiba como instalar e usar o İSTİO para criar uma malha de serviço em um cluster do AKS (serviço kubernetes do Azure)
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 245ac3b1fd88b8d2430e9ddefef3562efd16e6d1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885388"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalar e usar o İSTİO no serviço kubernetes do Azure (AKS)

O [İSTİO][istio-github] é uma malha de serviço de software livre que fornece um conjunto de chaves de funcionalidade em todos os microserviços em um cluster kubernetes. Esses recursos incluem gerenciamento de tráfego, identidade de serviço e segurança, imposição de política e observação. Para obter mais informações sobre İSTİO, consulte a documentação oficial [o que é o İSTİO?][istio-docs-concepts] .

Este artigo mostra como instalar o İSTİO. O binário do İSTİO `istioctl` Client é instalado no computador cliente e os componentes do İSTİO são instalados em um cluster kubernetes no AKS.

> [!NOTE]
> Estas instruções fazem referência à versão do İSTİO `1.3.2`e usam pelo menos a versão do Helm `2.14.2`.
>
> As versões de `1.3.x` do İSTİO foram testadas pela equipe do İSTİO em relação às versões do kubernetes `1.13`, `1.14``1.15`. Você pode encontrar versões adicionais do İSTİO em [versões do GitHub-İSTİO][istio-github-releases], informações sobre cada uma das versões em [İSTİO News][istio-release-notes] e versões de kubernetes com suporte em [perguntas frequentes][istio-faq]sobre o İSTİO.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Baixar e instalar o binário do cliente İSTİO istioctl
> * Instalar o İSTİO no AKS
> * Validar a instalação do İSTİO
> * Acessar os Complementos
> * Desinstalar o İSTİO do AKS

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste artigo pressupõem que você criou um cluster AKS (kubernetes `1.13` e superior, com o RBAC habilitado) e estabeleceu uma conexão de `kubectl` com o cluster. Se precisar de ajuda com qualquer um desses itens, consulte o guia de [início rápido do AKS][aks-quickstart].

Você precisará de [Helm][helm] para seguir estas instruções e instalar o İSTİO. É recomendável que você tenha a versão estável mais recente corretamente instalada e configurada em seu cluster. Se precisar de ajuda com a instalação do Helm, consulte as [diretrizes de instalação do AKS Helm][helm-install]. Todos os pods de İSTİO também devem ser agendados para serem executados em nós do Linux.

Verifique se você leu a documentação de [desempenho e escalabilidade do İSTİO](https://istio.io/docs/concepts/performance-and-scalability/) para entender os requisitos de recursos adicionais para executar o İSTİO em seu cluster AKs. Os requisitos de núcleo e memória variam de acordo com sua carga de trabalho específica. Escolha um número apropriado de nós e o tamanho da VM para atender à sua configuração.

Este artigo separa as diretrizes de instalação do İSTİO em várias etapas discretas. O resultado final é o mesmo na estrutura que as [diretrizes][istio-install-helm]de instalação oficial do İSTİO.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="add-the-istio-helm-chart-repository"></a>Adicionar o repositório de gráfico İSTİO Helm

Adicione o repositório de gráficos İSTİO Helm para a versão İSTİO. Certifique-se de executar o `helm repo update` para atualizar suas informações locais para o repositório de gráficos.

```azurecli
helm repo add istio.io https://storage.googleapis.com/istio-release/releases/$ISTIO_VERSION/charts/
helm repo update
```

## <a name="install-the-istio-crds-on-aks"></a>Instalar o İSTİO CRDs no AKS

O İSTİO usa [definições de recursos personalizados (crds)][kubernetes-crd] para gerenciar sua configuração de tempo de execução. Precisamos instalar o İSTİO CRDs primeiro, já que os componentes do İSTİO têm uma dependência neles. Use Helm e o gráfico de `istio-init` para instalar o İSTİO CRDs no namespace `istio-system` no cluster AKS:

```azurecli
helm install istio.io/istio-init --name istio-init --namespace istio-system
```

Os [trabalhos][kubernetes-jobs] são implantados como parte do gráfico de `istio-init` Helm para instalar o crds. Esses trabalhos devem levar menos de 20s para serem concluídos, dependendo do seu ambiente de cluster. Você pode verificar se os trabalhos foram concluídos com êxito da seguinte maneira:

```azurecli
kubectl get jobs -n istio-system
```

A saída de exemplo a seguir mostra os trabalhos concluídos com êxito.

```console
NAME                      COMPLETIONS   DURATION   AGE
istio-init-crd-10-1.3.2   1/1           14s        14s
istio-init-crd-11-1.3.2   1/1           12s        14s
istio-init-crd-12-1.3.2   1/1           14s        14s
```

Agora que confirmamos a conclusão bem-sucedida dos trabalhos, vamos verificar se temos o número correto de İSTİO CRDs instalado. Você pode verificar se todos os 23 İSTİO CRDs foram instalados executando o comando a seguir. O comando deve retornar o número `23`.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - check CRD count](includes/servicemesh/istio/install-check-crd-count-powershell.md)]

::: zone-end

Se você tiver até este ponto, isso significa que você instalou com êxito o İSTİO CRDs. Agora, vá para a próxima seção para [instalar os componentes do İSTİO no AKs](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Instalar os componentes do İSTİO no AKS

Instalaremos o [Grafana][grafana] e o [Kiali][kiali] como parte da nossa instalação do İSTİO. O Grafana fornece painéis de análise e monitoramento e o Kiali fornece um painel de observação de malha de serviço. Em nossa configuração, cada um desses componentes requer credenciais que devem ser fornecidas como um [segredo][kubernetes-secrets].

Antes que possamos instalar os componentes do İSTİO, devemos criar os segredos para Grafana e Kiali. 

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

Agora que criamos com êxito os segredos Grafana e Kiali em nosso cluster AKS, é hora de instalar os componentes do İSTİO. Use Helm e o gráfico de `istio` para instalar os componentes do İSTİO no namespace `istio-system` em seu cluster AKS. 

> [!NOTE]
> **Opções de instalação**
> 
> Estamos usando as seguintes opções como parte da nossa instalação:
> - `global.controlPlaneSecurityEnabled=true`-TLS mútuo habilitado para o plano de controle
> - `global.mtls.enabled=true`-exigir que toda a comunicação de serviço para o serviço tenha MTLS
> - `grafana.enabled=true`-habilitar a implantação Grafana para painéis de análise e monitoramento
> - `grafana.security.enabled=true`-habilitar a autenticação para Grafana
> - `tracing.enabled=true`-habilitar a implantação do Jaeger para rastreamento
> - `kiali.enabled=true`-habilitar a implantação do Kiali para um painel de observação de malha de serviço
>
> **Seletores de nó**
>
> No momento, o İSTİO deve ser agendado para ser executado em nós do Linux. Se você tiver nós do Windows Server em seu cluster, deverá garantir que os pods İSTİO sejam agendados apenas para execução em nós do Linux. Usaremos [seletores de nó][kubernetes-node-selectors] para garantir que os pods estejam agendados para os nós corretos.

> [!CAUTION]
> Os recursos [SDS (serviço de descoberta de segredo)][istio-feature-sds] e [İSTİO CNI][istio-feature-cni] İSTİO estão atualmente em [Alpha][istio-feature-stages], portanto, deve-se considerar antes de habilitá-los. Além disso, o recurso de kubernetes de [projeção do volume de token da conta de serviço][kubernetes-feature-sa-projected-volume] (um requisito para o SDS) não está habilitado nas versões atuais do AKS.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/istio/install-components-powershell.md)]

::: zone-end

O `istio` gráfico Helm implanta um grande número de objetos. Você pode ver a lista da saída do comando `helm install` acima. A implantação dos componentes İSTİO deve levar menos de 2 minutos para ser concluída, dependendo do seu ambiente de cluster.

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
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.164.244   <none>           3000/TCP                                                                                                                                     53s   app=grafana
istio-citadel            ClusterIP      10.0.49.16     <none>           8060/TCP,15014/TCP                                                                                                                           53s   istio=citadel
istio-galley             ClusterIP      10.0.175.173   <none>           443/TCP,15014/TCP,9901/TCP                                                                                                                   53s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.226.151   20.188.221.111   15020:31128/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:30817/TCP,15030:30436/TCP,15031:32485/TCP,15032:30980/TCP,15443:30124/TCP   53s   app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.102.158   <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       53s   istio=pilot
istio-policy             ClusterIP      10.0.234.53    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                                 53s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.216.8     <none>           443/TCP,15014/TCP                                                                                                                            53s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.154.215   <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       53s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                                   52s   app=jaeger
jaeger-collector         ClusterIP      10.0.26.109    <none>           14267/TCP,14268/TCP                                                                                                                          52s   app=jaeger
jaeger-query             ClusterIP      10.0.70.55     <none>           16686/TCP                                                                                                                                    52s   app=jaeger
kiali                    ClusterIP      10.0.36.206    <none>           20001/TCP                                                                                                                                    53s   app=kiali
prometheus               ClusterIP      10.0.236.99    <none>           9090/TCP                                                                                                                                     53s   app=prometheus
tracing                  ClusterIP      10.0.83.152    <none>           80/TCP                                                                                                                                       52s   app=jaeger
zipkin                   ClusterIP      10.0.25.86     <none>           9411/TCP                                                                                                                                     52s   app=jaeger
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
NAME                                     READY   STATUS      RESTARTS   AGE
grafana-7c48555456-msl7b                 1/1     Running     0          88s
istio-citadel-566fc66db7-m8wgl           1/1     Running     0          87s
istio-galley-5746db8d56-pl5gg            1/1     Running     0          88s
istio-ingressgateway-6c94f7c9bf-f5lt5    1/1     Running     0          88s
istio-init-crd-10-1.3.2-xw9g2            0/1     Completed   0          92m
istio-init-crd-11-1.3.2-54rz8            0/1     Completed   0          92m
istio-init-crd-12-1.3.2-789qj            0/1     Completed   0          92m
istio-pilot-6748968b6d-rvdfx             2/2     Running     0          87s
istio-policy-7576bbbcf7-2stft            2/2     Running     0          87s
istio-sidecar-injector-76d79d494-7jk9n   1/1     Running     0          87s
istio-telemetry-74b7bf676d-tfrcl         2/2     Running     0          88s
istio-tracing-655d9588bc-d2htg           1/1     Running     0          86s
kiali-65d55bcfb8-tqrfk                   1/1     Running     0          88s
prometheus-846f9849bd-br8kp              1/1     Running     0          87s
```

Deve haver três `istio-init-crd-*` pods com um status de `Completed`. Esses pods eram responsáveis por executar os trabalhos que criaram o CRDs em uma etapa anterior. Todos os outros pods devem mostrar um status de `Running`. Se os pods não tiverem esses status, aguarde um ou dois minutos até que eles façam. Se qualquer pods relatar um problema, use o comando [kubectl para descrever o Pod][kubectl-describe] para revisar sua saída e status.

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

Para remover o İSTİO do cluster do AKS, use os comandos a seguir. Os comandos `helm delete` removerão os gráficos `istio` e `istio-init`, e o comando `kubectl delete namespace` removerá o namespace `istio-system`.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete namespace istio-system
```

### <a name="remove-istio-crds-and-secrets"></a>Remover İSTİO CRDs e segredos

Os comandos acima excluem todos os componentes e o namespace İSTİO, mas ainda estamos com o İSTİO CRDs e os segredos. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Passos seguintes

A documentação a seguir descreve como você pode usar o İSTİO para fornecer roteamento inteligente para distribuir uma versão do canário:

> [!div class="nextstepaction"]
> [Cenário de roteamento inteligente do AKS İSTİO][istio-scenario-routing]

Para explorar mais opções de instalação e configuração para o İSTİO, consulte os seguintes artigos oficiais do İSTİO:

- [Guia de instalação do İSTİO-Helm][istio-install-helm]
- [Opções de instalação do İSTİO-Helm][istio-install-helm-options]

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
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
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
[helm-install]: ./kubernetes-helm.md
