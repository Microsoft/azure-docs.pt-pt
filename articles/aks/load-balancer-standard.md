---
title: Usar um balanceador de carga SKU padrão no serviço kubernetes do Azure (AKS)
description: Saiba como usar um balanceador de carga com um SKU Standard para expor seus serviços com o AKS (serviço kubernetes do Azure).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 9633975f53b3e398537067b17a870f621d9a7435
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045060"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Usar um balanceador de carga SKU padrão no serviço kubernetes do Azure (AKS)

Para fornecer acesso a aplicativos por meio de serviços Kubernetess do tipo `LoadBalancer` no AKS (serviço kubernetes do Azure), você pode usar um Azure Load Balancer. Um balanceador de carga em execução no AKS pode ser usado como um balanceador de carga interno ou externo. Um balanceador de carga interno torna um serviço kubernetes acessível somente para aplicativos em execução na mesma rede virtual que o cluster AKS. Um balanceador de carga externo recebe um ou mais IPs públicos para entrada e torna um serviço kubernetes acessível externamente usando os IPs públicos.

O Azure Load Balancer está disponível em dois SKUs- *básico* e *Standard*. Por padrão, o SKU *padrão* é usado quando você cria um cluster AKs. O uso de um balanceador de carga SKU *padrão* fornece recursos e funcionalidades adicionais, como um tamanho de pool de back-end maior e zonas de disponibilidade. É importante entender as diferenças entre os balanceadores de carga *padrão* e *básico* antes de escolher o que usar. Depois de criar um cluster AKS, você não pode alterar o SKU do balanceador de carga para esse cluster. Para obter mais informações sobre os SKUs *básico* e *Standard* , consulte [comparação de SKU do Azure Load Balancer][azure-lb-comparison].

Este artigo pressupõe uma compreensão básica dos conceitos de kubernetes e de Azure Load Balancer. Para obter mais informações, consulte [kubernetes Core Concepts for Azure kubernetes Service (AKs)][kubernetes-concepts] e [o que é Azure Load Balancer?][azure-lb].

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que você esteja executando o CLI do Azure versão 2.0.74 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][install-azure-cli].

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS com o Azure Load Balancer SKU *padrão* . Se você precisar de um cluster AKS, consulte o guia de início rápido do AKS [usando o CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

A entidade de serviço de cluster AKS precisa também de permissão para gerenciar recursos de rede se você usar uma sub-rede ou grupo de recursos existente. Em geral, atribua a função de *colaborador de rede* à sua entidade de serviço nos recursos delegados. Para obter mais informações sobre permissões, consulte [delegar acesso AKs a outros recursos do Azure][aks-sp].

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Movendo de um Load Balancer de SKU básico para SKU Standard

Se você tiver um cluster existente com o Load Balancer de SKU básico, haverá diferenças comportamentais importantes a serem observadas ao migrar para usar um cluster com o Load Balancer de SKU Standard.

Por exemplo, fazer implantações azuis/verdes para migrar clusters é uma prática comum, Considerando que o tipo de `load-balancer-sku` de um cluster só pode ser definido no momento da criação do cluster. No entanto, os balanceadores de carga de *SKU básicos* usam endereços IP de *SKU básicos* que não são compatíveis com os balanceadores de carga *SKU padrão* , pois exigem endereços IP de *SKU padrão* . Ao migrar clusters para atualizar Load Balancer SKUs, um novo endereço IP com um SKU de endereço IP compatível será necessário.

Para obter mais considerações sobre como migrar clusters, visite [nossa documentação sobre considerações de migração](acs-aks-migration.md) para exibir uma lista de tópicos importantes a serem considerados durante a migração. As limitações abaixo também são importantes diferenças comportamentais a serem observadas ao usar balanceadores de carga de SKU padrão no AKS.

### <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters AKS que dão suporte a um balanceador de carga com o SKU *Standard* :

* Pelo menos um IP público ou prefixo IP é necessário para permitir o tráfego de saída do cluster AKS. O IP público ou o prefixo IP também é necessário para manter a conectividade entre o plano de controle e os nós de agente, bem como para manter a compatibilidade com as versões anteriores do AKS. Você tem as seguintes opções para especificar IPs públicos ou prefixos IP com um balanceador de carga SKU *padrão* :
    * Forneça seus próprios IPs públicos.
    * Forneça seus próprios prefixos IP públicos.
    * Especifique um número até 100 para permitir que o cluster AKS crie vários IPs públicos de SKU *padrão* no mesmo grupo de recursos criado como o cluster AKs, que geralmente é nomeado com *MC_* no início. AKS atribui o IP público ao balanceador de carga SKU *padrão* . Por padrão, um IP público será criado automaticamente no mesmo grupo de recursos que o cluster AKS, se nenhum IP público, prefixo de IP público ou número de IPs for especificado. Você também deve permitir endereços públicos e evitar a criação de qualquer Azure Policy que banimentos a criação de IP.
* Ao usar o SKU *Standard* para um balanceador de carga, você deve usar o Kubernetes versão *1,13 ou superior*.
* Definir o SKU do balanceador de carga só pode ser feito quando você cria um cluster AKS. Você não pode alterar o SKU do balanceador de carga depois que um cluster AKS tiver sido criado.
* Você só pode usar um tipo de SKU do balanceador de carga (básico ou padrão) em um único cluster.
* *Padrão* Os balanceadores de carga de SKU oferecem suporte apenas a endereços IP de SKU *padrão* .

## <a name="use-the-standard-sku-load-balancer"></a>Usar o balanceador de carga SKU *padrão*

Quando você cria um cluster AKS, por padrão, o balanceador de carga SKU *padrão* é usado quando você executa os serviços nesse cluster. Por exemplo, [o início rápido usando o CLI do Azure][aks-quickstart-cli] implanta um aplicativo de exemplo que usa o balanceador de carga SKU *padrão* . 

## <a name="configure-the-load-balancer-to-be-internal"></a>Configurar o balanceador de carga para ser interno

Você também pode configurar o balanceador de carga para ser interno e não expor um IP público. Para configurar o balanceador de carga como interno, adicione `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` como uma anotação ao serviço *Balancer* . Você pode ver um exemplo de manifesto YAML, bem como mais detalhes sobre um balanceador de carga interno [aqui][internal-lb-yaml].

## <a name="scale-the-number-of-managed-public-ips"></a>Dimensionar o número de IPs públicos gerenciados

Ao usar um balanceador de carga de SKU *padrão* com IPs públicos de saída gerenciados, que são criados por padrão, você pode dimensionar o número de IPS públicos de saída gerenciados usando o parâmetro *Load-Balancer – Managed-IP-Count* .

Para atualizar um cluster existente, execute o comando a seguir. Esse parâmetro também pode ser definido em tempo de criação de cluster para ter vários IPs públicos de saída gerenciados.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

O exemplo acima define o número de IPs públicos de saída gerenciados para *2* para o cluster *MyAKSCluster* no *MyResource*. 

Você também pode usar o parâmetro *balanceador de carga-gerenciado-IP-Count* para definir o número inicial de IPS públicos de saída gerenciados ao criar o cluster acrescentando o parâmetro `--load-balancer-managed-outbound-ip-count` e definindo-o para o valor desejado. O número padrão de IPs públicos de saída gerenciados é 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Forneça seus próprios IPs públicos ou prefixos para saída

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

```azurecli-interactive
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

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="show-the-outbound-rule-for-your-load-balancer"></a>Mostrar a regra de saída para o balanceador de carga

Para mostrar a regra de saída criada no balanceador de carga, use [AZ Network lb Outbound List][az-network-lb-outbound-rule-list] e especifique o grupo de recursos do nó do seu cluster AKs:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Os comandos anteriores listarão a regra de saída para o balanceador de carga, por exemplo:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Na saída de exemplo, *AllocatedOutboundPorts* é 0. O valor de *AllocatedOutboundPorts* significa que a alocação de porta SNAT é revertida para atribuição automática com base no tamanho do pool de back-end. Consulte [Load Balancer regras de saída][azure-lb-outbound-rules] e [conexões de saída no Azure][azure-lb-outbound-connections] para obter mais detalhes.

## <a name="restrict-access-to-specific-ip-ranges"></a>Restringir o acesso a intervalos de IP específicos

Por padrão, o NSG (grupo de segurança de rede) associado à rede virtual para o balanceador de carga tem uma regra para permitir todo o tráfego externo de entrada. Você pode atualizar essa regra para permitir apenas intervalos de IP específicos para o tráfego de entrada. O manifesto a seguir usa *loadBalancerSourceRanges* para especificar um novo intervalo de IP para o tráfego externo de entrada:

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
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

O exemplo acima atualiza a regra para permitir somente o tráfego externo de entrada do intervalo de *MY_EXTERNAL_IP_RANGE* . Mais informações sobre como usar esse método para restringir o acesso ao serviço de balanceador de carga estão disponíveis na [documentação do kubernetes][kubernetes-cloud-provider-firewall].

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os serviços Kubernetess na [documentação dos serviços Kubernetess][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-cloud-provider-firewall]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-cloud-provider-firewall/#restrict-access-for-loadbalancer-service
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
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
