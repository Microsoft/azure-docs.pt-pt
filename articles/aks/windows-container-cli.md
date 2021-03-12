---
title: Criar um recipiente do Windows Server num cluster AKS utilizando o Azure CLI
description: Aprenda a criar rapidamente um cluster Kubernetes, implementar uma aplicação num contentor do Windows Server no Serviço Azure Kubernetes (AKS) utilizando o Azure CLI.
services: container-service
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 13b4fbd21bb348d1ef79a3ca68128869115745cc
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200895"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Criar um recipiente do Windows Server num cluster Azure Kubernetes Service (AKS) utilizando o Azure CLI

O Azure Kubernetes Service (AKS) é um serviço gerido pela Kubernetes que permite implementar e gerir rapidamente clusters. Neste artigo, você implanta um cluster AKS usando o Azure CLI. Também implementa uma aplicação de amostra ASP.NET num recipiente do Windows Server para o cluster.

![Imagem de navegação para ASP.NET aplicação da amostra](media/windows-container/asp-net-sample-app.png)

Este artigo pressupõe uma compreensão básica dos conceitos de Kubernetes. Para obter mais informações, consulte [os conceitos fundamentais da Kubernetes para o Serviço Azure Kubernetes (AKS)][kubernetes-concepts].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

### <a name="limitations"></a>Limitações

Aplicam-se as seguintes limitações quando cria e gere clusters AKS que suportam múltiplas piscinas de nó:

* Não pode apagar a primeira piscina de nós.

As seguintes limitações adicionais aplicam-se aos conjuntos de nó de nó do Windows Server:

* O cluster AKS pode ter um máximo de 10 piscinas de nó.
* O cluster AKS pode ter um máximo de 100 nós em cada piscina de nós.
* O nome do número de conjunto do Windows Server tem um limite de 6 caracteres.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. Quando cria um grupo de recursos, é-lhe pedido que especifique uma localização. Esta localização é onde os metadados do grupo de recursos são armazenados, é também onde os seus recursos funcionam em Azure se você não especificar outra região durante a criação de recursos. Criar um grupo de recursos utilizando o [grupo az criar][az-group-create] comando.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

> [!NOTE]
> Este artigo usa a sintaxe bash para os comandos neste tutorial.
> Se estiver a utilizar a Azure Cloud Shell, certifique-se de que o recuo na parte superior esquerda da janela Cloud Shell está definido para **Bash**.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

A saída de exemplo a seguir mostra o grupo de recursos criado com sucesso:

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

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Para executar um cluster AKS que suporta piscinas de nós para recipientes do Windows Server, o seu cluster precisa de utilizar uma política de rede que utilize plugin de rede [Azure CNI][azure-cni-about] (avançado). Para obter informações mais pormenorizadas para ajudar a planear as gamas de sub-redes necessárias e considerações de rede, consulte [a configuração da rede CNI Azure][use-advanced-networking]. Utilize os [az aks criar][az-aks-create] comando para criar um cluster AKS chamado *myAKSCluster*. Este comando criará os recursos de rede necessários se não existirem.

* O aglomerado está configurado com dois nós.
* Os `--windows-admin-password` `--windows-admin-username` parâmetros e parâmetros definem as credenciais de administração de quaisquer contentores do Windows Server criados no cluster e devem satisfazer os [requisitos de senha do Windows Server][windows-server-password]. Se não especificar o parâmetro *de palavra-passe para administração de janelas,* será solicitado que forneça um valor.
* A piscina de nó `VirtualMachineScaleSets` usa.

> [!NOTE]
> Para garantir que o seu cluster funcione de forma fiável, deverá executar pelo menos 2 (dois) nós na piscina de nós predefinidos.

Crie um nome de utilizador para utilizar como credenciais de administrador para os seus recipientes do Windows Server no seu cluster. Os seguintes comandos solicitam-lhe um nome de utilizador e define-o WINDOWS_USERNAME para utilização num comando posterior (lembre-se que os comandos deste artigo são introduzidos numa concha BASH).

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server containers on your cluster: " && read WINDOWS_USERNAME
```

Crie o seu cluster certificando-se de que especifica `--windows-admin-username` o parâmetro. O seguinte exemplo de comando cria um cluster utilizando o valor a partir de *WINDOWS_USERNAME* que definiu no comando anterior. Em alternativa, pode fornecer um nome de utilizador diferente diretamente no parâmetro em vez de utilizar *WINDOWS_USERNAME*. O seguinte comando também lhe solicitará a criação de uma palavra-passe para as credenciais de administrador para os seus Contentores do Servidor do Windows no seu cluster. Em alternativa, pode utilizar o parâmetro *de palavra-passe para administração de janelas* e especificar o seu próprio valor lá.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --generate-ssh-keys \
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --network-plugin azure
```

> [!NOTE]
> Se obter um erro de validação de palavra-passe, verifique se a palavra-passe que definiu satisfaz os [requisitos][windows-server-password]de senha do Windows Server . Se a sua palavra-passe satisfaz os requisitos, tente criar o seu grupo de recursos noutra região. Em seguida, tente criar o cluster com o novo grupo de recursos.

Após alguns minutos, o comando completa e devolve informações formatadas com JSON sobre o cluster. Ocasionalmente, o cluster pode demorar mais do que alguns minutos a providenciar. Deixe até 10 minutos nestes casos.

## <a name="add-a-windows-server-node-pool"></a>Adicione uma piscina de nó do servidor do Windows

Por padrão, um cluster AKS é criado com uma piscina de nó que pode executar recipientes Linux. Utilize `az aks nodepool add` o comando para adicionar uma piscina adicional de nós que pode executar os recipientes do Windows Server ao lado da piscina de nóleiros Linux.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

O comando acima cria uma nova piscina de nó chamado *npwin* e adiciona-o ao *myAKSCluster*. Ao criar uma piscina de nó para executar os recipientes do Windows Server, o valor padrão para *o tamanho do nó vm* é *Standard_D2s_v3*. Se optar por definir o parâmetro *de tamanho nó-vm,* verifique a lista de [tamanhos VM restritos][restricted-vm-sizes]. O tamanho mínimo recomendado é *Standard_D2s_v3*. O comando acima também utiliza a sub-rede predefinida na vnet predefinida criada ao executar `az aks create` .

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerir um cluster Kubernetes, você usa [kubectl,][kubectl]o cliente da linha de comando Kubernetes. Se utilizar a Azure Cloud Shell, `kubectl` já está instalada. Para instalar `kubectl` localmente, utilize o comando [az aks instalar-cli:][az-aks-install-cli]

```azurecli
az aks install-cli
```

Para configurar `kubectl` para se ligar ao cluster do Kubernetes, utilize o comando [az aks get-credentials][az-aks-get-credentials]. Este comando descarrega credenciais e configura o CLI de Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```console
kubectl get nodes
```

A saída de exemplo a seguir mostra todos os nós do cluster. Certifique-se de que o estado de todos os nós está *pronto:*

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.9
aksnpwin987654                      Ready    agent   108s   v1.16.9
```

## <a name="run-the-application"></a>Executar a aplicação

Um ficheiro manifesto kubernetes define um estado desejado para o cluster, como as imagens do recipiente a executar. Neste artigo, é utilizado um manifesto para criar todos os objetos necessários para executar a aplicação de amostra ASP.NET num recipiente do Windows Server. Este manifesto inclui uma [implementação de Kubernetes][kubernetes-deployment] para a aplicação de amostra ASP.NET e um [serviço externo de Kubernetes][kubernetes-service] para aceder à aplicação a partir da internet.

A aplicação ASP.NET amostra é fornecida como parte das [amostras-quadro .NET][dotnet-samples] e funciona num recipiente do Windows Server. O AKS exige que os contentores do Windows Server sejam baseados em imagens do *Windows Server 2019* ou superiores. O ficheiro manifesto Kubernetes também deve definir um [seletor de nó][node-selector] para dizer ao seu cluster AKS para executar o seu ASP.NET pod da aplicação da amostra num nó que pode executar recipientes do Windows Server.

Crie um ficheiro nomeado `sample.yaml` e copie na seguinte definição YAML. Se utilizar o Azure Cloud Shell, este ficheiro pode ser criado utilizando `vi` ou `nano` funcionando num sistema virtual ou físico:

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
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
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

Implemente a aplicação utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f sample.yaml
```

A saída de exemplo a seguir mostra a Implementação e o Serviço criados com sucesso:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testar a aplicação

Quando a aplicação é executado, um serviço Kubernetes expõe a linha frontal da aplicação para a internet. Este processo pode demorar alguns minutos a concluir. Ocasionalmente, o serviço pode demorar mais do que alguns minutos a providenciar. Deixe até 10 minutos nestes casos.

Para monitorizar o progresso, utilize o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```console
kubectl get service sample --watch
```

Inicialmente, o *EXTERNAL-IP* para o serviço de *amostragem* é apresentado como *pendente*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o endereço *EXTERNAL-IP* mudar de *pendente* para um endereço IP público real, use `CTRL-C` para parar o processo do `kubectl` relógio. A saída de exemplo a seguir mostra um endereço IP público válido atribuído ao serviço:

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver a aplicação da amostra em ação, abra um navegador web para o endereço IP externo do seu serviço.

![Imagem de navegação para ASP.NET aplicação da amostra](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Se receber um tempo limite de ligação ao tentar carregar a página, então deve verificar se a aplicação da amostra está pronta com o seguinte comando [kubectl get pods --watch]. Por vezes, o recipiente das janelas não será iniciado no momento em que o seu endereço IP externo estiver disponível.

## <a name="delete-cluster"></a>Eliminar o cluster

Quando o cluster já não for necessário, utilize o comando [az group delete][az-group-delete] para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter passos sobre como remover o principal de serviço, consulte [Considerações sobre e eliminação do principal de serviço AKS][sp-delete]. Se usou uma identidade gerida, a identidade é gerida pela plataforma e não necessita de remoção.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, implementou um cluster Kubernetes e implementou uma aplicação de amostra ASP.NET num recipiente do Windows Server. [Aceda ao painel web Kubernetes][kubernetes-dashboard] para o cluster que acabou de criar.

Para saber mais sobre o AKS e ver um exemplo completo de código para implementação, avance para o tutorial dos clusters de Kubernetes.

> [!div class="nextstepaction"]
> [Tutorial AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
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
[use-advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference