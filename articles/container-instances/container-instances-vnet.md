---
title: Implementar instâncias de contentor numa rede virtual do Azure
description: Saiba como implementar grupos de contentores a uma rede de virtual do Azure nova ou existente.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 01/03/2019
ms.author: danlep
ms.openlocfilehash: c6c82ee26fdbd824bdf42720ed7fc08135a872da
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372417"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Implementar instâncias de contentor numa rede virtual do Azure

[Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md) fornece redes seguras e privadas, incluindo a filtragem, encaminhamento e peering para o Azure e de recursos no local. Ao implementar grupos de contentores numa rede virtual do Azure, os contentores podem comunicar de forma segura com outros recursos na rede virtual.

Grupos de contentores implementados numa rede virtual do Azure ativar cenários como:

* Comunicação direta entre grupos de contentores na mesma sub-rede
* Envie [baseado em tarefas](container-instances-restart-policy.md) saída de carga de trabalho de instâncias de contentor para uma base de dados na rede virtual
* Obter conteúdo do container instances a partir de um [ponto final de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) na rede virtual
* Comunicação de contentor com as máquinas virtuais na rede virtual
* Comunicação de contentor com os recursos no local através de um [gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoute](../expressroute/expressroute-introduction.md)

> [!IMPORTANT]
> Esta funcionalidade está atualmente em pré-visualização e algumas [limitações aplicam-se](#preview-limitations). As pré-visualizações ser-lhe-ão disponibilizadas na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="virtual-network-deployment-limitations"></a>Limitações da implementação de rede virtual

Determinadas limitações aplicam-se ao implementar grupos de contentores a uma rede virtual.

* Para implementar grupos de contentores a uma sub-rede, a sub-rede não pode conter outros tipos de recursos. Remover todos os recursos existentes de uma sub-rede existente antes de implementar grupos de contentores ao mesmo, ou criar uma nova sub-rede.
* Grupos de contentores implementados numa rede virtual não suportam atualmente endereços IP públicos ou etiquetas de nome DNS.
* Não é possível utilizar um [identidade gerida](container-instances-managed-identity.md) num grupo de contentores implementados numa rede virtual.
* Devido à rede recursos adicionais envolvidos, implementar um grupo de contentores a uma rede virtual é normalmente um pouco mais lenta do que a implementação de uma instância de contentor padrão.

## <a name="preview-limitations"></a>Limitações de pré-visualização

Enquanto esta funcionalidade está em pré-visualização, as seguintes limitações aplicam-se ao implementar grupos de contentores a uma rede virtual. 

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

Limites de recursos de contentor podem ser diferente do limites para as instâncias de contentor não estejam em rede nestas regiões. Contentores de Linux atualmente, apenas são suportados para esta funcionalidade. O suporte do Windows está previsto.

### <a name="unsupported-network-resources-and-features"></a>Funcionalidades e recursos de rede não suportada

* Azure Load Balancer
* Peering de rede virtual

**Eliminação do recurso de rede** requer [passos adicionais](#delete-network-resources) após a implantação de grupos de contentores para a rede virtual.

## <a name="required-network-resources"></a>Recursos de rede necessários

Existem três recursos de rede Virtual do Azure necessários para implementar grupos de contentores a uma rede virtual: o [rede virtual](#virtual-network) propriamente dito, uma [delegada sub-rede](#subnet-delegated) dentro da rede virtual e um [perfil de rede](#network-profile). 

### <a name="virtual-network"></a>Rede virtual

Uma rede virtual define o espaço de endereço que vai criar uma ou mais sub-redes. Em seguida, implementar recursos do Azure (como grupos de contentores) para as sub-redes na sua rede virtual.

### <a name="subnet-delegated"></a>Sub-rede (delegado)

Sub-redes segmentar a rede virtual em espaços de endereço separados utilizáveis pelos recursos do Azure que coloca nos mesmos. Criar uma ou várias sub-redes numa rede virtual.

A sub-rede que utiliza para grupos de contentores pode conter apenas os grupos de contentores. Ao implementar primeiro um grupo de contentores a uma sub-rede, o Azure delega essa sub-rede no Azure Container Instances. Assim que o delegado, a sub-rede pode ser utilizada apenas para grupos de contentores. Se está tentando implantar recursos que não seja a grupos de contentores a uma sub-rede de delegado, a operação falhará.

### <a name="network-profile"></a>Perfil de rede

Um perfil de rede é um modelo de configuração de rede para recursos do Azure. Especifica determinadas propriedades de rede para o recurso, por exemplo, a sub-rede na qual devem ser implementado. Quando usa primeiro a [criar contentor de az] [ az-container-create] comando para implementar um grupo de contentor para uma sub-rede (e, portanto, uma rede virtual), o Azure cria um perfil de rede para. Em seguida, pode usar esse perfil de rede para implementações futuras para a sub-rede. 

Para utilizar um modelo do Resource Manager, o ficheiro YAML ou um método programático para implementar um grupo de contentores a uma sub-rede, terá de fornecer o ID de recurso do Resource Manager completo de um perfil de rede. Pode utilizar um perfil criado anteriormente utilizando [criar contentor de az][az-container-create], ou criar um perfil com um modelo do Resource Manager (veja [exemplo de modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) e [referência](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles)). Para obter o ID de um perfil criado anteriormente, utilize o [lista de perfil de rede de az] [ az-network-profile-list] comando. 

No diagrama seguinte, vários grupos de contentores que foram implementados a uma sub-rede de delegado para o Azure Container Instances. Após a implantação de um grupo de contentores a uma sub-rede, pode implementar grupos de contentores adicionais ao mesmo, especificando o mesmo perfil de rede.

![Grupos de contentores dentro de uma rede virtual][aci-vnet-01]

## <a name="deployment-scenarios"></a>Cenários de implementação

Pode usar [criar contentor de az] [ az-container-create] implementar grupos de contentores para uma nova rede virtual e permitir que o Azure para criar os recursos de rede necessária para ou implementar uma rede virtual existente. 

### <a name="new-virtual-network"></a>Nova rede virtual

Para implementar uma nova rede virtual e que o Azure crie automaticamente os recursos de rede para, especifique o seguinte quando executa [criar contentor de az][az-container-create]:

* Nome da rede virtual:
* Prefixo de endereço de rede virtual no formato CIDR
* Nome da sub-rede
* Prefixo de endereço de sub-rede no formato CIDR

A rede virtual e os prefixos de endereço de sub-rede especificam os espaços de endereços para a rede virtual e sub-rede, respectivamente. Estes valores são representados na notação de Classless entre domínios encaminhamento (CIDR), por exemplo `10.0.0.0/16`. Para obter mais informações sobre como trabalhar com sub-redes, veja [adicionar, alterar ou eliminar uma sub-rede de rede virtual](../virtual-network/virtual-network-manage-subnet.md).

Assim que tiver implantado o seu primeiro grupo de contentor com este método, pode implementar a mesma sub-rede, especificando a rede virtual e os nomes de sub-rede ou o perfil de rede que o Azure cria automaticamente para. Porque o Azure delega a sub-rede para o Azure Container Instances, pode implementar *apenas* grupos de contentores para a sub-rede.

### <a name="existing-virtual-network"></a>Rede virtual existente

Para implementar um grupo de contentores a uma rede virtual existente:

1. Criar uma sub-rede na sua rede virtual existente ou uma sub-rede existente de vazio *todos os* outros recursos
1. Implementar um grupo de contentores com [criar contentor de az] [ az-container-create] e especifique um dos seguintes:
   * Nome de rede virtual e o nome de sub-rede
   * Recurso de rede virtual ID e o ID de recurso de sub-rede, que permite o uso de uma rede virtual de um grupo de recursos diferente
   * Nome do perfil de rede ou o ID, que pode utilizar [lista de perfil de rede de az][az-network-profile-list]

Depois de implementar o seu primeiro grupo de contentores a uma sub-rede existente, o Azure delega essa sub-rede no Azure Container Instances. Já não pode implementar recursos que não seja a grupos de contentores a essa sub-rede.

## <a name="deployment-examples"></a>Exemplos de implementação

As secções seguintes descrevem como implementar grupos de contentores a uma rede virtual com a CLI do Azure. Os exemplos de comando são formatados para o **Bash** shell. Se preferir outro shell, tais como o PowerShell ou a linha de comandos, ajuste os caracteres de continuação de linha em conformidade.

### <a name="deploy-to-a-new-virtual-network"></a>Implementar uma nova rede virtual

Em primeiro lugar, implemente um grupo de contentores e especificar os parâmetros para uma nova rede virtual e uma sub-rede. Quando especificar estes parâmetros, o Azure cria a rede virtual e sub-rede, delega a sub-rede para o Azure Container instances e também cria um perfil de rede. Estes recursos são criados, seu grupo de contentores é implementado para a sub-rede.

Execute o seguinte [criar contentor de az] [ az-container-create] comando que especifica definições para uma nova rede virtual e uma sub-rede. Precisa fornecer o nome do grupo de recursos que foi criado numa região que [suporta](#preview-limitations) grupos de contentores numa rede virtual. Este comando implementa o Microsoft público [aci-helloworld] [ aci-helloworld] contentor que executa um servidor de Web do node. js pequeno que serve uma página web estática. Na secção seguinte, irá implementar um segundo grupo de contentores à mesma sub-rede e testar a comunicação entre as instâncias de contentores de dois.

```azurecli
az container create \
    --name appcontainer \
    --resource-group myResourceGroup \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --vnet aci-vnet \
    --vnet-address-prefix 10.0.0.0/16 \
    --subnet aci-subnet \
    --subnet-address-prefix 10.0.0.0/24
```

Quando implementar uma nova rede virtual através deste método, a implementação pode demorar alguns minutos enquanto os recursos de rede são criados. Após a implementação inicial, implementações de grupo de contentores adicionais concluir mais rapidamente.

### <a name="deploy-to-existing-virtual-network"></a>Implementar a rede virtual existente

Agora que implementou um grupo de contentores de uma nova rede virtual, implementar um segundo grupo de contentores à mesma sub-rede e verifique se a comunicação entre as instâncias de contentores de dois.

Primeiro, obtenha o endereço IP do primeiro grupo de contentores que implementou, o *appcontainer*:

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

A saída deverá apresentar o endereço IP do grupo de contentores na sub-rede privada:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Agora, defina `CONTAINER_GROUP_IP` para o IP de obteve com o `az container show` de comandos e execute o seguinte `az container create` comando. Este contentor de segundo *commchecker*, executa uma imagem com base em Alpine Linux e executa `wget` contra o endereço IP da primeiro grupo de contentores sub-rede privada.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-here>

az container create \
    --resource-group myResourceGroup \
    --name commchecker \
    --image alpine:3.5 \
    --command-line "wget $CONTAINER_GROUP_IP" \
    --restart-policy never \
    --vnet aci-vnet \
    --subnet aci-subnet
```

Concluída esta segunda implementação de contentor, extrair seus registos, para que possa ver a saída do `wget` comando ele executado:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Se o segundo contentor comunicadas com êxito com o primeiro, o resultado deve ser semelhante a:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

A saída de registo deve mostrar que `wget` foi capaz de se ligar e transferir o ficheiro de índice a partir do primeiro contentor com o respetivo endereço IP privado na sub-rede local. Tráfego de rede entre os grupos de dois contentor permaneceu dentro da rede virtual.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Implementar a rede virtual existente - YAML

Também pode implementar um grupo de contentores a uma rede virtual existente, utilizando um ficheiro YAML. Para implementar uma sub-rede numa rede virtual, especificar várias propriedades adicionais no YAML:

* `ipAddress`: As definições do endereço IP para o grupo de contentores.
  * `ports`: As portas para abrir, se aplicável.
  * `protocol`: O protocolo (TCP ou UDP) para a porta aberta.
* `networkProfile`: Especifica as definições de rede, como a rede virtual e uma sub-rede para um recurso do Azure.
  * `id`: O ID de recurso do Resource Manager completo do `networkProfile`.

Para implementar um grupo de contentores a uma rede virtual com um ficheiro YAML, tem primeiro de obter o ID do perfil de rede. Executar o [lista de perfil de rede de az] [ az-network-profile-list] comando, especificando o nome do grupo de recursos que contém a sua rede virtual e sub-rede delegado.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

A saída do comando apresenta o ID do recurso completo para o perfil de rede:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Depois de ter a rede de ID de perfil, copie o seguinte YAML para um novo ficheiro designado *aci.yaml vnet implementar*. Sob `networkProfile`, substitua o `id` valor com o ID que acabou obtido, em seguida, guarde o ficheiro. Este YAML cria um grupo de contentores com o nome *appcontaineryaml* na sua rede virtual.

```YAML
apiVersion: '2018-09-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/container/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Implementar o grupo de contentores com o [criar contentor de az] [ az-container-create] comando, especificando o nome de ficheiro YAML para o `--file` parâmetro:

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

Depois de concluída a implementação, execute o [show de contentor az] [ az-container-show] comando para apresentar o estado:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Limpar recursos

### <a name="delete-container-instances"></a>Eliminar instâncias de contentores

Quando tiver terminado a trabalhar com as instâncias de contentor que criou, eliminá-los com os seguintes comandos:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Eliminar recursos de rede

A pré-visualização inicial desse recurso exige vários comandos adicionais para eliminar os recursos de rede que criou anteriormente. Se utilizou os comandos de exemplo nas secções anteriores deste artigo para criar a rede virtual e sub-rede, em seguida, pode utilizar o seguinte script para eliminar os recursos de rede.

Antes de executar o script, defina o `RES_GROUP` variável para o nome do grupo de recursos que contém a rede virtual e a sub-rede que deve ser eliminado. O script é formatado para o shell de Bash. Se preferir outro shell, tais como o PowerShell ou a linha de comandos, terá de ajustar a atribuição de variáveis e acessadores de forma apropriada.

> [!WARNING]
> Este script elimina recursos! Elimina a rede virtual e todas as sub-redes que nele contidos. Certifique-se de que já não precisar *qualquer* dos recursos na rede virtual, incluindo quaisquer sub-redes contém, antes de executar este script. Depois de serem eliminadas **estes recursos são irrecuperáveis**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Get the service association link (SAL) ID
SAL_ID=$(az network vnet subnet show --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet --query id --output tsv)/providers/Microsoft.ContainerInstance/serviceAssociationLinks/default

# Delete the default SAL ID for the subnet
az resource delete --ids $SAL_ID --api-version 2018-07-01

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet --remove delegations 0

# Delete the subnet
az network vnet subnet delete --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Passos Seguintes

Para implementar uma nova rede virtual, a sub-rede, o perfil de rede e o grupo de contentores com um modelo do Resource Manager, veja [criar um grupo de contentor do Azure com VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).

Vários recursos de rede virtual e funcionalidades foram abordadas neste artigo, embora brevemente. A documentação da rede Virtual do Azure abrange extensivamente estes tópicos:

* [Rede virtual](../virtual-network/manage-virtual-network.md)
* [Sub-rede](../virtual-network/virtual-network-manage-subnet.md)
* [Pontos finais de serviço](../virtual-network/virtual-network-service-endpoints-overview.md)
* [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute](../expressroute/expressroute-introduction.md)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
