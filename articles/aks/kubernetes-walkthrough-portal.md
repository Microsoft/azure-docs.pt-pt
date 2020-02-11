---
title: Início rápido-criar um cluster do AKS (serviço kubernetes do Azure) no portal
description: Saiba como criar rapidamente um cluster kubernetes, implantar um aplicativo e monitorar o desempenho no AKS (serviço kubernetes do Azure) usando o portal do Azure.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: quickstart
ms.date: 01/21/2020
ms.author: mlearned
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: 756c3c198963da7f2f06a91c675f43da33df9e3e
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121467"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Início rápido: implantar um cluster do AKS (serviço kubernetes do Azure) usando o portal do Azure

O AKS (serviço kubernetes do Azure) é um serviço gerenciado kubernetes que permite implantar e gerenciar clusters rapidamente. Neste guia de introdução, vai implementar um cluster do AKS com o portal do Azure. Um aplicativo de vários contêineres que inclui um front-end da Web e uma instância Redis é executado no cluster. Em seguida, você verá como monitorar a integridade do cluster e os pods que executam seu aplicativo.

![Imagem de navegação para o exemplo de aplicação Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

Este guia de introdução parte do princípio de que possui conhecimentos básicos dos conceitos do Kubernetes. Para mais informações, consulte os [conceitos centrais da Kubernetes para o Serviço Azure Kubernetes (AKS)][kubernetes-concepts].

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Para criar um cluster do AKS, execute os passos seguintes:

1. No menu do portal Azure ou na página **Inicial,** selecione **Criar um recurso**.

2. Selecione **recipientes** >  **Serviço Kubernetes**.

3. Na página **Basics,** configure as seguintes opções:
    - **Detalhes do projeto**: Selecione uma **Subscrição**Azure, em seguida, selecione ou crie um grupo de **recursos**Azure , como o *myResourceGroup*.
    - **Detalhes do cluster**: Introduza um nome de **cluster Kubernetes,** como *myAKSCluster*. Selecione uma **versão Região,** **Kubernetes**e **prefixo de nome DNS** para o cluster AKS.
    - **Piscina principal**do nó : Selecione um tamanho de **nó** VM para os nódosos AKS. O tamanho vm *não pode* ser alterado uma vez que um cluster AKS foi implantado. 
            -Selecione o número de nós a serem implantados no cluster. Neste início rápido, defina **Contagem de nós** como *1*. O número de nós *pode* ser ajustado após a implementação do cluster.
    
    ![Criar cluster do AKS - indique informações básicas](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

    Selecione **Seguinte: Escala** quando estiver concluída.

4. Na página **Escala,** mantenha as opções predefinidas. Na parte inferior do ecrã, clique em **Seguinte: Autenticação**.
    > [!CAUTION]
    > A criação de novas entidades de serviço do AAD pode levar vários minutos para ser propagada e disponibilizada, fazendo com que a entidade de serviço não encontrou erros e falhas de validação no portal do Azure. Se você bater isso, por favor, visite [aqui](troubleshooting.md#im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one) para atenuações.

5. Na página **de Autenticação,** configure as seguintes opções:
    - Crie um novo diretor de serviço saindo do campo principal de **serviço** com **(novo) principal de serviço predefinido**. Ou pode escolher o diretor de *serviço configure* para utilizar um existente. Se você usar um existente, será necessário fornecer a ID e o segredo do cliente SPN.
    - Ative a opção para os controlos de acesso baseado em funções (RBAC) do Kubernetes. Isso fornecerá um controle mais refinado sobre o acesso aos recursos kubernetes implantados em seu cluster AKS.

Por predefinição, é utilizado o networking *básico* e o Monitor Azure para contentores está ativado. Clique em **Rever + criar** e, em seguida, **criar** quando a validação estiver completa.

Leva alguns minutos para criar o cluster AKS. Quando a sua implementação estiver concluída, clique em **ir para o recurso**, ou navegue para o grupo de recursos de cluster AKS, como o *myResourceGroup,* e selecione o recurso AKS, como *o myAKSCluster*. O painel do cluster AKS é mostrado, como neste exemplo:

![Exemplo de dashboard do AKS no portal do Azure](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerir um cluster Kubernetes, você usa [kubectl,][kubectl]o cliente da linha de comando Kubernetes. O cliente `kubectl` está pré-instalado no Azure Cloud Shell.

Abra a Cloud Shell utilizando o botão `>_` na parte superior do portal Azure.

![Abrir o Azure Cloud Shell no portal](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

Para configurar `kubectl` para se ligar ao seu cluster Kubernetes, use o comando [az aks get-credentials.][az-aks-get-credentials] Este comando descarrega credenciais e confunde o ClI Kubernetes para usá-las. O exemplo seguinte obtém credenciais para o nome do cluster *myAKSCluster* no grupo de recursos denominado *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo seguinte mostra o nó único criado nos passos anteriores. Certifique-se de que o estado do nó está *pronto:*

```
NAME                       STATUS    ROLES     AGE       VERSION
aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
```

## <a name="run-the-application"></a>Executar a aplicação

Um arquivo de manifesto kubernetes define um estado desejado para o cluster, como quais imagens de contêiner executar. Neste início rápido, é utilizado um manifesto para criar todos os objetos necessários para executar a aplicação Azure Vote. Este manifesto inclui duas [implantações kubernetes][kubernetes-deployment] - uma para as aplicações azure vote Python, e outra para uma instância Redis. São também criados dois [Serviços Kubernetes][kubernetes-service] - um serviço interno para a instância Redis, e um serviço externo para aceder à aplicação Azure Vote a partir da internet.

> [!TIP]
> Neste início rápido, crie e implemente manualmente os seus manifestos de aplicação para o cluster do AKS. Em cenários mais reais, você pode usar [O Azure Dev Spaces][azure-dev-spaces] para iterar rapidamente e desinchar o seu código diretamente no cluster AKS. Pode utilizar o Dev Spaces em várias plataformas do SO e ambientes de desenvolvimento, e trabalhar em conjunto com outras pessoas na sua equipa.

Na casca da nuvem, utilize o comando `nano azure-vote.yaml` ou `vi azure-vote.yaml` para criar um ficheiro chamado `azure-vote.yaml`. Em seguida, copie na seguinte definição de YAML:

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

Implemente a aplicação utilizando o [kubectl aplique][kubectl-apply] o comando e especifique o nome do seu manifesto YAML:

```azurecli-interactive
kubectl apply -f azure-vote.yaml
```

A saída de exemplo a seguir mostra as implantações e os serviços criados com êxito:

```
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

Inicialmente, o *IP EXTERNO* para o serviço de frente para *voto sinuoso* é apresentado como *pendente*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o endereço *EXTERNO-IP* passar de *pendente* para um endereço IP público real, utilize `CTRL-C` para parar o processo de observação `kubectl`. A saída de exemplo a seguir mostra um endereço IP público válido atribuído ao serviço:

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver o aplicativo de voto do Azure em ação, abra um navegador da Web para o endereço IP externo do seu serviço.

![Imagem de navegação para o exemplo de aplicação Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Monitorizar o estado de funcionamento e os registos

Quando você criou o cluster, o Azure Monitor para contêineres foi habilitado. Esta funcionalidade de monitorização proporciona métricas de estado de funcionamento para o cluster do AKS e para pods em execução no mesmo.

Pode demorar alguns minutos até que estes dados sejam povoados no portal do Azure. Para ver o estado atual, o tempo de atividade e a utilização de recursos relativamente aos pods do Azure Vote, regresse ao recurso do AKS no portal do Azure, como *myAKSCluster*. Em seguida, pode aceder ao estado de funcionamento da seguinte forma:

1. Sob **monitorização** no lado esquerdo, escolha **Insights**
1. Na parte superior, opte por **+ Adicionar Filtro**
1. Selecione *Espaço de nomes* como a propriedade e, em seguida, escolha *\<Todas, exceto o sistema kube\>*
1. Selecione para ver os **Contentores**.

Os contentores *azure-vote-back* e *azure-vote-front* são apresentados, conforme mostrado no exemplo seguinte:

![Ver o estado de funcionamento dos contentores em execução no AKS](media/kubernetes-walkthrough-portal/monitor-containers.png)

Para ver os registos para a `azure-vote-front` cápsula, selecione os **registos** do recipiente Ver a partir da queda da lista de contentores. Esses registos incluem os fluxos *stdout* e *stderr* do contentor.

![Ver os registos dos contentores no AKS](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Eliminar o cluster

Quando o cluster já não for necessário, elimine o recurso do cluster, o que, por sua vez, elimina todos os recursos associados. Esta operação pode ser levada a cabo no portal do Azure através da seleção do botão **Eliminar** no dashboard do cluster do AKS. Alternativamente, o comando [az aks eliminar][az-aks-delete] pode ser usado na Cloud Shell:

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter medidas sobre como remover o diretor de serviço, consulte as [principais considerações e a eliminação do serviço AKS.][sp-delete]

## <a name="get-the-code"></a>Obter o código

Neste guia de início rápido, as imagens de contêiner criadas previamente foram usadas para criar uma implantação do kubernetes. O código da aplicação relacionado, o Dockerfile, e o ficheiro de manifesto do Kubernetes, estão disponíveis no GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, implementou um cluster do Kubernetes e implementou uma aplicação de vários contentores no mesmo.

Para saber mais sobre o AKS e ver um exemplo completo de código para implementação, avance para o tutorial dos clusters de Kubernetes.

> [!div class="nextstepaction"]
> [Tutorial AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-delete]: /cli/azure/aks#az-aks-delete
[aks-monitor]: ../monitoring/monitoring-container-health.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
