---
title: 'Início rápido: implantar um cluster do serviço kubernetes do Azure'
description: Saiba como criar rapidamente um cluster kubernetes, implantar um aplicativo e monitorar o desempenho no AKS (serviço kubernetes do Azure) usando o CLI do Azure.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: quickstart
ms.date: 09/13/2019
ms.author: mlearned
ms.custom: H1Hack27Feb2017, mvc, devcenter, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 1d700afe36d51f0b939629f86d770e54072f47a9
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255522"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Início rápido: implantar um cluster do AKS (serviço kubernetes do Azure) usando o CLI do Azure

Neste guia de início rápido, você implanta um cluster do AKS (serviço kubernetes do Azure) usando o CLI do Azure. O AKS é um serviço gerenciado kubernetes que permite implantar e gerenciar clusters rapidamente. Um aplicativo de vários contêineres que inclui um front-end da Web e uma instância Redis é executado no cluster. Em seguida, você verá como monitorar a integridade do cluster e os pods que executam seu aplicativo.

Se você quiser usar contêineres do Windows Server (atualmente em visualização no AKS), consulte [criar um cluster AKs que dá suporte a contêineres do Windows Server][windows-container-cli].

![Aplicativo de votação do Azure em ação](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

Este guia de introdução parte do princípio de que possui conhecimentos básicos dos conceitos do Kubernetes. Para obter mais informações, consulte [kubernetes Core Concepts for Azure kubernetes Service (AKs)][kubernetes-concepts].

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá que você esteja executando o CLI do Azure versão 2.0.64 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

> [!Note]
> Se executar os comandos neste início rápido localmente (em vez de Azure Cloud Shell), certifique-se de executar os comandos como administrador.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. Quando cria um grupo de recursos, é-lhe pedido que especifique uma localização. Esse local é onde os metadados do grupo de recursos são armazenados, também é onde os recursos são executados no Azure se você não especificar outra região durante a criação do recurso. Crie um grupo de recursos usando o comando [AZ Group Create][az-group-create] .

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

A saída de exemplo a seguir mostra o grupo de recursos criado com êxito:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>Criar um cluster do AKS

Use o comando [AZ AKs Create][az-aks-create] para criar um cluster AKs. O exemplo seguinte cria um cluster com o nome *myAKSCluster* com um nó. O Azure Monitor para contentores também é ativado com o parâmetro *--enable-addons monitoring*.  Isso levará vários minutos para ser concluído.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

Após alguns minutos, o comando é concluído e retorna informações formatadas em JSON sobre o cluster.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerenciar um cluster kubernetes, você usa [kubectl][kubectl], o cliente de linha de comando kubernetes. Se você usar Azure Cloud Shell, o `kubectl` já estará instalado. Para instalar o `kubectl` localmente, use o comando [AZ AKs install-CLI][az-aks-install-cli] :

```azurecli
az aks install-cli
```

Para configurar `kubectl` para se conectar ao cluster kubernetes, use o comando [AZ AKs Get-Credentials][az-aks-get-credentials] . Esse comando baixa as credenciais e configura a CLI do kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo seguinte mostra o nó único criado nos passos anteriores. Verifique se o status do nó está *pronto*:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="run-the-application"></a>Executar a aplicação

Um arquivo de manifesto kubernetes define um estado desejado para o cluster, como quais imagens de contêiner executar. Neste início rápido, é utilizado um manifesto para criar todos os objetos necessários para executar a aplicação Azure Vote. Esse manifesto inclui duas [implantações de kubernetes][kubernetes-deployment] – uma para os aplicativos Python de exemplo do Azure e a outra para uma instância Redis. Dois [Serviços Kubernetess][kubernetes-service] também são criados – um serviço interno para a instância do Redis e um serviço externo para acessar o aplicativo Azure vote da Internet.

> [!TIP]
> Neste início rápido, crie e implemente manualmente os seus manifestos de aplicação para o cluster do AKS. Em cenários mais reais, você pode usar [Azure dev Spaces][azure-dev-spaces] para iterar e depurar rapidamente seu código diretamente no cluster AKs. Pode utilizar o Dev Spaces em várias plataformas do SO e ambientes de desenvolvimento, e trabalhar em conjunto com outras pessoas na sua equipa.

Crie um arquivo chamado `azure-vote.yaml` e copie na seguinte definição de YAML. Se você usar o Azure Cloud Shell, esse arquivo poderá ser criado usando `vi` ou `nano` como se estiver trabalhando em um sistema físico ou virtual:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Implante o aplicativo usando o comando [kubectl Apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```azurecli-interactive
kubectl apply -f azure-vote.yaml
```

A saída de exemplo a seguir mostra as implantações e os serviços criados com êxito:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testar a aplicação

Quando o aplicativo é executado, um serviço kubernetes expõe o front-end do aplicativo à Internet. Este processo pode demorar alguns minutos a concluir.

Para monitorizar o progresso, utilize o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Inicialmente, o *IP externo* para o serviço *Azure-vote-front* é mostrado como *pendente*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o endereço *IP externo* for alterado de *pendente* para um endereço IP público real, use `CTRL-C` para interromper o processo de inspeção `kubectl`. A saída de exemplo a seguir mostra um endereço IP público válido atribuído ao serviço:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver o aplicativo de voto do Azure em ação, abra um navegador da Web para o endereço IP externo do seu serviço.

![Aplicativo de votação do Azure em ação](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

Quando o cluster AKS foi criado, [Azure monitor para contêineres](../azure-monitor/insights/container-insights-overview.md) foi habilitado para capturar métricas de integridade para ambos os nós de cluster e pods. Estas métricas de estado de funcionamento estão disponíveis no portal do Azure.

## <a name="delete-the-cluster"></a>Eliminar o cluster

Para evitar cobranças do Azure, você deve limpar recursos desnecessários.  Quando o cluster não for mais necessário, use o comando [AZ Group Delete][az-group-delete] para remover o grupo de recursos, o serviço de contêiner e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter as etapas sobre como remover a entidade de serviço, consulte [considerações e exclusão da entidade de serviço AKs][sp-delete].

## <a name="get-the-code"></a>Obter o código

Neste guia de início rápido, as imagens de contêiner criadas previamente foram usadas para criar uma implantação do kubernetes. O código da aplicação relacionado, o Dockerfile, e o ficheiro de manifesto do Kubernetes, estão disponíveis no GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, implementou um cluster do Kubernetes e implementou uma aplicação de vários contentores no mesmo. Você também pode [acessar o painel da Web do kubernetes][kubernetes-dashboard] para o cluster do AKS.

Para saber mais sobre o AKS e ver um exemplo completo de código para implementação, avance para o tutorial dos clusters de Kubernetes.

> [!div class="nextstepaction"]
> [Tutorial do AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-container-cli]: windows-container-cli.md
