---
title: Encaminhamento inteligente e lançamentos canários com Istio no Serviço Azure Kubernetes (AKS)
description: Aprenda a usar istio para fornecer encaminhamento inteligente e implementar lançamentos canários num cluster azure Kubernetes Service (AKS)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 4c29658473aaa50168175c76234dfca34fcdad83
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594167"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Utilize encaminhamento inteligente e lançamentos canários com Istio no Serviço Azure Kubernetes (AKS)

[Istio][istio-github] é uma malha de serviço de código aberto que fornece um conjunto chave de funcionalidades em todos os microserviços em um cluster Kubernetes. Estas características incluem gestão de tráfego, identidade de serviço e segurança, aplicação de políticas e observabilidade. Para mais informações sobre Istio, consulte a documentação oficial [do Istio?][istio-docs-concepts]

Este artigo mostra-lhe como usar a funcionalidade de gestão de tráfego da Istio. Uma aplicação de votação AKS de amostra é usada para explorar lançamentos inteligentes de encaminhamento e canários.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Implementar a aplicação
> * Atualizar a aplicação
> * Lançar uma versão canária da aplicação
> * Finalizar o lançamento

## <a name="before-you-begin"></a>Antes de começar

> [!NOTE]
> Este cenário foi testado contra a versão Istio `1.3.2`.

Os passos detalhados neste artigo assumem que criou um cluster AKS (Kubernetes `1.13` e acima, com RBAC habilitado) e estabeleceu uma ligação `kubectl` com o cluster. Também vai precisar de Istio instalado no seu cluster.

Se precisar de ajuda com algum destes itens, consulte o arranque rápido da [AKS][aks-quickstart] e instale o Istio na orientação [AKS.][istio-install]

## <a name="about-this-application-scenario"></a>Sobre este cenário de candidatura

A aplicação de votação AKS da amostra fornece duas opções de voto **(Gatos** ou **Cães**) aos utilizadores. Existe um componente de armazenamento que persiste o número de votos por cada opção. Além disso, existe uma componente de análise que fornece detalhes em torno dos votos expressos para cada opção.

Neste cenário de aplicação, começa por implementar a versão `1.0` da aplicação de voto e versão `1.0` da componente de análise. A componente de análise fornece simples contagens para o número de votos. A aplicação de voto e o componente de análise interagem com a versão `1.0` do componente de armazenamento, que é apoiado pela Redis.

Atualiza o componente de análise para versão `1.1`, que fornece contagens, e agora totaliza e percentagens.

Um subconjunto de utilizadores testa a versão `2.0` da aplicação através de um lançamento canário. Esta nova versão utiliza um componente de armazenamento que é apoiado por uma base de dados MySQL.

Uma vez confiante que a versão `2.0` funciona como esperado no seu subconjunto de utilizadores, lança a versão `2.0` para todos os seus utilizadores.

## <a name="deploy-the-application"></a>Implementar a aplicação

Vamos começar por implantar a aplicação no seu cluster Azure Kubernetes Service (AKS). O diagrama seguinte mostra o que corre até ao final desta secção - versão `1.0` de todos os componentes com pedidos de entrada servidos através da porta de entrada Istio:

![Os componentes da aplicação de votação AKS e o encaminhamento.](media/servicemesh/istio/scenario-routing-components-01.png)

Os artefactos que precisa de seguir juntamente com este artigo estão disponíveis no repo GitHub de [votação azure/aks-voting.][github-azure-sample] Pode descarregar os artefactos ou clonar o repo da seguinte forma:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Mude para a seguinte pasta no repo descarregado/clonado e executar todos os passos subsequentes desta pasta:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Em primeiro lugar, crie um espaço de nome no seu cluster AKS para a aplicação de votação AKS da amostra chamada `voting` seguinte:

```azurecli
kubectl create namespace voting
```

Rotule o espaço de nome com `istio-injection=enabled`. Esta etiqueta instrui istio a injetar automaticamente os istio-proxies como sidecars em todas as suas cápsulas neste espaço de nome.

```azurecli
kubectl label namespace voting istio-injection=enabled
```

Agora vamos criar os componentes para a aplicação de votação AKS. Crie estes componentes no espaço de nome `voting` criado num passo anterior.

```azurecli
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

A saída de exemplo a seguir mostra os recursos que estão a ser criados:

```console
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio tem alguns requisitos específicos em torno de pods e serviços. Para mais informações, consulte os [Requisitos istio para pods e documentos de serviços.][istio-requirements-pods-and-services]

Para ver as cápsulas que foram criadas, use o [kubectl obter comando de cápsulas][kubectl-get] da seguinte forma:

```azurecli
kubectl get pods -n voting --show-labels
```

A saída de exemplo que se segue mostra que existem três casos da cápsula `voting-app` e uma única instância tanto das cápsulas de `voting-analytics` como de `voting-storage`. Cada uma das cápsulas tem dois contentores. Um destes recipientes é o componente, e o outro é o `istio-proxy`:

```console
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Para ver informações sobre a cápsula, usaremos o comando de [cápsula de utilização kubectl][kubectl-describe] com selecionadores de etiquetas para selecionar o `voting-analytics` pod. Filtraremos a saída para mostrar os detalhes dos dois recipientes presentes na cápsula:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

Não pode ligar-se à aplicação de votação até criar o Istio [Gateway][istio-reference-gateway] e o [Serviço Virtual.][istio-reference-virtualservice] Estes recursos Istio encaminham o tráfego desde a porta de entrada de entrada istio padrão para a nossa aplicação.

> [!NOTE]
> Um **Gateway** é um componente na borda da malha de serviço que recebe tráfego HTTP de entrada ou saída e TCP.
> 
> Um **Serviço Virtual** define um conjunto de regras de encaminhamento para um ou mais serviços de destino.

Utilize o comando `kubectl apply` para implantar o yaml gateway e serviço virtual. Lembre-se de especificar o espaço de nome em que estes recursos são implantados.

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

A saída de exemplo a seguir mostra a criação do novo Gateway e do Serviço Virtual:

```console
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Obtenha o endereço IP do Gateway Istio Ingress utilizando o seguinte comando:

```azurecli
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

A saída de exemplo a seguir mostra o endereço IP do Gateway Ingress:

```
20.188.211.19
```

Abra um navegador e cola no endereço IP. A aplicação de votação AKS da amostra é apresentada.

![A aplicação DE Votação AKS que funciona no nosso cluster Istio permitiu o cluster AKS.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

A informação na parte inferior do ecrã mostra que a aplicação usa versão `1.0` de `voting-app` e versão `1.0` de `voting-storage` (Redis).

## <a name="update-the-application"></a>Atualizar a aplicação

Vamos implementar uma nova versão do componente de análise. Esta nova versão `1.1` apresenta totais e percentagens para além da contagem para cada categoria.

O diagrama que se segue mostra o que estará a funcionar no final desta secção - apenas a versão `1.1` do nosso componente `voting-analytics` tem tráfego desviado do componente `voting-app`. Embora a versão `1.0` do nosso componente `voting-analytics` continue a funcionar e seja referenciada pelo serviço `voting-analytics`, os proxies Istio desempereo tráfego de e para ele.

![Os componentes da aplicação de votação AKS e o encaminhamento.](media/servicemesh/istio/scenario-routing-components-02.png)

Vamos implementar a versão `1.1` do componente `voting-analytics`. Crie este componente no espaço de `voting` nome:

```azurecli
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

A saída de exemplo a seguir mostra os recursos que estão a ser criados:

```console
deployment.apps/voting-analytics-1-1 created
```

Abra novamente a aplicação de voto AKS de amostra num browser, utilizando o endereço IP do Istio Ingress Gateway obtido no passo anterior.

O seu navegador alterna entre as duas vistas mostradas abaixo. Uma vez que está a utilizar um [Serviço][kubernetes-service] Kubernetes para o componente `voting-analytics` com apenas um seletor de etiqueta (`app: voting-analytics`), kubernetes usa o comportamento padrão do robin redondo entre as cápsulas que combinam com o seletor. Neste caso, é tanto a versão `1.0` como `1.1` das suas `voting-analytics` pods.

![Versão 1.0 do componente de análise em execução na nossa aplicação DE Votação AKS.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![Versão 1.1 do componente de análise em execução na nossa aplicação DE Votação AKS.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Pode visualizar a troca entre as duas versões do componente `voting-analytics` da seguinte forma. Lembre-se de usar o endereço IP do seu próprio Istio Ingress Gateway.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

A saída de exemplo seguinte mostra a parte relevante do site devolvido à medida que o site alterna entre versões:

```console
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Bloquear o tráfego para a versão 1.1 da aplicação

Agora vamos bloquear o tráfego apenas para ver `1.1` do componente `voting-analytics` e para ver `1.0` do componente `voting-storage`. Em seguida, define regras de encaminhamento para todos os outros componentes.

> * Um **Serviço Virtual** define um conjunto de regras de encaminhamento para um ou mais serviços de destino.
> * Uma **Regra de Destino** define políticas de tráfego e políticas específicas de versão.
> * Uma **Política** define quais os métodos de autenticação que podem ser aceites em cargas de trabalho.

Utilize o comando `kubectl apply` para substituir a definição de Serviço Virtual no seu `voting-app` e adicionar Regras de [Destino][istio-reference-destinationrule] e [Serviços Virtuais][istio-reference-virtualservice] para os outros componentes. Você adicionará uma [Política][istio-reference-policy] ao espaço de nome `voting` para garantir que todos os serviços são garantidos usando TLS mútuos e certificados de cliente.

* A Política `peers.mtls.mode` definido para `STRICT` para garantir que o TLS mútuo é aplicado entre os seus serviços dentro do espaço de nome `voting`.
* Também definimos o `trafficPolicy.tls.mode` para `ISTIO_MUTUAL` em todas as nossas Regras de Destino. A Istio fornece serviços com identidades fortes e assegura comunicações entre serviços utilizando TLS mútuos e certificados de cliente que a Istio gere de forma transparente.

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

A saída de exemplo seguinte mostra que a nova Política, Regras de Destino e Serviços Virtuais está a ser atualizada/criada:

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Se voltar a abrir a aplicação AKS Vote num browser, apenas a nova versão `1.1` do componente `voting-analytics` é utilizada pelo componente `voting-app`.

![Versão 1.1 do componente de análise em execução na nossa aplicação DE Votação AKS.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Pode visualizar que agora só está encaminhado para versão `1.1` do seu componente `voting-analytics` da seguinte forma. Lembre-se de usar o endereço IP do seu próprio Istio Ingress Gateway:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

A saída de exemplo a seguir mostra a parte relevante do site devolvido:

```console
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Vamos confirmar que o Istio está a usar tLS mútuo para garantir comunicações entre cada um dos nossos serviços. Para isso, usaremos o comando [authn tls-check][istioctl-authn-tls-check] no `istioctl` binário do cliente, que assume o seguinte formulário.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Este conjunto de comandos fornece informações sobre o acesso aos serviços especificados, de todas as cápsulas que se encontram num espaço de nome e combinam com um conjunto de etiquetas:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

Esta saída de exemplo seguinte mostra que o TLS mútuo é aplicado para cada uma das nossas consultas acima. A saída também mostra as Regras de Política e Destino que aplicam o TLS mútuo:

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

## <a name="roll-out-a-canary-release-of-the-application"></a>Lançar uma versão canária da aplicação

Agora vamos implementar uma nova versão `2.0` dos componentes `voting-app`, `voting-analytics`e `voting-storage`. Os novos componentes `voting-storage` utilizam o MySQL em vez do Redis, e os componentes `voting-app` e `voting-analytics` são atualizados para permitir a utilização deste novo componente `voting-storage`.

O componente `voting-app` suporta agora a funcionalidade de sinalização. Esta bandeira de funcionalidade permite-lhe testar a capacidade de libertação canária da Istio para um subconjunto de utilizadores.

O diagrama seguinte mostra o que terá a funcionar no final desta secção.

* Versão `1.0` do componente `voting-app`, versão `1.1` do componente `voting-analytics` e `1.0` versão do componente `voting-storage` são capazes de comunicar entre si.
* Versão `2.0` do componente `voting-app`, versão `2.0` do componente `voting-analytics` e `2.0` versão do componente `voting-storage` são capazes de comunicar entre si.
* A versão `2.0` do componente `voting-app` só são acessíveis aos utilizadores que tenham um conjunto específico de bandeiras de funcionalidade. Esta alteração é gerida através de uma bandeira de recurso através de um cookie.

![Os componentes da aplicação de votação AKS e o encaminhamento.](media/servicemesh/istio/scenario-routing-components-03.png)

Em primeiro lugar, atualize as Regras de Destino istio e os serviços virtuais para atender a estes novos componentes. Estas atualizações garantem que não encaminha o tráfego incorretamente para os novos componentes e os utilizadores não têm acesso inesperado:

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

A saída de exemplo seguinte mostra que as Regras de Destino e os Serviços Virtuais estão a ser atualizados:

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Em seguida, vamos adicionar os objetos Kubernetes para a nova versão `2.0` componentes. Também atualize o serviço `voting-storage` para incluir a porta `3306` para MySQL:

```azurecli
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

A saída de exemplo seguinte mostra que os objetos Kubernetes são atualizados ou criados com sucesso:

```console
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Aguarde até que toda a versão `2.0` cápsulas estejam em funcionamento. Utilize o [comando kubectl get pods][kubectl-get] com o `-w` interruptor de relógio para ver as alterações em todas as cápsulas no espaço de nome `voting`:

```azurecli
kubectl get pods --namespace voting -w
```

Deverá agora poder alternar entre a versão `1.0` e a versão `2.0` (canário) da aplicação de voto. O toggle da bandeira de características na parte inferior do ecrã define um cookie. Este cookie é utilizado pelo Serviço Virtual `voting-app` para direcionar os utilizadores para a nova versão `2.0`.

![Versão 1.0 da aplicação DE Votação AKS - bandeira de recurso NÃO está definida.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![Versão 2.0 da aplicação DE Votação AKS - conjunto de bandeira is.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

As contagem de votos são diferentes entre as versões da app. Esta diferença realça que está a usar duas facetas de armazenamento diferentes.

## <a name="finalize-the-rollout"></a>Finalizar o lançamento

Depois de ter testado com sucesso o lançamento do canário, atualize o Serviço Virtual `voting-app` para direcionar todo o tráfego para versão `2.0` do componente `voting-app`. Todos os utilizadores vêem a versão `2.0` da aplicação, independentemente de a bandeira da funcionalidade estar ou não definida:

![Os componentes da aplicação de votação AKS e o encaminhamento.](media/servicemesh/istio/scenario-routing-components-04.png)

Atualize todas as Regras de Destino para remover as versões dos componentes que já não deseja estar ativo. Em seguida, atualize todos os Serviços Virtuais para parar de fazer referência a essas versões.

Uma vez que já não existe qualquer tráfego para nenhuma das versões mais antigas dos componentes, pode agora eliminar com segurança todas as implementações desses componentes.

![Os componentes da aplicação de votação AKS e o encaminhamento.](media/servicemesh/istio/scenario-routing-components-05.png)

Lançou com sucesso uma nova versão da App de Votação AKS.

## <a name="clean-up"></a>Limpeza 

Pode remover a aplicação de votação AKS que usamos neste cenário do seu cluster AKS, eliminando o espaço de `voting` nome da seguinte forma:

```azurecli
kubectl delete namespace voting
```

A saída de exemplo que se segue mostra que todos os componentes da aplicação de votação AKS foram removidos do seu cluster AKS.

```console
namespace "voting" deleted
```

## <a name="next-steps"></a>Passos seguintes

Pode explorar cenários adicionais utilizando o exemplo da [Aplicação Istio Bookinfo.][istio-bookinfo-example]

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
