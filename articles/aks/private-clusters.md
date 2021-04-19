---
title: Criar um cluster privado de serviçoS Azure Kubernetes
description: Saiba como criar um cluster privado do Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 3/31/2021
ms.openlocfilehash: 339bb41aed5ead3d7ee7d1217bfbc771cf068832
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719120"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Criar um cluster privado de serviçoS Azure Kubernetes

Num cluster privado, o plano de controlo ou servidor API tem endereços IP internos que são definidos no [RFC1918 - Atribuição de endereços para](https://tools.ietf.org/html/rfc1918) documento de Internet Privada. Ao utilizar um cluster privado, pode garantir o tráfego de rede entre o seu servidor API e as suas piscinas de nó permanecem apenas na rede privada.

O avião de controlo ou servidor API está numa subscrição Azure Kubernetes gerida pelo Azure. O cluster ou piscina de nó de um cliente está na assinatura do cliente. O servidor e o cluster ou piscina de nó podem comunicar entre si através do [serviço Azure Private Link][private-link-service] na rede virtual do servidor API e um ponto final privado que está exposto na sub-rede do cluster AKS do cliente.

## <a name="region-availability"></a>Disponibilidade de região

O cluster privado está disponível em regiões públicas, governo Azure e regiões Azure China 21Vianet onde [a AKS é apoiada.](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)

> [!NOTE]
> Os sites do Governo de Azure são apoiados, no entanto, o Gov Texas dos EUA não é atualmente apoiado por falta de apoio ao Private Link.

## <a name="prerequisites"></a>Pré-requisitos

* A versão Azure CLI 2.2.0 ou mais tarde
* O serviço Private Link é suportado apenas no Balanceador de Carga Standard Azure. O Balanceador básico de carga do Azure não é suportado.  
* Para utilizar um servidor DNS personalizado, adicione o Azure DNS IP 168.63.129.16 como o servidor DNS a montante no servidor DNS personalizado.

## <a name="create-a-private-aks-cluster"></a>Criar um cluster AKS privado

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos ou utilize um grupo de recursos existente para o seu cluster AKS.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Rede básica predefinida 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Onde `--enable-private-cluster` está uma bandeira obrigatória para um aglomerado privado. 

### <a name="advanced-networking"></a>Rede avançada  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Onde `--enable-private-cluster` está uma bandeira obrigatória para um aglomerado privado. 

> [!NOTE]
> Se a ponte Docker se dirigir ao CIDR (172.17.0.1/16) se confrontar com o CIDR da sub-rede, altere o endereço da ponte Docker adequadamente.

## <a name="configure-private-dns-zone"></a>Configurar zona privada de DNS 

Os seguintes parâmetros podem ser alavancados para configurar a Zona Privada de DNS.

- "Sistema" é o valor predefinido. Se o argumento da zona de dns-private for omitido, a AKS criará uma Zona Privada de DNS no Grupo de Recursos de Nó.
- "Nenhum" significa que a AKS não criará uma Zona Privada de DNS.  Isto requer que você traga o seu próprio servidor DNS e configurar a resolução DNS para o FQDN privado.  Se não configurar a resolução DNS, o DNS só é resolúvel dentro dos nós do agente e causará problemas de cluster após a implementação. 
- "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID" requer que crie uma Zona Privada de DNS neste formato para uma nuvem global azul: `privatelink.<region>.azmk8s.io` . Vai precisar da identificação de recursos daquela Zona Privada de DNS.  Além disso, você precisará de um utilizador atribuído identidade ou principal de serviço com pelo menos as `private dns zone contributor`  funções e `vnet contributor` funções.
- "fqdn-subdomínio" só pode ser utilizado com "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID" apenas para fornecer capacidades de subdomínio para `privatelink.<region>.azmk8s.io`

### <a name="prerequisites"></a>Pré-requisitos

* A versão de pré-visualização AKS 0.5.7 ou posterior
* A versão api 2020-11-01 ou mais tarde

### <a name="create-a-private-aks-cluster-with-private-dns-zone"></a>Criar um cluster AKS privado com Zona Privada de DNS

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone [system|none]
```

### <a name="create-a-private-aks-cluster-with-a-custom-private-dns-zone"></a>Crie um cluster AKS privado com uma Zona DE DNS Privada Personalizada

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId> --fqdn-subdomain <subdomain-name>
```

## <a name="options-for-connecting-to-the-private-cluster"></a>Opções de ligação ao cluster privado

O ponto final do servidor API não tem endereço IP público. Para gerir o servidor API, terá de utilizar um VM que tenha acesso à Rede Virtual Azure (VNet) do cluster AKS. Existem várias opções para estabelecer a conectividade da rede com o cluster privado.

* Crie um VM na mesma Rede Virtual Azure (VNet) que o cluster AKS.
* Utilize um VM numa rede separada e crie [olhando a rede Virtual][virtual-network-peering].  Consulte a secção abaixo para mais informações sobre esta opção.
* Utilize uma rota expressa ou uma ligação [VPN.][express-route-or-VPN]
* Utilize a [função comando de execução AKS](#aks-run-command-preview).

Criar um VM no mesmo VNET que o cluster AKS é a opção mais fácil.  A Rota Express e as VPNs adicionam custos e exigem uma complexidade adicional de rede.  O espreguiçadamento da rede virtual requer que planeie as gamas CIDR da sua rede para garantir que não existem intervalos sobrepostos.

### <a name="aks-run-command-preview"></a>Comando de execução de AKS (pré-visualização)

Hoje, quando precisa de aceder a um cluster privado, deve fazê-lo dentro da rede virtual do cluster ou de uma rede ou máquina de clientes. Isto normalmente requer que a sua máquina seja conectada via VPN ou Rota Expressa para a rede virtual do cluster ou uma caixa de salto a ser criada na rede virtual do cluster. O comando de execução AKS permite-lhe invocar remotamente comandos num cluster AKS através da AKS API. Esta funcionalidade fornece uma API que permite, por exemplo, executar comandos just-in-time a partir de um portátil remoto para um cluster privado. Isto pode ajudar muito a ter acesso rápido just-in-time a um cluster privado quando a máquina cliente não está na rede privada do cluster, mantendo e executando os mesmos controlos RBAC e servidor API privado.

### <a name="register-the-runcommandpreview-preview-feature"></a>Registar a `RunCommandPreview` funcionalidade de pré-visualização

Para utilizar a nova API do Comando de Execução, tem de ativar a bandeira de `RunCommandPreview` funcionalidades na sua subscrição.

Registe o pavilhão de `RunCommandPreview` características utilizando o comando [az-feature-register], como mostra o seguinte exemplo:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "RunCommandPreview"
```

Demora alguns minutos para que o estado seja *apresentado.* Verifique o estado de registo utilizando o comando [da lista de recursos AZ:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/RunCommandPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, reaprovi o registo do fornecedor de recursos *Microsoft.ContainerService* utilizando o comando [de registo do fornecedor az:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-run-command"></a>Use o comando de execução de AKS

Comando simples

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl get pods -n kube-system"
```

Implementar um manifesto anexando o ficheiro específico

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f deployment.yaml
```

Implementar um manifesto anexando uma pasta inteira

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f .
```

Realizar uma instalação Helm e passar os valores específicos manifestar

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "helm repo add bitnami https://charts.bitnami.com/bitnami && helm repo update && helm install my-release -f values.yaml bitnami/nginx" -f values.yaml
```

## <a name="virtual-network-peering"></a>Peering de rede virtual

Como mencionado, o olhar de rede virtual é uma forma de aceder ao seu cluster privado. Para utilizar o espreitamento de rede virtual, é necessário estabelecer uma ligação entre a rede virtual e a zona privada de DNS.
    
1. Vá ao grupo de recursos de nó no portal Azure.  
2. Selecione a zona privada do DNS.   
3. No painel esquerdo, selecione a ligação **de rede Virtual.**  
4. Crie um novo link para adicionar a rede virtual do VM à zona privada de DNS. Leva alguns minutos para que a ligação da zona DNS fique disponível.  
5. No portal Azure, navegue para o grupo de recursos que contém a rede virtual do seu cluster.  
6. No painel direito, selecione a rede virtual. O nome da rede virtual está na forma *aks-vnet- \**.  
7. No painel esquerdo, **selecione Peerings**.  
8. **Selecione Adicionar,** adicione a rede virtual do VM e, em seguida, crie o espremiamento.  
9. Vá à rede virtual onde tem o VM, **selecione Peerings,** selecione a rede virtual AKS e, em seguida, crie o espremiamento. Se o endereço variar na rede virtual AKS e o choque de rede virtual do VM, o espreitamento falha. Para obter mais informações, consulte [a rede Virtual a espreitar.][virtual-network-peering]

## <a name="hub-and-spoke-with-custom-dns"></a>Hub e falou com DNS personalizados

[As arquiteturas hub e fala-da-voz](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) são comumente usadas para implantar redes em Azure. Em muitas destas implementações, as definições de DNS nos VNets de spoke são configuradas para referenciar um reencaminhador de DNS central para permitir a resolução de DNS no local e a resolução de DNS baseada em Azure. Ao colocar um cluster AKS num ambiente de ligação em rede, há algumas considerações especiais que devem ser tidas em conta.

![Centro de cluster privado e falou](media/private-clusters/aks-private-hub-spoke.png)

1. Por padrão, quando um cluster privado é aprovisionado, um ponto final privado (1) e uma zona privada de DNS (2) são criados no grupo de recursos geridos pelo cluster. O cluster usa um registo A na zona privada para resolver o IP do ponto final privado para comunicação ao servidor API.

2. A zona privada de DNS está ligada apenas ao VNet a que os nóns de cluster estão ligados (3). Isto significa que o ponto final privado só pode ser resolvido pelos anfitriões nesse VNet ligado. Em cenários em que nenhum DNS personalizado é configurado no VNet (padrão), este funciona sem problema como ponto de anfitrião em 168.63.129.16 para DNS que pode resolver registos na zona privada de DNS por causa do link.

3. Em cenários em que o VNet que contém o seu cluster tem configurações de DNS personalizadas (4), a implementação do cluster falha a menos que a zona privada de DNS esteja ligada ao VNet que contém os resolvers DNS personalizados (5). Esta ligação pode ser criada manualmente após a criação da zona privada durante o fornecimento de clusters ou através da automatização após a deteção da criação da zona utilizando mecanismos de implantação baseados em eventos (por exemplo, Azure Event Grid e Azure Functions).

> [!NOTE]
> Se estiver a utilizar [a Tabela De Rotas Bring Your Own com kubenet](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) e Bring Your Own DNS com Private Cluster, a criação do cluster falhará. Terá de associar o [RouteTable](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) no grupo de recursos de nó à sub-rede após a falha da criação do cluster, de modo a tornar a criação bem sucedida.

## <a name="limitations"></a>Limitações 
* As gamas autorizadas ip não podem ser aplicadas ao ponto final do servidor api privado, apenas se aplicam ao servidor API público
* [As limitações do serviço Azure Private Link][private-link-service] aplicam-se a clusters privados.
* Sem suporte para agentes hospedados pela Microsoft Azure DevOps com clusters privados. Considere usar [agentes auto-hospedados.](/azure/devops/pipelines/agents/agents?tabs=browser) 
* Para os clientes que precisam de permitir que o Registo de Contentores Azure trabalhe com AKS privado, a rede virtual de registo de contentores deve ser espreite com a rede virtual do cluster de agentes.
* Sem suporte para converter clusters AKS existentes em clusters privados
* A eliminação ou modificação do ponto final privado na sub-rede do cliente fará com que o cluster deixe de funcionar. 
* Depois de os clientes terem atualizado o registo A nos seus próprios servidores DNS, esses Pods ainda resolveriam o Apiserver FQDN para o IP mais antigo após a migração até que sejam reiniciados. Os clientes precisam de reiniciar os pods de rede de anfitriões e as cápsulas de DNSPolicy predefinidos após o controlo da migração do avião.
* No caso de manutenção no avião de controlo, o seu [IP AKS](./limit-egress-traffic.md) pode mudar. Neste caso, deve atualizar o registo A que aponta para o IP privado do servidor API no seu servidor DNS personalizado e reiniciar quaisquer cápsulas ou implementações personalizadas utilizando a hostNetwork.

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents
[availability-zones]: availability-zones.md
