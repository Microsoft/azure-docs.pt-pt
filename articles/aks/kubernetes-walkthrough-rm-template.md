---
title: Quickstart - Criar um cluster azure Kubernetes Service (AKS)
description: Aprenda a criar rapidamente um cluster Kubernetes usando um modelo de Gestor de Recursos Azure e implemente uma aplicação no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: quickstart
ms.date: 04/19/2019
ms.custom: mvc
ms.openlocfilehash: 5fe82dbce2069b0ae1a88599d020f4ba6b116027
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595336"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-azure-resource-manager-template"></a>Quickstart: Implemente um cluster azure Kubernetes Service (AKS) utilizando um modelo de Gestor de Recursos Azure

O Azure Kubernetes Service (AKS) é um serviço gerido pela Kubernetes que permite implementar e gerir rapidamente clusters. Neste arranque rápido, você implementa um cluster AKS usando um modelo de Gestor de Recursos Azure. Uma aplicação multi-contentor que inclui uma extremidade frontal web e uma instância Redis é executada no cluster.

![Imagem de navegação para o Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

Este guia de introdução parte do princípio de que possui conhecimentos básicos dos conceitos do Kubernetes. Para mais informações, consulte os [conceitos centrais da Kubernetes para o Serviço Azure Kubernetes (AKS)][kubernetes-concepts].

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este quickstart requer que esteja a executar a versão 2.0.61 do Azure CLI ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="prerequisites"></a>Pré-requisitos

Para criar um cluster AKS utilizando um modelo de Gestor de Recursos, fornece uma chave pública SSH e um diretor de serviço de Diretório Ativo Azure. Se precisar de algum destes recursos, consulte a seguinte secção; caso contrário, salte para a secção [de agrupamento criar uma AKS.](#create-an-aks-cluster)

### <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Para aceder aos nós AKS, ligue-se utilizando um par de chaves SSH. Utilize o comando `ssh-keygen` para gerar ficheiros de chaves públicas e privadas SSH. Por padrão, estes ficheiros são criados no diretório *~/.ssh.* Se um par de chaves SSH com o mesmo nome existir no local dado, esses ficheiros são substituídos.

O seguinte comando cria um par de chaves SSH usando encriptação RSA e um pouco de comprimento de 2048:

```azurecli-interactive
ssh-keygen -t rsa -b 2048
```

Para obter mais informações sobre a criação de chaves SSH, consulte [Criar e gerir as teclas SSH para autenticação em Azure][ssh-keys].

### <a name="create-a-service-principal"></a>Criar um principal de serviço

Para permitir que um cluster do AKS interaja com outros recursos do Azure, é utilizado um principal de serviço do Azure Active Directory. Crie um principal de serviço com o comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. O parâmetro `--skip-assignment` limita a atribuição de permissões adicionais. Por predefinição, este diretor de serviço é válido por um ano.

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

O modelo utilizado neste quickstart é implantar um cluster de [serviço Azure Kubernetes](https://azure.microsoft.com/resources/templates/101-aks/). Para mais amostras de AKS, consulte o site [de modelos de arranque rápido AKS.][aks-quickstart-templates]

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo.

    [![Implementar no Azure](./media/kubernetes-walkthrough-rm-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Selecione ou introduza os seguintes valores.  

    Para este arranque rápido, deixe os valores predefinidos para o *tamanho do disco OS GB,* Contagem de *Agentes,* *Tamanho do Agente VM,* *Tipo OS*e *versão Kubernetes*. Forneça os seus próprios valores para os seguintes parâmetros de modelo:

    * **Subscrição**: selecione uma subscrição do Azure.
    * **Grupo de recursos**: Selecione **Criar nova**. Introduza um nome único para o grupo de recursos, como o *myResourceGroup,* e depois escolha **OK**.
    * **Localização**: Selecione uma localização, como **east us**.
    * **Nome do cluster**: Introduza um nome único para o cluster AKS, como *myAKSCluster*.
    * **Prefixo DNS**: Introduza um prefixo DNS único para o seu cluster, como *myakscluster*.
    * **Nome de utilizador**do Administrador Linux : Introduza um nome de utilizador para se ligar utilizando o SSH, como *o azureuser*.
    * **Chave Pública SSH RSA**: Copiar e colar a parte *pública* do seu par de teclas SSH (por padrão, o conteúdo de *~/.ssh/id_rsa.pub).*
    * **Id principal de serviço Cliente:** Copie e cole a *appId* do seu diretor de serviço a partir do comando `az ad sp create-for-rbac`.
    * **Serviço Principal Cliente Secreto**: Copie e cole a *palavra-passe* do seu diretor de serviço a partir do comando `az ad sp create-for-rbac`.
    * **Concordo com os termos e condições acima referidos:** Verifique esta caixa para concordar.

    ![Modelo de Gestor de Recursos para criar um cluster de serviço Azure Kubernetes no portal](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Selecione **Comprar**.

Leva alguns minutos para criar o aglomerado AKS. Aguarde que o cluster seja implantado com sucesso antes de passar para o próximo passo.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerir um cluster Kubernetes, você usa [kubectl,][kubectl]o cliente da linha de comando Kubernetes. Se utilizar a Azure Cloud Shell, já se `kubectl` instalada. Para instalar `kubectl` localmente, utilize o comando [az aks install-cli:][az-aks-install-cli]

```azurecli
az aks install-cli
```

Para configurar `kubectl` para se ligar ao seu cluster Kubernetes, use o comando [az aks get-credentials.][az-aks-get-credentials] Este comando descarrega credenciais e confunde o ClI Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo seguinte mostra os nódosos criados nos passos anteriores. Certifique-se de que o estado de todos os nós está *pronto:*

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6
aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
```

## <a name="run-the-application"></a>Executar a aplicação

Um ficheiro manifesto Kubernetes define um estado desejado para o cluster, como quais as imagens de contentores a executar. Neste início rápido, é utilizado um manifesto para criar todos os objetos necessários para executar a aplicação Azure Vote. Este manifesto inclui duas [implantações kubernetes][kubernetes-deployment] - uma para as aplicações azure vote Python, e outra para uma instância Redis. São também criados dois [Serviços Kubernetes][kubernetes-service] - um serviço interno para a instância Redis, e um serviço externo para aceder à aplicação Azure Vote a partir da internet.

> [!TIP]
> Neste início rápido, crie e implemente manualmente os seus manifestos de aplicação para o cluster do AKS. Em cenários mais reais, você pode usar [O Azure Dev Spaces][azure-dev-spaces] para iterar rapidamente e desinchar o seu código diretamente no cluster AKS. Pode utilizar o Dev Spaces em várias plataformas do SO e ambientes de desenvolvimento, e trabalhar em conjunto com outras pessoas na sua equipa.

Crie um ficheiro chamado `azure-vote.yaml` e copie na seguinte definição YAML. Se utilizar a Casca de Nuvem Azure, este ficheiro pode ser criado utilizando `vi` ou `nano` como se trabalhasse num sistema virtual ou físico:

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

A saída de exemplo a seguir mostra as Implantações e Serviços criados com sucesso:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testar a aplicação

Quando a aplicação é executado, um serviço Kubernetes expõe a extremidade frontal da aplicação à internet. Este processo pode demorar alguns minutos a concluir.

Para monitorizar o progresso, utilize o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Inicialmente, o *IP EXTERNO* para o serviço de frente para *voto sinuoso* é apresentado como *pendente*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o endereço *EXTERNO-IP* passar de *pendente* para um endereço IP público real, utilize `CTRL-C` para parar o processo de observação `kubectl`. A saída de exemplo seguinte mostra um endereço IP público válido atribuído ao serviço:

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver a aplicação Azure Vote em ação, abra um navegador web para o endereço IP externo do seu serviço.

![Imagem de navegação para o Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="delete-cluster"></a>Eliminar o cluster

Quando o cluster já não for necessário, utilize o [grupo AZ eliminar][az-group-delete] o comando para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter medidas sobre como remover o diretor de serviço, consulte as [principais considerações e a eliminação do serviço AKS.][sp-delete]

## <a name="get-the-code"></a>Obter o código

Neste arranque rápido, imagens de contentores pré-criadas foram usadas para criar uma implantação kubernetes. O código da aplicação relacionado, o Dockerfile, e o ficheiro de manifesto do Kubernetes, estão disponíveis no GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, implementou um cluster do Kubernetes e implementou uma aplicação de vários contentores no mesmo. [Aceda ao painel web da Kubernetes][kubernetes-dashboard] para o cluster que criou.

Para saber mais sobre o AKS e ver um exemplo completo de código para implementação, avance para o tutorial dos clusters de Kubernetes.

> [!div class="nextstepaction"]
> [Tutorial AKS][aks-tutorial]

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
