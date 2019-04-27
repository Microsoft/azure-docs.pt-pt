---
title: Encaminhamento inteligente e versões de proteção com Istio no Azure Kubernetes Service (AKS)
description: Saiba como utilizar Istio fornecem encaminhamento inteligentes e implementar versões de proteção num cluster do Azure Kubernetes Service (AKS)
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 12/03/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 0a4e5e7e310a9949ee59291c2032eafda46955a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60465946"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Utilize o encaminhamento inteligente e versões de proteção com Istio no Azure Kubernetes Service (AKS)

[Istio] [ istio-github] é uma malha de serviço do código-fonte aberto que fornece um conjunto de chaves de funcionalidade entre os microsserviços num cluster do Kubernetes. Esses recursos incluem gerenciamento de tráfego, a identidade de serviço e segurança, imposição de políticas e observability. Para obter mais informações sobre Istio, consulte oficial [o que é Istio?] [ istio-docs-concepts] documentação.

Este artigo mostra-lhe como utilizar a funcionalidade de gestão de tráfego do Istio. Uma aplicação de votação de AKS de exemplo é utilizada para explorar inteligente de encaminhamento e versões de proteção.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Implementar a aplicação
> * Atualizar a aplicação
> * Implementar uma versão canary da aplicação
> * Finalizar a implementação

## <a name="before-you-begin"></a>Antes de começar

Os passos detalhados neste artigo partem do princípio de que criou um cluster do AKS (1.10 de Kubernetes e superior, com RBAC ativado) e estabeleceu uma `kubectl` ligação com o cluster. Também precisa Istio instalado no seu cluster.

Se precisar de ajuda com qualquer um desses itens, em seguida, consulte a [início rápido do AKS] [ aks-quickstart] e [instalar Istio no AKS][istio-install].

## <a name="about-this-application-scenario"></a>Sobre este cenário de aplicação

A aplicação de votação de AKS de exemplo fornece duas opções de votos (cães ou de gatos) aos utilizadores. Há um componente de armazenamento persistir o número de votos para cada opção. Além disso, há um componente de análise que fornece detalhes sobre os votos computados para cada opção.

Neste artigo, comece por implementar a versão *1.0* da aplicação de voto e versão *1.0* do componente de análise. O componente de análise fornece contagens simples para o número de votos. O componente de análise e aplicação de votos interagirem com a versão *1.0* do componente de armazenamento, o que é sustentado pelo Redis.

Atualizar o componente de análise para a versão *1.1*, que fornece contagens e agora os totais e percentagens.

Um subconjunto da versão de teste de usuários *2.0* da aplicação através de uma versão canary. Esta nova versão utiliza um componente de armazenamento que é apoiado por uma base de dados MySQL.

Assim que tiver a certeza de que a versão *2.0* funciona conforme esperado no seu subconjunto de utilizadores, distribuir versão *2.0* para todos os seus utilizadores.

## <a name="deploy-the-application"></a>Implementar a aplicação

Vamos começar por implementar a aplicação no seu cluster do Azure Kubernetes Service (AKS). O diagrama seguinte mostra o que é executado no final desta secção, versão *1.0* de todos os componentes com pedidos de entrada servidos através do gateway de entrada Istio:

![O AKS componentes da aplicação de voto e encaminhamento.](media/istio/components-and-routing-01.png)

Os artefactos tem de seguir este artigo estão disponíveis no [Azure-amostras/aks--aplicação voting] [ github-azure-sample] repositório do GitHub. Pode transferir os artefactos ou clone o repositório da seguinte forma:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Alterar para a seguinte pasta no repositório transferido / clonado e executar todos os passos subsequentes a partir desta pasta:

```console
cd scenarios/intelligent-routing-with-istio
```

Primeiro, crie um espaço de nomes no seu cluster do AKS para a aplicação votação de exemplo AKS com o nome *voto* da seguinte forma:

```console
kubectl create namespace voting
```

Com o espaço de nomes de etiqueta `istio-injection=enabled`. Esta etiqueta instrui Istio para inserir automaticamente os proxies de istio como sidecars em todos os seus pods neste espaço de nomes.

```console
kubectl label namespace voting istio-injection=enabled
```

Agora vamos criar os componentes da aplicação de votação do AKS. Criar esses componentes do *voto* espaço de nomes criado num passo anterior.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

O resultado de exemplo seguinte mostra que os recursos foram criados com êxito:

```
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio tem alguns requisitos específicos em torno de pods e serviços. Para obter mais informações, consulte a [Istio requisitos para a documentação de serviços e Pods][istio-requirements-pods-and-services].

Para ver os pods que foram criados, utilize o [kubectl obter pods] [ kubectl-get] comando da seguinte forma:

```console
kubectl get pods -n voting
```

O resultado de exemplo seguinte mostra que existem três instâncias do *aplicação voting* pod e uma única instância de ambos os *votação-analytics* e *armazenamento votação* pods. Cada um dos pods tem dois contentores. Um desses contêineres é o componente e o outro é o *istio proxy*:

```
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-669f99dcc8-lzh7k   2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-bdmld         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-gcrng         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-strzc         2/2       Running   0          1m
voting-storage-1-0-7954799d96-5fv9r     2/2       Running   0          1m
```

Para ver informações sobre o pod, utilize o [kubectl descrevem pod][kubectl-describe]. Substitua o nome de pod com o nome de um pod em seu próprio cluster do AKS a partir da saída anterior:

```console
kubectl describe pod voting-app-1-0-6c65c4bdd4-bdmld --namespace voting
```

O *istio proxy* contentor automaticamente tem sido injetado por Istio para gerir o tráfego de rede de e para os componentes, conforme mostrado no seguinte exemplo:

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         dockerhub.azk8s.cn/istio/proxyv2:1.0.4
[...]
```

Não é possível ligar para a aplicação de voto até criar o Istio [Gateway] [ istio-reference-gateway] e [serviço Virtual][istio-reference-virtualservice]. Estes recursos Istio encaminham o tráfego do gateway de entrada de Istio padrão para nosso aplicativo.

> [!NOTE]
> R *Gateway* é um componente na borda da malha de serviço que recebe o tráfego de entrada ou saído HTTP e TCP.
>
> R *serviço Virtual* define um conjunto de regras de encaminhamento para um ou mais serviços de destino.

Utilize o `istioctl` cliente binário para implementar o Gateway e o serviço Virtual yaml. Tal como acontece com o `kubectl apply` comando este problema, não se esqueça de especificar o espaço de nomes que esses recursos são implementados em.

```console
istioctl create -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Obtenha o endereço IP do Gateway de entrada Istio com o seguinte comando:

```console
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

O resultado de exemplo seguinte mostra o endereço IP do Gateway de entrada:

```
52.187.250.239
```

Abra um navegador e cole o endereço IP. A aplicação de votação de AKS de exemplo é apresentada.

![A aplicação de votação do AKS de mensagens em fila em execução no nosso Istio ativado o cluster do AKS.](media/istio/deploy-app-01.png)

As informações na parte inferior do ecrã mostram que a aplicação utiliza a versão *1.0* da *aplicação voting* e a versão *1.0* (Redis) como a opção de armazenamento.

## <a name="update-the-application"></a>Atualizar a aplicação

Nós vamos implementar uma nova versão do componente de análise. Esta nova versão *1.1* apresenta os totais e percentagens, além de contagem para cada categoria.

O diagrama seguinte mostra o que é executado no final desta secção, apenas a versão *1.1* de nossa *análise de votação* componente tem tráfego encaminhado do *aplicação voting* componente. Apesar de versão *1.0* de nossa *votação-analytics* componente continua a ser executado e é referenciada pelo *votação-analytics* serviço, proxies de Istio recusam o tráfego para e do mesmo.

![O AKS componentes da aplicação de voto e encaminhamento.](media/istio/components-and-routing-02.png)

Vamos implementar uma versão *1.1* da *análise de votação* componente. Criar este componente no *voto* espaço de nomes:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Abra o exemplo de AKS aplicação de votos num navegador mais uma vez, com o endereço IP do Gateway de entrada Istio obtida no passo anterior.

O browser alterna entre os dois modos de exibição mostrados abaixo. Uma vez que está a utilizar o Kubernetes [serviço] [ kubernetes-service] para o *votação-analytics* componente com apenas um Seletor de etiqueta única (`app: voting-analytics`), Kubernetes utiliza a predefinição comportamento de round robin entre os pods que correspondem a esse Seletor. Neste caso, é tanto versão *1.0* e *1.1* de seu *análise votação* pods.

![Versão 1.0 do componente de análise em execução na nossa aplicação de votação do AKS.](media/istio/deploy-app-01.png)

![Versão 1.1 do componente de análise em execução na nossa aplicação de votação do AKS.](media/istio/update-app-01.png)

É possível visualizar a alternância entre as duas versões dos *votação-analytics* componente da seguinte forma. Lembre-se utilizar o endereço IP do seu próprio Gateway de entrada Istio.

```console
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

O resultado de exemplo seguinte mostra a parte relevante do web site retornado como os comutadores de site entre versões:

```
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Bloquear o tráfego para a versão 1.1 do aplicativo

Agora vamos bloquear o tráfego para a versão só *1.1* da *análise de votação* componente e para a versão *1.0* do *armazenamento votação* componente. , Em seguida, definir regras de encaminhamento para todos os outros componentes.

> * R *serviço Virtual* define um conjunto de regras de encaminhamento para um ou mais serviços de destino.
> * R *regra de destino* define políticas de tráfego e políticas específicas de versão.
> * R *política* define os métodos de autenticação podem ser aceites no workload(s).

Utilizar o `istioctl` cliente binário para substituir a definição de serviço Virtual em seu *aplicação voting* e adicione [regras de destino] [ istio-reference-destinationrule] e [ Serviços virtuais] [ istio-reference-virtualservice] para os outros componentes.

Também adicionar um [diretiva] [ istio-reference-policy] para o *voto* espaço de nomes para se certificar de que todos comunicam entre serviços está protegido com TLS mútua e certificados de cliente.

Porque não existe uma definição de serviço Virtual existente para *aplicação de voto* que substituir, utilize o `istioctl replace` comando da seguinte forma:

```console
istioctl replace -f istio/step-2a-update-voting-app-virtualservice.yaml --namespace voting
```

O resultado de exemplo seguinte mostra o serviço Virtual Istio são atualizadas com êxito:

```
Updated config virtual-service/voting/voting-app to revision 141902
```

Em seguida, utilize o `istioctl create` comando para adicionar a nova política e também as novas regras de destino e serviços virtuais para todos os outros componentes.

* A política tem `peers.mtls.mode` definido como `STRICT` para se certificar de que o TLS mútua é aplicada entre seus serviços dentro do *voto* espaço de nomes.
* Também é definir os `trafficPolicy.tls.mode` para `ISTIO_MUTUAL` em todas as nossas regras de destino. Istio fornece serviços com identidades fortes e protege as comunicações entre serviços utilizando TLS mútua e certificados de cliente que Istio gere de forma transparente.

```console
istioctl create -f istio/step-2b-add-routing-for-all-components.yaml --namespace voting
```

O resultado de exemplo seguinte mostra a nova política, regras de destino, e serviços virtuais são criados com êxito:

```
Created config policy/voting/default to revision 142118
Created config destination-rule/voting/voting-app at revision 142119
Created config destination-rule/voting/voting-analytics at revision 142120
Created config virtual-service/voting/voting-analytics at revision 142121
Created config destination-rule/voting/voting-storage at revision 142122
Created config virtual-service/voting/voting-storage at revision 142123
```

Se abrir a aplicação de voto do AKS num navegador mais uma vez, a nova versão *1.1* da *análise de votação* componente é usado pelo *aplicação voting* componente.

![Versão 1.1 do componente de análise em execução na nossa aplicação de votação do AKS.](media/istio/update-app-01.png)

Pode ver mais facilmente, agora só encaminhadas para a versão *1.1* do seu *análise de votação* componente da seguinte forma. Lembre-se utilizar o endereço IP do seu gateway de entrada Istio.

É possível visualizar a agora apenas encaminhado para a versão *1.1* do seu *análise de votação* componente da seguinte forma. Lembre-se utilizar o endereço IP do seu próprio Gateway de entrada Istio:

```azurecli
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

O resultado de exemplo seguinte mostra a parte relevante do web site devolvido:

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Certifique-se que Istio usa mútua TLS para proteger a comunicação entre cada um dos nossos serviços. Os seguintes comandos Verifique as definições de TLS para cada um da *aplicação voting* serviços:

```console
istioctl authn tls-check voting-app.voting.svc.cluster.local
istioctl authn tls-check voting-analytics.voting.svc.cluster.local
istioctl authn tls-check voting-storage.voting.svc.cluster.local
```

Este resultado de exemplo seguinte mostra que o TLS mútua é imposta para cada um dos serviços através da política e as regras de destino:

```
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Implementar uma versão canary da aplicação

Agora vamos implementar uma nova versão *2.0* da *aplicação voting*, *votação-analytics*, e *armazenamento votação* componentes. A nova *armazenamento de voto* componente utilizar o MySQL, em vez de Redis e o *aplicação voting* e *análise votação* componentes são atualizados para permitir-lhes que utilizem este novo *armazenamento de voto* componente.

O *aplicação voting* componente agora suporta a funcionalidade do sinalizador de funcionalidade. Este sinalizador de funcionalidade permite-lhe a capacidade de versão canary do Istio para um subconjunto de utilizadores de teste.

O diagrama seguinte mostra o que é executado no final desta secção.

* Versão *1.0* da *aplicação voting* componente, versão *1.1* do *votação-analytics* componente e versão  *1.0* do *de votação-armazenamento* componentes conseguem comunicar entre si.
* Versão *2.0* da *aplicação voting* componente, versão *2.0* do *votação-analytics* componente e versão  *2.0* do *de votação-armazenamento* componentes conseguem comunicar entre si.
* Versão *2.0* da *aplicação voting* componente apenas podem ser acedidos por utilizadores que têm um conjunto de sinalizador de recurso específico. Esta alteração é gerida através de um sinalizador de funcionalidade por meio de um cookie.

![O AKS componentes da aplicação de voto e encaminhamento.](media/istio/components-and-routing-03.png)

Em primeiro lugar, Atualize as regras de destino de Istio e serviços virtuais sirva para esses novos componentes. Estas atualizações Certifique-se de que não encaminha o tráfego incorretamente para os novos componentes e os usuários não recebam acesso inesperado:

```console
istioctl replace -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

O resultado de exemplo seguinte mostra as regras de destino e serviços virtuais que são atualizados com êxito:

```
Updated config destination-rule/voting/voting-app to revision 150930
Updated config virtual-service/voting/voting-app to revision 150931
Updated config destination-rule/voting/voting-analytics to revision 150937
Updated config virtual-service/voting/voting-analytics to revision 150939
Updated config destination-rule/voting/voting-storage to revision 150940
Updated config virtual-service/voting/voting-storage to revision 150941
```

Em seguida, vamos adicionar os objetos de Kubernetes para a nova versão *2.0* componentes. Também de atualizar o *armazenamento de voto* serviço para incluir o *3306* porta para MySQL:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

O resultado de exemplo seguinte mostra os objetos de Kubernetes são atualizados ou criados com êxito:

```
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Aguarde até que todos os a versão *2.0* pods em execução. Utilize o [kubectl obter pods] [ kubectl-get] comando para ver todos os pods no *voto* espaço de nomes:

```azurecli
kubectl get pods --namespace voting
```

Agora deve ser capaz de alternar entre a versão *1.0* e a versão *2.0* (canary) da aplicação de voto. O botão de alternar de sinalizador de funcionalidade na parte inferior do ecrã define um cookie. Este cookie é utilizado pela *aplicação de voto* serviço Virtual para encaminhar os utilizadores para a nova versão *2.0*.

![Versão 1.0 da aplicação de voto AKS - sinalizador de funcionalidade não está definido.](media/istio/canary-release-01.png)

![Versão 2.0 da aplicação de voto AKS - funcionalidade sinalizador está definido.](media/istio/canary-release-02.png)

As contagens de voto são diferentes entre as versões da aplicação. Essa diferença destaca que está a utilizar duas back-ends de armazenamento diferente.

## <a name="finalize-the-rollout"></a>Finalizar a implementação

Quando tiver testado com êxito a versão canary, atualize o *aplicação de voto* serviço Virtual para encaminhar todo o tráfego para a versão *2.0* do *aplicação voting* componente. Todos os utilizadores, em seguida, veem a versão *2.0* do aplicativo, independentemente do sinalizador de funcionalidade é definido ou não:

![O AKS componentes da aplicação de voto e encaminhamento.](media/istio/components-and-routing-04.png)

Atualize todas as regras de destino para remover as versões dos componentes que já não quer Active Directory. Em seguida, atualize todos os serviços virtuais para parar a referência a essas versões.

Uma vez que já não existe qualquer tráfego para qualquer uma das versões mais antigas dos componentes, pode agora eliminar em segurança todas as implementações dos componentes.

![O AKS componentes da aplicação de voto e encaminhamento.](media/istio/components-and-routing-05.png)

Com êxito agora lançada uma nova versão da aplicação de votação de AKS.

## <a name="next-steps"></a>Passos Seguintes

Pode explorar cenários adicionais a utilizar o [exemplo de aplicativo de Bookinfo Istio][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/spec-requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#Gateway
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#VirtualService
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#DestinationRule
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md