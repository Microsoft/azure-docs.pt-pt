---
title: Use Istio para encaminhamento inteligente
titleSuffix: Azure Kubernetes Service
description: Saiba como usar o Istio para fornecer lançamentos inteligentes de encaminhamento e implantação de canários num cluster Azure Kubernetes Service (AKS)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 871a764c549de75d5a9e1449ba2e0737d38a4094
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83799946"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Utilize lançamentos inteligentes de encaminhamento e canário com istio no Serviço Azure Kubernetes (AKS)

[A Istio][istio-github] é uma malha de serviço de código aberto que fornece um conjunto chave de funcionalidades em todos os microserviços de um cluster Kubernetes. Estas características incluem gestão de tráfego, identidade de serviço e segurança, aplicação de políticas e observabilidade. Para mais informações sobre Istio, consulte o oficial O que é a documentação [istio?][istio-docs-concepts]

Este artigo mostra-lhe como utilizar a funcionalidade de gestão de tráfego da Istio. Uma aplicação de voto AKS de amostra é usada para explorar o encaminhamento inteligente e lançamentos canários.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Implementar a aplicação
> * Atualizar a aplicação
> * Lançar uma versão canária da aplicação
> * Finalizar o lançamento

## <a name="before-you-begin"></a>Antes de começar

> [!NOTE]
> Este cenário foi testado contra a versão `1.3.2` Istio.

Os passos detalhados neste artigo assumem que criou um cluster AKS (Kubernetes `1.13` e acima, com RBAC habilitado) e estabeleceu uma `kubectl` ligação com o cluster. Também vai precisar de Istio instalado no seu cluster.

Se precisar de ajuda com algum destes itens, consulte o [quickstart AKS][aks-quickstart] e instale o Istio na orientação [AKS.][istio-install]

## <a name="about-this-application-scenario"></a>Sobre este cenário de aplicação

A aplicação de voto AKS da amostra fornece duas opções de voto **(Gatos** ou **Cães)** aos utilizadores. Existe um componente de armazenamento que persiste o número de votos para cada opção. Além disso, existe uma componente de análise que fornece detalhes em torno dos votos expressos para cada opção.

Neste cenário de aplicação, começa por implementar a versão `1.0` da aplicação de voto e versão `1.0` da componente analítica. A componente de análise fornece contagens simples para o número de votos. A aplicação de voto e o componente de análise interagem com a versão `1.0` do componente de armazenamento, que é apoiado pela Redis.

Atualize o componente de análise para a versão `1.1` , que fornece contagens, e agora totais e percentagens.

Um subconjunto de utilizadores testa a versão `2.0` da aplicação através de uma versão canária. Esta nova versão utiliza um componente de armazenamento que é apoiado por uma base de dados MySQL.

Uma vez que está confiante de que a versão `2.0` funciona como esperado no seu subconjunto de utilizadores, lança a versão para todos os seus `2.0` utilizadores.

## <a name="deploy-the-application"></a>Implementar a aplicação

Comecemos por implementar a aplicação no seu cluster Azure Kubernetes Service (AKS). O seguinte diagrama mostra o que funciona até ao final desta secção - versão `1.0` de todos os componentes com pedidos de entrada servidos através do gateway istio ingress:

![Os componentes e encaminhamento da aplicação AKS Vote.](media/servicemesh/istio/scenario-routing-components-01.png)

Os artefactos que precisa de seguir juntamente com este artigo estão disponíveis na app GitHub de [Azure-Samples/aks-vote.][github-azure-sample] Pode descarregar os artefactos ou clonar o repo da seguinte forma:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Altere para a seguinte pasta no repo descarregado/clonado e executar todos os passos subsequentes desta pasta:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Em primeiro lugar, crie um espaço de nome no seu cluster AKS para a aplicação de voto AKS da amostra, nomeada `voting` da seguinte forma:

```console
kubectl create namespace voting
```

Rotular o espaço com nomes com `istio-injection=enabled` . Esta etiqueta instrui a Istio a injetar automaticamente os istio-proxies como sidecars em todas as suas cápsulas neste espaço de nome.

```console
kubectl label namespace voting istio-injection=enabled
```

Agora vamos criar os componentes para a aplicação de voto AKS. Crie estes componentes no `voting` espaço de nome criado num passo anterior.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

O seguinte exemplo de produção mostra os recursos que estão a ser criados:

```output
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> A Istio tem alguns requisitos específicos em torno de cápsulas e serviços. Para mais informações, consulte os [Requisitos Istio para a documentação de Pods e Serviços.][istio-requirements-pods-and-services]

Para ver as cápsulas que foram criadas, use o comando [kubectl get pods][kubectl-get] da seguinte forma:

```console
kubectl get pods -n voting --show-labels
```

A saída de exemplo a seguir mostra que existem três instâncias da `voting-app` cápsula e um único exemplo tanto das `voting-analytics` cápsulas como das `voting-storage` cápsulas. Cada uma das cápsulas tem dois contentores. Um destes contentores é o componente, e o outro é `istio-proxy` o:

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Para ver informações sobre o pod, usaremos o comando [do pod de design de kubectl][kubectl-describe] com seletores de etiquetas para selecionar o `voting-analytics` pod. Filtraremos a saída para mostrar os detalhes dos dois recipientes presentes na cápsula:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

Não pode ligar-se à aplicação de voto até criar o Istio [Gateway][istio-reference-gateway] e [o Serviço Virtual.][istio-reference-virtualservice] Estes recursos istio encaminham o tráfego da porta de entrada padrão da Istio para a nossa aplicação.

> [!NOTE]
> Um **Gateway** é um componente na borda da malha de serviço que recebe tráfego HTTP e TCP de entrada ou saída.
> 
> Um **Serviço Virtual** define um conjunto de regras de encaminhamento para um ou mais serviços de destino.

Utilize o `kubectl apply` comando para implantar o yaml gateway e serviço virtual. Lembre-se de especificar o espaço de nomes em que estes recursos são implantados.

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

A saída de exemplo a seguir mostra a criação do novo Gateway e Serviço Virtual:

```output
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Obtenha o endereço IP do Portal Istio Ingress utilizando o seguinte comando:

```output
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

A saída de exemplo a seguir mostra o endereço IP do Gateway Ingress:

```output
20.188.211.19
```

Abra um browser e cole no endereço IP. É apresentada a aplicação de voto AKS da amostra.

![A app AKS Vote em execução no nosso cluster Istio habilitado AKS.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

As informações na parte inferior do ecrã mostram que a aplicação utiliza versão `1.0` `voting-app` e versão de `1.0` `voting-storage` (Redis).

## <a name="update-the-application"></a>Atualizar a aplicação

Vamos implementar uma nova versão do componente de análise. Esta nova versão `1.1` apresenta totais e percentagens para além da contagem para cada categoria.

O diagrama seguinte mostra o que vai ser em execução no final desta secção - apenas a versão `1.1` do nosso componente tem o tráfego desviado do `voting-analytics` `voting-app` componente. Apesar de a versão `1.0` do nosso componente continuar a funcionar e ser `voting-analytics` referenciada pelo `voting-analytics` serviço, os proxies istio não autorizam o tráfego de e para ele.

![Os componentes e encaminhamento da aplicação AKS Vote.](media/servicemesh/istio/scenario-routing-components-02.png)

Vamos implementar a versão `1.1` do `voting-analytics` componente. Crie este componente no `voting` espaço de nomes:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

O seguinte exemplo de produção mostra os recursos que estão a ser criados:

```output
deployment.apps/voting-analytics-1-1 created
```

Abra novamente a aplicação de voto AKS de amostra num browser, utilizando o endereço IP do Portal Istio Ingress obtido no passo anterior.

O seu navegador alterna entre as duas vistas abaixo mostradas. Uma vez que está a utilizar um [Serviço][kubernetes-service] Kubernetes para o componente com apenas um seletor de `voting-analytics` etiquetas `app: voting-analytics` (), a Kubernetes utiliza o comportamento padrão de rodapé redondo entre as cápsulas que correspondem ao seletor. Neste caso, é tanto a versão como `1.0` `1.1` as suas `voting-analytics` cápsulas.

![Versão 1.0 do componente de análise em execução na nossa aplicação de voto AKS.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![Versão 1.1 do componente de análise em execução na nossa aplicação de voto AKS.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Pode visualizar a comutação entre as duas versões do componente da `voting-analytics` seguinte forma. Lembre-se de usar o endereço IP do seu próprio Portal Istio Ingress.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

A saída de exemplo a seguir mostra a parte relevante do web site devolvido à medida que o site muda entre versões:

```output
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Bloqueie o tráfego para a versão 1.1 da aplicação

Agora vamos bloquear o tráfego apenas para a versão `1.1` do componente e para a versão do `voting-analytics` `1.0` `voting-storage` componente. Em seguida, define regras de encaminhamento para todos os outros componentes.

> * Um **Serviço Virtual** define um conjunto de regras de encaminhamento para um ou mais serviços de destino.
> * Uma **Regra de Destino** define políticas de tráfego e políticas específicas de versão.
> * Uma **Política** define quais os métodos de autenticação que podem ser aceites na carga de trabalho.

Utilize o `kubectl apply` comando para substituir a definição de Serviço Virtual no seu e adicione `voting-app` [Regras][istio-reference-destinationrule] de Destino e [Serviços Virtuais][istio-reference-virtualservice] para os outros componentes. Irá adicionar uma [Política][istio-reference-policy] ao `voting` espaço de nomes para garantir que toda a comunicação entre serviços é assegurada usando TLS mútuos e certificados de cliente.

* A Política `peers.mtls.mode` definiu `STRICT` para garantir que o TLS mútuo seja aplicado entre os seus serviços dentro do `voting` espaço de nomes.
* Também definimos o a `trafficPolicy.tls.mode` que se passa em todas as `ISTIO_MUTUAL` nossas Regras de Destino. A Istio presta serviços com identidades fortes e assegura comunicações entre serviços utilizando TLS mútuos e certificados de cliente que a Istio gere de forma transparente.

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

O seguinte exemplo de saída mostra a nova Política, Regras de Destino e Serviços Virtuais a ser atualizado/criado:

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Se voltar a abrir a aplicação AKS Vote num browser, apenas a nova versão `1.1` do componente é utilizada pelo `voting-analytics` `voting-app` componente.

![Versão 1.1 do componente de análise em execução na nossa aplicação de voto AKS.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Pode visualizar que agora só está encaminhado para a versão `1.1` do seu componente da seguinte `voting-analytics` forma. Lembre-se de usar o endereço IP do seu próprio Portal Istio Ingress:

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

```output
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Vamos agora confirmar que a Istio está a usar tLS mútuos para garantir comunicações entre cada um dos nossos serviços. Para isso, usaremos o comando [authn tls-check][istioctl-authn-tls-check] no binário do `istioctl` cliente, que toma o seguinte formulário.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Este conjunto de comandos fornece informações sobre o acesso aos serviços especificados, de todas as cápsulas que se encontram num espaço de nome e correspondem a um conjunto de etiquetas:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

Este exemplo de produção mostra que o TLS mútuo é aplicado para cada uma das nossas consultas acima. A produção também mostra as Regras de Política e Destino que aplicam o TLS mútuo:

```output
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

Agora vamos implementar uma nova versão `2.0` do `voting-app` , e `voting-analytics` `voting-storage` componentes. O novo `voting-storage` componente utiliza o MySQL em vez do Redis, e os `voting-app` componentes e componentes são `voting-analytics` atualizados para permitir a utilização deste novo `voting-storage` componente.

O `voting-app` componente suporta agora a funcionalidade da bandeira. Esta bandeira de funcionalidade permite testar a capacidade de desbloqueio canário da Istio para um subconjunto de utilizadores.

O diagrama que se segue mostra o que terá em execução no final desta secção.

* A versão `1.0` do `voting-app` componente, a versão `1.1` do componente e a versão do componente são `voting-analytics` `1.0` `voting-storage` capazes de comunicar entre si.
* A versão `2.0` do `voting-app` componente, a versão `2.0` do componente e a versão do componente são `voting-analytics` `2.0` `voting-storage` capazes de comunicar entre si.
* A versão `2.0` do componente só é `voting-app` acessível aos utilizadores que tenham um conjunto de bandeiras de funcionalidades específicas. Esta alteração é gerida através de uma bandeira de recurso através de um cookie.

![Os componentes e encaminhamento da aplicação AKS Vote.](media/servicemesh/istio/scenario-routing-components-03.png)

Em primeiro lugar, atualize as Regras de Destino istio e serviços virtuais para atender a estes novos componentes. Estas atualizações garantem que não encaminha o tráfego incorretamente para os novos componentes e os utilizadores não têm acesso inesperado:

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

O seguinte exemplo de produção mostra que as Regras de Destino e Serviços Virtuais estão a ser atualizados:

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Em seguida, adicione os objetos Kubernetes para os novos componentes da `2.0` versão. Também atualize o `voting-storage` serviço para incluir a porta para o `3306` MySQL:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

A saída de exemplo a seguir mostra que os objetos Kubernetes são atualizados ou criados com sucesso:

```output
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Aguarde até que todas as cápsulas de versão `2.0` estejam a funcionar. Utilize o comando [kubectl get pods][kubectl-get] com o interruptor de `-w` relógio para ver se há alterações em todas as cápsulas no espaço de `voting` nomes:

```console
kubectl get pods --namespace voting -w
```

Deverá agora poder alternar entre a versão e a `1.0` versão `2.0` (canária) da aplicação de voto. A bandeira de recurso alterna na parte inferior do ecrã define um cookie. Este cookie é utilizado pelo `voting-app` Serviço Virtual para encaminhar os utilizadores para a nova `2.0` versão.

![Versão 1.0 da app de voto AKS - a bandeira da funcionalidade NÃO está definida.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![Versão 2.0 da app AKS Vote - conjunto de bandeira de recurso IS.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

As contagens de voto são diferentes entre as versões da app. Esta diferença realça que está a utilizar dois backends de armazenamento diferentes.

## <a name="finalize-the-rollout"></a>Finalizar o lançamento

Depois de ter testado com sucesso o lançamento do canário, atualize o `voting-app` Serviço Virtual para encaminhar todo o tráfego para a versão `2.0` do `voting-app` componente. Todos os utilizadores vêem então a versão `2.0` da aplicação, independentemente de a bandeira da funcionalidade estar definida ou não:

![Os componentes e encaminhamento da aplicação AKS Vote.](media/servicemesh/istio/scenario-routing-components-04.png)

Atualize todas as Regras de Destino para remover as versões dos componentes que já não deseja ativa. Em seguida, atualize todos os Serviços Virtuais para parar de fazer referência a essas versões.

Uma vez que já não existe tráfego para nenhuma das versões mais antigas dos componentes, pode agora eliminar com segurança todas as implementações para esses componentes.

![Os componentes e encaminhamento da aplicação AKS Vote.](media/servicemesh/istio/scenario-routing-components-05.png)

Lançou agora com sucesso uma nova versão da App de Voto AKS.

## <a name="clean-up"></a>Limpeza 

Pode remover a aplicação de voto AKS que utilizamos neste cenário a partir do seu cluster AKS, eliminando o `voting` espaço de nome da seguinte forma:

```console
kubectl delete namespace voting
```

A saída de exemplo a seguir mostra que todos os componentes da aplicação de voto AKS foram removidos do seu cluster AKS.

```output
namespace "voting" deleted
```

## <a name="next-steps"></a>Passos seguintes

Você pode explorar cenários adicionais usando o [exemplo da Aplicação Istio Bookinfo][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.mesh.v1alpha1/#AuthenticationPolicy
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
