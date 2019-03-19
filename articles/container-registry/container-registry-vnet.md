---
title: Implementar um registo de contentor do Azure numa rede virtual
description: Permita o acesso a um Azure container registry apenas a partir de recursos numa rede virtual do Azure ou a partir de intervalos de endereços IP públicos.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/14/2019
ms.author: danlep
ms.openlocfilehash: 044ff823e34f51257cb6186417535b9b542bc289
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136991"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Restringir o acesso a um Azure container registry através de uma rede virtual do Azure ou as regras de firewall

[Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md) fornece um sistema de rede seguro e privado para o Azure e de recursos no local. Ao implementar o seu registo privado de contentor do Azure numa rede virtual do Azure, pode certificar-se de que apenas os recursos na rede virtual aceder ao registo. Para cenários em vários locais, também pode configurar regras de firewall para permitir o registo de acesso apenas a partir de endereços IP específicos.

Este artigo mostra dois cenários para criar as regras de acesso de rede para limitar o acesso a um Azure container registry: a partir de uma máquina virtual implementada na mesma rede ou de endereço IP público de uma VM.

> [!IMPORTANT]
> Esta funcionalidade está atualmente em pré-visualização e algumas [limitações aplicam-se](#preview-limitations). As pré-visualizações ser-lhe-ão disponibilizadas na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).
>

## <a name="preview-limitations"></a>Limitações de pré-visualização

* Apenas um **Premium** registo de contentor pode ser configurado com regras de acesso de rede. Para obter informações sobre os escalões de serviço de registo, consulte [SKUs de registo de contentor do Azure](container-registry-skus.md). 

* Apenas um [do Azure Kubernetes Service](../aks/intro-kubernetes.md) cluster ou do Azure [máquina virtual](../virtual-machines/linux/overview.md) pode ser utilizado como um anfitrião para aceder a um registo de contentor numa rede virtual. *Outros serviços do Azure, incluindo o Azure Container Instances não são atualmente suportados.*

* Cada registro suporta um máximo de 100 regras de rede virtual.

## <a name="prerequisites"></a>Pré-requisitos

* Para utilizar o Azure CLI os passos neste artigo, a CLI do Azure versão 2.0.58 ou posterior é necessária. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

* Se ainda não tiver um registo de contentor, criar uma (é necessário um SKU Premium) e enviar uma imagem de exemplo como `hello-world` do Hub do Docker. Por exemplo, utilizar o [portal do Azure] [ quickstart-portal] ou o [da CLI do Azure] [ quickstart-cli] para criar um registo. 

## <a name="about-network-rules-for-a-container-registry"></a>Sobre regras de rede para um registo de contentor

Um registo de contentor do Azure por predefinição aceita ligações através da internet a partir de anfitriões em qualquer rede. Com uma rede virtual, pode permitir que apenas os recursos do Azure como um cluster do AKS ou VM do Azure para aceder de forma segura o registro, sem cruzamento entre um limite de rede. Também pode configurar as regras de firewall de rede para a lista de permissões específicas internet pública intervalos de endereços IP. 

Para limitar o acesso a um registo, primeiro altere a ação padrão do Registro para que ele nega todas as ligações de rede. Em seguida, adicione regras de acesso de rede. Os clientes concedido acesso através de regras de rede têm de continuar a [autenticar para o registo de contentor](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) e de estar autorizado a aceder aos dados.

### <a name="service-endpoint-for-subnets"></a>Ponto final de serviço para sub-redes

Para permitir o acesso a partir de uma sub-rede numa rede virtual, tem de adicionar uma [ponto final de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) para o serviço do Azure Container Registry. 

Serviços de multi-inquilinos, como o Azure Container Registry, utilizem um único conjunto de endereços IP para todos os clientes. Um ponto de extremidade de serviço atribui um ponto final para aceder a um registo. Este ponto final fornece tráfego de uma rota ideal para o recurso através da rede de backbone do Azure. As identidades de rede virtual e sub-rede também são transmitidas com cada solicitação.

### <a name="firewall-rules"></a>Regras da firewall

Para regras de rede IP, fornecer internet permitidos intervalos de endereços, por exemplo, utilizando a notação CIDR *16.17.18.0/24* ou endereços de um IP individual, como *16.17.18.19*. Regras de rede IP só são permitidas para *público* endereços de IP de internet. Intervalos de endereços IP reservados para redes privadas (conforme definido em RFC 1918) não são permitidos em regras de IP.

## <a name="create-a-docker-enabled-virtual-machine"></a>Criar uma máquina de virtual habilitados para Docker

Neste artigo, utilize uma VM do Ubuntu habilitados no Docker para aceder um Azure container registry. Para utilizar a autenticação do Azure Active Directory para o registo, também instalar o [CLI do Azure] [ azure-cli] na VM. Se já tiver uma máquina virtual do Azure, ignore este passo de criação.

Pode usar o mesmo grupo de recursos para sua máquina virtual e o seu registo de contentor. Esta configuração simplifica a limpeza no final, mas não é necessária. Se optar por criar um grupo de recursos separado para a máquina virtual e a rede virtual, execute [criar grupo az][az-group-create]. O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* no *westcentralus* localização:

```azurecli
az group create --name myResourceGroup --location westus
```

Agora, implementam um padrão máquina virtual do Ubuntu Azure com [az vm criar][az-vm-create]. O exemplo seguinte cria uma VM com o nome *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Demora alguns minutos até que a VM seja criada. Quando o comando for concluído, anote o `publicIpAddress` apresentado pela CLI do Azure. Utilize este endereço para facilitar a ligações SSH à VM e, opcionalmente, para a instalação posterior de regras de firewall.

### <a name="install-docker-on-the-vm"></a>Instalar o Docker na VM

Depois da VM está em execução, estabeleça uma ligação SSH à VM. Substitua *publicIpAddress* com o endereço IP público da sua VM.

```bash
ssh azureuser@publicIpAddress
```

Execute o seguinte comando para instalar o Docker na VM do Ubuntu:

```bash
sudo apt install docker.io -y
```

Após a instalação, execute o seguinte comando para verificar que o Docker está a funcionar corretamente na VM:

```bash
sudo docker run -it hello-world
```

Saída:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Instalar a CLI do Azure

Siga os passos em [instalar a CLI do Azure com apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) para instalar a CLI do Azure na sua máquina virtual do Ubuntu. Neste artigo, certifique-se que instale a versão 2.0.58 ou posterior.

Sair da ligação SSH.

## <a name="allow-access-from-a-virtual-network"></a>Permitir o acesso de uma rede virtual

Nesta secção, configure o seu registo de contentor para permitir o acesso a partir de uma sub-rede numa rede virtual do Azure. Passos equivalentes, com a CLI do Azure e o portal do Azure são fornecidos.

### <a name="allow-access-from-a-virtual-network---cli"></a>Permitir o acesso de uma rede virtual - CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Adicionar um ponto de extremidade de serviço a uma sub-rede

Quando cria uma VM, o Azure, por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual é baseado no nome da máquina virtual. Por exemplo, se nomear sua máquina virtual *myDockerVM*, o nome de rede virtual predefinido é *myDockerVMVNET*, com uma sub-rede denominada *myDockerVMSubnet*. Verificar isto no portal do Azure ou utilizando o [lista de vnet de rede de az] [ az-network-vnet-list] comando:

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
```

Saída:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

Utilize o [atualização de sub-rede de vnet de rede de az] [ az-network-vnet-subnet-update] comando para adicionar um **Microsoft. containerregistry** ponto final de serviço à sua sub-rede. Substitua os nomes da sua rede virtual e a sub-rede no comando seguinte:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Utilize o [show de sub-rede de vnet de rede de az] [ az-network-vnet-subnet-show] comando para obter o ID de recurso da sub-rede. Precisará num passo posterior para configurar uma regra de acesso de rede.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Saída:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>Alterar o acesso de rede predefinido para o registo

Por predefinição, um Azure container registry permite ligações a partir de anfitriões em qualquer rede. Para limitar o acesso a uma rede selecionada, altere a ação predefinida para negar o acesso. Substitua o nome do seu registo a seguir [atualização do az acr] [ az-acr-update] comando:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Adicionar regra de rede para o registo

Utilize o [Adicionar regra de rede do acr az] [ az-acr-network-rule-add] comando para adicionar uma regra de rede para o seu registo, que permite o acesso de sub-rede da VM. Substitua o nome do registo de contentor e o ID de recurso de sub-rede no comando seguinte: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Continuar a [verificar o acesso ao registo](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Permitir o acesso de uma rede virtual - portal

#### <a name="add-service-endpoint-to-subnet"></a>Adicionar ponto final de serviço à sub-rede

Quando cria uma VM, o Azure, por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual é baseado no nome da máquina virtual. Por exemplo, se nomear sua máquina virtual *myDockerVM*, o nome de rede virtual predefinido é *myDockerVMVNET*, com uma sub-rede denominada *myDockerVMSubnet*.

Para adicionar um ponto de extremidade de serviço do Azure Container Registry a uma sub-rede:

1. Na caixa de pesquisa na parte superior a [portal do Azure][azure-portal], introduza *redes virtuais*. Quando **redes virtuais** aparecer nos resultados da pesquisa, selecione-o.
1. Na lista de redes virtuais, selecione a rede virtual em que sua máquina virtual é implementada, tal como *myDockerVMVNET*.
1. Sob **configurações**, selecione **sub-redes**.
1. Selecione a sub-rede onde está implementada a sua máquina virtual, tal como *myDockerVMSubnet*.
1. Sob **pontos finais de serviço**, selecione **Microsoft. containerregistry**.
1. Selecione **Guardar**.

![Adicionar ponto final de serviço à sub-rede][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Configurar o acesso de rede para o registo

Por predefinição, um Azure container registry permite ligações a partir de anfitriões em qualquer rede. Para limitar o acesso à rede virtual:

1. No portal, navegue para o seu registo de contentor.
1. Sob **configurações**, selecione **firewalls e redes virtuais**.
1. Para negar o acesso por predefinição, optar por permitir acesso a partir **redes selecionadas**. 
1. Selecione **adicionar rede virtual existente**e selecione a rede virtual e sub-rede configurada com um ponto de extremidade de serviço. Selecione **Adicionar**.
1. Selecione **Guardar**.

![Configurar a rede virtual para o registo de contentor][acr-vnet-portal]

Continuar a [verificar o acesso ao registo](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Permitir o acesso de um endereço IP

Nesta secção, configure o seu registo de contentor para permitir o acesso a partir de uma sub-rede numa rede virtual do Azure. Passos equivalentes, com a CLI do Azure e o portal do Azure são fornecidos.

### <a name="allow-access-from-an-ip-address---cli"></a>Permitir o acesso de um endereço IP - CLI

#### <a name="change-default-network-access-to-registry"></a>Alterar o acesso de rede predefinido para o registo

Se ainda não o fez, atualize a configuração de registo para negar o acesso por predefinição. Substitua o nome do seu registo a seguir [atualização do az acr] [ az-acr-update] comando:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Remover regra de rede a partir do registo

Se tiver adicionado anteriormente uma regra de rede para permitir o acesso a partir sub-rede da VM, remova o ponto final de serviço da sub-rede e a regra de rede. Substitua o nome do registo de contentor e o ID de recurso da sub-rede que obteve no passo anterior na [remover a regra de rede do acr az] [ az-acr-network-rule-remove] comando: 

```azurecli
# Remove service endpoint

az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints ""

# Remove network rule

az acr network-rule remove --name mycontainerregistry --subnet <subnet-resource-id>
```

#### <a name="add-network-rule-to-registry"></a>Adicionar regra de rede para o registo

Utilize o [Adicionar regra de rede do acr az] [ az-acr-network-rule-add] comando para adicionar uma regra de rede para o seu registo, que permite o acesso de endereço IP da VM. Substitua o nome do registo de contentor e o endereço IP público da VM no comando seguinte.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Continuar a [verificar o acesso ao registo](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Permitir o acesso de um endereço IP - portal

#### <a name="remove-existing-network-rule-from-registry"></a>Remover a regra de rede existente de registo

Se tiver adicionado anteriormente uma regra de rede para permitir o acesso a partir sub-rede da VM, remova a regra existente. Ignore esta secção se pretender aceder ao registo de uma VM diferente.

* Atualize as definições de sub-rede para remover o ponto final de serviço da sub-rede do Azure Container Registry. 

  1. Na [portal do Azure][azure-portal], navegue para a rede virtual em que sua máquina virtual é implementada.
  1. Sob **configurações**, selecione **sub-redes**.
  1. Selecione a sub-rede onde a máquina virtual está implementada.
  1. Sob **pontos finais de serviço**, remova a caixa de verificação **Microsoft. containerregistry**. 
  1. Selecione **Guardar**.

* Remova a regra de rede que permite que a sub-rede para aceder ao registo.

  1. No portal, navegue para o seu registo de contentor.
  1. Sob **configurações**, selecione **firewalls e redes virtuais**.
  1. Sob **redes virtuais**, selecione o nome da rede virtual e, em seguida, selecione **remover**.
  1. Selecione **Guardar**.

#### <a name="add-network-rule-to-registry"></a>Adicionar regra de rede para o registo

1. No portal, navegue para o seu registo de contentor.
1. Sob **configurações**, selecione **firewalls e redes virtuais**.
1. Se ainda não o fez, optar por permitir acesso a partir **redes selecionadas**. 
1. Sob **redes virtuais**, certifique-se de que está selecionada nenhuma rede.
1. Sob **Firewall**, introduza o endereço IP público de uma VM. Em alternativa, introduza um intervalo de endereços em notação CIDR a que contém o endereço IP da VM.
1. Selecione **Guardar**.

![Configurar regra de firewall para o registo de contentor][acr-vnet-firewall-portal]

Continuar a [verificar o acesso ao registo](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Verificar o acesso ao registo

Depois de aguardar alguns minutos para que a configuração atualizar, certifique-se de que a VM pode aceder ao registo de contentor. Efetue uma ligação SSH à VM e execute o [início de sessão az acr] [ az-acr-login] comando para iniciar sessão para o seu registo. 

```bash
az acr login --name mycontainerregistry
```

Pode efetuar operações de registo como executar `docker pull` para solicitar uma imagem de exemplo do registo. Substitua um valor de etiqueta de imagem ou adequado para o seu registo, o prefixo do nome de servidor de início de sessão de registo (em minúsculas):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker recebe com êxito na imagem para a VM.

Este exemplo demonstra que pode aceder o registo de contentor privadas através da regra de acesso de rede. No entanto, o registo não pode ser acedido a partir de um anfitrião de início de sessão diferente que não tem uma regra de acesso de rede configurada. Se tentar iniciar sessão a partir de outro anfitrião utilizando o `az acr login` comando ou `docker login` de comando, o resultado é semelhante ao seguinte:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Restaurar o acesso de registo predefinido

Para restaurar o registo para permitir o acesso por padrão, remova quaisquer regras de rede que estão configuradas. Em seguida, defina a ação predefinida para permitir o acesso. Passos equivalentes, com a CLI do Azure e o portal do Azure são fornecidos.

### <a name="restore-default-registry-access---cli"></a>Restaurar o acesso de registo predefinido - CLI

#### <a name="remove-network-rules"></a>Remover regras de rede

Para ver uma lista de regras de rede configuradas para o seu registo, execute o seguinte procedimento [lista de regra de rede do az acr] [ az-acr-network-rule-list] comando:

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Para cada regra que está configurada, execute o [remover a regra de rede do acr az] [ az-acr-network-rule-remove] comando para removê-lo. Por exemplo:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet

# Remove a rule that allows access for an IP address or CIDR range such as 23.45.1.0/24.

az acr network-rule remove \
  --name mycontainerregistry \
  --ip-address 23.45.1.0/24
```

#### <a name="allow-access"></a>Permitir acesso

Substitua o nome do seu registo a seguir [atualização do az acr] [ az-acr-update] comando:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Restaurar predefinição acesso ao registo - portal


1. No portal, navegue para o seu registo de contentor e selecione **firewalls e redes virtuais**.
1. Sob **redes virtuais**, selecione cada rede virtual e, em seguida, selecione **remover**.
1. Sob **Firewall**, selecione cada intervalo de endereços e, em seguida, selecione o ícone Eliminar.
1. Sob **autorizar o acesso**, selecione **todas as redes**. 
1. Selecione **Guardar**.

## <a name="clean-up-resources"></a>Limpar recursos

Se criou todos os recursos do Azure no mesmo recurso de grupo e não precisam mais deles, pode, opcionalmente, elimine os recursos utilizando uma única [eliminação do grupo de az](/cli/azure/group#az_group_delete) comando:

```azurecli
az group delete --name myResourceGroup
```

Para limpar os seus recursos no portal, navegue para o grupo de recursos myResourceGroup. Quando o grupo de recursos estiver carregado, clique em **eliminar grupo de recursos** para remover o grupo de recursos e os recursos armazenados ali.

## <a name="next-steps"></a>Passos Seguintes

Vários recursos de rede virtual e funcionalidades foram abordadas neste artigo, embora brevemente. A documentação da rede Virtual do Azure abrange extensivamente estes tópicos:

* [Rede virtual](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [Sub-rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [Pontos finais de serviço](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png
[acr-vnet-portal]: ./media/container-registry-vnet/acr-vnet-portal.png
[acr-vnet-firewall-portal]: ./media/container-registry-vnet/acr-vnet-firewall-portal.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group#az_group_create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com