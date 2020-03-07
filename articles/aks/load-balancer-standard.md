---
title: Utilize um balancedor de carga SKU Standard no Serviço Azure Kubernetes (AKS)
description: Aprenda a utilizar um equilibrador de carga com um SKU Standard para expor os seus serviços com o Serviço Azure Kubernetes (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 9c414572e1c3b2f046ae9a14139885e9927ab3bb
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374704"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Utilize um balancedor de carga SKU Standard no Serviço Azure Kubernetes (AKS)

Para fornecer acesso a aplicações através de serviços kubernetes de tipo `LoadBalancer` no Serviço Azure Kubernetes (AKS), pode utilizar um Balancer de Carga Azure. Um equilibrador de carga em AKS pode ser utilizado como um equilibrador de carga interno ou externo. Um equilibrador de carga interno torna um serviço Kubernetes acessível apenas a aplicações que executam na mesma rede virtual que o cluster AKS. Um equilibrador de carga externo recebe um ou mais IPs públicos para ingresso e torna um serviço Kubernetes acessível externamente usando os IPs públicos.

O Equilíbrio de Carga Azure está disponível em duas SKUs - *Basic* and *Standard*. Por padrão, o *SKU Padrão* é usado quando cria um cluster AKS. A utilização de um equilibrador de carga *SKU Standard* fornece funcionalidades e funcionalidades adicionais, tais como um tamanho maior da piscina e zonas de disponibilidade. É importante que compreenda as diferenças entre os equilibradores de carga *Standard* e *Basic* antes de escolher qual usar. Uma vez criado um cluster AKS, não pode alterar o balancer de carga SKU para esse cluster. Para obter mais informações sobre as SKUs *Básicas* e *Standard,* consulte a [comparação SKU][azure-lb-comparison]do equilíbrio de carga Azure .

Este artigo assume uma compreensão básica dos conceitos Kubernetes e Azure Load Balancer. Para mais informações, consulte os [conceitos centrais da Kubernetes para o Serviço Azure Kubernetes (AKS)][kubernetes-concepts] e [o que é o Azure Load Balancer?][azure-lb]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este artigo requer que esteja a executar a versão Azure CLI 2.0.81 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][install-azure-cli].

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que tem um cluster AKS com o *Standard* SKU Azure Load Balancer. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

O principal serviço de cluster AKS também precisa de permissão para gerir os recursos da rede se utilizar uma subnet ou grupo de recursos existente. Em geral, atribuir o papel de colaborador da *Rede* ao seu principal de serviço sobre os recursos delegados. Para obter mais informações sobre permissões, consulte o [acesso do Delegado AKS a outros recursos Do Azure.][aks-sp]

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Passando de um Equilíbrio de Carga SKU Básico para SKU Padrão

Se tiver um cluster existente com o Equilíbrio de Carga SKU Básico, existem diferenças comportamentais importantes a notar ao migrar para utilizar um cluster com o Balancer de Carga SKU Padrão.

Por exemplo, fazer implantações azuis/verdes para migrar aglomerados é uma prática comum dado que o tipo `load-balancer-sku` de um cluster só pode ser definido em cluster criar tempo. No entanto, os equilibradores básicos de carga *SKU* utilizam endereços IP *Básicos SKU* que não são compatíveis com os Balanceadores de Carga *SKU Padrão,* uma vez que requerem endereços IP *Padrão SKU.* Ao migrar clusters para atualizar o Balancer SKUs, será necessário um novo endereço IP com um Endereço IP compatível SKU.

Para obter mais considerações sobre como migrar aglomerados, visite a [nossa documentação sobre considerações migratórias](aks-migration.md) para ver uma lista de tópicos importantes a considerar na migração. As limitações abaixo são também importantes diferenças comportamentais a notar ao utilizar os Equilibradores de Carga SKU Padrão em AKS.

### <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se quando cria e gere clusters AKS que suportam um equilibrador de carga com o *SKU Padrão:*

* É necessário pelo menos um prefixo IP ou IP público para permitir o tráfego de esgress do cluster AKS. O prefixo IP ou IP público também é necessário para manter a conectividade entre o plano de controlo e os nós do agente, bem como para manter a compatibilidade com versões anteriores do AKS. Tem as seguintes opções para especificar ips públicos ou prefixos IP com um equilibrador de carga *SKU padrão:*
    * Forneça os seus próprios iPs públicos.
    * Forneça os seus próprios prefixos de IP públicos.
    * Especifique um número até 100 para permitir que o cluster AKS crie muitos IPs públicos *Standard* SKU no mesmo grupo de recursos criado que o cluster AKS, que é geralmente nomeado com *MC_* no início. AkS atribui o IP público ao equilíbrio de carga *SKU Padrão.* Por predefinição, um IP público será automaticamente criado no mesmo grupo de recursos que o cluster AKS, se não for especificado ip público, prefixo IP público ou número de IPs. Deve também permitir endereços públicos e evitar a criação de qualquer Política Azure que proíba a criação de IP.
* Ao utilizar o *SKU Standard* para um equilibrador de carga, deve utilizar a versão *1.13 ou superior*da Kubernetes .
* Definir o equilíbrio de carga SKU só pode ser feito quando se cria um cluster AKS. Não é possível alterar o balanceador de carga SKU depois de ter sido criado um cluster AKS.
* Só pode utilizar um tipo de balanceador de carga SKU (Básico ou Standard) num único cluster.
* *Padrão* Os Balanceadores de Carga SKU suportam apenas endereços IP *SKU standard.*

## <a name="use-the-standard-sku-load-balancer"></a>Utilize o equilibrador de carga *SKU Padrão*

Quando cria um cluster AKS, por padrão, o equilibrador de carga *Standard* SKU é utilizado quando executa serviços nesse cluster. Por exemplo, [o quickstart utilizando o Azure CLI][aks-quickstart-cli] implementa uma aplicação de amostra que utiliza o equilíbrio de carga *SKU Padrão.*

> [!IMPORTANT]
> Os endereços IP públicos podem ser evitados personalizando uma rota definida pelo utilizador (UDR). Especificando o tipo de saída de um cluster AKS, uma vez que o UDR pode ignorar o fornecimento de IP e a configuração de piscina supérbio para o aks criado pelo equilíbrio de carga Azure. Consulte [a definição de um `outboundType` de cluster para 'userDefinedRouting'.](egress-outboundtype.md)

## <a name="configure-the-load-balancer-to-be-internal"></a>Configure o equilibrador de carga para ser interno

Também pode configurar o equilibrador de carga para ser interno e não expor um IP público. Para configurar o equilibrador de carga como interno, adicione `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` como uma anotação ao serviço *LoadBalancer.* Pode ver um manifesto de yaml exemplo, bem como mais detalhes sobre um equilibrador de carga interna [aqui][internal-lb-yaml].

## <a name="scale-the-number-of-managed-public-ips"></a>Escala remao o número de IPs públicos geridos

Ao utilizar um equilibrador de carga *SKU standard* com IPs públicos de saída geridos, que são criados por padrão, pode escalar o número de IPs públicos geridos de saída utilizando o parâmetro de contagem ip gerido pelo equilíbrio de *carga.*

Para atualizar um cluster existente, execute o seguinte comando. Este parâmetro também pode ser definido em cluster criar tempo para ter múltiplos IPs públicos geridos de saída.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

O exemplo acima define o número de IPs públicos geridos para *2* para o cluster *myAKSCluster* no *myResourceGroup*. 

Também pode utilizar o parâmetro de *contagem ip-contabilizado gerido* pelo carregador para definir o número inicial de IPs públicos geridos ao criar o seu cluster, fixando o parâmetro de `--load-balancer-managed-outbound-ip-count` e fixando-o ao seu valor desejado. O número padrão de IPs públicos de saída geridos é 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Forneça os seus próprios IPs públicos ou prefixos para a saída

Ao utilizar um equilibrador de carga *SKU Padrão,* o cluster AKS cria automaticamente um IP público no mesmo grupo de recursos criado para o cluster AKS e atribui o IP público ao equilíbrio de carga *SKU Padrão.* Em alternativa, pode atribuir o seu próprio IP público no momento da criação do cluster ou pode atualizar as propriedades de um cluster existente.

Ao trazer vários endereços IP ou prefixos, é possível definir vários serviços de apoio ao definir o endereço IP por trás de um único objeto de equilíbrio de carga. O ponto final de saída de nódos específicos dependerá do serviço a que estão associados.

> [!IMPORTANT]
> Deve utilizar IPs públicos *Standard* SKU para obter o seu *Standard* SKU o seu equilibrador de carga. Pode verificar o SKU dos seus IPs públicos utilizando o comando de [show ip da rede az:][az-network-public-ip-show]
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Use o comando [público-ip da rede az][az-network-public-ip-show] para listar as iDs dos seus iPs públicos.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

O comando acima mostra o ID para o ip público *myPublicIP* no grupo de recursos *myResourceGroup.*

Utilize o comando de *atualização az aks* com o parâmetro de saída de entrada de saída *do equilibrador* de carga para atualizar o seu cluster com os seus IPs públicos.

O exemplo seguinte utiliza o parâmetro de saída *de carga-balanceador-ips* com os IDs do comando anterior.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Também pode utilizar prefixos IP públicos para saída com o seu equilibrador de carga *SKU Standard.* O exemplo seguinte utiliza o comando de [prefixo público-ip][az-network-public-ip-prefix-show] da rede az para listar as IDs dos seus prefixos IP públicos:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

O comando acima mostra o ID para o prefixo IP público *myPublicIPPrefix* no grupo de recursos *myResourceGroup.*

O exemplo seguinte utiliza o parâmetro de saída *do carregador-outbound-ip-ip-prefixos* com os IDs do comando anterior.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> Os ippúblicos e os prefixos IP devem estar na mesma região e fazer parte da mesma subscrição que o seu cluster AKS. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Defina o seu próprio IP público ou prefixos no cluster criar tempo

Pode desejar trazer os seus próprios endereços IP ou prefixos IP para saídas no momento da criação do cluster para apoiar cenários como os pontos finais da lista branca. Aperte os mesmos parâmetros acima indicados para o seu passo de criação de cluster para definir os seus próprios IpS públicos e prefixos IP no início do ciclo de vida de um cluster.

Use o *az aks criar* comando com o parâmetro de *saída de carga-balancer-ips* para criar um novo cluster com os seus IPs públicos no início.

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

Utilize o *az aks criar* comando com o parâmetro de *pré-prefixos de saída de carga-ip-ip* para criar um novo cluster com os seus prefixos IP públicos no início.

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

## <a name="configure-outbound-ports-and-idle-timeout"></a>Configure portas de saída e tempo limite inativo

> [!WARNING]
> A secção seguinte destina-se a cenários avançados de networking de maior escala ou para abordar problemas de exaustão sNAT com as configurações predefinidas. Deve dispor de um inventário preciso da quota disponível para VMs e endereços IP antes de alterar os *OutoutoutPorts* ou *IdleTimeoutInMinutes* do seu valor predefinido, a fim de manter clusters saudáveis.
> 
> Alterar os valores para *AlocadosOutboundPorts* e *IdleTimeoutInMinutes* pode alterar significativamente o comportamento da regra de saída para o seu equilíbrio de carga. Reveja as regras de saída do [Balancer de Carga,][azure-lb-outbound-rules-overview]as regras de saída do [Balancer de carga][azure-lb-outbound-rules]e as [ligações de saída em Azure][azure-lb-outbound-connections] antes de atualizar estes valores para compreender completamente o impacto das suas alterações.

Os portos afetados à saída e os seus intervalos de inatividade são utilizados para [o SNAT][azure-lb-outbound-connections]. Por predefinição, o equilibrador de carga *Standard* SKU utiliza [a atribuição automática para o número de portas de saída com base no tamanho][azure-lb-outbound-preallocatedports] da piscina de backend e um tempo de inatividade de 30 minutos para cada porta. Para ver estes valores, utilize a lista de regras de saída da [rede az lb][az-network-lb-outbound-rule-list] para mostrar a regra de saída para o equilibrista de carga:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Os comandos anteriores listarão a regra de saída para o seu equilibrador de carga, por exemplo:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

A saída de exemplo mostra o valor predefinido para *OutboundPorts atribuídos* e *IdleTimeoutInMinutes*. Um valor de 0 para *AlocadosOutboundPorts* define o número de portas de saída usando a atribuição automática para o número de portas de saída com base no tamanho da piscina de backend. Por exemplo, se o cluster tiver 50 ou menos nós, 1024 portas para cada nó são atribuídas.

Considere alterar a definição de *OutboundPorts ou* *IdleTimeoutInMinutes* se esperar enfrentar a exaustão SNAT com base na configuração predefinida acima. Cada endereço IP adicional permite a atribuição de 64.000 portas adicionais, no entanto o Balancer de Carga Padrão Azure não aumenta automaticamente as portas por nó quando são adicionados mais endereços IP. Pode alterar estes valores definindo as *portas de saída do equilibrador de carga* e os parâmetros de tempo limite do equilíbrio de *carga.* Por exemplo:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> Deve [calcular a sua quota necessária][calculate-required-quota] antes de personalizar os *OutboundPorts atribuídos* para evitar problemas de conectividade ou escala. O valor que especifica para *os OutboundPorts atribuídos* também deve ser um múltiplo de 8.

Também pode utilizar os *parâmetros* de tempo limite de carga e de tempo limite de equilíbrio de *carga* ao criar um cluster, mas também deve especificar os parâmetros de tempo limite de carga geridos pelo *equilibrador,* o equilíbrio de *carga-out-out-ips,* ou também os prefixos de saída do *carregador-de-saída-ip.*  Por exemplo:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

Ao alterar os parâmetros de tempo limite de saída do *equilibrante* de carga e do tempo limite de *funcionamento* do seu padrão, afeta o comportamento do perfil do equilibrante de carga, que afeta todo o cluster.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>Quota exigida para personalizar portos atribuídos
Você deve ter capacidade IP de saída suficiente com base no número de VMs do seu nó e portas de saída desejadas. Para validar tem capacidade IP de saída suficiente, utilize a seguinte fórmula: 
 
*outboundIPs* \* 64.000 \> *nósVMs* \* *desejadosOutboundPorts*.
 
Por exemplo, se tiver 3 *nósVMs*, e 50.000 *OutboundOutboundPorts desejados,* precisa de ter pelo menos 3 *IPs*de saída . Recomenda-se que incorpore uma capacidade IP adicional para além do que necessita. Além disso, você deve explicar o autoescalador de cluster e a possibilidade de upgrades de piscina de nó ao calcular a capacidade IP de saída. Para o autoescalador de cluster, reveja a contagem atual do nó e a contagem máxima do nó e utilize o valor mais elevado. Para upgrade, procure um VM de nó adicional para cada piscina de nós que permita a modernização.
 
Ao definir o *IdleTimeoutInMinutes* para um valor diferente do padrão de 30 minutos, considere quanto tempo as suas cargas de trabalho precisarão de uma ligação de saída. Considere também que o valor de tempo de tempo padrão para um equilibrador de carga *SKU padrão* utilizado fora do AKS é de 4 minutos. Um valor *IdleTimeoutInMinutes* que reflita com mais precisão a sua carga de trabalho específica do AKS pode ajudar a diminuir a exaustão sNAT causada pela atar ligações que já não estão a ser utilizadas.

## <a name="restrict-access-to-specific-ip-ranges"></a>Restringir o acesso a gamas IP específicas

O Grupo de Segurança da Rede (NSG) associado à rede virtual para o equilibrante de carga, por padrão, tem uma regra que permite todo o tráfego externo de entrada. Pode atualizar esta regra apenas para permitir intervalos ip específicos para o tráfego de entrada. O manifesto seguinte utiliza *loadBalancerSourceRanges* para especificar uma nova gama IP para tráfego externo de entrada:

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

O exemplo acima atualiza a regra para permitir apenas o tráfego externo de entrada a partir da gama *MY_EXTERNAL_IP_RANGE.* Mais informações sobre a utilização deste método para restringir o acesso ao serviço de equilíbrio de carga estão disponíveis na [documentação kubernetes.][kubernetes-cloud-provider-firewall]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os serviços da Kubernetes na documentação dos [serviços da Kubernetes.][kubernetes-services]

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
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-rules-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[calculate-required-quota]: #required-quota-for-customizing-allocatedoutboundports
