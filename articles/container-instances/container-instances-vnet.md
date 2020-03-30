---
title: Implementar grupo de contentores para rede virtual Azure
description: Aprenda a implantar grupos de contentores numa rede virtual Azure nova ou existente.
ms.topic: article
ms.date: 01/06/2020
ms.author: danlep
ms.openlocfilehash: 318576e9b5c5b32bbc993ea16494c938b74bd2f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77200066"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Implementar instâncias de contentores numa rede virtual Azure

[A Rede Virtual Azure](../virtual-network/virtual-networks-overview.md) fornece redes seguras e privadas para os seus recursos Azure e no local. Ao implantar grupos de contentores numa rede virtual Azure, os seus contentores podem comunicar de forma segura com outros recursos da rede virtual.

Grupos de contentores implantados numa rede virtual Azure permitem cenários como:

* Comunicação direta entre grupos de contentores na mesma subrede
* Envie a saída de carga de trabalho [baseada em tarefas](container-instances-restart-policy.md) de instâncias de contentores para uma base de dados na rede virtual
* Recuperar conteúdo para instâncias de contentores a partir de um [ponto final](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço na rede virtual
* Comunicação de contentores com máquinas virtuais na rede virtual
* Comunicação de contentores com recursos no local através de um [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoute](../expressroute/expressroute-introduction.md)

> [!IMPORTANT]
> As implantações de grupos de contentores para uma rede virtual estão geralmente disponíveis para cargas de trabalho de produção apenas nas seguintes regiões: **Leste dos EUA, Centro-Sul dos EUA e Us 2**Ocidental . Noutras regiões onde a funcionalidade está disponível, as implementações de redes virtuais estão atualmente em pré-visualização, com disponibilidade geral prevista para um futuro próximo. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. 


## <a name="virtual-network-deployment-limitations"></a>Limitações de implantação de rede virtual

Certas limitações aplicam-se quando se implanta grupos de contentores numa rede virtual.

* Para implantar grupos de contentores numa sub-rede, a sub-rede não pode conter outros tipos de recursos. Remova todos os recursos existentes de uma sub-rede existente antes de implantar grupos de contentores para ela, ou crie uma nova sub-rede.
* Não se pode usar uma [identidade gerida](container-instances-managed-identity.md) num grupo de contentores implantado numa rede virtual.
* Não é possível permitir uma sonda de [vivacidade](container-instances-liveness-probe.md) ou uma sonda de [prontidão](container-instances-readiness-probe.md) num grupo de contentores implantado numa rede virtual.
* Devido aos recursos adicionais de rede envolvidos, a implantação de um grupo de contentores para uma rede virtual é tipicamente mais lenta do que a implantação de uma instância padrão de contentores.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

Os limites dos recursos dos contentores podem diferir dos limites para as instâncias de contentores não em rede nestas regiões. Atualmente apenas os recipientes Linux são suportados para esta funcionalidade. O suporte para windows está planeado.

### <a name="unsupported-networking-scenarios"></a>Cenários de networking não suportados 

* **Azure Load Balancer** - Colocação de um Balancer de Carga Azure em frente a instâncias de contentores num grupo de contentores em rede não é suportado
* **Peering de rede virtual**
  * O peering VNet não funcionará para a ACI se a rede que o ACI VNet estiver a espreitar para utilizar um espaço IP público. A rede peered precisa de um espaço IP privado RFC 1918 para que a VNet olhe para o trabalho. 
  * Só pode sondar o seu VNet por outro VNet
* **Encaminhamento de tráfego de rede virtual** - As rotas personalizadas não podem ser configuradas em torno de IPs públicos. As rotas podem ser criadas no espaço IP privado da subnet delegada em que os recursos da ACI são implantados 
* **Grupos** de segurança de rede - As regras de segurança de saída em NSGs aplicadas a uma subnet delegada em Casos de Contentores Azure não são atualmente aplicadas 
* **Etiqueta IP pública ou DNS** - Grupos de contentores implantados numa rede virtual não suportam atualmente expor contentores diretamente à internet com um endereço IP público ou um nome de domínio totalmente qualificado
* **Resolução de nomes internos** - Resolução de nomes para os recursos do Azure na rede virtual através do DNS interno do Azure não é suportada

**A eliminação** do recurso de rede requer [passos adicionais](#delete-network-resources) depois de ter implantado grupos de contentores na rede virtual.

## <a name="required-network-resources"></a>Recursos de rede necessários

Existem três recursos da Rede Virtual Azure necessários para a implantação de grupos de contentores para uma rede virtual: a [própria rede virtual,](#virtual-network) uma [subrede delegada](#subnet-delegated) dentro da rede virtual e um perfil de [rede.](#network-profile) 

### <a name="virtual-network"></a>Rede virtual

Uma rede virtual define o espaço de endereço no qual cria uma ou mais subredes. Em seguida, implanta recursos Azure (como grupos de contentores) nas subredes da sua rede virtual.

### <a name="subnet-delegated"></a>Subnet (delegado)

As subredes segmentam a rede virtual em espaços de endereços separados utilizáveis pelos recursos Azure que você colocar neles. Cria-se uma ou várias subredes dentro de uma rede virtual.

A sub-rede que utiliza para grupos de contentores pode conter apenas grupos de contentores. Quando se coloca pela primeira vez um grupo de contentores numa sub-rede, o Azure delega essa sub-rede para as instâncias de contentores de Azure. Uma vez delegada, a sub-rede só pode ser utilizada para grupos de contentores. Se tentar mobilizar recursos que não os grupos de contentores para uma sub-rede delegada, a operação falha.

### <a name="network-profile"></a>Perfil de rede

Um perfil de rede é um modelo de configuração de rede para recursos Azure. Especifica certas propriedades da rede para o recurso, por exemplo, a subrede na qual deve ser implantado. Quando utiliza o [recipiente Az criar][az-container-create] um comando para implantar um grupo de contentores numa subrede (e, portanto, uma rede virtual), o Azure cria um perfil de rede para si. Em seguida, pode utilizar esse perfil de rede para futuras implementações na sub-rede. 

Para utilizar um modelo de Gestor de Recursos, ficheiro YAML ou um método programático para implantar um grupo de contentores numa subnet, precisa de fornecer o ID completo do recurso do Gestor de Recursos de um perfil de rede. Pode utilizar um perfil previamente criado usando a criação do [recipiente Az,][az-container-create]ou criar um perfil utilizando um modelo de Gestor de Recursos (ver exemplo de [modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) e [referência).](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles) Para obter a identificação de um perfil previamente criado, use o comando da lista de perfis de [rede az.][az-network-profile-list] 

No diagrama seguinte, vários grupos de contentores foram implantados numa subnet delegada em Instâncias de Contentores De Azure. Depois de ter implantado um grupo de contentores numa subnet, pode implantar grupos de contentores adicionais para o mesmo perfil de rede.

![Grupos de contentores dentro de uma rede virtual][aci-vnet-01]

## <a name="deployment-scenarios"></a>Cenários de implementação

Pode utilizar a [criação de contentores Az][az-container-create] para implantar grupos de contentores numa nova rede virtual e permitir que o Azure crie os recursos de rede necessários para si, ou desloque-se para uma rede virtual existente. 

### <a name="new-virtual-network"></a>Nova rede virtual

Para implantar numa nova rede virtual e fazer com que o Azure crie automaticamente os recursos de rede para si, especifique o seguinte quando executar o [recipiente Az criar:][az-container-create]

* Nome da rede virtual
* Prefixo de endereço de rede virtual no formato CIDR
* Nome da sub-rede
* Prefixo de endereço subnet no formato CIDR

Os prefixos de endereço de rede virtual e sub-rede especificam os espaços de endereço para a rede virtual e sub-rede, respectivamente. Estes valores estão representados na notação de encaminhamento `10.0.0.0/16`inter-domínio sem classe (CIDR), por exemplo. Para obter mais informações sobre o trabalho com subredes, consulte [Adicionar, alterar ou eliminar uma subrede de rede virtual](../virtual-network/virtual-network-manage-subnet.md).

Uma vez implantado o seu primeiro grupo de contentores com este método, pode ser implantado na mesma sub-rede especificando os nomes de rede virtual e sub-rede, ou o perfil de rede que o Azure cria automaticamente para si. Uma vez que o Azure delega a subnet a Casos de Contentores Azure, só pode *implantar* grupos de contentores na sub-rede.

### <a name="existing-virtual-network"></a>Rede virtual existente

Para implantar um grupo de contentores numa rede virtual existente:

1. Crie uma subrede dentro da sua rede virtual existente, utilize uma subrede existente na qual um grupo de contentores já está implantado, ou utilize uma subrede existente esvaziada de *todos os* outros recursos
1. Implante um grupo de contentores com [recipiente az criar][az-container-create] e especificar um dos seguintes:
   * Nome de rede virtual e nome da sub-rede
   * ID de recursos de rede virtual e ID de recursos de subnet, que permite usar uma rede virtual de um grupo de recursos diferente
   * Nome ou ID de perfil de rede, que pode obter usando a lista de perfis de [rede az][az-network-profile-list]

Assim que implantar o seu primeiro grupo de contentores numa subrede existente, o Azure delega essa sub-rede para as Instâncias de Contentores Azure. Não pode mais utilizar recursos para além dos grupos de contentores para essa sub-rede.

## <a name="deployment-examples"></a>Exemplos de implantação

As seguintes secções descrevem como implantar grupos de contentores numa rede virtual com o Azure CLI. Os exemplos de comando estão formatados para **a** concha bash. Se preferir outra concha, como powerShell ou Command Prompt, ajuste os caracteres de continuação da linha em conformidade.

### <a name="deploy-to-a-new-virtual-network"></a>Implementar para uma nova rede virtual

Em primeiro lugar, implante um grupo de contentores e especifique os parâmetros para uma nova rede virtual e sub-rede. Quando especifica estes parâmetros, o Azure cria a rede virtual e a subnet, delega a subnet para as instâncias do Contentor Azure, e também cria um perfil de rede. Uma vez criados estes recursos, o seu grupo de contentores é implantado na sub-rede.

Executar o [seguinte recipiente az criar][az-container-create] um comando que especifica as definições para uma nova rede virtual e sub-rede. É necessário fornecer o nome de um grupo de recursos que foi criado numa região onde estão [disponíveis](#virtual-network-deployment-limitations)implantações de grupos de contentores numa rede virtual. Este comando implementa o recipiente público da Microsoft [aci-helloworld][aci-helloworld] que executa um pequeno webserver Node.js servindo uma página web estática. Na secção seguinte, irá colocar um segundo grupo de contentores na mesma sub-rede e testar a comunicação entre as duas instâncias do contentor.

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

Quando se implanta para uma nova rede virtual utilizando este método, a implementação pode demorar alguns minutos enquanto os recursos de rede são criados. Após a implantação inicial, as implantações adicionais do grupo de contentores completam-se mais rapidamente.

### <a name="deploy-to-existing-virtual-network"></a>Implementar para rede virtual existente

Agora que implantou um grupo de contentores para uma nova rede virtual, implante um segundo grupo de contentores para a mesma subnet, e verifique a comunicação entre as duas instâncias de contentores.

Primeiro, obtenha o endereço IP do primeiro grupo de contentores que implementou, o contentor de *aplicações:*

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

A saída deve apresentar o endereço IP do grupo de contentores na sub-rede privada:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Agora, `CONTAINER_GROUP_IP` adere ao IP `az container show` que recuperou com `az container create` o comando e execute o seguinte comando. Este segundo recipiente, *commchecker,* executa uma imagem `wget` baseada em Alpine Linux e executa contra o endereço IP da subnet privada do primeiro grupo de contentores.

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

Depois de concluída esta segunda implantação do contentor, puxe os `wget` seus troncos para que possa ver a saída do comando que executou:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Se o segundo recipiente comunicar com sucesso com o primeiro, a saída deve ser semelhante a:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

A saída de `wget` registo deve mostrar que foi capaz de ligar e descarregar o ficheiro de índice do primeiro recipiente utilizando o seu endereço IP privado na subnet local. O tráfego de rede entre os dois grupos de contentores manteve-se dentro da rede virtual.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Implemente para a rede virtual existente - YAML

Também pode implantar um grupo de contentores numa rede virtual existente utilizando um ficheiro YAML, um modelo de Gestor de Recursos ou outro método programático, como o Python SDK. Para implantar numa subrede numa rede virtual, especifice várias propriedades adicionais no YAML:

* `ipAddress`: As definições de endereço IP para o grupo de contentores.
  * `ports`: As portas para abrir, se houver.
  * `protocol`: O protocolo (TCP ou UDP) para a porta aberta.
* `networkProfile`: Especifica definições de rede como a rede virtual e a sub-rede para um recurso Azure.
  * `id`: O ID completo do `networkProfile`recurso do Gestor de Recursos do .

Para implantar um grupo de contentores numa rede virtual com um ficheiro YAML, primeiro é necessário obter a identificação do perfil da rede. Execute o comando da lista de perfis de [rede Az,][az-network-profile-list] especificando o nome do grupo de recursos que contém a sua rede virtual e subnet delegado.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

A saída do comando apresenta o ID completo do recurso para o perfil de rede:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Assim que tiver o ID do perfil de rede, copie o seguinte YAML num novo ficheiro chamado *vnet-deploy-aci.yaml*. Em `networkProfile`baixo, `id` substitua o valor por ID que acabou de recuperar e, em seguida, guarde o ficheiro. Este YAML cria um grupo de contentores chamado *appcontaineryaml* na sua rede virtual.

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
    id: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Implante o grupo de contentores com o [recipiente az criar][az-container-create] `--file` comando, especificando o nome de ficheiro YAML para o parâmetro:

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

Uma vez concluída a implantação, execute o comando de exibição do [contentor az][az-container-show] para mostrar o seu estado:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Limpar recursos

### <a name="delete-container-instances"></a>Eliminar instâncias de contentores

Quando terminar de trabalhar com as instâncias do contentor que criou, elimine-as com os seguintes comandos:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Eliminar recursos de rede

Esta funcionalidade requer atualmente vários comandos adicionais para eliminar os recursos de rede que criou anteriormente. Se usou os comandos de exemplo em secções anteriores deste artigo para criar a sua rede virtual e sub-rede, então pode utilizar o seguinte script para eliminar esses recursos de rede. O script pressupõe que o seu grupo de recursos contém uma única rede virtual com um único perfil de rede.

Antes de executar o `RES_GROUP` script, detete a variável para o nome do grupo de recursos que contém a rede virtual e a sub-rede que devem ser eliminadas. Atualize o nome da rede virtual `aci-vnet` se não tiver utilizado o nome sugerido anteriormente. O guião está formatado para a concha bash. Se preferir outra concha como powerShell ou Command Prompt, terá de ajustar a atribuição variável e os acessórios em conformidade.

> [!WARNING]
> Este guião elimina recursos! Elimina a rede virtual e todas as redes que contém. Certifique-se de que já não precisa de *nenhum* dos recursos na rede virtual, incluindo quaisquer subredes que contenha, antes de executar este script. Uma vez eliminados, **estes recursos são irrecuperáveis.**

```azurecli
# Replace <my-resource-group> with the name of your resource group
# Assumes one virtual network in resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
# Assumes one profile in virtual network
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Passos seguintes

Para implantar uma nova rede virtual, subnet, perfil de rede e grupo de contentores usando um modelo de Gestor de Recursos, consulte Criar um grupo de [contentores Azure com VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).

Vários recursos e funcionalidades de rede virtuais foram discutidos neste artigo, embora brevemente. A documentação da Rede Virtual Azure aborda estes tópicos extensivamente:

* [Rede virtual](../virtual-network/manage-virtual-network.md)
* [Sub-rede](../virtual-network/virtual-network-manage-subnet.md)
* [Pontos finais de serviço](../virtual-network/virtual-network-service-endpoints-overview.md)
* [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)
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
