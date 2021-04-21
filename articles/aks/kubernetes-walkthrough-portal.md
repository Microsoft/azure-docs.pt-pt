---
title: 'Quickstart: Implementar um cluster AKS utilizando o portal Azure'
titleSuffix: Azure Kubernetes Service
description: Aprenda a criar rapidamente um cluster Kubernetes, implementar uma aplicação e monitorizar o desempenho no Serviço Azure Kubernetes (AKS) utilizando o portal Azure.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc, seo-javascript-october2019, contperf-fy21q3
ms.openlocfilehash: 28ba2ffd2007aeb45081cf66b05395a2b8456bf7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779710"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Quickstart: Implementar um cluster Azure Kubernetes Service (AKS) utilizando o portal Azure

O Azure Kubernetes Service (AKS) é um serviço gerido pela Kubernetes que permite implementar e gerir rapidamente clusters. Neste arranque rápido, você vai:
* Implemente um cluster AKS utilizando o portal Azure. 
* Executar uma aplicação multi-contentor com uma extremidade frontal web e uma instância Redis no cluster. 
* Monitorize a saúde do cluster e das cápsulas que executam a sua aplicação.

![Imagem de navegação para o exemplo de aplicação Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

Este guia de introdução parte do princípio de que possui conhecimentos básicos dos conceitos do Kubernetes. Para obter mais informações, consulte [os conceitos fundamentais da Kubernetes para o Serviço Azure Kubernetes (AKS)][kubernetes-concepts].

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**.

2. Selecione **Contentores** > **Serviço do Kubernetes**.

3. Na página **Informações Básicas**, configure as opções seguintes:
    - **Detalhes do projeto:** 
        * Selecione uma **Subscrição** do Azure.
        * Selecione ou crie um **grupo de Recursos** Azure, como o *myResourceGroup*.
    - **Detalhes do cluster:** 
        * Introduza um **nome para o cluster do Kubernetes**, como *myAKSCluster*. 
        * Selecione uma **versão Região** e **Kubernetes** para o cluster AKS.
    - **Piscina de nó primário:** 
        * Selecione um **Tamanho do nó** da VM para os nós do AKS. O tamanho da VM *não pode* ser alterado após a implementação de um cluster de AKS.
        * Selecione o número de nós a implementar no cluster. Neste início rápido, defina **Contagem de nós** como *1*. O número de nós *pode* ser ajustado após a implementação do cluster.
    
    ![Criar cluster do AKS - indique informações básicas](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

4. Selecione **Seguinte: Piscinas de nó quando concluídas.**

5. Mantenha as opções de **piscinas de nó** prede padrão. Na parte inferior do ecrã, clique em **Seguinte: Autenticação**.
    > [!CAUTION]
    > Os recém-criados diretores de serviços Azure AD podem demorar vários minutos a propagar-se e a ficar disponíveis, causando erros de "principal serviço não encontrados" e falhas de validação no portal Azure. Se atingir este galo, visite o [nosso artigo de resolução de problemas](troubleshooting.md#received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster) para mitigação.

6. Na página **Autenticação**, configure as opções seguintes:
    - Criar uma nova identidade de cluster por:
        * Saindo do campo **de autenticação** com **identidade gerida de sistema,** ou
        * Escolher o **diretor de serviço** para usar um diretor de serviço. 
            * Selecione *(novo) principal de serviço predefinido* para criar um principal de serviço predefinido, ou
            * Selecione *o principal do serviço Configure* para usar um existente. Terá de fornecer a identificação e segredo do cliente spn do diretor existente.
    - Ative a opção de controlo de acesso baseado em funções da Kubernetes (Kubernetes RBAC) para fornecer um controlo mais fino sobre o acesso aos recursos Kubernetes implantados no seu cluster AKS.

    Por predefinição, é utilizada a rede *básica* e o Monitor Azure para contentores está ativado. 

7. Clique em **Rever + criar** e, em seguida, em **Criar** quando a validação for concluída. 


8. A criação do cluster do AKS demora alguns minutos. Quando a sua implantação estiver concluída, navegue para o seu recurso por:
    * Clicar **em ir para o recurso,** ou
    * Navegar para o grupo de recursos de cluster AKS e selecionar o recurso AKS. 
        * Por exemplo, o painel de instrumentos abaixo: navegação para *o myResourceGroup* e selecione o recurso *myAKSCluster.*

        ![Exemplo de dashboard do AKS no portal do Azure](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerir um cluster Kubernetes, utilize o cliente da linha de comando Kubernetes, [kubectl][kubectl]. `kubectl` já está instalado se utilizar a Azure Cloud Shell. 

1. Open Cloud Shell utilizando o `>_` botão na parte superior do portal Azure.

    ![Abrir o Azure Cloud Shell no portal](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

    > [!NOTE]
    > Para realizar estas operações numa instalação local de conchas:
    > 1. Verifique se o Azure CLI está instalado.
    > 2. Ligue-se a Azure através do `az login` comando.

2. Configurar `kubectl` para ligar ao seu cluster Kubernetes usando o comando [az aks get-credentials.][az-aks-get-credentials] O comando seguinte descarrega credenciais e configura o CLI de Kubernetes para usá-las.

    ```azurecli
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Verifique a ligação ao seu cluster utilizando `kubectl get` para devolver uma lista dos nós do cluster.

    ```console
    kubectl get nodes
    ```

    A saída mostra o único nó criado nos passos anteriores. Certifique-se de que o estado do nó está *pronto:*

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
    ```

## <a name="run-the-application"></a>Executar a aplicação

Um ficheiro manifesto de Kubernetes define o estado desejado de um cluster, como quais as imagens do contentor a executar. 

Neste arranque rápido, utilizará um manifesto para criar todos os objetos necessários para executar a aplicação Azure Vote. Este manifesto inclui duas implementações de Kubernetes:
* A amostra Azure Vote Python aplicações.
* Um exemplo do Redis. 

São também criados dois Serviços Kubernetes:
* Um serviço interno para o caso Redis.
* Um serviço externo para aceder à aplicação Azure Vote a partir da internet.

1. Na Cloud Shell, utilize um editor para criar um ficheiro chamado `azure-vote.yaml` , como:
    * `code azure-vote.yaml`
    * `nano azure-vote.yaml` ou  
    * `vi azure-vote.yaml`. 

1. Copiar na seguinte definição YAML:

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
            image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
            env:
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
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
            image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
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

1. Implemente a aplicação utilizando o `kubectl apply` comando e especifique o nome do seu manifesto YAML:

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    A produção mostra as implementações e serviços criados com sucesso:

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

## <a name="test-the-application"></a>Testar a aplicação

Quando a aplicação é executado, um serviço Kubernetes expõe a linha frontal da aplicação para a internet. Este processo pode demorar alguns minutos a concluir.

Para monitorizar o progresso, use o `kubectl get service` comando com o `--watch` argumento.

```console
kubectl get service azure-vote-front --watch
```

A saída **EXTERNAL-IP** do `azure-vote-front` serviço irá inicialmente mostrar como *pendente*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Uma vez que o endereço **EXTERNAL-IP** muda de *pendente* para um endereço IP público real, use `CTRL-C` para parar o processo do `kubectl` relógio. A saída de exemplo a seguir mostra um endereço IP público válido atribuído ao serviço:


```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver a aplicação Azure Vote em ação, abra um navegador web para o endereço IP externo do seu serviço.

![Imagem de navegação para o exemplo de aplicação Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Monitorizar o estado de funcionamento e os registos

Quando criou o cluster, o Azure Monitor para contentores estava ativado. O Azure Monitor para recipientes fornece métricas de saúde tanto para o cluster AKS como para as cápsulas que correm no cluster.

Os dados métricos demoram alguns minutos a preencher-se no portal Azure. Para ver o estado de saúde atual, o uptime e o uso de recursos para as cápsulas de voto Azure:

1. Procure de volta ao recurso AKS no portal Azure.
1. Em **Monitorização** do lado esquerdo, escolha **Insights**.
1. Em toda a parte superior, opte por **+ Adicionar Filtro.**
1. Selecione **Namespace** como propriedade e, em seguida, escolha *\<All but kube-system\>* .
1. Selecione **Recipientes** para os visualizar.

Os `azure-vote-back` `azure-vote-front` contentores e os recipientes serão exibidos, como mostra o seguinte exemplo:

![Ver o estado de funcionamento dos contentores em execução no AKS](media/kubernetes-walkthrough-portal/monitor-containers.png)

Para visualizar os registos da `azure-vote-front` cápsula, selecione **Ver os registos** dos contentores da lista de contentores. Estes troncos incluem os fluxos *de stderr* e *stderr* do recipiente.

![Ver os registos dos contentores no AKS](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Eliminar o cluster

Para evitar as acusações do Azure, limpe os seus recursos desnecessários. Selecione o botão **Eliminar** no painel de instrumentos AKS. Também pode utilizar o comando [az aks eliminar][az-aks-delete] na Cloud Shell:

```azurecli
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```
> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter passos sobre como remover o principal de serviço, consulte [Considerações sobre e eliminação do principal de serviço AKS][sp-delete].
> 
> Se usou uma identidade gerida, a identidade é gerida pela plataforma e não necessita de remoção.

## <a name="get-the-code"></a>Obter o código

Imagens de contentores pré-existentes foram usadas neste quickstart para criar uma implantação de Kubernetes. O código de aplicação relacionado, Dockerfile e Kubernetes manifesto file estão [disponíveis no GitHub.][azure-vote-app]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, lançou um cluster Kubernetes e, em seguida, implantou uma aplicação de vários contentores para o mesmo. Aceda ao painel web Kubernetes para o seu cluster AKS.


Para saber mais sobre a AKS, percorrendo um exemplo completo, incluindo a construção de uma aplicação, implantação a partir do Registo de Contentores Azure, atualização de uma aplicação de execução, e dimensionamento e upgrade do seu cluster, continue para o tutorial do cluster Kubernetes.

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
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-delete]: /cli/azure/aks#az_aks_delete
[aks-monitor]: ../azure-monitor/containers/container-insights-overview.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations