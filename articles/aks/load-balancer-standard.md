---
title: Pré-visualizar - utilizar um balanceador de carga de SKU padrão no Azure Kubernetes Service (AKS)
description: Saiba como utilizar um balanceador de carga com um SKU padrão para expor os seus serviços com o Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/25/2019
ms.author: zarhoads
ms.openlocfilehash: a9cf3db3a15fab5a2f067a146950e02923a20379
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476813"
---
# <a name="preview---use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Pré-visualizar - utilizar um balanceador de carga de SKU padrão no Azure Kubernetes Service (AKS)

Para fornecer acesso às suas aplicações no Azure Kubernetes Service (AKS), pode criar e utilizar um balanceador de carga do Azure. Um balanceador de carga em execução no AKS pode ser utilizado como um interna ou um balanceador de carga externo. Um balanceador de carga interno torna um serviço do Kubernetes acessível apenas para aplicações em execução na mesma rede virtual que o cluster do AKS. Um balanceador de carga externo recebe uma ou mais IPs públicos para entrada e faz com que um serviço do Kubernetes acessível externamente usando os IPs públicos.

O Balanceador de carga do Azure está disponível em dois SKUs - *básica* e *padrão*. Por predefinição, o *básica* SKU é utilizada quando é utilizado um manifesto de serviço para criar um balanceador de carga no AKS. Utilizar um *padrão* Balanceador de carga SKU fornece recursos adicionais e funcionalidades, como o maior tamanho de conjunto de back-end e as zonas de disponibilidade. É importante que compreenda as diferenças entre *padrão* e *básica* balanceadores de carga antes de escolher quais pode ser utilizado. Depois de criar um cluster do AKS, não é possível alterar o Balanceador de carga SKU para esse cluster. Para obter mais informações sobre o *básica* e *padrão* SKUs, consulte [comparação SKU do Balanceador de carga do Azure][azure-lb-comparison].

Este artigo mostra-lhe como criar e utilizar um balanceador de carga do Azure com o *padrão* SKU com o Azure Kubernetes Service (AKS).

Este artigo pressupõe uma compreensão básica dos conceitos do Kubernetes e do Balanceador de carga do Azure. Para obter mais informações, consulte [Kubernetes principais conceitos para o Azure Kubernetes Service (AKS)][kubernetes-concepts] and [What is Azure Load Balancer?][azure-lb].

Esta funcionalidade encontra-se em pré-visualização.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0.59 da CLI do Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][install-azure-cli].

## <a name="before-you-begin"></a>Antes de começar

Principal de serviço de cluster AKS necessita da permissão para gerir recursos de rede, se utilizar uma sub-rede existente ou um grupo de recursos. Em geral, atribuir os *contribuinte de rede* função para o seu principal de serviço nos recursos de delegado. Para obter mais informações sobre as permissões, consulte [AKS delegado acesso a outros recursos do Azure][aks-sp].

Tem de criar um cluster do AKS que define o SKU para o Balanceador de carga *padrão* em vez da predefinição *básica*. Criar um cluster do AKS é abordado num passo posterior, mas primeiro tem de ativar algumas funcionalidades de pré-visualização.

> [!IMPORTANT]
> Funcionalidades de pré-visualização do AKS são self-service, participar. Eles são fornecidos para recolher comentários e bugs de nossa Comunidade. Em pré-visualização, esses recursos não se destinam a utilização de produção. Funcionalidades em pré-visualização pública enquadram-se em suporte "melhor esforço". Assistência das equipas de suporte técnico do AKS está disponível durante o horário do Pacífico fuso horário (PST) apenas. Para obter mais informações, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS][aks-support-policies]
> * [FAQ de suporte do Azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão CLI de pré-visualização do aks

Utilizar o Balanceador de carga do Azure standard SKU, é necessário o *pré-visualização do aks* CLI versão da extensão 0.4.1 ou superior. Instalar o *pré-visualização do aks* extensão da CLI do Azure com o [Adicionar extensão az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] comando::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-aksazurestandardloadbalancer-preview-feature"></a>Registe-se a funcionalidade de pré-visualização de AKSAzureStandardLoadBalancer

Para criar um cluster do AKS que pode utilizar um balanceador de carga com o *padrão* SKU, tem de ativar a *AKSAzureStandardLoadBalancer* sinalizador na sua subscrição de recursos. O *AKSAzureStandardLoadBalancer* funcionalidade também utiliza *VMSSPreview* quando criar um cluster com os conjuntos de dimensionamento de máquinas virtuais. Esta funcionalidade fornece o mais recente conjunto de melhorias ao serviço, quando configurar um cluster. Embora não seja necessário, recomenda-se ativar a *VMSSPreview* também o sinalizador de funcionalidade.

> [!CAUTION]
> Quando registra um recurso numa assinatura, não pode atualmente anular o registo essa funcionalidade. Depois de ativar a algumas funcionalidades de pré-visualização, as predefinições podem ser utilizadas para todos os clusters do AKS, em seguida, criados na subscrição. Não a ativar funcionalidades de pré-visualização em subscrições de produção. Utilize uma subscrição separada para testar as funcionalidades de pré-visualização e colher comentários baseados em.

Registe-se a *VMSSPreview* e *AKSAzureStandardLoadBalancer* sinalizadores de recurso com o [Registre-se de funcionalidade de az][az-feature-register] comando conforme mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "VMSSPreview"
az feature register --namespace "Microsoft.ContainerService" --name "AKSAzureStandardLoadBalancer"
```

> [!NOTE]
> Qualquer cluster do AKS criar depois de registar com êxito a *VMSSPreview* ou *AKSAzureStandardLoadBalancer* sinalizadores de recurso utilizam esta experiência de cluster de pré-visualização. Para continuar a criar clusters regulares, totalmente suportada, não a ativar funcionalidades de pré-visualização em subscrições de produção. Utilize um desenvolvimento subscrição do Azure ou de teste separada para fins de teste de funcionalidades de pré-visualização.

Demora alguns minutos para que o estado a mostrar *registado*. Pode verificar o estado de registo com o [lista de funcionalidades de az][az-feature-list] comando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registo do *containerservice* fornecedor de recursos com o [Registre-se fornecedor de az][az-provider-register] comando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se ao criar e gerir clusters do AKS que oferecem suporte a um balanceador de carga com o *padrão* SKU:

* Ao utilizar o *padrão* SKU para um balanceador de carga, tem de permitir endereços públicos e evitar a criação de qualquer política do Azure que bans a criação de IP. O cluster do AKS cria automaticamente uma *padrão* IP público de SKU no mesmo grupo de recursos criado para o cluster do AKS, que normalmente denominados com *MC_* no início. AKS atribui o IP público para o *padrão* SKU Balanceador de carga. O IP público é necessário para permitir o tráfego de saída do AKS cluster. Este IP público também é necessário para manter a conetividade entre os controle plano e o agente de nós, bem como para manter a compatibilidade com versões anteriores do AKS.
* Ao utilizar o *padrão* SKU para um balanceador de carga, tem de utilizar o Kubernetes versão 1.13.5 ou superior.

Enquanto esta funcionalidade está em pré-visualização, aplicam-se as seguintes limitações adicionais:

* Ao utilizar o *padrão* SKU para um balanceador de carga no AKS, não é possível definir o seu próprio endereço IP público para a saída do Balanceador de carga. Tem de utilizar o endereço IP que AKS atribui ao seu Balanceador de carga.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. Quando cria um grupo de recursos, é-lhe pedido que especifique uma localização. Esta localização é onde os metadados de grupo de recursos são armazenados, também é onde executar a seus recursos no Azure se não especificar outra região durante a criação de recursos. Criar um grupo de recursos utilizando o [criar grupo az][az-group-create] comando.

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
Para executar um cluster do AKS que oferece suporte a um balanceador de carga com o *padrão* SKU, o cluster tem de definir o *sku de Balanceador de carga* parâmetro *padrão*. Este parâmetro cria um balanceador de carga com o *padrão* SKU quando o cluster é criado. Quando executa uma *LoadBalancer* serviço no seu cluster, a configuração da *padrão* Balanceador de carga SK é atualizado com a configuração do serviço. Utilize o [criar az aks][az-aks-create] comando para criar um cluster do AKS com o nome *myAKSCluster*.

> [!NOTE]
> O *sku de Balanceador de carga* propriedade só pode ser utilizada quando o cluster é criado. Não é possível alterar o SKU do Balanceador de carga depois de ter sido criado um cluster do AKS. Além disso, pode utilizar apenas um tipo de Balanceador de carga SKU num único cluster.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --kubernetes-version 1.14.0 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

Após alguns minutos, o comando é concluído e devolve o formato JSON informações sobre o cluster.

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

A saída de exemplo seguinte mostra o nó único criado nos passos anteriores. Certifique-se de que o estado do nó é *pronto*:

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.14.0
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>Certifique-se de que o cluster utiliza a *padrão* SKU

Utilize o [show do az aks][az-aks-show] para exibir a configuração do cluster.

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster

{
  "aadProfile": null,
  "addonProfiles": null,
   ...
   "networkProfile": {
    "dnsServiceIp": "10.0.0.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "loadBalancerSku": "standard",
    ...
```

Verifique se o *loadBalancerSku* propriedade é apresentado como *padrão*.

## <a name="use-the-load-balancer"></a>Utilizar o Balanceador de carga

Para utilizar o Balanceador de carga no seu cluster, crie um manifesto de serviço com o tipo de serviço *LoadBalancer*. Para mostrar o Balanceador de carga a funcionar, crie outro manifesto com um aplicativo de exemplo para executar no seu cluster. Este aplicativo de exemplo é exposto por meio do Balanceador de carga e pode ser visualizado através de um browser.

Criar um manifesto com o nome `sample.yaml` conforme mostrado no exemplo a seguir:

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
```

O manifesto acima configura duas implementações: *azure-vote-front* e *do azure-vote-back*. Para configurar *azure-vote-front* implantação sejam expostas a utilizar o Balanceador de carga, criar um manifesto com o nome `standard-lb.yaml` conforme mostrado no exemplo a seguir:

```yaml
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

O serviço *azure-vote-front* utiliza o *LoadBalancer* tipo para configurar o Balanceador de carga no seu cluster do AKS para ligar ao *azure-vote-front* implementação.

Implementar a aplicação de exemplo e utilizar o Balanceador de carga a [kubectl aplicar][kubectl-apply] e especifique o nome de seus manifestos YAML:

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

O *padrão* Balanceador de carga SKU está agora configurado para expor a aplicação de exemplo. Ver os detalhes do serviço de *azure-vote-front* usando [kubectl obter][kubectl-get] para ver o IP público do Balanceador de carga. O endereço IP público do Balanceador de carga é mostrado na *EXTERNAL-IP* coluna. Poderá demorar um minuto ou dois para que o endereço IP para alterar a partir *\<pendente\>* para um real endereço IP externo, como mostrado no exemplo a seguir:

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

Navegue para o IP público num browser e certifique-se de que verá a aplicação de exemplo. No exemplo acima, o IP público é `52.179.23.131`.

![Imagem de navegação para o Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> Pode também configurar o Balanceador de carga para ser interno e não expõem um IP público. Para configurar o Balanceador de carga como internos, adicione `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` como uma anotação para o *LoadBalancer* serviço. Pode ver um yaml de exemplo de manifesto, bem como obter mais detalhes sobre o Balanceador de carga interno [aqui][internal-lb-yaml].

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>Limpar a configuração de Balanceador de carga de Standard SKU

Para remover a configuração de Balanceador de carga e aplicação de exemplo, utilize [kubectl eliminar][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre os serviços do Kubernetes com o [documentação dos serviços de Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
