---
title: Utilize um equilibrador de carga SKU standard
titleSuffix: Azure Kubernetes Service
description: Saiba como utilizar um equilibrador de carga com um SKU Standard para expor os seus serviços com o Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 09/27/2019
ms.openlocfilehash: 9f75153868633079f24992955c7585f5954befc4
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465784"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Utilize um balanceador de carga Standard SKU no Serviço Azure Kubernetes (AKS)

Para fornecer acesso a aplicações através dos serviços de tipo Kubernetes no `LoadBalancer` Serviço Azure Kubernetes (AKS), pode utilizar um Equilibrador de Carga Azure. Um equilibrador de carga em funcionamento em AKS pode ser usado como um equilibrador de carga interno ou externo. Um equilibrador de carga interno torna um serviço Kubernetes acessível apenas a aplicações que são executando na mesma rede virtual que o cluster AKS. Um equilibrador de carga externo recebe um ou mais IPs públicos para entrada e torna um serviço Kubernetes acessível externamente usando os IPs públicos.

O Azure Load Balancer está disponível em dois SKUs - *Básico* e *Standard*. Por padrão, o *SKU padrão* é utilizado quando cria um cluster AKS. A utilização de um balanceador de carga *SKU standard* fornece funcionalidades e funcionalidades adicionais, tais como um tamanho de piscina de backend maior e Zonas de Disponibilidade. É importante que compreenda as diferenças entre os equilibradores de carga *Standard* e *Basic* antes de escolher qual usar. Uma vez criado um cluster AKS, não é possível alterar o balanceador de carga SKU para esse cluster. Para obter mais informações sobre os SKUs *básicos* e *padrão,* consulte [a comparação SKU do balanceador de carga Azure][azure-lb-comparison].
O cluster AKS deve utilizar o balanceador de carga Standard SKU para utilizar várias piscinas de nós, a função não é suportada com equilibradores de carga Basic SKU, ver [Criar e gerir várias piscinas de nós para um cluster em AKS][use-multiple-node-pools].

Este artigo pressupõe uma compreensão básica dos conceitos kubernetes e Azure Load Balancer. Para obter mais informações, consulte [os conceitos fundamentais da Kubernetes para o Serviço Azure Kubernetes (AKS)][kubernetes-concepts] e [o que é o Azure Load Balancer?][azure-lb]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este artigo requer que esteja a executar a versão Azure CLI 2.0.81 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem um cluster AKS com o *Balancer de* Carga Standard SKU Azure. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

O principal do serviço de cluster AKS também precisa de permissão para gerir os recursos da rede se utilizar uma sub-rede ou grupo de recursos existentes. Em geral, atribua o papel *de contribuinte da Rede* ao seu principal serviço nos recursos delegados. Em vez de um principal de serviço, também pode utilizar o sistema atribuído à identidade gerida para permissões. Para obter mais informações, consulte [utilização de identidades geridas.](use-managed-identity.md) Para obter mais informações sobre permissões, consulte [o delegado AKS acesso a outros recursos Azure][aks-sp].

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Passar de um Balanceador de Carga SKU básico para o SKU Standard

Se tiver um cluster existente com o Balanceador de Carga Básico SKU, existem diferenças comportamentais importantes a notar ao migrar para usar um cluster com o Balancer de Carga SKU Padrão.

Por exemplo, fazer implantações azuis/verdes para migrar clusters é uma prática comum dado que o `load-balancer-sku` tipo de cluster só pode ser definido no cluster criar tempo. No entanto, os balanceadores *básicos de* carga SKU utilizam endereços *IP Básicos SKU* que não são compatíveis com os Balanceadores de Carga *SKU padrão,* uma vez que requerem endereços *IP Standard SKU.* Ao migrar clusters para atualizar os SKUs do Balancer de Carga, será necessário um novo endereço IP com um endereço IP compatível SKU.

Para mais considerações sobre como migrar aglomerados, visite a [nossa documentação sobre considerações migratórias](aks-migration.md) para ver uma lista de tópicos importantes a ter em conta na migração. As limitações abaixo também são diferenças comportamentais importantes a notar ao usar balanceadores de carga padrão SKU em AKS.

### <a name="limitations"></a>Limitações

Aplicam-se as seguintes limitações quando cria e gere clusters AKS que suportam um equilibrador de carga com o *SKU padrão:*

* Pelo menos um prefixo IP ou IP público é necessário para permitir o tráfego de saída do cluster AKS. O prefixo IP ou IP público também é necessário para manter a conectividade entre o plano de controlo e os nós do agente, bem como para manter a compatibilidade com as versões anteriores da AKS. Tem as seguintes opções para especificar iPs públicos ou prefixos IP com um balanceador de carga *SKU standard:*
    * Forneça os seus próprios iPs públicos.
    * Forneça os seus próprios prefixos IP públicos.
    * Especifique um número até 100 para permitir que o cluster AKS crie muitos IPs públicos *Standard* SKU no mesmo grupo de recursos criado como o cluster AKS, que é geralmente nomeado com *MC_* no início. AKS atribui o IP público ao *balanceador de* carga Standard SKU. Por padrão, um IP público será automaticamente criado no mesmo grupo de recursos que o cluster AKS, se não for especificado nenhum IP público, prefixo IP público ou número de IPs. Também deve permitir endereços públicos e evitar criar qualquer Política de Azure que proíba a criação de IP.
* Ao utilizar o *SKU Standard* para um equilibrador de carga, deve utilizar a versão *1.13 ou superior de*Kubernetes .
* A definição do balanceador de carga SKU só pode ser feita quando se cria um cluster AKS. Não é possível alterar o balanceador de carga SKU depois de ter sido criado um cluster AKS.
* Só pode utilizar um tipo de balanceador de carga SKU (Básico ou Standard) num único cluster.
* *Padrão* Os balançadores de carga SKU suportam apenas endereços IP *Standard* SKU.

## <a name="use-the-standard-sku-load-balancer"></a>Utilize o *equilibrador de* carga Standard SKU

Quando cria um cluster AKS, por padrão, o balanceador de carga *Standard* SKU é utilizado quando gere serviços nesse cluster. Por exemplo, [o quickstart utilizando o Azure CLI][aks-quickstart-cli] implementa uma aplicação de amostra que utiliza o balanceador de carga *Standard* SKU.

> [!IMPORTANT]
> Os endereços IP públicos podem ser evitados personalizando uma rota definida pelo utilizador (UDR). Especificando o tipo de saída de um cluster AKS, uma vez que o UDR pode saltar o provisionamento IP e a configuração do pool de backend para o equilibrador de carga AZS criado pela AKS. Consulte [a definição de um cluster `outboundType` para 'userDefinedRouting'.](egress-outboundtype.md)

## <a name="configure-the-load-balancer-to-be-internal"></a>Configure o equilibrador de carga para ser interno

Também pode configurar o equilibrador de carga para ser interno e não expor um IP público. Para configurar o equilibrador de carga como interno, adicione `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` como anotação ao serviço *LoadBalancer.* Pode ver um manifesto de exemplo yaml, bem como mais detalhes sobre um equilibrador de carga interno [aqui][internal-lb-yaml].

## <a name="scale-the-number-of-managed-public-ips"></a>Escalar o número de IPs públicos geridos

Ao utilizar um balanceador de carga *Standard* SKU com IPs públicos de saída geridos, que são criados por padrão, pode escalar o número de IPs públicos geridos de saída utilizando o parâmetro de *contagem de ip gerido pelo balanceador de carga.*

Para atualizar um cluster existente, executar o seguinte comando. Este parâmetro também pode ser definido no intervalo para ter múltiplos IPs públicos geridos.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

O exemplo acima define o número de IPs públicos geridos de saída para *2* para o cluster *myAKSCluster* no *myResourceGroup*. 

Também pode utilizar o parâmetro de contagem de ip gerido pelo *balanceador de carga* para definir o número inicial de IPs públicos geridos ao criar o seu cluster, fixando o `--load-balancer-managed-outbound-ip-count` parâmetro e definindo-o para o valor pretendido. O número predefinido de IPs públicos geridos é 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Forneça os seus próprios IPs públicos ou prefixos para saídas

Ao utilizar um balanceador de carga *Standard* SKU, o cluster AKS cria automaticamente um IP público no mesmo grupo de recursos criado para o cluster AKS e atribui o IP público ao balanceador de carga *Standard* SKU. Em alternativa, pode atribuir o seu próprio IP público no tempo de criação do cluster ou pode atualizar as propriedades do equilibrador de carga existentes.

Ao trazer vários endereços IP ou prefixos, é capaz de definir vários serviços de suporte ao definir o endereço IP por trás de um único objeto de balançador de carga. O ponto final de saída de nós específicos dependerá do serviço a que estão associados.

### <a name="pre-requisites-to-bring-your-own-ip-addresses-or-ip-prefixes"></a>Pré-requisitos para trazer os seus próprios endereços IP ou prefixos IP
1. Tem de utilizar os IPs públicos *Standard* SKU para obter saídas com o seu *Esquilibr de* carga Standard. Pode verificar o SKU dos seus IPs públicos utilizando o comando [de show público-ip da rede Az:][az-network-public-ip-show]

   ```azurecli-interactive
   az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
   ```
 1. Os ips públicos e prefixos IP devem estar na mesma região e parte da mesma subscrição que o seu cluster AKS.
 1. Os iPs públicos e prefixos IP não podem ser criados pela AKS como um IP gerido. Certifique-se de que quaisquer IPs especificados como IPs personalizados foram criados manualmente e não ser o serviço AKS.
 1. Os ips públicos e prefixos IP não podem ser utilizados por outro recurso ou serviço.

 ### <a name="define-your-own-public-ip-or-prefixes-on-an-existing-cluster"></a>Defina o seu próprio IP público ou prefixos num cluster existente

Utilize o comando [de exibição público-ip da rede az][az-network-public-ip-show] para listar os IDs dos seus IPs públicos.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

O comando acima mostra o ID para o ip público *myPublicIP* no grupo de recursos *myResourceGroup.*

Utilize o comando *de atualização az aks* com o parâmetro *de saída ips de saída de saldo de carga* para atualizar o seu cluster com os seus IPs públicos.

O exemplo a seguir utiliza o parâmetro *de saída-out-out-out-balanceador de carga* com os IDs do comando anterior.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Também pode utilizar prefixos IP públicos para saídas com o seu balanceador de carga *SKU Standard.* O exemplo a seguir utiliza o comando [de programa de prefixo ip da rede az][az-network-public-ip-prefix-show] para listar os IDs dos seus prefixos IP públicos:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

O comando acima mostra o ID para o prefixo IP público *myPublicIPPrefix* no grupo de recursos *myResourceGroup.*

O exemplo a seguir utiliza o parâmetro *de saída-ip-prefixos de carregamento-balanceador* com os IDs do comando anterior.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Defina o seu próprio IP público ou prefixos no cluster criar tempo

Você pode desejar trazer seus próprios endereços IP ou prefixos IP para saídas no tempo de criação de cluster para apoiar cenários como whitelisting egress pontos finais. Anexar os mesmos parâmetros acima mostrados ao seu passo de criação de cluster para definir os seus próprios iPs públicos e prefixos IP no início do ciclo de vida de um cluster.

Utilize os *az aks criar* comando com o parâmetro *de saída ips de saída de equilíbrio de carga* para criar um novo cluster com os seus IPs públicos no início.

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

Utilize o *az aks criar* comando com o parâmetro *de pré-prefixos de saída ip-out para* criar um novo cluster com os seus prefixos IP públicos no início.

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

## <a name="configure-outbound-ports-and-idle-timeout"></a>Configure portas de saída e tempo limite de marcha lenta

> [!WARNING]
> A secção seguinte destina-se a cenários avançados de rede de maior escala ou para abordar problemas de exaustão SNAT com as configurações padrão. Tem de ter um inventário preciso da quota disponível para endereços VMs e IP antes de alterar *Os Portos Outbound atribuídos* ou *IdleTimeoutInMinutes* do seu valor padrão, a fim de manter clusters saudáveis.
> 
> Alterar os valores para *AlocedOutboundPorts* e *IdleTimeoutInMinutes* pode alterar significativamente o comportamento da regra de saída para o seu equilibrador de carga. Reveja as [regras de saída do Balancer de Carga][azure-lb-outbound-rules-overview], [carregue as regras de saída do Balancer][azure-lb-outbound-rules]e [as ligações de saída em Azure][azure-lb-outbound-connections] antes de atualizar estes valores para entender completamente o impacto das suas alterações.

As portas alocadas de saída e os seus intervalos de tempo inativos são utilizados para [o SNAT][azure-lb-outbound-connections]. Por predefinição, o balanceador de carga *Standard* SKU utiliza [uma atribuição automática para o número de portas de saída com base no tamanho da piscina de backend][azure-lb-outbound-preallocatedports] e um tempo limite de 30 minutos para cada porta. Para ver estes valores, utilize [a lista de regras de saída da rede Az lb][az-network-lb-outbound-rule-list] para mostrar a regra de saída para o equilibrador de carga:

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

A saída exemplo mostra o valor predefinido para *Portos Outbound atribuídos* e *IdleTimeoutInMinutes*. Um valor de 0 para *aLocedOutboundPorts* define o número de portas de saída utilizando a atribuição automática para o número de portas de saída com base no tamanho da piscina de backend. Por exemplo, se o cluster tiver 50 ou menos nós, são atribuídas 1024 portas para cada nó.

Considere alterar a definição de *OutboundPorts* ou *IdleTimeoutInMinutes* se espera enfrentar a exaustão do SNAT com base na configuração acima padrão. Cada endereço IP adicional permite 64.000 portas adicionais para alocação, no entanto o Azure Standard Load Balancer não aumenta automaticamente as portas por nó quando mais endereços IP são adicionados. Pode alterar estes valores definindo as portas de saída do balanceador de carga e os parâmetros *de tempo limite de equilibramento de carga.* *load-balancer-outbound-ports* Por exemplo:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> Deve [calcular a sua quota necessária][calculate-required-quota] antes de personalizar os *OutboundPorts atribuídos* para evitar problemas de conectividade ou de escala. O valor que especifica para *os Portos Outbound atribuídos* também deve ser um múltiplo de 8.

Também pode utilizar as portas de saída do *balanceador de carga* e os parâmetros de tempo limite de *equilibramento de carga* ao criar um cluster, mas também deve especificar quer o *balanceador de carga gerido-gerido-out-ip-count,* *load-balancer-out-ips*, ou *load-balancer-out-ip-prefixs.*  Por exemplo:

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

Ao alterar as *portas de saída do balanceador de carga* e os parâmetros de tempo de tempo de *escrupulo* de carga do seu padrão, afeta o comportamento do perfil do balançador de carga, que impacta todo o cluster.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>Quota necessária para personalizar OutboundPorts atribuídos
Você deve ter capacidade IP de saída suficiente com base no número de seus VMs de nó e portas de saída alocadas desejadas. Para validar tem capacidade IP de saída suficiente, utilize a seguinte fórmula: 
 
*saídaIPs* \* 64.000 \> *nodeVMs* \* *desejados Portos De Saída.*
 
Por exemplo, se tiver 3 *nodeVMs*, e 50.000 *Portos De Saída De Saída desejados,* precisa de ter pelo menos 3 *Blocos de Saída*. Recomenda-se que incorpore capacidade ip adicional de saída para além do que precisa. Além disso, você deve ter em conta o autoescalador do cluster e a possibilidade de atualizações de piscina de nó ao calcular a capacidade ip de saída. Para o autoescalador do cluster, reveja a contagem de nós corrente e a contagem máxima do nó e utilize o valor mais elevado. Para a atualização, contabiliza um VM adicional de nó para cada piscina de nó que permite a atualização.
 
Ao configurar *o IdleTimeoutInMinutes* para um valor diferente do padrão de 30 minutos, considere quanto tempo as suas cargas de trabalho precisarão de uma ligação de saída. Considere também o valor de tempo limite padrão para um balanceador de carga *Standard* SKU usado fora de AKS é de 4 minutos. Um valor *IdleTimeoutInMinutes* que reflita com mais precisão a sua carga de trabalho específica da AKS pode ajudar a diminuir a exaustão do SNAT causada pela ligação que já não é utilizada.

## <a name="restrict-access-to-specific-ip-ranges"></a>Restringir o acesso a gamas IP específicas

O Grupo de Segurança da Rede (NSG) associado à rede virtual para o balançador de carga, por padrão, tem uma regra que permite todo o tráfego externo de entrada. Pode atualizar esta regra apenas para permitir gamas IP específicas para o tráfego de entrada. O manifesto a seguir utiliza *loadBalancerSourceRanges* para especificar uma nova gama IP para tráfego externo de entrada:

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

O exemplo acima atualiza a regra para permitir apenas o tráfego externo de entrada a partir da gama *MY_EXTERNAL_IP_RANGE.* Mais informações sobre a utilização deste método para restringir o acesso ao serviço de balançadores de carga estão disponíveis na [documentação de Kubernetes.][kubernetes-cloud-provider-firewall]

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre os serviços da Kubernetes na documentação dos [serviços kubernetes.][kubernetes-services]

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
[azure-lb-comparison]: ../load-balancer/skus.md
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
[use-multiple-node-pools]: use-multiple-node-pools.md
