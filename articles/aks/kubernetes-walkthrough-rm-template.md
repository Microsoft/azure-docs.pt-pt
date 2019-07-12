---
title: Início rápido - criar um cluster do Azure Kubernetes Service (AKS)
description: Saiba como criar rapidamente um cluster do Kubernetes com um modelo Azure Resource Manager e implementar uma aplicação no Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: quickstart
ms.date: 04/19/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: e7cc9b63768385e4665e330b2b02a884b84c2188
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615387"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-azure-resource-manager-template"></a>Início rápido: Implementar um cluster de Azure Kubernetes Service (AKS) com um modelo Azure Resource Manager

O Azure Kubernetes Service (AKS) é um serviço de Kubernetes gerido que permite-lhe implementar e gerir clusters rapidamente. Neste início rápido, vai implementar um cluster do AKS com um modelo Azure Resource Manager. Uma aplicação de vários contentores que inclui um front-end da web e numa instância de Redis é executada no cluster.

![Imagem de navegação para o Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

Este guia de introdução parte do princípio de que possui conhecimentos básicos dos conceitos do Kubernetes. Para obter mais informações, consulte [Kubernetes principais conceitos para o Azure Kubernetes Service (AKS)][kubernetes-concepts].

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este início rápido requer a execução da versão 2.0.61 da CLI do Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="prerequisites"></a>Pré-requisitos

Para criar um cluster do AKS com um modelo do Resource Manager, é fornecer uma chave pública SSH e um principal de serviço do Azure Active Directory. Se precisar de qualquer um destes recursos, consulte a secção seguinte; caso contrário, avance para o [criar um cluster do AKS](#create-an-aks-cluster) secção.

### <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Para aceder a nós do AKS, estabelece ligação utilizar um par de chaves SSH. Utilize o `ssh-keygen` comando para gerar ficheiros de chaves públicos e privados SSH. Por predefinição, estes ficheiros são criados no *~/.ssh* diretório. Se existir um par de chaves SSH com o mesmo nome na localização especificada, esses arquivos são substituídos.

O comando seguinte cria um par de chaves SSH com a criptografia RSA e um comprimento de bits de 2048:

```azurecli-interactive
ssh-keygen -t rsa -b 2048
```

Para obter mais informações sobre a criação de chaves SSH, veja [criar e gerir chaves SSH para autenticação no Azure][ssh-keys].

### <a name="create-a-service-principal"></a>Criar um principal de serviço

Para permitir que um cluster do AKS interaja com outros recursos do Azure, é utilizado um principal de serviço do Azure Active Directory. Crie um principal de serviço com o comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. O parâmetro `--skip-assignment` limita a atribuição de permissões adicionais. Por predefinição, este principal de serviço é válido durante um ano.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

O resultado é semelhante ao seguinte exemplo:

```json
{
  "appId": "8b1ede42-d407-46c2-a1bc-6b213b04295f",
  "displayName": "azure-cli-2019-04-19-21-42-11",
  "name": "http://azure-cli-2019-04-19-21-42-11",
  "password": "27e5ac58-81b0-46c1-bd87-85b4ef622682",
  "tenant": "73f978cf-87f2-41bf-92ab-2e7ce012db57"
}
```

Anote o *appId* e a *palavra-passe*. São utilizados os seguintes valores nos passos seguintes.

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

É o modelo utilizado neste início rápido [implementar um cluster do Azure Kubernetes Service](https://azure.microsoft.com/resources/templates/101-aks/). Para obter mais amostras AKS, consulte a [modelos de início rápido do AKS][aks-quickstart-templates] site.

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo.

    [![Implementar no Azure](./media/kubernetes-walkthrough-rm-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Selecione ou introduza os seguintes valores.  

    Neste início rápido, deixe os valores predefinidos para o *GB de tamanho de disco do SO*, *contagem de agentes*, *tamanho da VM do agente*, *tipo de SO*e o *Versão do Kubernetes*. Fornece seus próprios valores para os seguintes parâmetros de modelo:

    * **Subscrição**: Selecione uma subscrição do Azure.
    * **Grupo de recursos**: Selecione **Criar novo**. Introduza um nome exclusivo para o grupo de recursos, tal como *myResourceGroup*, em seguida, escolha **OK**.
    * **Localização**: Selecione uma localização, como **E.U.A. Leste**.
    * **Nome do cluster**: Introduza um nome exclusivo para o cluster do AKS, tal como *myAKSCluster*.
    * **Prefixo DNS**: Introduza um prefixo DNS exclusivo para o seu cluster, tal como *myakscluster*.
    * **O nome de utilizador do Linux administrador**: Introduza um nome de utilizador para ligar através de SSH, tal como *azureuser*.
    * **SSH chave pública RSA**: Copie e cole o *pública* faz parte do par de chaves SSH (por predefinição, o conteúdo do *~/.ssh/id_rsa.pub*).
    * **Id de cliente do Principal de serviço**: Copie e cole o *appId* do seu serviço principal do `az ad sp create-for-rbac` comando.
    * **Segredo do cliente de Principal de serviço**: Copie e cole o *palavra-passe* do seu serviço principal do `az ad sp create-for-rbac` comando.
    * **Eu concordo com o estado de termos e condições acima**: Marque esta caixa para aceitar.

    ![Modelo do Resource Manager para criar um cluster do serviço Kubernetes do Azure no portal](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Selecione **Comprar**.

Demora alguns minutos para criar o cluster do AKS. Aguarde que o cluster ser implementado com êxito antes de passar para o passo seguinte.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerir um cluster de Kubernetes, utilize [kubectl][kubectl], o cliente de linha de comandos do Kubernetes. Se utilizar o Azure Cloud Shell, `kubectl` já está instalado. Para instalar `kubectl` localmente, utilize o [az aks install-cli][az-aks-install-cli] comando:

```azurecli
az aks install-cli
```

Para configurar `kubectl` para ligar ao seu cluster do Kubernetes, utilize o [az aks get-credentials][az-aks-get-credentials] comando. Este comando transfere credenciais e configura a CLI do Kubernetes para utilizá-los.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

O resultado de exemplo seguinte mostra os nós criado nos passos anteriores. Certifique-se de que o estado de todos os nós é *pronto*:

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6
aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
```

## <a name="run-the-application"></a>Executar a aplicação

Um arquivo de manifesto do Kubernetes define um estado pretendido para o cluster, como o contentor de imagens para executar. Neste início rápido, é utilizado um manifesto para criar todos os objetos necessários para executar a aplicação Azure Vote. Esse manifesto inclui dois [implementações de Kubernetes][kubernetes-deployment] - one for the sample Azure Vote Python applications, and the other for a Redis instance. Two [Kubernetes Services][kubernetes-service] também são criados - um serviço interno para a instância de Redis e um serviço externo para aceder a aplicação Azure Vote a partir da internet.

> [!TIP]
> Neste início rápido, crie e implemente manualmente os seus manifestos de aplicação para o cluster do AKS. Em mais cenários de mundo real, pode usar [do Azure Dev espaços][azure-dev-spaces] rapidamente iterar e depurar seu código diretamente no cluster do AKS. Pode utilizar o Dev Spaces em várias plataformas do SO e ambientes de desenvolvimento, e trabalhar em conjunto com outras pessoas na sua equipa.

Crie um ficheiro denominado `azure-vote.yaml` e copie a seguinte definição de YAML. Se utilizar o Azure Cloud Shell, este ficheiro pode ser criado usando `vi` ou `nano` como se trabalhar num sistema físico ou virtual:

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

Implemente a aplicação com o [kubectl aplicar][kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```azurecli-interactive
kubectl apply -f azure-vote.yaml
```

O resultado de exemplo seguinte mostra as implementações e os serviços criados com êxito:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testar a aplicação

Quando o aplicativo for executado, um serviço do Kubernetes expõe à aplicação de front-end à internet. Este processo pode demorar alguns minutos a concluir.

Para monitorizar o progresso, utilize o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Inicialmente o *EXTERNAL-IP* para o *do azure-vote-front* serviço é mostrado como *pendente*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o *EXTERNAL-IP* é alterado de endereço *pendente* para um endereço IP público real, utilize `CTRL-C` para parar o `kubectl` veja processo. O resultado de exemplo seguinte mostra um endereço IP público válido atribuído ao serviço:

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver a aplicação Azure Vote em ação, abra um navegador da web para o endereço IP externo do seu serviço.

![Imagem de navegação para o Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Eliminar o cluster

Quando o cluster já não é necessário, utilize o [eliminação do grupo de az][az-group-delete] de comando para remover o grupo de recursos, o serviço de contentor e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter passos sobre como remover o principal de serviço, consulte [considerações principal e de eliminação do serviço AKS][sp-delete].

## <a name="get-the-code"></a>Obter o código

Neste início rápido, foram utilizadas imagens de contentores pré-criadas para criar uma implementação de Kubernetes. O código da aplicação relacionado, o Dockerfile, e o ficheiro de manifesto do Kubernetes, estão disponíveis no GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, implementou um cluster do Kubernetes e implementou uma aplicação de vários contentores no mesmo. [Aceder ao dashboard do Kubernetes web][kubernetes-dashboard] para o cluster que criou.

Para saber mais sobre o AKS e ver um exemplo completo de código para implementação, avance para o tutorial dos clusters de Kubernetes.

> [!div class="nextstepaction"]
> [Tutorial do AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

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
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
