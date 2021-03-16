---
title: Quickstart - Criar um cluster de serviço Azure Kubernetes (AKS)
description: Saiba como criar rapidamente um cluster Kubernetes utilizando um modelo de Gestor de Recursos Azure e implementar uma aplicação no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc,subject-armqs, devx-track-azurecli
ms.openlocfilehash: e88c56f050f2f6d1183eef23a844f5eaf1f671c2
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492970"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-arm-template"></a>Quickstart: Implementar um cluster de serviço Azure Kubernetes (AKS) usando um modelo ARM

O Azure Kubernetes Service (AKS) é um serviço gerido pela Kubernetes que permite implementar e gerir rapidamente clusters. Neste arranque rápido, você vai:
* Implemente um cluster AKS utilizando um modelo de Gestor de Recursos Azure. 
* Executar uma aplicação multi-contentor com uma extremidade frontal web e uma instância Redis no cluster. 

![Imagem de navegação para o Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Este guia de introdução parte do princípio de que possui conhecimentos básicos dos conceitos do Kubernetes. Para obter mais informações, consulte [os conceitos fundamentais da Kubernetes para o Serviço Azure Kubernetes (AKS)][kubernetes-concepts].

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.61 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

- Para criar um cluster AKS utilizando um modelo de Gestor de Recursos, fornece uma chave pública SSH. Se necessitar deste recurso, consulte a seguinte secção; de outra forma, saltar para a [secção de revisão do modelo.](#review-the-template)

### <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Para aceder aos nós AKS, conecta-se utilizando um par de chaves SSH (público e privado), que gera através do `ssh-keygen` comando. Por predefinição, estes ficheiros são criados no *diretório ~/ssh.* Executar o `ssh-keygen` comando substituirá qualquer par de chaves SSH com o mesmo nome já existente no local indicado.

1. Vá [https://shell.azure.com](https://shell.azure.com) para abrir a Cloud Shell no seu navegador.

1. Execute o comando `ssh-keygen`. O exemplo a seguir cria um par de chaves SSH usando encriptação RSA e um pouco de comprimento de 2048:

    ```console
    ssh-keygen -t rsa -b 2048
    ```

Para obter mais informações sobre a criação de teclas SSH, consulte [criar e gerir as teclas SSH para autenticação em Azure][ssh-keys].

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-aks/).

:::code language="json" source="~/quickstart-templates/101-aks/azuredeploy.json":::

Para obter mais amostras de AKS, consulte o site [de modelos de arranque rápido AKS.][aks-quickstart-templates]

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione o seguinte botão para iniciar seduca e abra um modelo.

    [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Selecione ou introduza os seguintes valores.

    Para este arranque rápido, deixe os valores padrão para o tamanho do *disco oss GB*, *Contagem de agentes,* *tamanho do agente VM,* *tipo OS* e *Versão Kubernetes*. Forneça os seus próprios valores para os seguintes parâmetros do modelo:

    * **Assinatura**: Selecione uma subscrição Azure.
    * **Grupo de recursos**: Selecione **Criar novo**. Introduza um nome único para o grupo de recursos, como *o myResourceGroup,* em seguida, escolha **OK**.
    * **Localização**: Selecione uma localização, como **East US**.
    * **Nome do cluster**: Introduza um nome único para o cluster AKS, como *myAKSCluster*.
    * **Prefixo DNS**: Introduza um prefixo DNS único para o seu cluster, como *o myakscluster*.
    * **Nome de utilizador Linux Admin**: Introduza um nome de utilizador para ligar utilizando sSH, como *azureuser*.
    * **Tecla pública SSH RSA**: Copiar e colar a parte *pública* do seu par de chaves SSH (por padrão, o conteúdo de *~/.ssh/id_rsa.pub).*

    ![Modelo de Gestor de Recursos para criar um cluster de serviço Azure Kubernetes no portal](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Selecione **Rever + Criar**.

A criação do cluster do AKS demora alguns minutos. Aguarde que o cluster seja implantado com sucesso antes de passar ao passo seguinte.

## <a name="validate-the-deployment"></a>Validar a implementação

### <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerir um cluster Kubernetes, utilize o cliente da linha de comando Kubernetes, [kubectl][kubectl]. `kubectl` já está instalado se utilizar a Azure Cloud Shell. 

1. Instale `kubectl` localmente utilizando o comando [az aks instalar-cli:][az-aks-install-cli]

    ```azurecli
    az aks install-cli
    ```

2. Configurar `kubectl` para ligar ao seu cluster Kubernetes usando o comando [az aks get-credentials.][az-aks-get-credentials] Este comando descarrega credenciais e configura o CLI de Kubernetes para usá-las.

    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Verifique a ligação ao seu cluster utilizando o [comando kubectl.][kubectl-get] Este comando devolve uma lista dos nós do cluster.

    ```console
    kubectl get nodes
    ```

    A saída mostra os nós criados nos passos anteriores. Certifique-se de que o estado de todos os nós está *pronto:*

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6    
    aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
    aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
    ```

### <a name="run-the-application"></a>Executar a aplicação

Um [ficheiro manifesto de Kubernetes][kubernetes-deployment] define o estado desejado de um cluster, como quais as imagens do contentor a executar. 

Neste arranque rápido, utilizará um manifesto para criar todos os objetos necessários para executar a [aplicação Azure Vote][azure-vote-app]. Este manifesto inclui duas [missões de Kubernetes:][kubernetes-deployment]
* A amostra Azure Vote Python aplicações.
* Um exemplo do Redis. 

São também criados dois [Serviços Kubernetes:][kubernetes-service]
* Um serviço interno para o caso Redis.
* Um serviço externo para aceder à aplicação Azure Vote a partir da internet.

1. Crie um ficheiro com o nome `azure-vote.yaml`.
    * Se utilizar o Azure Cloud Shell, este ficheiro pode ser criado utilizando `vi` ou `nano` como se funcionasse num sistema virtual ou físico
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

1. Implemente a aplicação utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

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

### <a name="test-the-application"></a>Testar a aplicação

Quando a aplicação é executado, um serviço Kubernetes expõe a linha frontal da aplicação para a internet. Este processo pode demorar alguns minutos a concluir.

Monitorize o progresso utilizando o [comando de serviço kubectl][kubectl-get] com o `--watch` argumento.

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

![Imagem de navegação para o Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Para evitar as acusações do Azure, limpe os seus recursos desnecessários. Utilize o comando [az group delete][az-group-delete] para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter passos sobre como remover o principal de serviço, consulte [Considerações sobre e eliminação do principal de serviço AKS][sp-delete].
> 
> Se usou uma identidade gerida, a identidade é gerida pela plataforma e não necessita de remoção.

## <a name="get-the-code"></a>Obter o código

Imagens de contentores pré-existentes foram usadas neste quickstart para criar uma implantação de Kubernetes. O código de aplicação relacionado, Dockerfile e Kubernetes manifesto file estão [disponíveis no GitHub.][azure-vote-app]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, lançou um cluster Kubernetes e, em seguida, implantou uma aplicação de vários contentores para o mesmo. [Aceda ao painel web Kubernetes][kubernetes-dashboard] para o seu cluster AKS.

Para saber mais sobre o AKS e ver um exemplo completo de código para implementação, avance para o tutorial dos clusters de Kubernetes.

> [!div class="nextstepaction"]
> [Tutorial AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
