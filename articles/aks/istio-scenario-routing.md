---
title: Roteamento inteligente e versões de canário com İSTİO no serviço kubernetes do Azure (AKS)
description: Saiba como usar o İSTİO para fornecer roteamento inteligente e implantar versões do canário em um cluster do AKS (serviço do kubernetes do Azure)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 7baa2adbd615a449c73e70e1b96524fc1e18b25d
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000165"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Usar o roteamento inteligente e as versões de canário com İSTİO no serviço kubernetes do Azure (AKS)

O [İSTİO][istio-github] é uma malha de serviço de software livre que fornece um conjunto de chaves de funcionalidade em todos os microserviços em um cluster kubernetes. Esses recursos incluem gerenciamento de tráfego, identidade de serviço e segurança, imposição de política e observação. Para obter mais informações sobre İSTİO, consulte a documentação oficial [o que é o İSTİO?][istio-docs-concepts] .

Este artigo mostra como usar a funcionalidade de gerenciamento de tráfego do İSTİO. Um aplicativo de votação AKS de exemplo é usado para explorar o roteamento inteligente e as versões de canário.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Implementar a aplicação
> * Atualizar a aplicação
> * Distribuir uma versão canário do aplicativo
> * Finalizar a distribuição

## <a name="before-you-begin"></a>Antes de começar

> [!NOTE]
> Este cenário foi testado em relação à versão `1.1.3`İSTİO.

As etapas detalhadas neste artigo pressupõem que você criou um cluster AKs (kubernetes `1.11` e superior, com o RBAC habilitado) e estabeleceu `kubectl` uma conexão com o cluster. Você também precisará do İSTİO instalado no cluster.

Se você precisar de ajuda com qualquer um desses itens, consulte o guia de [início rápido do AKS][aks-quickstart] e [Instale o İSTİO no AKs][istio-install] Guidance.

## <a name="about-this-application-scenario"></a>Sobre este cenário de aplicativo

O aplicativo de votação AKS de exemplo fornece duas opções de votação (**gatos** ou **cachorros**) para os usuários. Há um componente de armazenamento que persiste o número de votos para cada opção. Além disso, há um componente de análise que fornece detalhes sobre a conversão de votos para cada opção.

Neste cenário de aplicativo, você começa implantando a `1.0` versão do aplicativo de votação e `1.0` a versão do componente de análise. O componente de análise fornece contagens simples para o número de votos. O aplicativo de votação e o componente de análise `1.0` interagem com a versão do componente de armazenamento, que é apoiada por Redis.

Você atualiza o componente de análise para `1.1`a versão, que fornece contagens e agora os totais e percentuais.

Um subconjunto da versão `2.0` de teste dos usuários do aplicativo por meio de uma versão canário. Essa nova versão usa um componente de armazenamento que é apoiado por um banco de dados MySQL.

Quando tiver certeza de que a `2.0` versão funciona conforme o esperado em seu subconjunto de usuários, distribua `2.0` a versão para todos os usuários.

## <a name="deploy-the-application"></a>Implementar a aplicação

Vamos começar implantando o aplicativo em seu cluster do AKS (serviço kubernetes do Azure). O diagrama a seguir mostra o que é executado no final desta seção – `1.0` versão de todos os componentes com solicitações de entrada atendidas por meio do gateway de entrada do İSTİO:

![Os componentes do aplicativo de votação AKS e o roteamento.](media/istio/components-and-routing-01.png)

Os artefatos que você precisa seguir juntamente com este artigo estão disponíveis no repositório GitHub [Azure-Samples/AKs-votação-app][github-azure-sample] . Você pode baixar os artefatos ou clonar o repositório da seguinte maneira:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Altere para a seguinte pasta no repositório baixado/clonado e execute todas as etapas subsequentes nesta pasta:

```console
cd scenarios/intelligent-routing-with-istio
```

Primeiro, crie um namespace em seu cluster AKs para o aplicativo de votação AKs de `voting` exemplo chamado da seguinte maneira:

```azurecli
kubectl create namespace voting
```

Rotular o namespace `istio-injection=enabled`com. Este rótulo instrui o İSTİO a injetar automaticamente os proxies İSTİO como sidecars em todos os pods nesse namespace.

```azurecli
kubectl label namespace voting istio-injection=enabled
```

Agora, vamos criar os componentes para o aplicativo de votação AKS. Crie esses componentes no `voting` namespace criado em uma etapa anterior.

```azurecli
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

A saída de exemplo a seguir mostra os recursos que estão sendo criados:

```console
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> O İSTİO tem alguns requisitos específicos em relação a pods e serviços. Para obter mais informações, consulte a [documentação requisitos do İSTİO para pods e serviços][istio-requirements-pods-and-services].

Para ver os pods que foram criados, use o comando [kubectl Get pods][kubectl-get] da seguinte maneira:

```azurecli
kubectl get pods -n voting
```

A saída de exemplo a seguir mostra que há três instâncias `voting-app` do pod e uma única instância de `voting-analytics` e `voting-storage` pods. Cada um dos pods tem dois contêineres. Um desses contêineres é o componente e o outro é o `istio-proxy`:

```console
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s
```

Para ver informações sobre o Pod, use o [kubectl descrever Pod][kubectl-describe]. Substitua o nome do pod pelo nome de um pod em seu próprio cluster AKS da saída anterior:

```azurecli
kubectl describe pod voting-app-1-0-956756fd-d5w7z --namespace voting
```

O `istio-proxy` contêiner foi injetado automaticamente pelo İSTİO para gerenciar o tráfego de rede de e para seus componentes, conforme mostrado na seguinte saída de exemplo:

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.1.3
[...]
```

Você não pode se conectar ao aplicativo de votação até criar o [Gateway][istio-reference-gateway] İSTİO e o [serviço virtual][istio-reference-virtualservice]. Esses recursos İSTİO roteiam o tráfego do gateway de entrada do İSTİO padrão para nosso aplicativo.

> [!NOTE]
> Um **Gateway** é um componente na borda da malha de serviço que recebe o tráfego HTTP e TCP de entrada ou saída.
> 
> Um **serviço virtual** define um conjunto de regras de roteamento para um ou mais serviços de destino.

Use o `kubectl apply` comando para implantar o gateway e o YAML do serviço virtual. Lembre-se de especificar o namespace no qual esses recursos são implantados.

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

A saída de exemplo a seguir mostra o novo gateway e o serviço virtual que está sendo criado:

```console
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Obtenha o endereço IP do gateway de entrada do İSTİO usando o seguinte comando:

```azurecli
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

A saída de exemplo a seguir mostra o endereço IP do gateway de entrada:

```
20.188.211.19
```

Abra um navegador e cole o endereço IP. O aplicativo de votação AKS de exemplo é exibido.

![O aplicativo de votação AKS em execução em nosso cluster AKS habilitado para İSTİO.](media/istio/deploy-app-01.png)

As informações na parte inferior da tela mostram que o aplicativo usa a versão `1.0` do `voting-app` e a `1.0` versão `voting-storage` do (Redis).

## <a name="update-the-application"></a>Atualizar a aplicação

Vamos implantar uma nova versão do componente de análise. Essa nova versão `1.1` exibe os totais e as porcentagens, além da contagem de cada categoria.

O diagrama a seguir mostra o que será executado no final desta seção-somente a versão `1.1` do nosso `voting-analytics` componente `voting-app` tem o tráfego roteado do componente. Embora a versão `1.0` do nosso `voting-analytics` componente continue a ser executada `voting-analytics` e seja referenciada pelo serviço, os proxies İSTİO não permitem o tráfego de e para ela.

![Os componentes do aplicativo de votação AKS e o roteamento.](media/istio/components-and-routing-02.png)

Vamos implantar a `voting-analytics` versão `1.1` do componente. Crie este componente no `voting` namespace:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

A saída de exemplo a seguir mostra os recursos que estão sendo criados:

```console
deployment.apps/voting-analytics-1-1 created
```

Abra o aplicativo de votação AKS de exemplo em um navegador novamente, usando o endereço IP do gateway de entrada do İSTİO obtido na etapa anterior.

Seu navegador alterna entre os dois modos de exibição mostrados abaixo. Como você está usando um [serviço][kubernetes-service] kubernetes para o `voting-analytics` componente com apenas um seletor de rótulo`app: voting-analytics`(), kubernetes usa o comportamento padrão de Round Robin entre os pods que correspondem a esse seletor. Nesse caso, é a versão `1.0` e `1.1` o `voting-analytics` pods.

![Versão 1,0 do componente de análise em execução em nosso aplicativo de votação AKS.](media/istio/deploy-app-01.png)

![Versão 1,1 do componente de análise em execução em nosso aplicativo de votação AKS.](media/istio/update-app-01.png)

Você pode visualizar a alternância entre as duas versões do `voting-analytics` componente da seguinte maneira. Lembre-se de usar o endereço IP do seu próprio gateway de entrada do İSTİO.

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

Powershell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

A saída de exemplo a seguir mostra a parte relevante do site da Web retornado como o site alterna entre as versões:

```
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Bloquear o tráfego para a versão 1,1 do aplicativo

Agora `1.1` `voting-analytics` ,vamos`1.0` bloquear o tráfego para apenas a versão do componente e para a versão do componente.`voting-storage` Em seguida, você define as regras de roteamento para todos os outros componentes.

> * Um **serviço virtual** define um conjunto de regras de roteamento para um ou mais serviços de destino.
> * Uma **regra de destino** define políticas de tráfego e políticas específicas de versão.
> * Uma **política** define quais métodos de autenticação podem ser aceitos na carga de trabalho (s).

Use o `kubectl apply` comando para substituir a definição de serviço virtual em `voting-app` seu e adicionar [regras de destino][istio-reference-destinationrule] e [Serviços virtuais][istio-reference-virtualservice] para os outros componentes. Você adicionará uma [política][istio-reference-policy] ao `voting` namespace para garantir que todas as comunicações entre serviços sejam protegidas usando TLS mútuo e certificados de cliente.

* A política foi `peers.mtls.mode` definida como `STRICT` para garantir que o TLS mútuo seja imposto `voting` entre os serviços no namespace.
* Também definimos o `trafficPolicy.tls.mode` como `ISTIO_MUTUAL` em todas as nossas regras de destino. O İSTİO fornece serviços com identidades fortes e protege as comunicações entre serviços usando o TLS mútuo e certificados de cliente que o İSTİO gerencia de forma transparente.

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

A saída de exemplo a seguir mostra a nova política, as regras de destino e os serviços virtuais que estão sendo atualizados/criados:

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Se você abrir o aplicativo de votação AKS em um navegador novamente, somente a nova `1.1` versão `voting-analytics` do `voting-app` componente será usada pelo componente.

![Versão 1,1 do componente de análise em execução em nosso aplicativo de votação AKS.](media/istio/update-app-01.png)

Você pode visualizar que agora é roteado apenas para a `1.1` versão do `voting-analytics` seu componente da seguinte maneira. Lembre-se de usar o endereço IP do seu próprio gateway de entrada do İSTİO:

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

Powershell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

A saída de exemplo a seguir mostra a parte relevante do site da Web retornado:

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Agora, vamos confirmar se o İSTİO está usando o TLS mútuo para proteger as comunicações entre cada um de nossos serviços. Para isso, usaremos o comando [Authn TLS-check][istioctl-authn-tls-check] no binário do `istioctl` cliente, que usa o seguinte formulário.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Esse conjunto de comandos fornece informações sobre o acesso aos serviços especificados, de todos os pods que estão em um namespace e correspondem a um conjunto de rótulos:

Bash

```bash
# mTLS configuration between each of the istio ingress pods and the voting-app service
kubectl get pod -n istio-system -l app=istio-ingressgateway | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.istio-system voting-app.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-analytics service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-analytics.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-storage service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
kubectl get pod -n voting -l app=voting-analytics,version=1.1 | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local
```

Powershell

```powershell
# mTLS configuration between each of the istio ingress pods and the voting-app service
(kubectl get pod -n istio-system -l app=istio-ingressgateway | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".istio-system") voting-app.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-analytics service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-analytics.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-analytics,version=1.1 | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }
```

A saída de exemplo a seguir mostra que o TLS mútuo é imposto para cada uma de nossas consultas acima. A saída também mostra as regras de política e destino que impõe o TLS mútuo:

```console
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Distribuir uma versão canário do aplicativo

Agora, vamos implantar uma nova versão `2.0` `voting-app`dos componentes, `voting-analytics`e `voting-storage` . O novo `voting-storage` componente usa o MySQL em vez do Redis, `voting-app` e `voting-analytics` os componentes e são atualizados para permitir que eles usem `voting-storage` esse novo componente.

O `voting-app` componente agora dá suporte à funcionalidade de sinalizador de recursos. Esse sinalizador de recurso permite testar o recurso de liberação de canário do İSTİO para um subconjunto de usuários.

O diagrama a seguir mostra o que você terá em execução no final desta seção.

* Versão `1.0` `1.1` `1.0` `voting-storage` do `voting-app` componente,versãodocomponenteeversãodocomponente,`voting-analytics` são capazes de se comunicar entre si.
* Versão `2.0` `2.0` `2.0` `voting-storage` do `voting-app` componente,versãodocomponenteeversãodocomponente,`voting-analytics` são capazes de se comunicar entre si.
* `2.0` A`voting-app` versão do componente só pode ser acessada por usuários que têm um sinalizador de recurso específico definido. Essa alteração é gerenciada usando um sinalizador de recurso por meio de um cookie.

![Os componentes do aplicativo de votação AKS e o roteamento.](media/istio/components-and-routing-03.png)

Primeiro, atualize as regras de destino do İSTİO e os serviços virtuais para atender a esses novos componentes. Essas atualizações garantem que você não rotear o tráfego incorretamente para os novos componentes e os usuários não obtenham acesso inesperado:

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

A saída de exemplo a seguir mostra as regras de destino e os serviços virtuais que estão sendo atualizados:

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Em seguida, vamos adicionar os objetos kubernetes para os novos componentes `2.0` de versão. Você também atualiza o `voting-storage` serviço para incluir a `3306` porta para MySQL:

```azurecli
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

A saída de exemplo a seguir mostra que os objetos kubernetes são atualizados ou criados com êxito:

```console
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Aguarde até que todos os `2.0` pods de versão estejam em execução. Use o comando [kubectl Get pods][kubectl-get] para exibir todos os pods no `voting` namespace:

```azurecli
kubectl get pods --namespace voting
```

Agora você deve ser capaz de alternar entre a versão `1.0` e a `2.0` versão (canário) do aplicativo de votação. A alternância de sinalizador de recurso na parte inferior da tela define um cookie. Esse cookie é usado pelo `voting-app` serviço virtual para rotear usuários para a nova versão. `2.0`

![A versão 1,0 do sinalizador de recurso do aplicativo de votação AKS não está definida.](media/istio/canary-release-01.png)

![A versão 2,0 do sinalizador de recurso do aplicativo de votação AKS está definida.](media/istio/canary-release-02.png)

As contagens de voto são diferentes entre as versões do aplicativo. Essa diferença realça que você está usando dois back-ends de armazenamento diferentes.

## <a name="finalize-the-rollout"></a>Finalizar a distribuição

Depois de testar com êxito a versão do canário, atualize o `voting-app` serviço virtual para rotear todo o tráfego `2.0` para a `voting-app` versão do componente. Em seguida, todos os `2.0` usuários veem a versão do aplicativo, independentemente de o sinalizador de recurso ser definido ou não:

![Os componentes do aplicativo de votação AKS e o roteamento.](media/istio/components-and-routing-04.png)

Atualize todas as regras de destino para remover as versões dos componentes que você não deseja mais que estejam ativas. Em seguida, atualize todos os serviços virtuais para parar de referenciar essas versões.

Como não há mais nenhum tráfego para nenhuma das versões mais antigas dos componentes, agora você pode excluir com segurança todas as implantações para esses componentes.

![Os componentes do aplicativo de votação AKS e o roteamento.](media/istio/components-and-routing-05.png)

Agora você distribuiu com êxito uma nova versão do aplicativo de votação AKS.

## <a name="clean-up"></a>Limpeza 

Você pode remover o aplicativo de votação AKs que usamos neste cenário do cluster AKs excluindo o `voting` namespace da seguinte maneira:

```azurecli
kubectl delete namespace voting
```

A saída de exemplo a seguir mostra que todos os componentes do aplicativo de votação AKS foram removidos do cluster AKS.

```console
namespace "voting" deleted
```

## <a name="next-steps"></a>Passos seguintes

Você pode explorar cenários adicionais usando o [exemplo de aplicativo İSTİO BookInfo][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md
