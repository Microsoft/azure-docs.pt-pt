---
title: Implementar grupo de contentores para a rede virtual Azure
description: Aprenda a implantar um grupo de contentores para uma rede virtual Azure nova ou existente utilizando a interface de linha de comando Azure.
ms.topic: article
ms.date: 07/02/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: b791d3f37809c2eca53f5a3cd34f7c44dd11ce40
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98028884"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Implementar instâncias de contentores numa rede virtual do Azure

[A Azure Virtual Network](../virtual-network/virtual-networks-overview.md) fornece redes seguras e privadas para os seus recursos Azure e no local. Ao implantar grupos de contentores numa rede virtual Azure, os seus contentores podem comunicar de forma segura com outros recursos na rede virtual.

Este artigo mostra como usar o [contentor az criar][az-container-create] comando no CLI Azure para implantar grupos de contentores para uma nova rede virtual ou uma rede virtual existente. 

Para cenários e limitações de networking, consulte [cenários e recursos de rede virtual para instâncias de contentores Azure](container-instances-virtual-network-concepts.md).

> [!IMPORTANT]
> A implantação do grupo de contentores para uma rede virtual está geralmente disponível para contentores Linux, na maioria das regiões onde existem instâncias para contentores Azure. Para mais informações, consulte [Regiões e disponibilidade de recursos.][container-regions] 

Exemplos neste artigo são formatados para a concha bash. Se preferir outra concha, como PowerShell ou Command Prompt, ajuste os caracteres de continuação da linha em conformidade.


## <a name="deploy-to-new-virtual-network"></a>Implementar para nova rede virtual

Para implementar uma nova rede virtual e fazer com que o Azure crie automaticamente os recursos de rede para si, especifique o seguinte quando executar [o contentor az criar:][az-container-create]

* Nome da rede virtual
* Prefixo de endereço de rede virtual no formato CIDR
* Nome da sub-rede
* Prefixo de endereço de sub-rede no formato CIDR

Os prefixos de endereço de rede virtual e sub-rede especificam os espaços de endereço para a rede virtual e sub-rede, respectivamente. Estes valores estão representados na notação de encaminhamento de Inter-Domain (CIDR) sem classe, por exemplo `10.0.0.0/16` . Para obter mais informações sobre o trabalho com sub-redes, consulte [Adicionar, alterar ou eliminar uma sub-rede de rede virtual.](../virtual-network/virtual-network-manage-subnet.md)

Uma vez implantado o seu primeiro grupo de contentores com este método, pode implantar-se na mesma sub-rede especificando os nomes de rede virtual e sub-redes, ou o perfil de rede que o Azure cria automaticamente para si. Uma vez que o Azure delega a sub-rede para instâncias de contentores Azure, *só* pode colocar grupos de contentores na sub-rede.

### <a name="example"></a>Exemplo

O [seguinte recipiente az cria][az-container-create] comando especifica definições para uma nova rede virtual e sub-rede. Forneça o nome de um grupo de recursos criado numa região onde estão [disponíveis](container-instances-region-availability.md)implantações de grupos de contentores numa rede virtual . Este comando implementa o recipiente público [aci-helloworld][aci-helloworld] da Microsoft que executa um pequeno webserver Node.js que serve uma página web estática. Na secção seguinte, colocará um segundo grupo de contentores na mesma sub-rede e testará a comunicação entre as duas instâncias do contentor.

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

Quando se implementa numa nova rede virtual utilizando este método, a implementação pode demorar alguns minutos enquanto os recursos de rede são criados. Após a colocação inicial, as colocações adicionais do grupo de contentores para a mesma sub-rede completam-se mais rapidamente.

## <a name="deploy-to-existing-virtual-network"></a>Implementar para a rede virtual existente

Para implantar um grupo de contentores numa rede virtual existente:

1. Crie uma sub-rede dentro da sua rede virtual existente, utilize uma sub-rede existente na qual um grupo de contentores já esteja implantado, ou utilize uma sub-rede existente esvaziada de *todos os* outros recursos
1. Colocar um grupo de contentores com [recipiente az criar][az-container-create] e especificar um dos seguintes:
   * Nome de rede virtual e nome da sub-rede
   * ID de recursos de rede virtual e ID de recursos de sub-rede, que permite usar uma rede virtual de um grupo de recursos diferente
   * Nome de perfil de rede ou ID, que pode obter usando [a lista de perfis de rede az][az-network-profile-list]

### <a name="example"></a>Exemplo

O exemplo seguinte coloca um segundo grupo de contentores na mesma sub-rede criada anteriormente e verifica a comunicação entre as duas instâncias de contentores.

Em primeiro lugar, obtenha o endereço IP do primeiro grupo de contentores que implementou, o *appcontainer*:

```azurecli
az container show --resource-group myResourceGroup \
  --name appcontainer \
  --query ipAddress.ip --output tsv
```

A saída apresenta o endereço IP do grupo de contentores na sub-rede privada. Por exemplo:

```console
10.0.0.4
```

Agora, `CONTAINER_GROUP_IP` coloque-se no IP que recuperou com o `az container show` comando, e execute o seguinte `az container create` comando. Este segundo recipiente, *commchecker,* executa uma imagem baseada em Alpine Linux e executa `wget` contra o endereço IP da sub-rede do primeiro grupo de contentores.

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

Depois de concluída esta segunda colocação do contentor, puxe os seus registos para que possa ver a saída do `wget` comando que executou:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Se o segundo recipiente comunicar com sucesso com o primeiro, a saída é semelhante a:

```console
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

A saída de registo deve mostrar que `wget` foi capaz de ligar e descarregar o ficheiro de índice do primeiro recipiente utilizando o seu endereço IP privado na sub-rede local. O tráfego de rede entre os dois grupos de contentores permaneceu dentro da rede virtual.

### <a name="example---yaml"></a>Exemplo - YAML

Também pode implantar um grupo de contentores para uma rede virtual existente utilizando um ficheiro YAML, um [modelo de Gestor de Recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
), ou outro método programático, como o Python SDK. 

Por exemplo, ao utilizar um ficheiro YAML, pode implantar-se numa rede virtual com uma sub-rede delegada em Instâncias de Contentores Azure. Especificar as seguintes propriedades:

* `ipAddress`: As definições privadas do endereço IP para o grupo de contentores.
  * `ports`: As portas a abrir, se houver.
  * `protocol`: O protocolo (TCP ou UDP) para a porta aberta.
* `networkProfile`: Definições de rede para a rede virtual e sub-rede.
  * `id`: O ID completo do gestor de recursos do `networkProfile` .

Para obter o ID do perfil de rede, execute o comando da lista de perfis de [rede az,][az-network-profile-list] especificando o nome do grupo de recursos que contém a sua rede virtual e sub-rede delegada.

``` azurecli
az network profile list --resource-group myResourceGroup \
  --query [0].id --output tsv
```

Resultado do exemplo:

```console
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Assim que tiver o ID do perfil de rede, copie o YAML seguinte para um novo ficheiro chamado *vnet-deploy-aci.yaml*. Em `networkProfile` baixo, substitua o `id` valor por ID que acabou de recuperar e guarde o ficheiro. Este YAML cria um grupo de contentores chamado *appcontaineryaml* na sua rede virtual.

```YAML
apiVersion: '2019-12-01'
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

Desloque o grupo de contentores com o [comando de criação de um recipiente az,][az-container-create] especificando o nome do ficheiro YAML para o `--file` parâmetro:

```azurecli
az container create --resource-group myResourceGroup \
  --file vnet-deploy-aci.yaml
```

Uma vez concluída a colocação, executar o comando [do contentor az][az-container-show] para mostrar o seu estado. Resultado do exemplo:

```console
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Limpar os recursos

### <a name="delete-container-instances"></a>Eliminar instâncias de contentores

Quando terminar de trabalhar com as instâncias do contentor que criou, elimine-as com os seguintes comandos:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Eliminar recursos de rede

Esta funcionalidade requer atualmente vários comandos adicionais para eliminar os recursos de rede que criou anteriormente. Se usou os comandos de exemplo em secções anteriores deste artigo para criar a sua rede virtual e sub-rede, então pode utilizar o seguinte script para eliminar esses recursos de rede. O script assume que o seu grupo de recursos contém uma única rede virtual com um único perfil de rede.

Antes de executar o script, deslote a `RES_GROUP` variável para o nome do grupo de recursos que contém a rede virtual e a sub-rede que devem ser eliminadas. Atualize o nome da rede virtual se não utilizar o `aci-vnet` nome sugerido anteriormente. O guião está formatado para a casca bash. Se preferir outra concha, como PowerShell ou Command Prompt, terá de ajustar a atribuição variável e os acessórios em conformidade.

> [!WARNING]
> Este script elimina recursos! Elimina a rede virtual e todas as sub-redes que contém. Certifique-se de que já não precisa de *nenhum* dos recursos da rede virtual, incluindo quaisquer sub-redes que contenha, antes de executar este script. Uma vez **eliminados, estes recursos são irrecuperáveis.**

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

Para implementar uma nova rede virtual, sub-rede, perfil de rede e grupo de contentores utilizando um modelo de Gestor de Recursos, consulte [Criar um grupo de contentores Azure com VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
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
[container-regions]: container-instances-region-availability.md
