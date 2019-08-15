---
title: Visualização-usar um balanceador de carga de SKU padrão no serviço de kubernetes do Azure (AKS)
description: Saiba como usar um balanceador de carga com um SKU Standard para expor seus serviços com o AKS (serviço kubernetes do Azure).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/25/2019
ms.author: zarhoads
ms.openlocfilehash: 4e234d3849e09bd8c57a8c33bb378ab801ce0f6d
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019452"
---
# <a name="preview---use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Visualização-usar um balanceador de carga de SKU padrão no serviço de kubernetes do Azure (AKS)

Para fornecer acesso aos seus aplicativos no AKS (serviço kubernetes do Azure), você pode criar e usar um Azure Load Balancer. Um balanceador de carga em execução no AKS pode ser usado como um balanceador de carga interno ou externo. Um balanceador de carga interno torna um serviço kubernetes acessível somente para aplicativos em execução na mesma rede virtual que o cluster AKS. Um balanceador de carga externo recebe um ou mais IPs públicos para entrada e torna um serviço kubernetes acessível externamente usando os IPs públicos.

O Azure Load Balancer está disponível em dois SKUs- *básico* e *Standard*. Por padrão, a SKU *básica* é usada quando um manifesto de serviço é usado para criar um balanceador de carga no AKs. O uso de um balanceador de carga SKU *padrão* fornece recursos e funcionalidades adicionais, como tamanho e zonas de disponibilidade de pools de back-end maiores. É importante entender as diferenças entre os balanceadores de carga *padrão* e *básico* antes de escolher o que usar. Depois de criar um cluster AKS, você não pode alterar o SKU do balanceador de carga para esse cluster. Para obter mais informações sobre os SKUs *básico* e *Standard* , consulte [comparação de SKU do Azure Load][azure-lb-comparison]Balancer.

Este artigo mostra como criar e usar um Azure Load Balancer com o SKU *Standard* com o AKs (serviço kubernetes do Azure).

Este artigo pressupõe uma compreensão básica dos conceitos de kubernetes e de Azure Load Balancer. Para obter mais informações, consulte [kubernetes Core Concepts for Azure kubernetes Service (AKs)][kubernetes-concepts] e [o que é Azure Load Balancer?][azure-lb].

Esta funcionalidade encontra-se em pré-visualização.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que você esteja executando o CLI do Azure versão 2.0.59 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][install-azure-cli].

## <a name="before-you-begin"></a>Antes de começar

A entidade de serviço de cluster AKS precisará de permissão para gerenciar recursos de rede se você usar uma sub-rede ou grupo de recursos existente. Em geral, atribua a função de *colaborador de rede* à sua entidade de serviço nos recursos delegados. Para obter mais informações sobre permissões, consulte [delegar acesso AKs a outros recursos do Azure][aks-sp].

Você deve criar um cluster AKS que defina a SKU do balanceador de carga para *Standard* em vez do *básico*padrão. A criação de um cluster AKS é abordada em uma etapa posterior, mas primeiro você precisa habilitar alguns recursos de visualização.

> [!IMPORTANT]
> Os recursos de visualização do AKS são de autoatendimento e aceitação. Eles são fornecidos para reunir comentários e bugs de nossa comunidade. Na versão prévia, esses recursos não são destinados ao uso em produção. Os recursos na visualização pública se enquadram no suporte "melhor esforço". A assistência das equipes de suporte técnico do AKS está disponível durante o horário comercial do fuso horário do Pacífico (PST). Para obter informações adicionais, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS][aks-support-policies]
> * [Perguntas frequentes do suporte do Azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI do AKS-Preview

Para usar o SKU padrão do Azure Load Balancer, você precisa da extensão da CLI *AKs-Preview* versão 0.4.1 ou superior. Instale a extensão de CLI do Azure *de AKs-Preview* usando o comando [AZ Extension Add][az-extension-add] e, em seguida, verifique se há atualizações disponíveis usando o comando [AZ Extension Update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-aksazurestandardloadbalancer-preview-feature"></a>Registrar o recurso de visualização do AKSAzureStandardLoadBalancer

Para criar um cluster AKS que possa usar um balanceador de carga com o SKU *Standard* , você deve habilitar o sinalizador de recurso *AKSAzureStandardLoadBalancer* em sua assinatura. O recurso *AKSAzureStandardLoadBalancer* também usa *VMSSPreview* ao criar um cluster usando conjuntos de dimensionamento de máquinas virtuais. Esse recurso fornece o conjunto mais recente de aprimoramentos de serviço ao configurar um cluster. Embora não seja necessário, é recomendável habilitar o sinalizador de recurso *VMSSPreview* também.

> [!CAUTION]
> Quando você registra um recurso em uma assinatura, não é possível cancelar o registro desse recurso no momento. Depois de habilitar alguns recursos de visualização, os padrões podem ser usados para todos os clusters AKS, em seguida, criados na assinatura. Não habilite os recursos de visualização em assinaturas de produção. Use uma assinatura separada para testar recursos de visualização e coletar comentários.

Registre os sinalizadores de recurso *VMSSPreview* e *AKSAzureStandardLoadBalancer* usando o comando [AZ Feature Register][az-feature-register] , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "VMSSPreview"
az feature register --namespace "Microsoft.ContainerService" --name "AKSAzureStandardLoadBalancer"
```

> [!NOTE]
> Qualquer cluster AKS que você criar depois de registrar com êxito os sinalizadores de recurso *VMSSPreview* ou *AKSAzureStandardLoadBalancer* usará essa experiência de visualização de cluster. Para continuar a criar clusters regulares e com suporte total, não habilite os recursos de visualização em assinaturas de produção. Use uma assinatura de teste ou desenvolvimento separada do Azure para testar os recursos de visualização.

Leva alguns minutos para que o status seja mostrado *registrado*. Você pode verificar o status do registro usando o comando [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos *Microsoft. ContainerService* usando o comando [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters AKS que dão suporte a um balanceador de carga com o SKU *Standard* :

* Ao usar o SKU *Standard* para um balanceador de carga, você deve permitir endereços públicos e evitar a criação de qualquer Azure Policy que banimentos a criação de IP. O cluster AKS cria automaticamente um IP público de SKU *padrão* no mesmo grupo de recursos criado para o cluster AKs, que geralmente é nomeado com *MC_* no início. AKS atribui o IP público ao balanceador de carga SKU *padrão* . O IP público é necessário para permitir o tráfego de saída do cluster AKS. Esse IP público também é necessário para manter a conectividade entre o plano de controle e os nós de agente, bem como para manter a compatibilidade com as versões anteriores do AKS.
* Ao usar o SKU *Standard* para um balanceador de carga, você deve usar o kubernetes versão 1.13.5 ou superior.
* Se estiver usando o [recurso de IP público do nó](use-multiple-node-pools.md#assign-a-public-ip-per-node-in-a-node-pool) com balanceadores de carga padrão, você poderá definir uma regra de saída SLB ou um IP público para o nó. Você deve selecionar um ou outro porque uma VM não pode ser anexada a uma regra de saída SLB e a um IP público simultaneamente.

Embora esse recurso esteja em versão prévia, as seguintes limitações adicionais se aplicam:

* Ao usar o SKU *Standard* para um balanceador de carga no AKs, você não pode definir seu próprio endereço IP público para saída para o balanceador de carga. Você deve usar o endereço IP AKS atribui ao balanceador de carga.

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
  "tags": null,
  "type": null
}
```

## <a name="create-aks-cluster"></a>Criar um cluster do AKS
Para executar um cluster AKS que dá suporte a um balanceador de carga com o SKU *Standard* , o cluster precisa definir o parâmetro *Load-Balancer-SKU* como *Standard*. Esse parâmetro cria um balanceador de carga com o SKU *Standard* quando o cluster é criado. Quando você executa um serviço Balancer em seu cluster, a configuração do balanceador de carga *padrão* do SK é atualizada com a configuração do serviço. Use o comando [AZ AKs Create][az-aks-create] para criar um cluster AKs chamado *myAKSCluster*.

> [!NOTE]
> A propriedade do balanceador de *carga-SKU* só pode ser usada quando o cluster é criado. Você não pode alterar o SKU do balanceador de carga depois que um cluster AKS tiver sido criado. Além disso, você só pode usar um tipo de SKU de balanceador de carga em um único cluster.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

Após alguns minutos, o comando é concluído e retorna informações formatadas em JSON sobre o cluster.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerenciar um cluster kubernetes, você usa [kubectl][kubectl], o cliente de linha de comando kubernetes. Se você usar o Azure cloud Shell `kubectl` , o já estará instalado. Para instalar `kubectl` localmente, use o comando [AZ AKs install-CLI][az-aks-install-cli] :

```azurecli
az aks install-cli
```

Para configurar `kubectl` o para se conectar ao cluster do kubernetes, use o comando [AZ AKs Get-Credentials][az-aks-get-credentials] . Esse comando baixa as credenciais e configura a CLI do kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo seguinte mostra o nó único criado nos passos anteriores. Verifique se o status do nó está *pronto*:

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.13.9
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>Verifique se o cluster usa o SKU *Standard*

Use [AZ AKs show][az-aks-show] para exibir a configuração do cluster.

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

Verifique se a propriedade *loadBalancerSku* é mostrada como *padrão*.

## <a name="use-the-load-balancer"></a>Usar o balanceador de carga

Para usar o balanceador de carga em seu cluster, crie um manifesto de serviço com otipo de serviço balanceador. Para mostrar o balanceador de carga funcionando, crie outro manifesto com um aplicativo de exemplo para ser executado no cluster. Esse aplicativo de exemplo é exposto por meio do balanceador de carga e pode ser exibido por meio de um navegador.

Crie um manifesto chamado `sample.yaml` conforme mostrado no exemplo a seguir:

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

O manifesto acima configura duas implantações: *Azure-vote-front* e *Azure-vote-back*. Para configurar a implantação *do Azure-vote-front* para ser exposta usando o balanceador de carga, crie `standard-lb.yaml` um manifesto chamado, conforme mostrado no exemplo a seguir:

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

O serviço *Azure-vote-front* usa o tipo de balanceador para configurar o balanceador de carga no cluster AKs para se conectar à implantação *do Azure-vote-front* .

Implante o aplicativo de exemplo e o balanceador de carga usando o [kubectl Apply][kubectl-apply] e especifique o nome dos seus manifestos YAML:

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

O balanceador de carga SKU *Standard* agora está configurado para expor o aplicativo de exemplo. Exiba os detalhes do serviço do *Azure-vote-front* usando [kubectl Get][kubectl-get] para ver o IP público do balanceador de carga. O endereço IP público do balanceador de carga é mostrado na coluna *IP externo* . Pode levar um minuto ou dois para que o endereço IP seja alterado de *\<pendente\>* para um endereço IP externo real, conforme mostrado no exemplo a seguir:

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

Navegue até o IP público em um navegador e verifique se você vê o aplicativo de exemplo. No exemplo acima, o IP público é `52.179.23.131`.

![Imagem de navegação para o Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> Você também pode configurar o balanceador de carga para ser interno e não expor um IP público. Para configurar o balanceador de carga como interno, `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` adicione como uma anotação ao serviço Balancer. Você pode ver um exemplo de manifesto YAML, bem como mais detalhes sobre um balanceador de carga interno [aqui][internal-lb-yaml].

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>Limpar a configuração do balanceador de carga SKU padrão

Para remover o aplicativo de exemplo e a configuração do balanceador de carga, use [kubectl Delete][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre os serviços Kubernetess na [documentação dos serviços Kubernetess][kubernetes-services].

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
