---
title: Usar um balanceador de carga SKU padrão no serviço kubernetes do Azure (AKS)
description: Saiba como usar um balanceador de carga com um SKU Standard para expor seus serviços com o AKS (serviço kubernetes do Azure).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: c9b6f6cf52d71451d2e1de27d0637eeb749b1e0b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349059"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Usar um balanceador de carga SKU padrão no serviço kubernetes do Azure (AKS)

Para fornecer acesso aos seus aplicativos no AKS (serviço kubernetes do Azure), você pode criar e usar um Azure Load Balancer. Um balanceador de carga em execução no AKS pode ser usado como um balanceador de carga interno ou externo. Um balanceador de carga interno torna um serviço kubernetes acessível somente para aplicativos em execução na mesma rede virtual que o cluster AKS. Um balanceador de carga externo recebe um ou mais IPs públicos para entrada e torna um serviço kubernetes acessível externamente usando os IPs públicos.

O Azure Load Balancer está disponível em dois SKUs- *básico* e *Standard*. Por padrão, a SKU *básica* é usada quando um manifesto de serviço é usado para criar um balanceador de carga no AKs. O uso de um balanceador de carga SKU *padrão* fornece recursos e funcionalidades adicionais, como tamanho e zonas de disponibilidade de pools de back-end maiores. É importante entender as diferenças entre os balanceadores de carga *padrão* e *básico* antes de escolher o que usar. Depois de criar um cluster AKS, você não pode alterar o SKU do balanceador de carga para esse cluster. Para obter mais informações sobre os SKUs *básico* e *Standard* , consulte [comparação de SKU do Azure Load Balancer][azure-lb-comparison].

Este artigo mostra como criar e usar um Azure Load Balancer com o SKU *Standard* com o AKs (serviço kubernetes do Azure).

Este artigo pressupõe uma compreensão básica dos conceitos de kubernetes e de Azure Load Balancer. Para obter mais informações, consulte [kubernetes Core Concepts for Azure kubernetes Service (AKs)][kubernetes-concepts] e [o que é Azure Load Balancer?][azure-lb].

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que você esteja executando o CLI do Azure versão 2.0.74 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][install-azure-cli].

## <a name="before-you-begin"></a>Antes de começar

A entidade de serviço de cluster AKS precisará de permissão para gerenciar recursos de rede se você usar uma sub-rede ou grupo de recursos existente. Em geral, atribua a função de *colaborador de rede* à sua entidade de serviço nos recursos delegados. Para obter mais informações sobre permissões, consulte [delegar acesso AKs a outros recursos do Azure][aks-sp].

Você deve criar um cluster AKS que defina a SKU do balanceador de carga para *Standard* em vez do *básico*padrão.

### <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters AKS que dão suporte a um balanceador de carga com o SKU *Standard* :

* Pelo menos um IP público ou prefixo IP é necessário para permitir o tráfego de saída do cluster AKS. O IP público ou o prefixo IP também é necessário para manter a conectividade entre o plano de controle e os nós de agente, bem como para manter a compatibilidade com as versões anteriores do AKS. Você tem as seguintes opções para especificar IPs públicos ou prefixos IP com um balanceador de carga SKU *padrão* :
    * Forneça seus próprios IPs públicos.
    * Forneça seus próprios prefixos IP públicos.
    * Especifique um número até 100 para permitir que o cluster AKS crie vários IPs públicos de SKU *padrão* no mesmo grupo de recursos criado como o cluster AKs, que geralmente é nomeado com *MC_* no início. AKS atribui o IP público ao balanceador de carga SKU *padrão* . Por padrão, um IP público será criado automaticamente no mesmo grupo de recursos que o cluster AKS, se nenhum IP público, prefixo de IP público ou número de IPs for especificado. Você também deve permitir endereços públicos e evitar a criação de qualquer Azure Policy que banimentos a criação de IP.
* Ao usar o SKU *Standard* para um balanceador de carga, você deve usar o Kubernetes versão 1,13 ou superior.
* Definir o SKU do balanceador de carga só pode ser feito quando você cria um cluster AKS. Você não pode alterar o SKU do balanceador de carga depois que um cluster AKS tiver sido criado.
* Você só pode usar um SKU do balanceador de carga em um único cluster.

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
Para executar um cluster AKS que dá suporte a um balanceador de carga com o SKU *Standard* , o cluster precisa definir o parâmetro *Load-Balancer-SKU* como *Standard*. Esse parâmetro cria um balanceador de carga com o SKU *Standard* quando o cluster é criado. Quando você executa um serviço *Balancer* em seu cluster, a configuração do balanceador de carga SKU *padrão* é atualizada com a configuração do serviço. Use o comando [AZ AKs Create][az-aks-create] para criar um cluster AKs chamado *myAKSCluster*.

> [!NOTE]
> A propriedade do *balanceador de carga-SKU* só pode ser usada quando o cluster é criado. Você não pode alterar o SKU do balanceador de carga depois que um cluster AKS tiver sido criado. Além disso, você só pode usar um tipo de SKU de balanceador de carga em um único cluster.
> 
> Se você quiser usar seus próprios IPs públicos, use os parâmetros *Load-Balancer-Outbound-IPS*ou *Load-Balancer-Outbound-IP-Prefixes* . Ambos os parâmetros também podem ser usados ao [atualizar o cluster](#optional---provide-your-own-public-ips-or-prefixes-for-egress).

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
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
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.13.10
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

Para usar o balanceador de carga em seu cluster, crie um manifesto de serviço com o tipo de serviço *balanceador*. Para mostrar o balanceador de carga funcionando, crie outro manifesto com um aplicativo de exemplo para ser executado no cluster. Esse aplicativo de exemplo é exposto por meio do balanceador de carga e pode ser exibido por meio de um navegador.

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

O serviço *Azure-vote-front* usa o tipo de *balanceador* para configurar o balanceador de carga no cluster AKs para se conectar à implantação *do Azure-vote-front* .

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

## <a name="optional---scale-the-number-of-managed-public-ips"></a>Opcional-dimensionar o número de IPs públicos gerenciados

Ao usar um balanceador de carga de SKU *padrão* com IPs públicos de saída gerenciados, que são criados por padrão, você pode dimensionar o número de IPS públicos de saída gerenciados usando o parâmetro *Load-Balancer – Managed-IP-Count* .

Para atualizar um cluster existente, execute o comando a seguir. Esse parâmetro também pode ser definido em tempo de criação de cluster para ter vários IPs públicos de saída gerenciados.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

O exemplo acima define o número de IPs públicos de saída gerenciados para *2* para o cluster *MyAKSCluster* no *MyResource*. 

Você também pode usar o parâmetro *balanceador de carga-gerenciado-IP-Count* para definir o número inicial de IPS públicos de saída gerenciados ao criar o cluster acrescentando `--load-balancer-managed-outbound-ip-count` o parâmetro e definindo-o para o valor desejado. O número padrão de IPs públicos de saída gerenciados é 1.

## <a name="optional---provide-your-own-public-ips-or-prefixes-for-egress"></a>Opcional-forneça seus próprios IPs públicos ou prefixos para saída

Ao usar um balanceador de carga SKU *padrão* , o cluster AKs cria automaticamente um IP público no mesmo grupo de recursos criado para o cluster AKs e atribui o IP público ao balanceador de carga SKU *padrão* . Como alternativa, você pode atribuir seu próprio IP público no momento da criação do cluster ou pode atualizar as propriedades do balanceador de carga de um cluster existente.

Ao trazer vários endereços IP ou prefixos, você pode definir vários serviços de backup ao definir o endereço IP por trás de um único objeto de balanceador de carga. O ponto de extremidade de saída de nós específicos dependerá de qual serviço está associado.

> [!IMPORTANT]
> Você deve usar IPs públicos de SKU *padrão* para saída com seu SKU *Standard* seu balanceador de carga. Você pode verificar a SKU de seus IPs públicos usando o comando [AZ Network Public-IP show][az-network-public-ip-show] :
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Use o comando [AZ Network Public-IP show][az-network-public-ip-show] para listar as IDs de seus IPs públicos.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

O comando acima mostra a ID para o IP público *myPublicIP* no grupo de recursos *MyResource* Group.

Use o comando *AZ AKs Update* com o parâmetro *Load-Balancer-Outbound-IPS* para atualizar o cluster com seus IPs públicos.

O exemplo a seguir usa o parâmetro *Load-Balancer-Outbound-IPS* com as IDs do comando anterior.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Você também pode usar prefixos IP públicos para saída com o balanceador de carga SKU *padrão* . O exemplo a seguir usa o comando [AZ Network Public-IP prefix show][az-network-public-ip-prefix-show] para listar as IDs de seus prefixos de IP público:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

O comando acima mostra a ID para o prefixo de IP público *myPublicIPPrefix* no grupo de recursos *MyResource* Group.

O exemplo a seguir usa o parâmetro *Load-Balancer-Outbound-IP-Prefixes* com as IDs do comando anterior.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> Os prefixos IP e IPs públicos devem estar na mesma região e parte da mesma assinatura que o cluster AKS. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Definir seu próprio IP público ou prefixos no momento da criação do cluster

Talvez você queira trazer seus próprios endereços IP ou prefixos IP para saída no momento da criação do cluster para dar suporte a cenários como pontos de extremidade de saída de lista de permissões. Acrescente os mesmos parâmetros mostrados acima à sua etapa de criação de cluster para definir seus próprios IPs públicos e IP no início do ciclo de vida de um cluster.

Use o comando *AZ AKs Create* com o parâmetro *Load-Balancer-Outbound-IPS* para criar um novo cluster com seus IPs públicos no início.

```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Use o comando *AZ AKs Create* com o parâmetro *balanceador de carga-Outbound-IP-Prefixes* para criar um novo cluster com seus prefixos IP públicos no início.

```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>Limpar a configuração do balanceador de carga SKU padrão

Para remover o aplicativo de exemplo e a configuração do balanceador de carga, use [kubectl Delete][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>Passos seguintes

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
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

