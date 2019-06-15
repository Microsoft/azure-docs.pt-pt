---
title: Pré-visualizar - criar um contentor do Windows Server num cluster do Azure Kubernetes Service (AKS)
description: Aprenda rapidamente a criar um cluster do Kubernetes, implementar uma aplicação num contentor do Windows Server no Azure Kubernetes Service (AKS) com a CLI do Azure.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 06/06/2019
ms.author: twhitney
ms.openlocfilehash: cdcc1b985c570d1af4bbb33ac29a37e63b1dfa90
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66752384"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Pré-visualizar - criar um contentor do Windows Server num cluster do Azure Kubernetes Service (AKS) utilizando a CLI do Azure

O Azure Kubernetes Service (AKS) é um serviço de Kubernetes gerido que permite-lhe implementar e gerir clusters rapidamente. Neste artigo, vai implementar um cluster do AKS com a CLI do Azure. Também é implantar um aplicativo de exemplo ASP.NET num contentor do Windows Server para o cluster.

Esta funcionalidade encontra-se em pré-visualização.

![Imagem de navegação à aplicação de exemplo ASP.NET](media/windows-container/asp-net-sample-app.png)

Este artigo pressupõe um conhecimento básico dos conceitos do Kubernetes. Para obter mais informações, consulte [Kubernetes principais conceitos para o Azure Kubernetes Service (AKS)][kubernetes-concepts].

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0.61 da CLI do Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="before-you-begin"></a>Antes de começar

Tem de adicionar um conjunto de nós adicionais depois de criar o cluster que pode executar contentores do Windows Server. Adicionar um conjunto de nós adicionais trata-se num passo posterior, mas primeiro tem de ativar algumas funcionalidades de pré-visualização.

> [!IMPORTANT]
> Funcionalidades de pré-visualização do AKS são self-service, participar. Eles são fornecidos para recolher comentários e bugs de nossa Comunidade. Em pré-visualização, esses recursos não se destinam a utilização de produção. Funcionalidades em pré-visualização pública enquadram-se em suporte "melhor esforço". Assistência das equipas de suporte técnico do AKS está disponível durante o horário do Pacífico fuso horário (PST) apenas. Para obter mais informações, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS][aks-support-policies]
> * [FAQ de suporte do Azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão CLI de pré-visualização do aks
    
Os comandos da CLI para criar e gerir vários conjuntos de nós estão disponíveis no *pré-visualização do aks* extensão da CLI. Instalar o *pré-visualização do aks* extensão da CLI do Azure com o [Adicionar extensão az] [ az-extension-add] de comando, conforme mostrado no exemplo a seguir:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Se instalou anteriormente a *pré-visualização do aks* extensão, instale qualquer disponível atualizações com o `az extension update --name aks-preview` comando.

### <a name="register-windows-preview-feature"></a>Registe-se a funcionalidade de pré-visualização do Windows

Para criar um cluster do AKS que pode utilizar vários conjuntos de nós e executar contentores do Windows Server, ativar a *WindowsPreview* sinalizadores na sua subscrição de recursos. O *WindowsPreview* recurso também usa clusters de conjunto de vários nós e o dimensionamento de máquinas virtuais definido como gerir a implementação e configuração de nós do Kubernetes. Registe-se a *WindowsPreview* através do sinalizador de funcionalidade a [Registre-se de funcionalidade de az] [ az-feature-register] comando conforme mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Qualquer cluster do AKS criar depois de registar com êxito a *WindowsPreview* sinalizador de funcionalidade utiliza esta experiência de cluster de pré-visualização. Para continuar a criar clusters regulares, totalmente suportada, não a ativar funcionalidades de pré-visualização em subscrições de produção. Utilize um desenvolvimento subscrição do Azure ou de teste separada para fins de teste de funcionalidades de pré-visualização.

Demora alguns minutos para que o estado a mostrar *registado*. Pode verificar o estado de registo com o [lista de funcionalidades de az] [ az-feature-list] comando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registo do *containerservice* fornecedor de recursos com o [Registre-se fornecedor de az] [ az-provider-register] comando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se ao criar e gerir clusters do AKS que oferecem suporte a vários conjuntos de nós:

* Vários conjuntos de nós estão disponíveis nos clusters criados após ter registrado com êxito a *WindowsPreview*. Vários conjuntos de nós também estão disponíveis se registar os *MultiAgentpoolPreview* e *VMSSPreview* funcionalidades para a sua subscrição. Não é possível adicionar ou gerir conjuntos de nós com um cluster do AKS existente criado antes desses recursos foram registados com êxito.
* Não é possível eliminar o primeiro conjunto de nós.

Enquanto esta funcionalidade está em pré-visualização, aplicam-se as seguintes limitações adicionais:

* O cluster do AKS pode ter um máximo de oito conjuntos de nós.
* O cluster do AKS pode ter um máximo de 400 nós entre esses conjuntos de oito nós.
* O nome de agrupamento do Windows Server nó tem um limite de 6 carateres.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. Quando cria um grupo de recursos, é-lhe pedido que especifique uma localização. Esta localização é onde os metadados de grupo de recursos são armazenados, também é onde executar a seus recursos no Azure se não especificar outra região durante a criação de recursos. Criar um grupo de recursos utilizando o [criar grupo az] [ az-group-create] comando.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

O resultado de exemplo seguinte mostra o grupo de recursos criado com êxito:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-aks-cluster"></a>Criar um cluster do AKS
Para executar um cluster do AKS que suporta conjuntos de nós para contentores do Windows Server, o cluster tem de utilizar uma política de rede que utiliza [Azure CNI] [ azure-cni-about] Plug-in de rede (avançada). Para obter mais informações para ajudar a planear os intervalos de sub-rede obrigatório e considerações de rede, consulte [configurar redes do Azure CNI][use-advanced-networking]. Utilize o [criar az aks] [ az-aks-create] comando para criar um cluster do AKS com o nome *myAKSCluster*. Este comando irá criar os recursos de rede necessárias, caso não existam.
  * O cluster está configurado com um nó
  * O *windows-admin-palavra-passe* e *windows--nome de utilizador administrador* parâmetros definir as credenciais de administrador para quaisquer contentores do Windows Server criadas no cluster.

Fornecer seu próprio segura *PASSWORD_WIN*.

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.0 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

Após alguns minutos, o comando é concluído e devolve o formato JSON informações sobre o cluster.

## <a name="add-a-windows-server-node-pool"></a>Adicionar um conjunto de nós do Windows Server

Por predefinição, é criado um cluster do AKS com um conjunto de nós que pode executar contentores do Linux. Utilize `az aks nodepool add` comando para adicionar um conjunto de nós adicionais que pode executar contentores do Windows Server.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.0
```

O comando acima cria um novo conjunto de nós com o nome *npwin* e adiciona-o para o *myAKSCluster*. Ao criar um conjunto de nós para executar contentores do Windows Server, o valor predefinido para *tamanho da vm de nó* é *Standard_D2s_v3*. Se optar por definir os *tamanho da vm de nó* parâmetro, verifique a lista de [restrito tamanhos de VM][restricted-vm-sizes]. É o mínimo de tamanho recomendado *Standard_D2s_v3*. O comando acima utiliza também a sub-rede de predefinição na vnet predefinida criada durante a execução `az aks create`.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerir um cluster de Kubernetes, utilize [kubectl][kubectl], o cliente de linha de comandos do Kubernetes. Se utilizar o Azure Cloud Shell, `kubectl` já está instalado. Para instalar `kubectl` localmente, utilize o [az aks install-cli] [ az-aks-install-cli] comando:

```azurecli
az aks install-cli
```

Para configurar `kubectl` para se ligar ao cluster do Kubernetes, utilize o comando [az aks get-credentials][az-aks-get-credentials]. Este comando transfere credenciais e configura a CLI do Kubernetes para utilizá-los.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo seguinte mostra o nó único criado nos passos anteriores. Certifique-se de que o estado do nó é *pronto*:

```
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.0
aksnpwin987654                      Ready    agent   108s   v1.14.0
```

## <a name="run-the-application"></a>Executar a aplicação

Um arquivo de manifesto do Kubernetes define um estado pretendido para o cluster, como o contentor de imagens para executar. Neste artigo, é utilizado um manifesto para criar todos os objetos necessários para executar o aplicativo de exemplo ASP.NET num contentor do Windows Server. Esse manifesto inclui um [implementação do Kubernetes] [ kubernetes-deployment] para o aplicativo de exemplo ASP.NET e um externo [serviço de Kubernetes] [ kubernetes-service] para Aceda à aplicação a partir da internet.

O aplicativo de exemplo do ASP.NET é fornecido como parte da [amostras do .NET Framework] [ dotnet-samples] e é executado num contentor do Windows Server. AKS requer contentores do Windows Server para ser baseadas nas imagens de *Windows Server 2019* ou superior. O Kubernetes, arquivo de manifesto também tem de definir uma [Seletor de nó] [ node-selector] para informar ao seu cluster do AKS para executar o pod de seu aplicativo de exemplo ASP.NET num nó que pode executar contentores do Windows Server.

Crie um ficheiro denominado `sample.yaml` e copie a seguinte definição de YAML. Se utilizar o Azure Cloud Shell, este ficheiro pode ser criado usando `vi` ou `nano` como se trabalhar num sistema físico ou virtual:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800m
          requests:
            cpu: .1
            memory: 300m
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Implemente a aplicação com o [aplicam-se de kubectl] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```azurecli-interactive
kubectl apply -f sample.yaml
```

O resultado de exemplo seguinte mostra a implementação e o serviço foi criado com êxito:

```
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testar a aplicação

Quando o aplicativo for executado, um serviço do Kubernetes expõe à aplicação de front-end à internet. Este processo pode demorar alguns minutos a concluir.

Para monitorizar o progresso, utilize o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```azurecli-interactive
kubectl get service sample --watch
```

Inicialmente o *EXTERNAL-IP* para o *exemplo* serviço é mostrado como *pendente*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o *EXTERNAL-IP* é alterado de endereço *pendente* para um endereço IP público real, utilize `CTRL-C` para parar o `kubectl` veja processo. O resultado de exemplo seguinte mostra um endereço IP público válido atribuído ao serviço:

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver a aplicação de exemplo em ação, abra um navegador da web para o endereço IP externo do seu serviço.

![Imagem de navegação à aplicação de exemplo ASP.NET](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>Eliminar o cluster

Quando o cluster já não for necessário, utilize o comando [az group delete][az-group-delete] para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter passos sobre como remover o principal de serviço, consulte [Considerações sobre e eliminação do principal de serviço AKS][sp-delete].

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, implementou um cluster de Kubernetes e implementou uma aplicação de exemplo ASP.NET num contentor do Windows Server ao mesmo. [Aceder ao dashboard de web do Kubernetes] [ kubernetes-dashboard] para o cluster que acabou de criar.

Para saber mais sobre o AKS e ver um exemplo completo de código para implementação, avance para o tutorial dos clusters de Kubernetes.

> [!div class="nextstepaction"]
> [Tutorial do AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-advanced-networking.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
