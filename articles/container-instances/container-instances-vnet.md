---
title: Implementar grupo de contentores para rede virtual Azure
description: Aprenda a implantar um grupo de contentores numa rede virtual Azure nova ou existente utilizando a interface de linha de comando Azure.
ms.topic: article
ms.date: 04/29/2020
ms.author: danlep
ms.openlocfilehash: 7e54690efc7955eaaa88ca87a6f7a086dd3e19a4
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583651"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Implementar instâncias de contentores numa rede virtual Azure

[A Rede Virtual Azure](../virtual-network/virtual-networks-overview.md) fornece redes seguras e privadas para os seus recursos Azure e no local. Ao implantar grupos de contentores numa rede virtual Azure, os seus contentores podem comunicar de forma segura com outros recursos da rede virtual.

Este artigo mostra como usar o [contentor az criar][az-container-create] comando no Azure CLI para implantar grupos de contentores para uma nova rede virtual ou uma rede virtual existente. 

Para cenários e limitações em rede, consulte cenários e recursos da rede Virtual para as [Instâncias de Contentores Azure](container-instances-virtual-network-concepts.md).

> [!IMPORTANT]
> A implantação do grupo de contentores para uma rede virtual está geralmente disponível para contentores Linux, na maioria das regiões onde o Azure Container Instances está disponível. Para mais detalhes, consulte [Regiões e disponibilidade de recursos.](container-instances-virtual-network-concepts.md#where-to-deploy) 

Exemplos neste artigo são formatados para a concha bash. Se preferir outra concha, como powerShell ou Command Prompt, ajuste os caracteres de continuação da linha em conformidade.


## <a name="deploy-to-new-virtual-network"></a>Implantação para nova rede virtual

Para implantar numa nova rede virtual e fazer com que o Azure crie automaticamente os recursos de rede para si, especifique o seguinte quando executar o [recipiente Az criar:][az-container-create]

* Nome da rede virtual
* Prefixo de endereço de rede virtual no formato CIDR
* Nome da sub-rede
* Prefixo de endereço subnet no formato CIDR

Os prefixos de endereço de rede virtual e sub-rede especificam os espaços de endereço para a rede virtual e sub-rede, respectivamente. Estes valores estão representados na notação de encaminhamento `10.0.0.0/16`inter-domínio sem classe (CIDR), por exemplo. Para obter mais informações sobre o trabalho com subredes, consulte [Adicionar, alterar ou eliminar uma subrede de rede virtual](../virtual-network/virtual-network-manage-subnet.md).

Uma vez implantado o seu primeiro grupo de contentores com este método, pode ser implantado na mesma sub-rede especificando os nomes de rede virtual e sub-rede, ou o perfil de rede que o Azure cria automaticamente para si. Uma vez que o Azure delega a subnet a Casos de Contentores Azure, só pode *implantar* grupos de contentores na sub-rede.

### <a name="example"></a>Exemplo

O [recipiente Az][az-container-create] seguinte cria comando especifica definições para uma nova rede virtual e sub-rede. Forneça o nome de um grupo de recursos criado numa região onde estão [disponíveis](container-instances-region-availability.md#availability---virtual-network-deployment)implantações de grupos de contentores numa rede virtual. Este comando implementa o recipiente público da Microsoft [aci-helloworld][aci-helloworld] que executa um pequeno webserver Node.js servindo uma página web estática. Na secção seguinte, irá colocar um segundo grupo de contentores na mesma sub-rede e testar a comunicação entre as duas instâncias do contentor.

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

Quando se implanta para uma nova rede virtual utilizando este método, a implementação pode demorar alguns minutos enquanto os recursos de rede são criados. Após a implantação inicial, as implantações adicionais do grupo de contentores para a mesma sub-rede completam-se mais rapidamente.

## <a name="deploy-to-existing-virtual-network"></a>Implementar para rede virtual existente

Para implantar um grupo de contentores numa rede virtual existente:

1. Crie uma subrede dentro da sua rede virtual existente, utilize uma subrede existente na qual um grupo de contentores já está implantado, ou utilize uma subrede existente esvaziada de *todos os* outros recursos
1. Implante um grupo de contentores com [recipiente az criar][az-container-create] e especificar um dos seguintes:
   * Nome de rede virtual e nome da sub-rede
   * ID de recursos de rede virtual e ID de recursos de subnet, que permite usar uma rede virtual de um grupo de recursos diferente
   * Nome ou ID de perfil de rede, que pode obter usando a lista de perfis de [rede az][az-network-profile-list]

### <a name="example"></a>Exemplo

O exemplo seguinte implanta um segundo grupo de contentores para a mesma subnet criada anteriormente, e verifica a comunicação entre as duas instâncias do contentor.

Primeiro, obtenha o endereço IP do primeiro grupo de contentores que implementou, o contentor de *aplicações:*

```azurecli
az container show --resource-group myResourceGroup \
  --name appcontainer \
  --query ipAddress.ip --output tsv
```

A saída apresenta o endereço IP do grupo de contentores na sub-rede privada. Por exemplo:

```console
10.0.0.4
```

Agora, `CONTAINER_GROUP_IP` adere ao IP `az container show` que recuperou com `az container create` o comando e execute o seguinte comando. Este segundo recipiente, *commchecker,* executa uma imagem `wget` baseada em Alpine Linux e executa contra o endereço IP da subnet privada do primeiro grupo de contentores.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-address>

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

Se o segundo recipiente comunicasse com sucesso com o primeiro, a saída é semelhante a:

```console
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

A saída de `wget` registo deve mostrar que foi capaz de ligar e descarregar o ficheiro de índice do primeiro recipiente utilizando o seu endereço IP privado na subnet local. O tráfego de rede entre os dois grupos de contentores manteve-se dentro da rede virtual.

### <a name="example---yaml"></a>Exemplo - YAML

Também pode implantar um grupo de contentores numa rede virtual existente utilizando um ficheiro YAML, um modelo de Gestor de [Recursos,](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)ou outro método programático, como o Python SDK. 

Por exemplo, ao utilizar um ficheiro YAML, pode ser implantado numa rede virtual com uma subrede delegada em Instâncias de Contentores Azure. Especifique as seguintes propriedades:

* `ipAddress`: As definições de endereço IP privado saem para o grupo de contentores.
  * `ports`: As portas para abrir, se houver.
  * `protocol`: O protocolo (TCP ou UDP) para a porta aberta.
* `networkProfile`: Definições de rede para a rede virtual e sub-rede.
  * `id`: O ID completo do `networkProfile`recurso do Gestor de Recursos do .

Para obter a identificação do perfil de rede, execute o comando da lista de perfis de [rede AZ,][az-network-profile-list] especificando o nome do grupo de recursos que contém a sua rede virtual e subnet delegado.

``` azurecli
az network profile list --resource-group myResourceGroup \
  --query [0].id --output tsv
```

Resultado do exemplo:

```console
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Assim que tiver o ID do perfil de rede, copie o seguinte YAML num novo ficheiro chamado *vnet-deploy-aci.yaml*. Em `networkProfile`baixo, `id` substitua o valor por ID que acabou de recuperar e, em seguida, guarde o ficheiro. Este YAML cria um grupo de contentores chamado *appcontaineryaml* na sua rede virtual.

```YAML
apiVersion: '2018-10-01'
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
az container create --resource-group myResourceGroup \
  --file vnet-deploy-aci.yaml
```

Uma vez concluída a implantação, execute o comando [do contentor az][az-container-show] para mostrar o seu estado. Resultado do exemplo:

```console
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

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
