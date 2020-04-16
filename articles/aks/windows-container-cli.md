---
title: Executar o recipiente do Servidor Windows no cluster do Serviço Azure Kubernetes
description: Aprenda a criar rapidamente um cluster Kubernetes, implemente uma aplicação num recipiente do Windows Server no Serviço Azure Kubernetes (AKS) utilizando o Azure CLI.
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 2aecebcc45cb24c9ab3a594aa4d74b1584c7ffa7
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392665"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Pré-visualização - Crie um recipiente de Servidor Windows num cluster do Serviço Azure Kubernetes (AKS) utilizando o Azure CLI

O Azure Kubernetes Service (AKS) é um serviço gerido pela Kubernetes que permite implementar e gerir rapidamente clusters. Neste artigo, você implanta um cluster AKS usando o Azure CLI. Também implementa uma aplicação de amostra ASP.NET num recipiente do Windows Server para o cluster.

Esta funcionalidade encontra-se em pré-visualização.

![Imagem de navegação para ASP.NET aplicação da amostra](media/windows-container/asp-net-sample-app.png)

Este artigo assume uma compreensão básica dos conceitos kubernetes. Para mais informações, consulte os [conceitos centrais da Kubernetes para o Serviço Azure Kubernetes (AKS)][kubernetes-concepts].

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este artigo requer que esteja a executar a versão Azure CLI 2.0.61 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="before-you-begin"></a>Antes de começar

Tem de adicionar uma piscina adicional de nó depois de criar o seu cluster que pode executar os recipientes do Windows Server. A adição de uma piscina adicional de nó é coberta por um passo posterior, mas primeiro precisa de ativar algumas funcionalidades de pré-visualização.

> [!IMPORTANT]
> As funcionalidades de pré-visualização AKS são opt-in self-service. As pré-visualizações são fornecidas "as-is" e "conforme disponível" e estão excluídas dos acordos de nível de serviço e da garantia limitada. As pré-visualizações aks são parcialmente cobertas pelo apoio ao cliente na melhor base de esforço. Como tal, estas características não se destinam à utilização da produção. Para obter informações adicionais, consulte os seguintes artigos de apoio:
>
> * [Políticas de apoio aks][aks-support-policies]
> * [FaQ de suporte azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Instale a extensão CLI de pré-visualização de aks

Para utilizar os recipientes do Windows Server, necessita da versão de extensão CLI de *pré-visualização* de aks 0.4.12 ou superior. Instale a extensão Azure CLI de *pré-visualização de aks* utilizando o comando de adição de [extensão az][az-extension-add] e, em seguida, verifique se há atualizações disponíveis utilizando o comando de atualização de [extensão az::][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>Registar funcionalidade de pré-visualização do Windows

Para criar um cluster AKS que pode usar várias piscinas de nós e executar recipientes Do Windows Server, primeiro ative as bandeiras da funcionalidade *WindowsPreview* na sua subscrição. A funcionalidade *WindowsPreview* também utiliza clusters de piscinas de vários nós e conjunto de escala de máquina virtual para gerir a implementação e configuração dos nós kubernetes. Registe a bandeira da funcionalidade *WindowsPreview* utilizando o comando de registo de [funcionalidades Az,][az-feature-register] como mostra o seguinte exemplo:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Qualquer cluster AKS que criar depois de ter registado com sucesso a bandeira da funcionalidade *WindowsPreview* utiliza esta experiência de cluster de pré-visualização. Para continuar a criar clusters regulares e totalmente suportados, não permita funcionalidades de pré-visualização nas subscrições de produção. Utilize uma assinatura Azure de teste ou desenvolvimento separada para testar funcionalidades de pré-visualização.

Leva alguns minutos para a inscrição ser completada. Verifique o estado de registo utilizando o comando da [lista de características az:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Quando o estado `Registered`de registo estiver, pressione ctrl-C para parar de monitorizar o estado.  Em seguida, atualizar o registo do fornecedor de recursos *Microsoft.ContainerService* utilizando o comando de registo do [fornecedor az:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se quando cria e gere clusters AKS que suportam múltiplos conjuntos de nós:

* Não pode apagar a primeira piscina de nó.

Enquanto esta funcionalidade estiver em pré-visualização, aplicam-se as seguintes limitações adicionais:

* O cluster AKS pode ter um máximo de oito piscinas de nós.
* O aglomerado AKS pode ter um máximo de 400 nós através dessas oito piscinas de nós.
* O nome da piscina do Windows Server tem um limite de 6 caracteres.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. Quando cria um grupo de recursos, é-lhe pedido que especifique uma localização. Esta localização é onde os metadados do grupo de recursos são armazenados, é também onde os seus recursos funcionam em Azure se você não especificar outra região durante a criação de recursos. Criar um grupo de recursos utilizando o [grupo AZ criar][az-group-create] comando.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

> [!NOTE]
> Este artigo usa a sintaxe bash para os comandos neste tutorial.
> Se estiver a utilizar a Casca de Nuvem Azure, certifique-se de que a queda na parte superior esquerda da janela Cloud Shell está definida para **bash**.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

A saída de exemplo seguinte mostra o grupo de recursos criado com sucesso:

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

Para executar um cluster AKS que suporta piscinas de nós para contentores Do Windows Server, o seu cluster precisa de usar uma política de rede que utilize plugin de rede [Azure CNI][azure-cni-about] (avançado). Para obter informações mais detalhadas para ajudar a planear as gamas de subnet necessárias e considerações de rede, consulte a configuração da [rede Azure CNI][use-advanced-networking]. Use o [az aks criar][az-aks-create] comando para criar um cluster AKS chamado *myAKSCluster*. Este comando criará os recursos de rede necessários se não existirem.
  * O cluster está configurado com dois nós
  * Os parâmetros *windows-admin-password* e *windows-admin-username* definiram as credenciais de administração para quaisquer recipientes do Windows Server criados no cluster.

> [!NOTE]
> Para garantir que o seu cluster funcione de forma fiável, deve executar pelo menos 2 (dois) nós na piscina de nós padrão.

Forneça o seu próprio *PASSWORD_WIN* seguro (lembre-se que os comandos deste artigo são inseridos numa concha BASH):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --kubernetes-version 1.15.7 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --network-plugin azure
```

> [!Note]
> Se tiver um erro de validação de palavra-passe, tente criar o seu grupo de recursos noutra região.
> Em seguida, tente criar o cluster com o novo grupo de recursos.

> [!Note]
> Se não conseguir criar o cluster AKS porque a versão não é suportada nesta região, então pode usar o comando [az aks get-versions -- localização eastus] para encontrar a lista de versão suportada para esta região.


Após alguns minutos, o comando completa e devolve informações formatadas da JSON sobre o cluster. Ocasionalmente, o cluster pode demorar mais do que alguns minutos a fornecer. Deixe até 10 minutos nestes casos. 

## <a name="add-a-windows-server-node-pool"></a>Adicione uma piscina de nó do Windows Server

Por padrão, um cluster AKS é criado com uma piscina de nó que pode executar contentores Linux. Utilize `az aks nodepool add` o comando para adicionar uma piscina adicional de nó que pode executar os recipientes do Windows Server.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.15.7
```

O comando acima cria uma nova piscina de nó chamada *npwin* e adiciona-a ao *myAKSCluster*. Ao criar uma piscina de nó para executar recipientes do Windows Server, o valor padrão para o tamanho do *nó-vm* é *Standard_D2s_v3*. Se optar por definir o parâmetro do tamanho do *nó vm,* verifique a lista de [tamanhos vm restritos][restricted-vm-sizes]. O tamanho mínimo recomendado é *Standard_D2s_v3*. O comando acima também utiliza a sub-rede predefinida na vnet predefinida criada durante a execução `az aks create`.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerir um cluster Kubernetes, você usa [kubectl,][kubectl]o cliente da linha de comando Kubernetes. Se utilizar a Azure `kubectl` Cloud Shell, já está instalada. Para `kubectl` instalar localmente, utilize o comando [az aks install-cli:][az-aks-install-cli]

```azurecli
az aks install-cli
```

Para configurar `kubectl` para se ligar ao cluster do Kubernetes, utilize o comando [az aks get-credentials][az-aks-get-credentials]. Este comando descarrega credenciais e confunde o ClI Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```console
kubectl get nodes
```

A saída de exemplo que se segue mostra todos os nós do cluster. Certifique-se de que o estado de todos os nós está *pronto:*

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.15.7
aksnpwin987654                      Ready    agent   108s   v1.15.7
```

## <a name="run-the-application"></a>Executar a aplicação

Um ficheiro manifesto Kubernetes define um estado desejado para o cluster, como quais as imagens de contentores a executar. Neste artigo, é utilizado um manifesto para criar todos os objetos necessários para executar a aplicação de amostra ASP.NET num recipiente do Windows Server. Este manifesto inclui uma [implementação de Kubernetes][kubernetes-deployment] para a aplicação de amostra seletiva ASP.NET e um [serviço externo de Kubernetes][kubernetes-service] para aceder à aplicação a partir da internet.

A aplicação ASP.NET amostra é fornecida como parte das [Amostras de Quadro .NET][dotnet-samples] e funciona num recipiente do Windows Server. O AKS exige que os recipientes do Windows Server sejam baseados em imagens do *Windows Server 2019* ou superior. O ficheiro manifesto Kubernetes também deve definir um [seletor][node-selector] de nó para dizer ao seu cluster AKS para executar o seu casulo de ASP.NET aplicação de amostra num nó que pode executar recipientes do Windows Server.

Crie um `sample.yaml` ficheiro nomeado e copie na seguinte definição YAML. Se utilizar a Casca de Nuvem Azure, `vi` `nano` este ficheiro pode ser criado utilizando ou como se trabalhasse num sistema virtual ou físico:

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

Implemente a aplicação utilizando o [kubectl aplique][kubectl-apply] o comando e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f sample.yaml
```

A saída de exemplo a seguir mostra a Implantação e o Serviço criados com sucesso:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testar a aplicação

Quando a aplicação é executado, um serviço Kubernetes expõe a extremidade frontal da aplicação à internet. Este processo pode demorar alguns minutos a concluir. Ocasionalmente, o serviço pode demorar mais do que alguns minutos a fornecer. Deixe até 10 minutos nestes casos.

Para monitorizar o progresso, utilize o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```console
kubectl get service sample --watch
```

Inicialmente, o *IP EXTERNO* para o serviço de *amostragem* é apresentado como *pendente*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o endereço *EXTERNO-IP* passar de *pendente* para `CTRL-C` um `kubectl` endereço IP público real, utilize para parar o processo de observação. A saída de exemplo seguinte mostra um endereço IP público válido atribuído ao serviço:

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver a aplicação da amostra em ação, abra um navegador web para o endereço IP externo do seu serviço.

![Imagem de navegação para ASP.NET aplicação da amostra](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Se receber um tempo de ligação ao tentar carregar a página, deve verificar se a aplicação da amostra está pronta com o seguinte comando [kubectl get pods --watch]. Por vezes, o recipiente para janelas não será iniciado quando o seu endereço IP externo estiver disponível.

## <a name="delete-cluster"></a>Eliminar o cluster

Quando o cluster já não for necessário, utilize o comando [az group delete][az-group-delete] para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter passos sobre como remover o principal de serviço, consulte [Considerações sobre e eliminação do principal de serviço AKS][sp-delete]. Se usou uma identidade gerida, a identidade é gerida pela plataforma e não requer remoção.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, implementou um cluster Kubernetes e implementou uma aplicação de amostra ASP.NET num recipiente do Windows Server para o mesmo. [Aceda ao painel web da Kubernetes][kubernetes-dashboard] para o cluster que acabaste de criar.

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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
