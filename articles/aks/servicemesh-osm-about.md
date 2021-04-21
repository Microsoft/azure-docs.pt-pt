---
title: Rede de serviço aberto (pré-visualização)
description: Malha de serviço aberto (OSM) no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: bbc07a7ee3f996c778cfc1b9d1764f10a613c50b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782950"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>Open Service Mesh Addon AKS (Pré-visualização)

## <a name="overview"></a>Descrição Geral

[A Rede de Serviço Aberto (OSM)](https://docs.openservicemesh.io/) é uma malha de serviço nativa leve, extensível que permite aos utilizadores gerir, proteger e obter funcionalidades de observabilidade fora da caixa para ambientes de microserviços altamente dinâmicos.

A OSM executa um avião de controlo baseado em Kubernetes, pode ser configurado com [APIs SMI,](https://smi-spec.io/) e funciona injetando um enviado proxy como um recipiente de sidecar ao lado de cada instância da sua aplicação. O proxy enviado contém e executa regras em torno das políticas de controlo de acesso, implementa a configuração do encaminhamento e captura métricas. O plano de controlo configura continuamente os proxies para garantir que as políticas e as regras de encaminhamento estão atualizadas e garante que os proxies são saudáveis.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="capabilities-and-features"></a>Capacidades e Funcionalidades

A OSM fornece o seguinte conjunto de capacidades e funcionalidades para fornecer uma malha de serviço nativa em nuvem para os seus clusters Azure Kubernetes Service (AKS):

- Serviço seguro à comunicação de serviço, permitindo mTLS

- Aplicações facilmente a bordo na malha, permitindo a injeção automática de sidecar do enviado proxy

- Configurações facilmente e transparentes para deslocação de tráfego em implantações

- Capacidade de definir e executar políticas de controlo de acesso fino para serviços

- Visibilidade e insights sobre as métricas de aplicação para serviços de depuragem e monitorização

- Integração com serviços/soluções de gestão de certificados externos com interface pluggable

## <a name="scenarios"></a>Cenários

A OSM pode ajudar as suas implementações AKS com os seguintes cenários:

- Fornecer comunicações encriptadas entre pontos finais de serviço implantados no cluster

- Autorização de tráfego tanto http/HTTPS como TCP na malha

- Configuração de controlos de tráfego ponderados entre dois ou mais serviços para implantações A/B ou canárias

- Recolha e visualização de KPI's do tráfego de aplicações

## <a name="osm-service-quotas-and-limits-preview"></a>Quotas e Limites de Serviço OSM (Pré-visualização)

As limitações de pré-visualização da OSM para as quotas de serviço e limites podem ser encontradas na página AKS [Quotas e limites regionais.](https://docs.microsoft.com/azure/aks/quotas-skus-regions)

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/osm/install-osm-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/osm/install-osm-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/osm/install-osm-binary-windows.md)]

::: zone-end

> [!WARNING]
> Não tente instalar o OSM a partir do binário utilizando `osm install` . Isto resultará numa instalação de OSM que não está integrada como um complemento para a AKS.

### <a name="register-the-aks-openservicemesh-preview-feature"></a>Registar a `AKS-OpenServiceMesh` funcionalidade de pré-visualização

Para criar um cluster AKS que possa utilizar o addon de malha de serviço aberto, tem de ativar a bandeira de `AKS-OpenServiceMesh` funcionalidade na sua subscrição.

Registe o `AKS-OpenServiceMesh` pavilhão de funcionalidades utilizando o comando [de registo de recursos az,][az-feature-register] como mostra o seguinte exemplo:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

Demora alguns minutos para que o estado seja _apresentado._ Verifique o estado de registo utilizando o comando [da lista de recursos AZ:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

Quando estiver pronto, reaprovi o registo do fornecedor de recursos _Microsoft.ContainerService_ utilizando o comando [de registo do fornecedor az:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>Instalar o serviço de malha de serviço aberto (OSM) Azure Kubernetes Service (AKS) para um novo cluster AKS

Para um novo cenário de implantação de cluster AKS, começará com uma nova implementação de um cluster AKS que permite o addon OSM no cluster criar operação.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Em Azure, aloca recursos relacionados a um grupo de recursos. Crie um grupo de recursos utilizando [o grupo Az create](/cli/azure/group#az_group_create). O exemplo a seguir cria um grupo de recursos chamado _myOsmAksGroup_ na localização _eastus2_ (região):

```azurecli-interactive
az group create --name <myosmaksgroup> --location <eastus2>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>Implementar um cluster AKS com o add-on osm ativado

Irá agora implantar um novo cluster AKS com o add-on osm ativado.

> [!NOTE]
> Tenha em atenção que o seguinte comando de implantação AKS utiliza discos efémeros OS. Pode encontrar mais informações aqui sobre [discos Efemérides de OS para AKS](https://docs.microsoft.com/azure/aks/cluster-configuration#ephemeral-os)

```azurecli-interactive
az aks create -n osm-addon-cluster -g <myosmaksgroup> --kubernetes-version 1.19.6 --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>Obtenha credenciais de acesso ao cluster AKS

Obtenha credenciais de acesso para o novo cluster gerido de Kubernetes.

```azurecli-interactive
az aks get-credentials -n <myosmakscluster> -g <myosmaksgroup>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>Ativar o addon do Serviço Azure Kubernetes (AKS) de rede de serviço de serviço aberto para um cluster AKS existente

Para um cenário de cluster AKS existente, você irá ativar o add-on OSM a um cluster AKS existente que já foi implementado.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>Ativar o addon OSM para o cluster AKS existente

Para ativar o addon AKS OSM, terá de executar o `az aks enable-addons --addons` comando que passa o parâmetro `open-service-mesh`

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <resource group name> -n <AKS cluster name>
```

Deverá ver uma saída semelhante à saída apresentada abaixo para confirmar que o addon AKS OSM foi instalado.

```json
{- Finished ..
  "aadProfile": null,
  "addonProfiles": {
    "KubeDashboard": {
      "config": null,
      "enabled": false,
      "identity": null
    },
    "openServiceMesh": {
      "config": {},
      "enabled": true,
      "identity": {
...
```

## <a name="validate-the-aks-osm-add-on-installation"></a>Validar a instalação de addon AKS OSM

Existem vários comandos a executar para verificar todos os componentes do addon AKS OSM estão ativados e em execução:

Primeiro podemos consultar os perfis adicionais do cluster para verificar o estado ativado dos addons instalados. O seguinte comando deve voltar "verdadeiro".

```azurecli-interactive
az aks list -g <resource group name> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

Os `kubectl` seguintes comandos reportarão o estado do controlador de osm.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on"></a>Acesso ao addon AKS OSM

Atualmente pode aceder e configurar a configuração do controlador OSM através do configmap. Para visualizar as definições de configuração do controlador OSM, consulte o pedido de osm-config configmap para `kubectl` visualizar as definições de configuração.

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

A saída do mapa de configmap osmis deve parecer-se com o seguinte:

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254/32,168.63.129.16/32,<YOUR_API_SERVER_PUBLIC_IP>/32",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Note que o **permissive_traffic_policy_mode** está configurado para **ser verdadeiro**. O modo de política de tráfego permissivo em OSM é um modo em que a aplicação da política de tráfego [SMI](https://smi-spec.io/) é ignorada. Neste modo, a OSM descobre automaticamente serviços que fazem parte da malha de serviço e programa as regras de política de tráfego em cada enviado proxy sidecar para poder comunicar com estes serviços.

> [!WARNING]
> Antes de prosseguir, verifique se o seu modo de política de tráfego permissivo está definido como verdadeiro, se não, por favor, altere-o para **verdadeiro** usando o comando abaixo

```OSM Permissive Mode to True
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"true"}}'
```

## <a name="deploy-a-new-application-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Implementar uma nova aplicação a ser gerida pelo serviço de malha de serviço aberto (OSM) Azure Kubernetes Service (AKS)

### <a name="before-you-begin"></a>Antes de começar

Os passos detalhados nesta caminhada assumem que criou um cluster AKS (Kubernetes `1.19+` e acima, com o RBAC de Kubernetes ativado), estabeleceram uma `kubectl` ligação com o cluster (Se precisar de ajuda com algum destes itens, consulte o [quickstart AKS](./kubernetes-walkthrough.md), e instale o addon AKS OSM.

Deve ter os seguintes recursos instalados:

- O Azure CLI, versão 2.20.0 ou mais tarde
- A `aks-preview` versão de extensão 0.5.5 ou mais tarde
- Versão OSM v0.8.0 ou mais tarde
- apt-get instalar jq

### <a name="create-namespaces-for-the-application"></a>Criar espaços de nome para a aplicação

Nesta passagem, utilizaremos a aplicação da livraria OSM que tem os seguintes serviços Kubernetes:

- bookbuyer
- bookthief
- livraria
- casa de bookware

Crie espaços de nome para cada um destes componentes de aplicação.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Deverá ver o seguinte resultado:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>A bordo dos espaços de nome a gerir pela OSM

Quando adicionar os espaços de nome à malha OSM, isto permitirá ao controlador OSM injetar automaticamente os recipientes de procuração sidecar envoy com a sua aplicação. Executar o seguinte comando a bordo dos espaços de nome de aplicações da livraria OSM.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Deverá ver o seguinte resultado:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Implementar a aplicação da Livraria para o cluster AKS

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Todas as saídas de implantação são resumidas abaixo.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="checkpoint-what-got-installed"></a>Checkpoint: O que foi instalado?

O exemplo A aplicação da Livraria é uma app multi-tiered que consiste em quatro serviços, sendo o bookbuyer, bookthief, livraria e bookwarehouse. Tanto o bookbuyer como o serviço bookthief comunicam ao serviço da livraria para recuperar livros do serviço da livraria. O serviço de livraria recupera livros do serviço de bookwarehouse para fornecer o bookbuyer e o bookthief. Esta é uma aplicação simples de vários níveis que funciona bem para mostrar como uma malha de serviço pode ser usada para proteger e autorizar comunicações entre os serviços de aplicações. À medida que continuarmos através da passagem, estaremos a permitir e desativar políticas de Interface de Malha de Serviço (SMI) para permitir e desativar os serviços para comunicar via OSM. Abaixo está um diagrama de arquitetura do que foi instalado para a aplicação da livraria.

![Arquitetura de aplicativos de bookbuyer OSM](./media/aks-osm-addon/osm-bookstore-app-arch.png)

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Verifique a aplicação da Livraria que está a decorrer dentro do cluster AKS

A partir de agora, implantamos a aplicação de mulita-contentor da livraria, mas só está acessível dentro do cluster AKS. Tutoriais posteriores irão ajudá-lo a expor a aplicação fora do cluster através de um controlador de entrada. Para já, vamos utilizar o encaminhamento portuário para aceder à aplicação de compradores de livros dentro do cluster AKS para verificar se está a comprar livros do serviço de livraria.

Para verificar se a aplicação está a decorrer dentro do cluster, usaremos uma porta para ver tanto o leitor de livros como os componentes do livro UI.

Primeiro vamos pegar o nome do comprador de livros

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Deverá ver um resultado semelhante ao seguinte. O seu casulo de compradores terá um nome único anexado.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Assim que tivermos o nome da cápsula, podemos agora usar o comando portuário para configurar um túnel do nosso sistema local para a aplicação dentro do cluster AKS. Executar o seguinte comando para configurar o porto para a frente para a porta de sistema local 8080. Utilize novamente o nome do seu pod de comprador de livros especificado.

> [!NOTE]
> Para todos os comandos de encaminhamento de portas, é melhor utilizar um terminal adicional para que possa continuar a trabalhar através deste passadiço e não desligar o túnel. Também é melhor que estabeleça o túnel portuário para a frente fora da Azure Cloud Shell.

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Devia ver uma saída semelhante a esta.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Enquanto a sessão de reencaminhamento da porta estiver no lugar, navegue para o seguinte url a partir de um browser `http://localhost:8080` . Deverá agora poder ver a aplicação de compradores de livros UI no navegador semelhante à imagem abaixo.

![Imagem UI do comprador de livros OSM](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

Notará ainda que o número total de livros comprados continua a incrementar para o serviço de livraria v1. O serviço de livraria V2 ainda não foi implantado. Implantaremos o serviço de livraria v2 quando demonstrarmos as políticas de divisão de tráfego do SMI.

Também pode verificar o mesmo para o serviço bookthief.

```azurecli-interactive
kubectl get pod -n bookthief
```

Deverá ver um resultado semelhante ao seguinte. O seu casulo bookthief terá um nome único anexado.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

Porto para a frente para a cápsula bookthief.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

Navegue para o seguinte url a partir de um browser `http://localhost:8080` . Devias ver que o bookthief está neste momento a roubar livros do serviço da livraria! Mais tarde, implementaremos uma política de tráfego para parar a política de apostas.

![Imagem uI da aplicação osm bookthief](./media/aks-osm-addon/osm-bookthief-service-ui.png)

### <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>Desative o modo de tráfego permissivo OSM para a malha

Como mencionado anteriormente ao visualizar a configuração do cluster OSM, a configuração OSM predefine para permitir a política de modo de tráfego permissivo. Neste modo, a aplicação da política de tráfego é ignorada e a OSM descobre automaticamente serviços que fazem parte da malha de serviço e programa regras de política de tráfego em cada enviado proxy sidecar para poder comunicar com estes serviços.

Vamos agora desativar a política permissiva do modo de tráfego e a OSM precisará de políticas [explícitas de SMI](https://smi-spec.io/) implementadas no cluster para permitir comunicações na malha a partir de cada serviço. Para desativar o modo de tráfego permissivo, executar o seguinte comando para atualizar a propriedade configmap alterando o valor de `true` `false` .

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"false"}}'
```

Deverá ver um resultado semelhante ao seguinte. O seu casulo bookthief terá um nome único anexado.

```Output
configmap/osm-config patched
```

Para verificar se o modo de tráfego permissivo foi desativado, volte para o bookbuyer ou para o bookthief pod para ver a sua UI no navegador e ver se os livros comprados ou os livros roubados já não estão a aumentar. Certifique-se de atualizar o navegador. Se o incremento tiver parado, a política foi corretamente aplicada. Conseguiu impedir o bookthief de roubar livros, mas nem o comprador de livros pode comprar na livraria nem a livraria pode recuperar livros da casa de apostas. Em seguida, implementaremos políticas [de SMI](https://smi-spec.io/) para permitir apenas os serviços na malha que você gostaria de comunicar para fazê-lo.

### <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>Aplicar políticas de acesso ao tráfego da Interface de Malha de Serviço (SMI)

Agora que desativamos todas as comunicações na malha, vamos permitir que o nosso serviço de bookbuyer comunique ao nosso serviço de livraria para a compra de livros, e permitir que o nosso serviço de livrarias comunique ao nosso serviço de bookwarehouse para recuperar livros para vender.

Implementar as seguintes políticas [de SMI.](https://smi-spec.io/)

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookstore-access-bookwarehouse
  namespace: bookwarehouse
spec:
  destination:
    kind: ServiceAccount
    name: bookwarehouse
    namespace: bookwarehouse
  rules:
  - kind: HTTPRouteGroup
    name: bookwarehouse-service-routes
    matches:
    - restock-books
  sources:
  - kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  - kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookwarehouse-service-routes
  namespace: bookwarehouse
spec:
  matches:
    - name: restock-books
      methods:
      - POST
      headers:
      - host: bookwarehouse.bookwarehouse
EOF
```

Deverá ver um resultado semelhante ao seguinte.

```Output
traffictarget.access.smi-spec.io/bookbuyer-access-bookstore-v1 created
httproutegroup.specs.smi-spec.io/bookstore-service-routes created
traffictarget.access.smi-spec.io/bookstore-access-bookwarehouse created
httproutegroup.specs.smi-spec.io/bookwarehouse-service-routes created
```

Agora pode configurar uma sessão de reencaminhamento de portos nas cápsulas de bookbuyer ou livraria e ver se tanto os livros comprados como os livros vendidos estão a voltar a aumentar. Você também pode fazer o mesmo para a cápsula bookthief para verificar que ainda não é capaz de roubar livros.

### <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>Aplicar políticas de divisão de tráfego da Interface de Malha de Serviço (SMI)

Para a nossa demonstração final, criaremos uma política de divisão de tráfego [SMI](https://smi-spec.io/) para configurar o peso das comunicações de um serviço para vários serviços como backend. A funcionalidade de divisão de tráfego permite-lhe mover progressivamente as ligações de um serviço para outro, ponderando o tráfego numa escala de 0 a 100.

O gráfico abaixo é um diagrama da política de Divisão de Tráfego [SMI](https://smi-spec.io/) a ser implementado. Vamos implantar uma livraria adicional versão 2 e depois dividir o tráfego de entrada do caixa de apostas, ponderando 25% do tráfego para o serviço de livraria v1 e 75% para o serviço de livraria v2.

![Diagrama dividido de tráfego de bookbuyer osM](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

Desdobre o serviço de livraria v2.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: v1
kind: Service
metadata:
  name: bookstore-v2
  namespace: bookstore
  labels:
    app: bookstore-v2
spec:
  ports:
  - port: 14001
    name: bookstore-port
  selector:
    app: bookstore-v2
---
# Deploy bookstore-v2 Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookstore-v2
  namespace: bookstore
---
# Deploy bookstore-v2 Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bookstore-v2
  namespace: bookstore
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bookstore-v2
  template:
    metadata:
      labels:
        app: bookstore-v2
    spec:
      serviceAccountName: bookstore-v2
      containers:
        - name: bookstore
          image: openservicemesh/bookstore:v0.8.0
          imagePullPolicy: Always
          ports:
            - containerPort: 14001
              name: web
          command: ["/bookstore"]
          args: ["--path", "./", "--port", "14001"]
          env:
            - name: BOOKWAREHOUSE_NAMESPACE
              value: bookwarehouse
            - name: IDENTITY
              value: bookstore-v2
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookbuyer-access-bookstore-v2
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
EOF
```

Deverá ver o seguinte resultado.

```Output
service/bookstore-v2 configured
serviceaccount/bookstore-v2 created
deployment.apps/bookstore-v2 created
traffictarget.access.smi-spec.io/bookstore-v2 created
```

Agora implemente a política de divisão de tráfego para dividir o tráfego de compradores entre as duas livrarias v1 e v2 serviço.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: split.smi-spec.io/v1alpha2
kind: TrafficSplit
metadata:
  name: bookstore-split
  namespace: bookstore
spec:
  service: bookstore.bookstore
  backends:
  - service: bookstore
    weight: 25
  - service: bookstore-v2
    weight: 75
EOF
```

Deverá ver o seguinte resultado.

```Output
trafficsplit.split.smi-spec.io/bookstore-split created
```

Instale um túnel portuário para a cápsula de caixa de livros e deverá agora ver os livros a serem comprados no serviço de v2 da livraria. Se continuar a assistir ao incremento das compras, deverá notar um incremento mais rápido das compras que acontecem através do serviço de livraria v2.

![Livros de bookbuyer osm](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)

## <a name="manage-existing-deployed-applications-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Gerir as aplicações implementadas existentes para ser geridas pelo complemento do Serviço de Malha De Serviço Azure Kubernetes (AKS)

### <a name="before-you-begin"></a>Antes de começar

Os passos detalhados neste walkthrough assumem que já ativou previamente o addon OSM AKS para o seu cluster AKS. Caso contrário, reveja a secção [Enable Open Service (OSM) Azure Kubernetes Service (AKS) add-on para um cluster AKS existente](#enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster) antes de prosseguir. Além disso, o seu cluster AKS precisa de ser versão Kubernetes `1.19+` e acima, ter Kubernetes RBAC ativado, e ter estabelecido uma `kubectl` ligação com o cluster (Se precisar de ajuda com algum destes itens, consulte o [quickstart AKS](./kubernetes-walkthrough.md), e instale o add-on AKS OSM.

Deve ter os seguintes recursos instalados:

- O Azure CLI, versão 2.20.0 ou mais tarde
- A `aks-preview` versão de extensão 0.5.5 ou mais tarde
- Versão OSM v0.8.0 ou mais tarde
- apt-get instalar jq

### <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Verifique a política de modo de tráfego permissivo de malha de serviço aberto (OSM)

O modo política de tráfego permissivo OSM é um modo em que a aplicação da política de tráfego [SMI](https://smi-spec.io/) é ignorada. Neste modo, a OSM descobre automaticamente serviços que fazem parte da malha de serviço e programa as regras de política de tráfego em cada enviado proxy sidecar para poder comunicar com estes serviços.

Para verificar o atual modo de tráfego permissivo de OSM para o seu cluster, execute o seguinte comando:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

A saída do mapa de configmap osmis deve parecer-se com o seguinte:

```Output
{
  "egress": "true",
  "enable_debug_server": "true",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Se o **permissive_traffic_policy_mode** estiver configurado para **ser verdadeiro,** pode embarcar com segurança nos espaços do seu nome sem qualquer interrupção nas suas comunicações de serviço-a-serviço. Se o **permissive_traffic_policy_mode** estiver configurado em **falso,** terá de garantir que tem os manifestos de acesso ao tráfego [SMI](https://smi-spec.io/) corretos, bem como garantir que tem uma conta de serviço representando cada serviço implantado no espaço de nomes. Siga as orientações para [aplicações implementadas a bordo com malha de serviço aberto (OSM) Política de Tráfego Permissivo configurada como Falsa](#onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false)

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Aplicações implementadas a bordo com malha de serviço aberto (OSM) Política de Tráfego Permissivo configurada como Verdadeira

A primeira coisa que vamos fazer é adicionar o espaço de nomes de aplicação implantado ao OSM para gerir.

```azurecli-interactive
osm namespace add bookstore
```

Deverá ver o seguinte resultado:

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

Em seguida, vamos dar uma olhada na implementação atual do pod no espaço de nomes. Executar o seguinte comando para ver as cápsulas no espaço de nome designado.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Deve ver a seguinte saída semelhante:

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

Note a coluna **READY** mostrando **1/1**, o que significa que a cápsula de aplicação tem apenas um recipiente. Em seguida, teremos de reiniciar as suas implementações de aplicação para que a OSM possa injetar o contentor de procuração sidecar enviado com a sua cápsula de aplicação. Vamos obter uma lista de implantações no espaço de nomes.

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Deverá ver o seguinte resultado:

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

Agora vamos reiniciar a implantação para injetar o contentor de procuração sidecar enviado com a sua cápsula de aplicação. Execute o seguinte comando.

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

Deverá ver o seguinte resultado:

```Output
deployment.apps/bookbuyer restarted
```

Se voltarmos a ver as cápsulas no espaço de nomes:

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Irá agora notar que a coluna **READY** está agora a mostrar **que 2/2** recipientes estão prontos para a sua cápsula. O segundo contentor é o enviado sidecar proxy.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

Podemos inspecionar ainda mais a cápsula para ver o representante do Enviado, executando o comando de descrever para ver a configuração.

```azurecli-interactive
kubectl describe pod bookbuyer-84446dd5bd-j4tlr -n bookbuyer
```

```Output
Containers:
  bookbuyer:
    Container ID:  containerd://b7503b866f915711002292ea53970bd994e788e33fb718f1c4f8f12cd4a88198
    Image:         openservicemesh/bookbuyer:v0.8.0
    Image ID:      docker.io/openservicemesh/bookbuyer@sha256:813874bd2dc9c5a259b9657995348cf0822b905e29c4e86f21fdefa0ef21dcee
    Port:          <none>
    Host Port:     <none>
    Command:
      /bookbuyer
    State:          Running
      Started:      Tue, 23 Mar 2021 10:52:53 -0400
    Ready:          True
    Restart Count:  0
    Environment:
      BOOKSTORE_NAMESPACE:  bookstore
      BOOKSTORE_SVC:        bookstore
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from bookbuyer-token-zft2r (ro)
  envoy:
    Container ID:  containerd://f5f1cb5db8d5304e23cc984eb08146ea162a3e82d4262c4472c28d5579c25e10
    Image:         envoyproxy/envoy-alpine:v1.17.1
    Image ID:      docker.io/envoyproxy/envoy-alpine@sha256:511e76b9b73fccd98af2fbfb75c34833343d1999469229fdfb191abd2bbe3dfb
    Ports:         15000/TCP, 15003/TCP, 15010/TCP
    Host Ports:    0/TCP, 0/TCP, 0/TCP
```

Verifique se a sua aplicação ainda está funcional após a injeção de procuração do sidecar enviado.

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Aplicações implementadas a bordo com malha de serviço aberto (OSM) Política de Tráfego Permissivo configurada como Falsa

Quando a configuração osm para a política de tráfego permissiva estiver definida, a `false` OSM exigirá políticas explícitas de acesso ao tráfego [SMI](https://smi-spec.io/) implementadas para que a comunicação serviço-a-serviço aconteça dentro do seu cluster. Atualmente, a OSM também utiliza as contas de serviço da Kubernetes como parte da autorização de comunicações de serviço-a-serviço também. Para garantir que as aplicações implementadas existentes comunicarão quando geridas pela malha OSM, teremos de verificar a existência de uma conta de serviço para utilizar, atualizar a implementação da aplicação com a informação da conta de serviço, aplicar as políticas de acesso ao tráfego do [SMI.](https://smi-spec.io/)

#### <a name="verify-kubernetes-service-accounts"></a>Verificar contas de serviço da Kubernetes

Verifique se tem uma conta de serviço kubernetes no espaço de nome para onde a sua aplicação está implantada.

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

No seguinte, existe uma conta de serviço nomeada no espaço de nomes do `bookbuyer` comprador de livros.

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

Se não tiver uma conta de serviço listada para além da conta predefinida, terá de criar uma para a sua aplicação. Use o seguinte comando como exemplo para criar uma conta de serviço no espaço de nomes implantado da aplicação.

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

#### <a name="view-your-applications-current-deployment-specification"></a>Consulte a especificação de implementação atual da sua aplicação

Se teve de criar uma conta de serviço a partir da secção anterior, é provável que a sua implementação de candidatura não esteja configurada com uma `serviceAccountName` especificação específica na especificação de implementação. Podemos ver as especificações de implantação da sua aplicação com os seguintes comandos:

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Uma lista de implementações será listada na saída.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

Vamos agora descrever a implementação como um cheque para ver se existe uma conta de serviço listada na secção Modelo de Pod.

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

Nesta implementação em particular, pode ver que existe uma conta de serviço associada à implementação listada na secção Modelo de Pod. Esta implementação está a utilizar o corretor de contas de serviço. Se não vir a **Conta Serivce:** propriedade, a sua implantação não está configurada para utilizar uma conta de serviço.

```Output
Pod Template:
  Labels:           app=bookbuyer
                    version=v1
  Annotations:      kubectl.kubernetes.io/restartedAt: 2021-03-23T10:52:49-04:00
  Service Account:  bookbuyer
  Containers:
   bookbuyer:
    Image:      openservicemesh/bookbuyer:v0.8.0

```

Existem várias técnicas para atualizar a sua implementação para adicionar uma conta de serviço kubernetes. Reveja a documentação da Kubernetes sobre [a atualização de uma linha de implementação](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment) ou [configure contas de serviço para pods](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/). Uma vez atualizada a sua especificação de implantação com a conta de serviço, reimplante (kubectl apply -f your-deployment.yaml) a sua implantação para o cluster.

#### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>Implementar as políticas necessárias de Interface de Malha de Serviço (SMI)

O último passo para permitir que o tráfego autorizado flua na malha é implementar as políticas de acesso ao tráfego [de tráfego necessárias](https://smi-spec.io/) para a sua aplicação. A quantidade de configuração que pode obter com as políticas de acesso ao tráfego [SMI](https://smi-spec.io/) está fora do âmbito desta passagem, mas vamos detalhar alguns dos componentes comuns da especificação e mostrar como configurar uma política simples do TrafficTarget e do HTTPRouteGroup para permitir a comunicação de serviço-a-serviço para a sua aplicação.

A especificação de [**Controlo de Acesso a Tráfego**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control) [SMI](https://smi-spec.io/) permite que os utilizadores definam a política de controlo de acesso para as suas aplicações. Vamos focar-nos nos recursos do **TrafficTarget** e **HTTPRoutGroup** api.

O recurso TrafficTarget consiste em três configurações principais destino, regras e fontes. Um exemplo do TrafficTarget é mostrado abaixo.

```TrafficTarget Example spec
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
```

Na especificação do TrafficTarget acima, a `destination` conta de serviço que está configurada para o serviço de origem de destino. Lembre-se que a conta de serviço que foi adicionada à implementação mais cedo será usada para autorizar o acesso à implantação a que está anexada. A `rules` secção , neste exemplo particular, define o tipo de tráfego HTTP que é permitido sobre a ligação. Pode configurar padrões finos de regex de grãos finos para que os cabeçalhos HTTP sejam específicos sobre o tráfego permitido através de HTTP. A `sources` secção é o serviço de comunicações originárias. Esta especificação diz que o comprador precisa de comunicar à livraria.

O recurso HTTPRouteGroup é composto por um ou um conjunto de partidas de informações de cabeçalho HTTP e é um requisito para a especificação TrafficTarget. No exemplo abaixo, pode ver que o HTTPRouteGroup está a autorizar três ações HTTP, duas GET e um POST.

```HTTPRouteGroup Example Spec
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
```

Se não estiver familiarizado com o tipo de tráfego HTTP que a sua aplicação frontal faz para outros níveis da aplicação, uma vez que a especificação TrafficTarget requer uma regra, pode criar o equivalente a uma regra de permitir toda a regra utilizando a especificação abaixo para HTTPRouteGroup.

```HTTPRouteGroup Allow All Example
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: allow-all
  namespace: yournamespace
spec:
  matches:
  - name: allow-all
    pathRegex: '.*'
    methods: ["GET","PUT","POST","DELETE","PATCH"]
```

Assim que configurar as especificações TrafficTarget e HTTPRouteGroup, pode juntá-las como um YAML e implementar. Abaixo está a configuração do exemplo da livraria.

```Bookstore Example TrafficTarget and HTTPRouteGroup configuration
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
EOF
```

Visite o site [do SMI](https://smi-spec.io/) para obter informações mais detalhadas sobre a especificação.

### <a name="manage-the-applications-namespace-with-osm"></a>Gerir o espaço de nome da aplicação com o OSM

Em seguida, vamos configurar o OSM para gerir o espaço de nomes e reiniciar as implementações para obter o proxy sidecar enviado injetado com a aplicação.

Executar o seguinte comando para configurar o `azure-vote` espaço de nomes para ser gerido o meu OSM.

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

Em seguida reinicie os `azure-vote-front` mesmos e `azure-vote-back` os destacamentos com os seguintes comandos.

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

Se virmos as cápsulas para o `azure-vote` espaço de nome, veremos a fase **READY** tanto do `azure-vote-front` `azure-vote-back` 2/2, o que significa que o representante do sidecar enviado foi injetado ao lado da aplicação.

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>Tutorial: Implementar uma aplicação gerida por Rede de Serviço Aberto (OSM) com entrada NGINX

A Rede de Serviço Aberto (OSM) é uma malha de serviço nativa leve, extensível que permite aos utilizadores gerir, proteger e obter funcionalidades de observabilidade fora da caixa para ambientes de microserviços altamente dinâmicos.

Neste tutorial, vai:

> [!div class="checklist"]
>
> - Ver a configuração atual do cluster OSM
> - Criar o espaço de nomes para os OSM gerirem as aplicações implementadas no ou no espaço de nomes
> - A bordo dos espaços de nome a gerir pela OSM
> - Implementar o exemplo de aplicação
> - Verifique a aplicação que está a decorrer dentro do cluster AKS
> - Criar um controlador de entrada NGINX utilizado para a aplicação
> - Expor um serviço através da entrada do Azure Application Gateway para a internet

### <a name="before-you-begin"></a>Antes de começar

Os passos detalhados neste artigo assumem que criou um cluster AKS (Kubernetes `1.19+` e acima, com o RBAC de Kubernetes ativado), estabeleceu uma `kubectl` ligação com o cluster (Se precisar de ajuda com algum destes itens, consulte o [quickstart AKS](./kubernetes-walkthrough.md), e instale o addon AKS OSM.

Deve ter os seguintes recursos instalados:

- O Azure CLI, versão 2.20.0 ou mais tarde
- A `aks-preview` versão de extensão 0.5.5 ou mais tarde
- Versão OSM v0.8.0 ou mais tarde
- apt-get instalar jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Ver e verificar a configuração atual do cluster OSM

Uma vez ativado o addon OSM para AKS no cluster AKS, pode visualizar os parâmetros de configuração atuais no osm-config Kubernetes ConfigMap. Executar o seguinte comando para ver as propriedades ConfigMap:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

A saída mostra a configuração atual do OSM para o cluster.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Note que o **permissive_traffic_policy_mode** está configurado para **ser verdadeiro**. O modo de política de tráfego permissivo em OSM é um modo em que a aplicação da política de tráfego [SMI](https://smi-spec.io/) é ignorada. Neste modo, a OSM descobre automaticamente serviços que fazem parte da malha de serviço e programa as regras de política de tráfego em cada enviado proxy sidecar para poder comunicar com estes serviços.

### <a name="create-namespaces-for-the-application"></a>Criar espaços de nome para a aplicação

Neste tutorial utilizamos a aplicação da livraria OSM que tem os seguintes componentes de aplicação:

- bookbuyer
- bookthief
- livraria
- casa de bookware

Crie espaços de nome para cada um destes componentes de aplicação.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Deverá ver o seguinte resultado:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>A bordo dos espaços de nome a gerir pela OSM

A adição dos espaços de nome à malha OSM permitirá ao controlador OSM injetar automaticamente os recipientes de procuração sidecar enviados com a sua aplicação. Executar o seguinte comando a bordo dos espaços de nome de aplicações da livraria OSM.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Deverá ver o seguinte resultado:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Implementar a aplicação da Livraria para o cluster AKS

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Todas as saídas de implantação são resumidas abaixo.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>Atualizar o Serviço de Compradores de Livros

Atualize o serviço de compradores de livros para a configuração correta da porta de entrada com o seguinte manifesto de serviço.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Verifique a aplicação da Livraria que está a decorrer dentro do cluster AKS

A partir de agora, implantamos a aplicação de mulita-contentor da livraria, mas só está acessível dentro do cluster AKS. Mais tarde, adicionaremos o controlador de entrada de entrada de gateway de aplicação Azure para expor a aplicação fora do cluster AKS. Para verificar se a aplicação está a decorrer dentro do cluster, usaremos uma porta para visualizar o componente de entrada de livros UI.

Primeiro vamos pegar o nome do comprador de livros

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Deverá ver um resultado semelhante ao seguinte. O seu casulo de compradores terá um nome único anexado.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Assim que tivermos o nome da cápsula, podemos agora usar o comando portuário para configurar um túnel do nosso sistema local para a aplicação dentro do cluster AKS. Executar o seguinte comando para configurar o porto para a frente para a porta de sistema local 8080. Utilize novamente o nome do seu pod de comprador de livros especificado.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Devia ver uma saída semelhante a esta.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Enquanto a sessão de reencaminhamento da porta estiver no lugar, navegue para o seguinte url a partir de um browser `http://localhost:8080` . Deverá agora poder ver a aplicação de compradores de livros UI no navegador semelhante à imagem abaixo.

![Aplicação de compradores de livros OSM para imagem NGINX UI](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>Criar um controlador de entrada NGINX no Serviço Azure Kubernetes (AKS)

Um controlador de entrada é uma peça de software que fornece procuração inversa, encaminhamento de tráfego configurável e rescisão de TLS para serviços Kubernetes. Os recursos de entrada de Kubernetes são usados para configurar as regras e rotas de entrada para serviços individuais de Kubernetes. Utilizando um controlador de entrada e regras de entrada, um único endereço IP pode ser usado para encaminhar o tráfego para vários serviços num cluster kubernetes.

Utilizaremos o controlador de entrada para expor a aplicação gerida pela OSM à internet. Para criar o controlador de entrada, utilize o Helm para instalar a entrada de nginx. Para uma maior redundância, são implementadas duas réplicas dos controladores de entrada do NGINX com o parâmetro `--set controller.replicaCount`. Para beneficiar totalmente da execução de réplicas do controlador de entrada, certifique-se de que há mais de um nó no seu cluster AKS.

O controlador de entrada também tem de estar agendado num nó do Linux. Os nós do Windows Server não devem executar o controlador de entrada. É especificado um seletor de nós com o parâmetro `--set nodeSelector` para indicar ao agendador do Kubernetes que execute o controlador de entrada do NGINX num nó baseado no Linux.

> [!TIP]
> O exemplo a seguir cria um espaço de nome Kubernetes para os recursos _ingressos denominados ingress-basic_. Especifique um espaço de nome para o seu próprio ambiente, conforme necessário.

```azurecli-interactive
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Update the helm repo(s)
helm repo update

# Use Helm to deploy an NGINX ingress controller in the ingress-basic namespace
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=1 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Quando o serviço de balançador de carga Kubernetes é criado para o controlador de entrada NGINX, é atribuído um endereço IP público dinâmico, como mostra a saída do exemplo seguinte:

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Ainda não foram criadas regras de entrada, pelo que a página padrão 404 do controlador NGINX é apresentada se navegar para o endereço IP interno. As regras de ingress são configuradas nos seguintes passos.

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>Expor o serviço de compradores de livros à internet

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: nginx

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

Deverá ver o seguinte resultado:

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

### <a name="view-the-nginx-logs"></a>Ver os registos NGINX

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

A saída mostra o estado do controlador de entrada NGINX quando a regra de entrada foi aplicada com sucesso:

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

### <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>Ver os serviços NGINX e o serviço de bookbuyer externamente

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

Uma vez que o nome do anfitrião no manifesto de entrada é um nome psuedo usado para testes, o nome DNS não estará disponível na internet. Em alternativa, podemos utilizar o programa curl e passar o cabeçalho do nome anfitrião para o endereço IP público NGINX e receber um código de 200 que nos liga com sucesso ao serviço de compradores de livros.

```azurecli-interactive
curl -H 'Host: bookbuyer.contoso.com' http://EXTERNAL-IP/
```

Deverá ver o seguinte resultado:

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>1833</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>1556</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 15:02:53 UTC</strong>
  </body>
</html>
```

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>Tutorial: Implementar uma aplicação gerida por Rede de Serviço Aberto (OSM) utilizando o add-on Agress AKS da Aplicação AZure

A Rede de Serviço Aberto (OSM) é uma malha de serviço nativa leve, extensível que permite aos utilizadores gerir, proteger e obter funcionalidades de observabilidade fora da caixa para ambientes de microserviços altamente dinâmicos.

Neste tutorial, vai:

> [!div class="checklist"]
>
> - Ver a configuração atual do cluster OSM
> - Criar o espaço de nomes para os OSM gerirem as aplicações implementadas no ou no espaço de nomes
> - A bordo dos espaços de nome a gerir pela OSM
> - Implementar o exemplo de aplicação
> - Verifique a aplicação que está a decorrer dentro do cluster AKS
> - Criar um Gateway de aplicação Azure para ser usado como controlador de entrada para a aplicação
> - Expor um serviço através da entrada do Azure Application Gateway para a internet

### <a name="before-you-begin"></a>Antes de começar

Os passos detalhados neste artigo assumem que criou um cluster AKS (Kubernetes `1.19+` e acima, com o Site DENSC de Kubernetes), estabeleceu uma `kubectl` ligação com o cluster (Se precisar de ajuda com algum destes itens, então veja o [quickstart AKS,](./kubernetes-walkthrough.md)instalou o add-on AKS OSM, e estará a criar um novo Gateway de Aplicação Azure para ingress.

Deve ter os seguintes recursos instalados:

- O Azure CLI, versão 2.20.0 ou mais tarde
- A `aks-preview` versão de extensão 0.5.5 ou mais tarde
- AKS cluster versão 1.19+ utilizando a rede Azure CNI (anexada a um Azure Vnet)
- Versão OSM v0.8.0 ou mais tarde
- apt-get instalar jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Ver e verificar a configuração atual do cluster OSM

Uma vez ativado o addon OSM para AKS no cluster AKS, pode visualizar os parâmetros de configuração atuais no osm-config Kubernetes ConfigMap. Executar o seguinte comando para ver as propriedades ConfigMap:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

A saída mostra a configuração atual do OSM para o cluster.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Note que o **permissive_traffic_policy_mode** está configurado para **ser verdadeiro**. O modo de política de tráfego permissivo em OSM é um modo em que a aplicação da política de tráfego [SMI](https://smi-spec.io/) é ignorada. Neste modo, a OSM descobre automaticamente serviços que fazem parte da malha de serviço e programa as regras de política de tráfego em cada enviado proxy sidecar para poder comunicar com estes serviços.

### <a name="create-namespaces-for-the-application"></a>Criar espaços de nome para a aplicação

Neste tutorial utilizamos a aplicação da livraria OSM que tem os seguintes componentes de aplicação:

- bookbuyer
- bookthief
- livraria
- casa de bookware

Crie espaços de nome para cada um destes componentes de aplicação.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Deverá ver o seguinte resultado:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>A bordo dos espaços de nome a gerir pela OSM

Quando adicionar os espaços de nome à malha OSM, isto permitirá ao controlador OSM injetar automaticamente os recipientes de procuração sidecar envoy com a sua aplicação. Executar o seguinte comando a bordo dos espaços de nome de aplicações da livraria OSM.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Deverá ver o seguinte resultado:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Implementar a aplicação da Livraria para o cluster AKS

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Todas as saídas de implantação são resumidas abaixo.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>Atualizar o Serviço de Compradores de Livros

Atualize o serviço de compradores de livros para a configuração correta da porta de entrada com o seguinte manifesto de serviço.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Verifique a aplicação da Livraria que está a decorrer dentro do cluster AKS

A partir de agora, implantamos a aplicação multi-contentores da livraria, mas só está acessível a partir do cluster AKS. Mais tarde, adicionaremos o controlador de entrada de entrada de gateway de aplicação Azure para expor a aplicação fora do cluster AKS. Para verificar se a aplicação está a decorrer dentro do cluster, usaremos uma porta para visualizar o componente de entrada de livros UI.

Primeiro vamos pegar o nome do comprador de livros

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Deverá ver um resultado semelhante ao seguinte. O seu casulo de compradores terá um nome único anexado.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Assim que tivermos o nome da cápsula, podemos agora usar o comando portuário para configurar um túnel do nosso sistema local para a aplicação dentro do cluster AKS. Executar o seguinte comando para configurar o porto para a frente para a porta de sistema local 8080. Use novamente o nome específico do seu pod de comprador de livros.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Devia ver uma saída semelhante a esta.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Enquanto a sessão de reencaminhamento da porta estiver no lugar, navegue para o seguinte url a partir de um browser `http://localhost:8080` . Deverá agora poder ver a aplicação de compradores de livros UI no navegador semelhante à imagem abaixo.

![Aplicação de compradores de livros OSM para imagem de UI app Gateway](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application-outside-the-aks-cluster"></a>Crie um Gateway de aplicação Azure para expor a aplicação do comprador de livros fora do cluster AKS

> [!NOTE]
> As seguintes direções criarão uma nova instância do Gateway de Aplicação Azure para ser usado para entrada. Se tiver um Gateway de Aplicação Azure existente que deseje utilizar, salte para a secção para ativar o addon do Controlador de Entradas de Gateway de Aplicação.

#### <a name="deploy-a-new-application-gateway"></a>Implementar um novo Gateway de Aplicações

> [!NOTE]
> Estamos a fazer referência à documentação existente para permitir o addon do Controlador de Entrada de Aplicação para um cluster AKS existente. Foram feitas algumas modificações que se adequam aos materiais OSM. Documentação mais detalhada sobre o assunto pode ser consultada [aqui.](https://docs.microsoft.com/azure/application-gateway/tutorial-ingress-controller-add-on-existing)

Irá agora implementar um novo Gateway de Aplicações, para simular ter um Gateway de Aplicação existente que pretende utilizar para carregar o tráfego de equilíbrio para o seu cluster AKS, _myCluster_. O nome do Gateway de Aplicação será _myApplicationGateway,_ mas primeiro terá de criar um recurso IP público, chamado _myPublicIp,_ e uma nova rede virtual chamada _myVnet_ com espaço de endereço 11.0.0.0/8, e uma sub-rede com o espaço de endereço 11.1.0.0/16 chamada _mySubnet_, e implementar o seu Gateway de aplicação no _meuSubnet_ usando _o meuPubnet_.

Ao utilizar um cluster AKS e Gateway de aplicação em redes virtuais separadas, os espaços de endereço das duas redes virtuais não devem sobrepor-se. O espaço de endereço predefinido que um cluster AKS implementa é de 10.0.0.0/8, pelo que definimos o prefixo de endereço virtual do Gateway de Aplicação para 11.0.0.0/8.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> O controlador de entrada de entrada de aplicação (AGIC) **suporta apenas** o Application Gateway v2 SKUs (Standard e WAF), e **não** o Gateway de Aplicação v1 SKUs.

#### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>Ativar o addon AGIC para um cluster AKS existente através do Azure CLI

Se quiser continuar a utilizar o Azure CLI, pode continuar a ativar o addon AGIC no cluster AKS que criou, _myCluster,_ e especificar o addon AGIC para utilizar o Gateway de Aplicação existente que criou, _myApplicationGateway_.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

Pode verificar se o addon Azure Application Gateway AKS foi ativado pelo seguinte comando.

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

Este comando deve mostrar a saída como `true` .

#### <a name="peer-the-two-virtual-networks-together"></a>Par as duas redes virtuais juntas

Uma vez que implementámos o cluster AKS na sua própria rede virtual e o Application Gateway noutra rede virtual, terás de espreitar as duas redes virtuais em conjunto para que o tráfego flua do Gateway de Aplicação para as cápsulas do cluster. Espreitar as duas redes virtuais requer executar o comando Azure CLI duas vezes diferentes, para garantir que a ligação é bidisal. O primeiro comando criará uma ligação de espreitar da rede virtual Application Gateway para a rede virtual AKS; o segundo comando criará uma ligação de espreitar na outra direção.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>Expor o serviço de compradores de livros à internet

Aplique o seguinte manifesto de entrada no cluster AKS para expor o serviço de compradores de livros à internet através do Azure Application Gateway.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

Deverá ver a seguinte saída

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

Uma vez que o nome do anfitrião no manifesto de entrada é um pseudo-nome usado para testes, o nome DNS não estará disponível na internet. Em alternativa, podemos utilizar o programa curl e passar o cabeçalho do nome anfitrião para o endereço IP público Azure Application Gateway e receber um código de 200 que nos liga com sucesso ao serviço de compradores de livros.

```azurecli-interactive
appGWPIP=$(az network public-ip show -g MyResourceGroup -n myPublicIp -o tsv --query "ipAddress")
curl -H 'Host: bookbuyer.contoso.com' http://$appGWPIP/
```

Deverá ver a seguinte saída

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>5969</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>5692</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 16:34:30 UTC</strong>
  </body>
</html>
```

### <a name="troubleshooting"></a>Resolução de problemas

- [Documentação de resolução de problemas da AGIC](https://docs.microsoft.com/azure/application-gateway/ingress-controller-troubleshoot)
- [Ferramentas adicionais de resolução de problemas estão disponíveis no repo GitHub da AGIC](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)

## <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Monitorização e Observabilidade da Malha de Serviço Aberto (OSM) utilizando insights de monitores e aplicações do Azure Monitor e aplicações

Tanto o Azure Monitor como o Azure Application Insights ajudam-no a maximizar a disponibilidade e desempenho das suas aplicações e serviços, fornecendo uma solução abrangente para recolher, analisar e agir em telemetria a partir dos ambientes da sua nuvem e no local.

O addon OSM AKS terá integrações profundas em ambos estes serviços Azure, e fornecerá uma experiência Azure aparentemente sem sentido para visualização e resposta a KPI's críticos fornecidos pelas métricas OSM. Para obter mais informações sobre como ativar e configurar estes serviços para o addon OSM AKS, visite o [Monitor Azure para](https://aka.ms/azmon/osmpreview) obter mais informações.

## <a name="tutorial-manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>Tutorial: Implementar manualmente Prometheus, Grafana e Jaeger para ver métricas de malha de serviço aberto (OSM) para observabilidade

> [!WARNING]
> A instalação de Prometheus, Grafana e Jaeger são fornecidas como orientação geral para mostrar como estas ferramentas podem ser utilizadas para visualizar dados métricos da OSM. A orientação de instalação não deve ser utilizada para uma instalação de produção. Consulte a documentação de cada ferramenta sobre a melhor forma de adequar as suas instalações às suas necessidades. O mais notável será a falta de armazenamento persistente, o que significa que todos os dados são perdidos uma vez que um Prometheus Grafana, e/ou jaeger pod(s) são terminados.

A Malha de Serviço Aberto (OSM) gera métricas detalhadas relacionadas com todo o tráfego dentro da malha. Estas métricas fornecem insights sobre o comportamento das aplicações na malha ajudando os utilizadores a resolver problemas, manter e analisar as suas aplicações.

A partir de hoje, o OSM recolhe métricas diretamente dos proxies sidecar (Enviado). A OSM fornece métricas ricas para o tráfego de entrada e saída para todos os serviços na malha. Com estas métricas, o utilizador pode obter informações sobre o volume global de tráfego, erros dentro do tráfego e o tempo de resposta para pedidos.

A OSM utiliza a Prometheus para recolher e armazenar métricas de tráfego consistentes e estatísticas para todas as aplicações em execução na malha. Prometheus é um kit de ferramentas de monitorização e alerta de código aberto, que é comumente usado em (mas não se limitando a) kubernetes e ambientes de malha de serviço.

Cada aplicação que faz parte da malha corre em um Pod que contém um sidecar enviado que expõe métricas (métricas de procuração) no formato Prometeu. Além disso, cada Pod que faz parte da malha tem anotações de Prometeu, o que permite que o servidor Prometheus raspe a aplicação dinamicamente. Este mecanismo permite automaticamente a raspagem de métricas sempre que um novo espaço de nome/pod/serviço é adicionado à malha.

As métricas osm podem ser vistas com Grafana, que é um software de visualização e análise de código aberto. Permite-lhe consultar, visualizar, alertar e explorar as suas métricas.

Neste tutorial, vai:

> [!div class="checklist"]
>
> - Criar e implementar uma instância Prometeu
> - Configure OSM para permitir a raspagem de Prometeu
> - Atualizar o Configmap Prometeu
> - Criar e implementar uma instância Grafana
> - Configure Grafana com a fonte de dados Prometheus
> - Painel DE IMPORTAÇÃO OSM para Grafana
> - Criar e implementar uma instância Jaeger
> - Configurar o rastreio de Jaeger para ossm

### <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>Implementar e configurar uma instância Prometeu para a OSM

Usaremos Helm para implantar o caso Prometeu. Executar os seguintes comandos para instalar Prometeu via Helm:

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

Deve ver uma saída semelhante abaixo se a instalação tiver sido bem sucedida. Tome nota da porta do servidor Prometheus e do nome DNS do cluster. Esta informação será usada mais tarde para configurar Prometeu como fonte de dados para Grafana.

```Output
NAME: stable
LAST DEPLOYED: Fri Mar 26 13:34:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-server.default.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090


The Prometheus alertmanager can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-alertmanager.default.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been moved to a global property.  #####
######            use .Values.podSecurityPolicy.enabled with pod-based      #####
######            annotations                                               #####
######            (e.g. .Values.nodeExporter.podSecurityPolicy.annotations) #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
stable-prometheus-pushgateway.default.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
```

#### <a name="configure-osm-to-allow-prometheus-scraping"></a>Configure OSM para permitir a raspagem de Prometeu

Para garantir que os componentes OSM estão configurados para raspas de Prometeu, vamos querer verificar a configuração **prometheus_scraping** localizada no ficheiro configurar osm-config. Ver a configuração com o seguinte comando:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

A saída do comando anterior deve regressar `true` se o OSM estiver configurado para a raspagem de Prometeu. Se o valor devolvido `false` for, teremos de atualizar a configuração para ser `true` . Executar o seguinte comando para **ligar** a raspagem OSM Prometheus:

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

Deverá ver o seguinte resultado.

```Output
configmap/osm-config patched
```

#### <a name="update-the-prometheus-configmap"></a>Atualizar o Configmap Prometeu

A instalação padrão de Prometeu conterá dois configmaps kubernetes. Pode ver a lista de Prometeu configmaps com o seguinte comando.

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

Teremos de substituir a configuração prometheus.yml localizada no **configmap estável-prometheus-servidor** com a seguinte configuração OSM. Existem várias técnicas de edição de ficheiros para realizar esta tarefa. Uma forma simples e segura é exportar o configmap, criar uma cópia para cópia de segurança e depois editá-la com um editor como código Visual Studio.

> [!NOTE]
> Se não tiver o Código do Estúdio Visual instalado, pode descarregar e instalá-lo [aqui](https://code.visualstudio.com/Download).

Vamos primeiro exportar o configmap **do servidor prometheus estável** e depois fazer uma cópia para cópia.

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

Em seguida, vamos abrir o ficheiro usando o Código do Estúdio Visual para editar.

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

Uma vez aberto o configmap no editor do Código do Estúdio Visual, substitua o ficheiro prometheus.yml pela configuração OSM abaixo e guarde o ficheiro.

> [!WARNING]
> É extremamente importante que garanta que mantém a estrutura de indenção do ficheiro yaml. Quaisquer alterações na estrutura do ficheiro yaml podem resultar na não aplicação do configmap.

```OSM Prometheus Configmap Configuration
prometheus.yml: |
    global:
      scrape_interval: 10s
      scrape_timeout: 10s
      evaluation_interval: 1m

    scrape_configs:
      - job_name: 'kubernetes-apiservers'
        kubernetes_sd_configs:
        - role: endpoints
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          # TODO need to remove this when the CA and SAN match
          insecure_skip_verify: true
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(apiserver_watch_events_total|apiserver_admission_webhook_rejection_count)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https

      - job_name: 'kubernetes-nodes'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics

      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
        - role: pod
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(envoy_server_live|envoy_cluster_upstream_rq_xx|envoy_cluster_upstream_cx_active|envoy_cluster_upstream_cx_tx_bytes_total|envoy_cluster_upstream_cx_rx_bytes_total|envoy_cluster_upstream_cx_destroy_remote_with_active_rq|envoy_cluster_upstream_cx_connect_timeout|envoy_cluster_upstream_cx_destroy_local_with_active_rq|envoy_cluster_upstream_rq_pending_failure_eject|envoy_cluster_upstream_rq_pending_overflow|envoy_cluster_upstream_rq_timeout|envoy_cluster_upstream_rq_rx_reset|^osm.*)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: source_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: source_pod_name
        - regex: '(__meta_kubernetes_pod_label_app)'
          action: labelmap
          replacement: source_service
        - regex: '(__meta_kubernetes_pod_label_osm_envoy_uid|__meta_kubernetes_pod_label_pod_template_hash|__meta_kubernetes_pod_label_version)'
          action: drop
        # for non-ReplicaSets (DaemonSet, StatefulSet)
        # __meta_kubernetes_pod_controller_kind=DaemonSet
        # __meta_kubernetes_pod_controller_name=foo
        # =>
        # workload_kind=DaemonSet
        # workload_name=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          target_label: source_workload_kind
        - source_labels: [__meta_kubernetes_pod_controller_name]
          action: replace
          target_label: source_workload_name
        # for ReplicaSets
        # __meta_kubernetes_pod_controller_kind=ReplicaSet
        # __meta_kubernetes_pod_controller_name=foo-bar-123
        # =>
        # workload_kind=Deployment
        # workload_name=foo-bar
        # deplyment=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          regex: ^ReplicaSet$
          target_label: source_workload_kind
          replacement: Deployment
        - source_labels:
          - __meta_kubernetes_pod_controller_kind
          - __meta_kubernetes_pod_controller_name
          action: replace
          regex: ^ReplicaSet;(.*)-[^-]+$
          target_label: source_workload_name

      - job_name: 'smi-metrics'
        kubernetes_sd_configs:
        - role: pod
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: 'envoy_.*osm_request_(total|duration_ms_(bucket|count|sum))'
          action: keep
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_(\d{3})_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: response_code
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_total
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_total
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_total)
          target_label: __name__

        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_duration_ms_(bucket|sum|count))
          target_label: __name__

      - job_name: 'kubernetes-cadvisor'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(container_cpu_usage_seconds_total|container_memory_rss)'
          action: keep
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
```

Aplique o ficheiro configmap yaml atualizado com o seguinte comando.

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> Pode receber uma mensagem sobre a falta de anotação de kubernetes. Isto pode ser ignorado por enquanto.

#### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>Verifique se o Prometeu está configurado para raspar a malha OSM e os pontos finais da API

Para verificar se o Prometeu está corretamente configurado para raspar a malha OSM e os pontos finais da API, vamos encaminhar-nos para a cápsula Prometheus e ver a configuração do alvo. Execute os seguintes comandos.

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

Abra um navegador até `http://localhost:9090/targets`

Se rolar para baixo, deve ver todos os pontos finais métricos SMI serem **UP,** bem como outras métricas de OSM definidas como ilustrado abaixo.

![OsM Prometheus Target Metrics UI imagem](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

### <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>Implementar e configurar uma instância grafana para a OSM

Usaremos o Helm para implantar o caso Grafana. Executar os seguintes comandos para instalar Grafana via Helm:

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

Em seguida, recuperaremos a senha padrão de Grafana para entrar no site da Grafana.

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

Tome nota da senha de Grafana.

Em seguida, recuperaremos o pod grafana para avançar para o tablier Grafana para iniciar sessão.

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

Abra um navegador até `http://localhost:3000`

No ecrã de login abaixo, **introduza** o administrador como nome de utilizador e use a palavra-passe grafana capturada anteriormente.

![Imagem UI da página de login da OSM Grafana](./media/aks-osm-addon/osm-grafana-ui-login.png)

#### <a name="configure-the-grafana-prometheus-data-source"></a>Configure a fonte de dados grafana Prometheus

Uma vez que tenha iniciado sessão com sucesso em Grafana, o próximo passo é adicionar Prometheus como fonte de dados para Grafana. Para tal, navegue no ícone de configuração no menu esquerdo e selecione Fontes de Dados como mostrado abaixo.

![Imagem UI de página de fontes de dados OSM Grafana](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

Clique no botão **Adicionar fonte de dados** e selecione Prometheus em bases de dados de séries de tempo.

![Imagem UI de página de fontes de dados OSM Grafana](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

Na **configuração da sua fonte de dados Prometheus abaixo,** insira o cluster Kubernetes FQDN para o serviço Prometheus para a definição DE URL HTTP. O FQDN predefinido deve ser `stable-prometheus-server.default.svc.cluster.local` . Uma vez introduzido o ponto final do serviço Prometheus, percorra para a parte inferior da página e selecione **Save & Test**. Deverá receber uma caixa de verificação verde indicando que a fonte de dados está a funcionar.

#### <a name="importing-osm-dashboards"></a>Importar dashboards OSM

Os dashboards OSM estão disponíveis tanto através de:

- [Nosso repositório](https://github.com/grafana/grafana), e são importados como json blobs através do portal de administração web
- ou [online em Grafana.com](https://grafana.com/grafana/dashboards/14145)

Para importar um dashboard, procure o `+` sinal no menu esquerdo e selecione `import` .
Pode importar diretamente o dashboard através da sua `Grafana.com` identificação. Por exemplo, o nosso `OSM Mesh Details` painel de instrumentos utiliza o ID, `14145` pode utilizar o ID diretamente no formulário e selecionar: `import`

![Imagem UI da página uI da página de importação do painel osm Grafana](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

Assim que selecionar a importação, irá levá-lo automaticamente ao seu painel de instrumentos importado.

![OsM Grafana Dashboard Malha De Malha Imagem UI](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

### <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>Implementar e configurar um operador Jaeger em Kubernetes para os OSM

[Jaeger](https://www.jaegertracing.io/) é um sistema de rastreio de código aberto usado para monitorizar e resolver problemas de sistemas distribuídos. Pode ser implantado com a OSM como uma nova instância ou pode trazer o seu próprio exemplo. As seguintes instruções implantam uma nova instância de Jaeger no `jaeger` espaço de nomes no cluster AKS.

#### <a name="deploy-jaeger-to-the-aks-cluster"></a>Implemente o Jaeger no cluster AKS

Aplique o seguinte manifesto para instalar o Jaeger:

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
        - name: jaeger
          image: jaegertracing/all-in-one
          args:
          - --collector.zipkin.host-port=9411
          imagePullPolicy: IfNotPresent
          ports:
          - containerPort: 9411
          resources:
            limits:
              cpu: 500m
              memory: 512M
            requests:
              cpu: 100m
              memory: 256M
---
kind: Service
apiVersion: v1
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  selector:
    app: jaeger
  ports:
  - protocol: TCP
    # Service port and target port are the same
    port: 9411
  type: ClusterIP
EOF
```

```Output
deployment.apps/jaeger created
service/jaeger created
```

#### <a name="enable-tracing-for-the-osm-add-on"></a>Ativar o rastreio para o add-on OSM

Em seguida, teremos de permitir o rastreio para o add-on OSM.

> [!NOTE]
> A partir de agora, as propriedades de rastreio não são viáveis no configmap osm-config neste momento. Isto será visto numa nova versão do addon OSM AKS.

Executar o seguinte comando para permitir o rastreio do addon OSM:

```azurecli-interactive
kubectl patch configmap osm-config -n kube-system -p '{"data":{"tracing_enable":"true", "tracing_address":"jaeger.jaeger.svc.cluster.local", "tracing_port":"9411", "tracing_endpoint":"/api/v2/spans"}}' --type=merge
```

```Output
configmap/osm-config patched
```

#### <a name="view-the-jaeger-ui-with-port-forwarding"></a>Ver a UI Jaeger com encaminhamento portuário

A UI do Jaeger está a funcionar no porto 16686. Para ver a UI web, pode utilizar o kubectl porta-a-frente:

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

No navegador, deverá ver um dropdown de Serviço, que lhe permite selecionar entre as várias aplicações implementadas pela demo da livraria. Selecione um serviço para visualizar todos os períodos a partir dele. Por exemplo, se selecionar o bookbuyer com um Lookback de uma hora, pode ver as suas interações com a livraria-v1 e a livraria-v2 ordenadas pelo tempo.

![Imagem uI da página de rastreio de OSM Jaeger](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

Selecione qualquer item para vê-lo mais detalhadamente. Selecione vários itens para comparar vestígios. Por exemplo, pode comparar as interações do comprador de livros com a livraria e a livraria-v2 num determinado momento do tempo.

Também pode selecionar o separador Arquitetura do Sistema para visualizar um gráfico de como as várias aplicações têm interagido/comunicação. Isto fornece uma ideia de como o tráfego está a fluir entre as aplicações.

![Imagem uI de arquitetura do sistema OSM Jaeger](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)

## <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Guias de resolução de problemas de rede de serviço aberto (OSM) AKS

Quando implementa o addon OSM AKS, pode ocasionalmente ter um problema. Os seguintes guias irão ajudá-lo a resolver erros de resolução de problemas e a resolver problemas comuns.

### <a name="verifying-and-troubleshooting-osm-components"></a>Verificar e resolver problemas componentes ossm

#### <a name="check-osm-controller-deployment"></a>Verifique a implementação do controlador OSM

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

Um controlador saudável da OSM seria assim:

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

#### <a name="check-the-osm-controller-pod"></a>Verifique o Pod do Controlador OSM

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

Um Pod OSM saudável seria assim:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

Apesar de termos um controlador despejado em algum momento, temos outro que é READY 1/1 e Correr com 0 recomeços. Se a coluna READY for outra coisa que não 1/1, a malha de serviço estaria em estado de avaria.
Coluna PRONTA com 0/1 indica que o contentor do avião de controlo está a despenhar-se - precisamos de obter registos. Consulte os registos do controlador OSM da secção do Centro de Suporte Azure abaixo. Coluna PRONTA com um número superior a 1 após o /indicaria que existem sidecars instalados. O controlador OSM provavelmente não funcionaria com nenhum sidecars ligado a ele.

> [!NOTE]
> A partir da versão v0.8.2 o controlador OSM não está no modo HA e será executado numa contagem de réplicas de 1 - único casulo. A cápsula tem sondas de saúde e será reiniciada pelo kubelet se necessário.

#### <a name="check-osm-controller-service"></a>Verifique o serviço de controlador osm

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

Um serviço saudável de controlador OSM seria assim:

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> O CLUSTER-IP seria diferente. O nome de serviço e o PORT(S) devem ser os mesmos que o exemplo acima.

#### <a name="check-osm-controller-endpoints"></a>Verifique pontos finais do controlador OSM

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

Um ponto final saudável do controlador osm seria assim:

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

#### <a name="check-osm-injector-deployment"></a>Verifique a implantação do injetor OSM

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Uma implantação saudável do injetor osm seria assim:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-pod"></a>Verifique o Pod do Injetor OSM

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Uma cápsula de injetor osm saudável seria assim:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-service"></a>Verifique o serviço de injetores OSM

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

Um serviço saudável de injetor OSM seria assim:

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

#### <a name="check-osm-endpoints"></a>Verifique pontos finais da OSM

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

Um ponto final saudável da OSM seria assim:

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

#### <a name="check-validating-and-mutating-webhooks"></a>Verifique a validação e a mutação de webhooks

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Um Webhook de validação saudável da OSM seria assim:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

Um Webhook mutante saudável da OSM seria assim:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>Verifique o serviço e o pacote ca do webhook validador

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Uma configuração de webhook validada bem configurada seria exatamente igual a isto:

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>Verifique o serviço e o pacote ca do webhook mutante

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Uma configuração de webhook de mutantes bem configurada seria exatamente assim:

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

#### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>Verifique se o Controlador OSM deu o Webhook de Validação (ou Mutação) um Pacote CA

> [!NOTE]
> A partir de v0.8.2 É importante saber que o AKS RP instala o Webhook Validante, o AKS Reconciler garante que existe, mas o Controlador OSM é o que preenche o Pacote CA.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

Este número indica o número de bytes, ou o tamanho do pacote CA. Se isto estiver vazio, 0 ou algum número abaixo de 1000, indicaria que o pacote CA não está corretamente previsto. Sem um pacote CA correto, o Webhook de validação estaria a errar e a proibir o utilizador de fazer alterações no osm-config ConfigMap no espaço de nomes do sistema kube.

Um erro de amostra quando o pacote CA está incorreto:

- Uma tentativa de alterar o osm-config ConfigMap:

```azurecli-interactive
kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
```

- Erro:

```
Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
```

Trabalhe para quando a **Configuração do** Webhook validar tiver um mau certificado:

- Opção 1 - Reiniciar o controlador OSM - isto reiniciará o controlador OSM. No início, substituirá o pacote ca de ambos os webhooks mutantes e validadores.

```azurecli-interactive
kubectl rollout restart deployment -n kube-system osm-controller
```

- Opção 2 - Opção 2. Eliminar o Webhook validador - remover o Webhook de validação faz com que as mutações do `osm-config` ConfigMap deixem de ser validadas. Qualquer remendo passará. O AKS Reconciler irá, em algum momento, garantir que o Webhook de validação existe e irá recriá-lo. O controlador OSM pode ter de ser reiniciado para reescrever rapidamente o pacote CA.

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
```

- Opção 3 - Eliminar e Corrigir: O seguinte comando eliminará o webhook de validação, permitindo-nos adicionar quaisquer valores, e tentará imediatamente aplicar um patch. O mais provável é que o AKS Reconciler não tenha tempo suficiente para conciliar e restaurar o Webhook validante, dando-nos a oportunidade de aplicar uma mudança como último recurso:

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
```

#### <a name="check-the-osm-config-configmap"></a>Verifique o `osm-config` **ConfigMap**

> [!NOTE]
> O controlador OSM não requer que o `osm-config` ConfigMap esteja presente no espaço de nomes do sistema kube. O controlador tem valores padrão razoáveis para o config e pode operar sem ele.

Verifique a existência:

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config
```

Verifique o conteúdo do osm-config ConfigMap

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config -o json | jq '.data'
```

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.20.233",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

`osm-config` Valores ConfigMap:

| Chave                              | Tipo   | Valores Permitidos                                          | Valor Predefinido                          | Função                                                                                                                                                                                                                                |
| -------------------------------- | ------ | ------------------------------------------------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| saída                           | bool   | TRUE, false                                             | `"false"`                              | Permite saídas na malha.                                                                                                                                                                                                             |
| enable_debug_server              | bool   | TRUE, false                                             | `"true"`                               | Permite que um ponto final de depuração na cápsula do controlador osm-controlador listasse informações relativas à malha, tais como ligações por procuração, certificados e políticas de SMI.                                                                                    |
| enable_privileged_init_container | bool   | TRUE, false                                             | `"false"`                              | Permite recipientes init privilegiados para cápsulas de malha. Quando falsos, os recipientes só têm NET_ADMIN.                                                                                                                                   |
| envoy_log_level                  | string | traço, depurar, informação, aviso, aviso, erro, crítico, fora | `"error"`                              | Define a verbosidade de registo do sidecar enviado, apenas aplicável às cápsulas recém-criadas que unem a malha. Para atualizar o nível de registo das cápsulas existentes, reinicie a implementação com `kubectl rollout restart` .                            |
| outbound_ip_range_exclusion_list | string | lista separada por vírgula das gamas IP do formulário a.b.c.d/x | `-`                                    | Lista global de intervalos de endereços IP para excluir da interceção de tráfego de saída pelo representante do sidecar.                                                                                                                                    |
| permissive_traffic_policy_mode   | bool   | TRUE, false                                             | `"false"`                              | Definição para `true` , permite o modo de permitir tudo na malha, ou seja, não aplica a aplicação da política de tráfego na malha. Se definido, permite negar toda a `false` política de tráfego na malha, ou seja, é necessário para que os `SMI Traffic Target` serviços se comuniquem. |
| prometheus_scraping              | bool   | TRUE, false                                             | `"true"`                               | Permite que as métricas de Prometeu raspem nos proxies do sidecar.                                                                                                                                                                                 |
| service_cert_validity_duration   | string | 24h, 1h30m (qualquer duração)                          | `"24h"`                                | Define a duração da validade do certificado de serviço, representada como uma sequência de números decimais cada um com fração opcional e um sufixo de unidade.                                                                                             |
| tracing_enable                   | bool   | TRUE, false                                             | `"false"`                              | Permite que o Jaeger rastreie a malha.                                                                                                                                                                                                    |
| tracing_address                  | string | jaeger.mesh-namespace.svc.cluster.local                 | `jaeger.kube-system.svc.cluster.local` | Endereço da implantação do Jaeger, se o rastreio estiver ativado.                                                                                                                                                                                |
| tracing_endpoint                 | string | /api/v2/vãos                                           | /api/v2/vãos                          | Ponto final para rastreio de dados, se o rastreio for ativado.                                                                                                                                                                                          |
| tracing_port                     | int    | qualquer valor inteiro não zero                              | `"9411"`                               | Porto em que o rastreio está ativado.                                                                                                                                                                                                       |
| use_https_ingress                | bool   | TRUE, false                                             | `"false"`                              | Permite entrada HTTPS na malha.                                                                                                                                                                                                      |
| config_resync_interval           | string | menos de 1 minuto desativa esta                            | 0 (deficiente)                           | Quando um valor superior a 1 m (60s) for fornecido, o Controlador OSM enviará todos os config disponíveis para cada enviado conectado no intervalo dado                                                                                                    |

#### <a name="check-namespaces"></a>Verifique espaços de nome

> [!NOTE]
> O espaço de nomes do sistema kube nunca participará numa malha de serviço e nunca será rotulado e/ou anotado com as chaves/valores abaixo.

Usamos o `osm namespace add` comando para unir espaços de nome a uma determinada malha de serviço.
Quando um espaço de nome k8s faz parte da malha (ou para fazer parte da malha), o seguinte deve ser verdadeiro:

Ver as anotações com

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

Deve estar presente a seguinte anotação:

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

Ver os rótulos com

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

Deve estar presente o seguinte rótulo:

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

Se um espaço de nome não for anotado com `"openservicemesh.io/sidecar-injection": "enabled"` ou não rotulado com o `"openservicemesh.io/monitored-by": "osm"` injetor OSM não adicionará sidecars enviados.

> Nota: Depois `osm namespace add` de ser chamado apenas **novas** cápsulas serão injetadas com um sidecar enviado. As cápsulas existentes devem ser reiniciadas com `kubectl rollout restart deployment ...`

#### <a name="verify-the-smi-crds"></a>Verifique os CRDs do SMI:

Verifique se o cluster tem os CRDs necessários:

```azurecli-interactive
kubectl get crds
```

Temos de ter o seguinte instalado no cluster:

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

Instale as versões dos CRDs com este comando:

```azurecli-interactive
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

Resultado esperado:

```Output
httproutegroups.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


tcproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


trafficsplits.split.smi-spec.io
----
v1alpha2


traffictargets.access.smi-spec.io
----
v1alpha3
v1alpha2
v1alpha1


udproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1
```

O controlador OSM v0.8.2 requer as seguintes versões:

- traffictargets.access.smi-spec.io - [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io - Não suportado
- trafficsplits.split.smi-spec.io - [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*.metrics.smi-spec.io - [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

Se faltarem CRDs, utilize os seguintes comandos para instalá-los no cluster:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>Desativar o addon de malha de serviço aberto (OSM) para o seu cluster AKS

Para desativar o addon OSM, executar o seguinte comando:

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register
