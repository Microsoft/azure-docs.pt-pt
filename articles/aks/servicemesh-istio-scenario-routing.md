---
title: Roteamento inteligente e versões de canário com İSTİO no serviço kubernetes do Azure (AKS)
description: Saiba como usar o İSTİO para fornecer roteamento inteligente e implantar versões do canário em um cluster do AKS (serviço do kubernetes do Azure)
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 48daf2be4a05922982479a86e6574f3aa85d2130
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530293"
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
> Esse cenário foi testado em relação à versão İSTİO `1.3.2`.

As etapas detalhadas neste artigo pressupõem que você criou um cluster AKS (kubernetes `1.13` e superior, com o RBAC habilitado) e estabeleceu uma conexão de `kubectl` com o cluster. Você também precisará do İSTİO instalado no cluster.

Se você precisar de ajuda com qualquer um desses itens, consulte o guia de [início rápido do AKS][aks-quickstart] e [Instale o İSTİO no AKs][istio-install] Guidance.

## <a name="about-this-application-scenario"></a>Sobre este cenário de aplicativo

O aplicativo de votação AKS de exemplo fornece duas opções de votação (**gatos** ou **cachorros**) para os usuários. Há um componente de armazenamento que persiste o número de votos para cada opção. Além disso, há um componente de análise que fornece detalhes sobre a conversão de votos para cada opção.

Neste cenário de aplicativo, você começa implantando a versão `1.0` do aplicativo de votação e a versão `1.0` do componente de análise. O componente de análise fornece contagens simples para o número de votos. O aplicativo de votação e o componente de análise interagem com a versão `1.0` do componente de armazenamento, que é apoiado pelo Redis.

Você atualiza o componente de análise para a versão `1.1`, que fornece contagens e agora os totais e percentuais.

Um subconjunto de usuários `2.0` versão de teste do aplicativo por meio de uma versão canário. Essa nova versão usa um componente de armazenamento que é apoiado por um banco de dados MySQL.

Quando tiver certeza de que a versão `2.0` funciona conforme o esperado em seu subconjunto de usuários, distribua a versão `2.0` para todos os usuários.

## <a name="deploy-the-application"></a>Implementar a aplicação

Vamos começar implantando o aplicativo em seu cluster do AKS (serviço kubernetes do Azure). O diagrama a seguir mostra o que é executado no final desta seção – versão `1.0` de todos os componentes com solicitações de entrada atendidas por meio do gateway de entrada do İSTİO:

![Os componentes do aplicativo de votação AKS e o roteamento.](media/servicemesh/istio/scenario-routing-components-01.png)

Os artefatos que você precisa seguir juntamente com este artigo estão disponíveis no repositório GitHub [Azure-Samples/AKs-votação-app][github-azure-sample] . Você pode baixar os artefatos ou clonar o repositório da seguinte maneira:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Altere para a seguinte pasta no repositório baixado/clonado e execute todas as etapas subsequentes nesta pasta:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Primeiro, crie um namespace em seu cluster AKS para o aplicativo de votação AKS de exemplo chamado `voting` da seguinte maneira:

```azurecli
kubectl create namespace voting
```

Rotule o namespace com `istio-injection=enabled`. Este rótulo instrui o İSTİO a injetar automaticamente os proxies İSTİO como sidecars em todos os pods nesse namespace.

```azurecli
kubectl label namespace voting istio-injection=enabled
```

Agora, vamos criar os componentes para o aplicativo de votação AKS. Crie esses componentes no namespace `voting` criado em uma etapa anterior.

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
kubectl get pods -n voting --show-labels
```

A saída de exemplo a seguir mostra que há três instâncias do pod de `voting-app` e uma única instância de `voting-analytics` e `voting-storage` pods. Cada um dos pods tem dois contêineres. Um desses contêineres é o componente e o outro é o `istio-proxy`:

```console
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Para ver informações sobre o Pod, usaremos o comando [Pod kubectl para descrever][kubectl-describe] os seletores de rótulo para selecionar o pod de `voting-analytics`. Filtraremos a saída para mostrar os detalhes dos dois contêineres presentes no pod:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

Você não pode se conectar ao aplicativo de votação até criar o [Gateway][istio-reference-gateway] İSTİO e o [serviço virtual][istio-reference-virtualservice]. Esses recursos İSTİO roteiam o tráfego do gateway de entrada do İSTİO padrão para nosso aplicativo.

> [!NOTE]
> Um **Gateway** é um componente na borda da malha de serviço que recebe o tráfego HTTP e TCP de entrada ou saída.
> 
> Um **serviço virtual** define um conjunto de regras de roteamento para um ou mais serviços de destino.

Use o comando `kubectl apply` para implantar o gateway e o YAML do serviço virtual. Lembre-se de especificar o namespace no qual esses recursos são implantados.

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

![O aplicativo de votação AKS em execução em nosso cluster AKS habilitado para İSTİO.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

As informações na parte inferior da tela mostram que o aplicativo usa a versão `1.0` do `voting-app` e a versão `1.0` do `voting-storage` (Redis).

## <a name="update-the-application"></a>Atualizar a aplicação

Vamos implantar uma nova versão do componente de análise. Essa nova versão `1.1` exibe os totais e as porcentagens, além da contagem de cada categoria.

O diagrama a seguir mostra o que será executado ao final desta seção somente `1.1` de versão do nosso componente `voting-analytics` tem tráfego roteado do componente `voting-app`. Embora a versão `1.0` do nosso componente de `voting-analytics` continue a ser executada e seja referenciada pelo serviço de `voting-analytics`, os proxies İSTİO não permitem o tráfego de e para ela.

![Os componentes do aplicativo de votação AKS e o roteamento.](media/servicemesh/istio/scenario-routing-components-02.png)

Vamos implantar a versão `1.1` do componente `voting-analytics`. Crie este componente no namespace `voting`:

```azurecli
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

A saída de exemplo a seguir mostra os recursos que estão sendo criados:

```console
deployment.apps/voting-analytics-1-1 created
```

Abra o aplicativo de votação AKS de exemplo em um navegador novamente, usando o endereço IP do gateway de entrada do İSTİO obtido na etapa anterior.

Seu navegador alterna entre os dois modos de exibição mostrados abaixo. Como você está usando um [serviço][kubernetes-service] kubernetes para o componente `voting-analytics` com apenas um seletor de rótulo (`app: voting-analytics`), o kubernetes usa o comportamento padrão de Round Robin entre os pods que correspondem a esse seletor. Nesse caso, ele é a versão `1.0` e `1.1` do seu `voting-analytics` pods.

![Versão 1,0 do componente de análise em execução em nosso aplicativo de votação AKS.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![Versão 1,1 do componente de análise em execução em nosso aplicativo de votação AKS.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Você pode visualizar a alternância entre as duas versões do componente `voting-analytics` da seguinte maneira. Lembre-se de usar o endereço IP do seu próprio gateway de entrada do İSTİO.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

A saída de exemplo a seguir mostra a parte relevante do site da Web retornado como o site alterna entre as versões:

```console
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Bloquear o tráfego para a versão 1,1 do aplicativo

Agora, vamos bloquear o tráfego somente para a versão `1.1` do componente `voting-analytics` e para a versão `1.0` do componente `voting-storage`. Em seguida, você define as regras de roteamento para todos os outros componentes.

> * Um **serviço virtual** define um conjunto de regras de roteamento para um ou mais serviços de destino.
> * Uma **regra de destino** define políticas de tráfego e políticas específicas de versão.
> * Uma **política** define quais métodos de autenticação podem ser aceitos na carga de trabalho (s).

Use o comando `kubectl apply` para substituir a definição de serviço virtual em seu `voting-app` e adicionar [regras de destino][istio-reference-destinationrule] e [Serviços virtuais][istio-reference-virtualservice] para os outros componentes. Você adicionará uma [política][istio-reference-policy] ao namespace `voting` para garantir que todas as comunicações entre serviços sejam protegidas usando TLS mútuo e certificados de cliente.

* A política tem `peers.mtls.mode` definido como `STRICT` para garantir que o TLS mútuo seja imposto entre os serviços no namespace `voting`.
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

Se você abrir o aplicativo de votação AKS em um navegador novamente, somente a nova versão `1.1` do componente `voting-analytics` será usada pelo componente `voting-app`.

![Versão 1,1 do componente de análise em execução em nosso aplicativo de votação AKS.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Você pode visualizar que agora é roteado apenas para a versão `1.1` do seu componente `voting-analytics` da seguinte maneira. Lembre-se de usar o endereço IP do seu próprio gateway de entrada do İSTİO:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

A saída de exemplo a seguir mostra a parte relevante do site da Web retornado:

```console
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Agora, vamos confirmar se o İSTİO está usando o TLS mútuo para proteger as comunicações entre cada um de nossos serviços. Para isso, usaremos o comando [Authn TLS-check][istioctl-authn-tls-check] no binário do `istioctl` Client, que usa o seguinte formulário.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Esse conjunto de comandos fornece informações sobre o acesso aos serviços especificados, de todos os pods que estão em um namespace e correspondem a um conjunto de rótulos:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

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

Agora, vamos implantar uma nova versão `2.0` dos componentes `voting-app`, `voting-analytics` e `voting-storage`. O novo componente de `voting-storage` usa o MySQL em vez do Redis, e os componentes `voting-app` e `voting-analytics` são atualizados para permitir que eles usem esse novo componente de `voting-storage`.

O componente `voting-app` agora dá suporte à funcionalidade de sinalizador de recursos. Esse sinalizador de recurso permite testar o recurso de liberação de canário do İSTİO para um subconjunto de usuários.

O diagrama a seguir mostra o que você terá em execução no final desta seção.

* @No__t_0 de versão do componente `voting-app`, a versão `1.1` do componente `voting-analytics` e a versão `1.0` do componente `voting-storage` são capazes de se comunicar entre si.
* @No__t_0 de versão do componente `voting-app`, a versão `2.0` do componente `voting-analytics` e a versão `2.0` do componente `voting-storage` são capazes de se comunicar entre si.
* A versão `2.0` do componente `voting-app` só pode ser acessada por usuários que têm um sinalizador de recurso específico definido. Essa alteração é gerenciada usando um sinalizador de recurso por meio de um cookie.

![Os componentes do aplicativo de votação AKS e o roteamento.](media/servicemesh/istio/scenario-routing-components-03.png)

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

Em seguida, vamos adicionar os objetos kubernetes para a nova versão `2.0` Components. Você também atualiza o serviço de `voting-storage` para incluir a porta `3306` para MySQL:

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

Aguarde até que toda a versão `2.0` pods esteja em execução. Use o comando [kubectl Get pods][kubectl-get] com a opção `-w` Watch para observar alterações em todos os pods no namespace `voting`:

```azurecli
kubectl get pods --namespace voting -w
```

Agora você deve ser capaz de alternar entre a versão `1.0` e a versão `2.0` (canário) do aplicativo de votação. A alternância de sinalizador de recurso na parte inferior da tela define um cookie. Esse cookie é usado pelo serviço virtual `voting-app` para rotear usuários para a nova versão `2.0`.

![A versão 1,0 do sinalizador de recurso do aplicativo de votação AKS não está definida.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![A versão 2,0 do sinalizador de recurso do aplicativo de votação AKS está definida.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

As contagens de voto são diferentes entre as versões do aplicativo. Essa diferença realça que você está usando dois back-ends de armazenamento diferentes.

## <a name="finalize-the-rollout"></a>Finalizar a distribuição

Depois de testar com êxito a versão do canário, atualize o serviço virtual `voting-app` para rotear todo o tráfego para a versão `2.0` do componente `voting-app`. Em seguida, todos os usuários veem a versão `2.0` do aplicativo, independentemente de o sinalizador de recurso ser definido ou não:

![Os componentes do aplicativo de votação AKS e o roteamento.](media/servicemesh/istio/scenario-routing-components-04.png)

Atualize todas as regras de destino para remover as versões dos componentes que você não deseja mais que estejam ativas. Em seguida, atualize todos os serviços virtuais para parar de referenciar essas versões.

Como não há mais nenhum tráfego para nenhuma das versões mais antigas dos componentes, agora você pode excluir com segurança todas as implantações para esses componentes.

![Os componentes do aplicativo de votação AKS e o roteamento.](media/servicemesh/istio/scenario-routing-components-05.png)

Agora você distribuiu com êxito uma nova versão do aplicativo de votação AKS.

## <a name="clean-up"></a>Limpeza 

Você pode remover o aplicativo de votação AKS que usamos neste cenário do cluster AKS excluindo o namespace `voting` da seguinte maneira:

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
[istio-install]: ./servicemesh-istio-install.md
