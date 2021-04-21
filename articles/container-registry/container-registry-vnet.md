---
title: Restringir o acesso usando um ponto final de serviço
description: Restringir o acesso a um registo de contentores Azure utilizando um ponto final de serviço numa rede virtual Azure. O acesso ao ponto final de serviço é uma característica do nível de serviço Premium.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 8a67a011c75a192df9ad3460458fd766b5ec1ec1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773475"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>Restringir o acesso a um registo de contentores utilizando um ponto final de serviço numa rede virtual Azure

[A Azure Virtual Network](../virtual-network/virtual-networks-overview.md) fornece redes seguras e privadas para os seus recursos Azure e no local. Um [ponto final de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) permite-lhe proteger o endereço IP público do seu registo de contentores apenas na sua rede virtual. Este ponto final proporciona ao tráfego uma rota ideal para o recurso sobre a rede de espinha dorsal Azure. As identidades da rede virtual e da sub-rede também são transmitidas a cada pedido.

Este artigo mostra como configurar um ponto final do serviço de registo de contentores (pré-visualização) numa rede virtual. 

> [!IMPORTANT]
> O Registo de Contentores Azure suporta agora [o Azure Private Link,](container-registry-private-link.md)permitindo que os pontos finais privados de uma rede virtual sejam colocados num registo. Os pontos finais privados são acessíveis a partir da rede virtual, utilizando endereços IP privados. Recomendamos a utilização de pontos finais privados em vez de pontos finais de serviço na maioria dos cenários de rede.

A configuração de um ponto final do serviço de registo está disponível no nível de serviço de registo de contentores **Premium.** Para obter informações sobre os níveis e limites do serviço de registo de registo, consulte [os níveis de serviço de registo de contentores Azure](container-registry-skus.md).

## <a name="preview-limitations"></a>Limitações de pré-visualização

* O desenvolvimento futuro dos pontos finais de serviço para o Registo de Contentores Azure não está atualmente previsto. Recomendamos a utilização de [pontos finais privados.](container-registry-private-link.md)
* Não é possível utilizar o portal Azure para configurar os pontos finais de serviço num registo.
* Apenas um cluster [de serviço Azure Kubernetes](../aks/intro-kubernetes.md) ou [uma máquina virtual](../virtual-machines/linux/overview.md) Azure podem ser usados como hospedeiro para aceder a um registo de contentores utilizando um ponto final de serviço. *Outros serviços da Azure, incluindo as instâncias do contentor Azure, não são suportados.*
* Os pontos finais de serviço para o Registo de Contentores Azure não são suportados na nuvem do Governo dos EUA ou na nuvem Azure China.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Para utilizar os passos do Azure CLI neste artigo, é necessária a versão 2.0.58 do Azure CLI. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

* Se ainda não tiver um registo de contentores, crie um (nível Premium necessário) e empurre uma imagem de amostra, como `hello-world` a do Docker Hub. Por exemplo, utilize o [portal Azure][quickstart-portal] ou o [CLI Azure][quickstart-cli] para criar um registo. 

* Se pretender restringir o acesso ao registo utilizando um ponto final de serviço numa subscrição diferente do Azure, registe o fornecedor de recursos do Registo de Contentores Azure nessa subscrição. Por exemplo:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>Configure o acesso à rede para registo

Nesta secção, configuure o seu registo de contentores para permitir o acesso a partir de uma sub-rede numa rede virtual Azure. Os passos são fornecidos utilizando o Azure CLI.

### <a name="add-a-service-endpoint-to-a-subnet"></a>Adicione um ponto final de serviço a uma sub-rede

Quando cria um VM, o Azure por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual baseia-se no nome da máquina virtual. Por exemplo, se nomear a sua máquina virtual *myDockerVM,* o nome de rede virtual padrão é *myDockerVMVNET,* com uma sub-rede chamada *myDockerVMSubnet*. Verifique-o utilizando o comando da [lista de vnets da rede Az:][az-network-vnet-list]

```azurecli
az network vnet list \
  --resource-group myResourceGroup \
  --query "[].{Name: name, Subnet: subnets[0].name}"
```

Resultado:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

Utilize o comando [de atualização da sub-rede de rede az][az-network-vnet-subnet-update] para adicionar um ponto final de serviço **Microsoft.ContainerRegistry** à sua sub-rede. Substitua os nomes da sua rede virtual e sub-rede no seguinte comando:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Utilize o comando [de exibição da sub-rede de rede az][az-network-vnet-subnet-show] para recuperar o ID de recurso da sub-rede. Precisa disto mais tarde para configurar uma regra de acesso à rede.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Resultado:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="change-default-network-access-to-registry"></a>Alterar o acesso da rede padrão ao registo

Por predefinição, um registo de contentores Azure permite ligações de anfitriões em qualquer rede. Para limitar o acesso a uma rede selecionada, altere a ação padrão para negar o acesso. Substitua o nome do seu registo no seguinte comando [de atualização az acr:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Adicione a regra da rede ao registo

Utilize o comando de adicionar a [regra de rede az acr][az-acr-network-rule-add] para adicionar uma regra de rede ao seu registo que permite o acesso a partir da sub-rede do VM. Substitua o nome do registo do contentor e o documento de identificação do recurso da sub-rede no seguinte comando: 

 ```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>Verificar o acesso ao registo

Depois de esperar alguns minutos para a configuração atualizar, verifique se o VM pode aceder ao registo do contentor. Faça uma ligação SSH ao seu VM e execute o comando [de login az acr][az-acr-login] para iniciar sessão no seu registo. 

```bash
az acr login --name mycontainerregistry
```

Pode efetuar operações de registo, tais como correr `docker pull` para retirar uma imagem de amostra do registo. Substitua uma imagem e valor de marcação apropriado para o seu registo, pré-fixado com o nome do servidor de login do registo (todos os minúsculos):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker puxa com sucesso a imagem para o VM.

Este exemplo demonstra que pode aceder ao registo de contentores privados através da regra de acesso à rede. No entanto, o registo não pode ser acedido a partir de um anfitrião de login que não tenha uma regra de acesso à rede configurada. Se tentar iniciar sessão a partir de outro anfitrião utilizando o `az acr login` comando ou `docker login` comando, a saída é semelhante à seguinte:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Restaurar o acesso ao registo predefinido

Para restaurar o registo para permitir o acesso por defeito, remova quaisquer regras de rede configuradas. Em seguida, desafine a ação padrão para permitir o acesso. 

### <a name="remove-network-rules"></a>Remover regras de rede

Para ver uma lista de regras de rede configuradas para o seu registo, execute o seguinte comando [da lista de regras de rede az acr:][az-acr-network-rule-list]

```azurecli
az acr network-rule list --name mycontainerregistry 
```

Para cada regra que está configurada, executar o comando [de remoção da regra de rede az acr para removê-lo.][az-acr-network-rule-remove] Por exemplo:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="allow-access"></a>Permitir acesso

Substitua o nome do seu registo no seguinte comando [de atualização az acr:][az-acr-update]
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se criou todos os recursos Azure no mesmo grupo de recursos e já não precisa deles, pode eliminar opcionalmente os recursos utilizando um único comando [de eliminação do grupo AZ:](/cli/azure/group)

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

* Para restringir o acesso a um registo utilizando um ponto final privado numa rede virtual, consulte o [Link Privado Configure Azure para um registo de contentores Azure](container-registry-private-link.md).
* Se precisar de configurar regras de acesso ao registo por detrás de uma firewall do cliente, consulte [as regras de Configuração para aceder a um registo de contentores Azure atrás de uma firewall](container-registry-firewall-access-rules.md).


<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png


<!-- LINKS - External -->
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
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_repository_list
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az_acr_network_rule_add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az_acr_network_rule_remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az_acr_network_rule_list
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-list]: /cli/azure/network/vnet/#az_network_vnet_list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
