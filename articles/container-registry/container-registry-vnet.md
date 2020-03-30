---
title: Restringir o acesso com uma rede virtual
description: Permitir o acesso a um registo de contentores Azure apenas a partir de recursos numa rede virtual Azure ou a partir de gamas públicas de endereços IP.
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: a6b89b074c25ea0948597ede7e5681b100c7f429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454342"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Restringir o acesso a um registo de contentores Azure utilizando uma rede virtual Azure ou regras de firewall

[A Rede Virtual Azure](../virtual-network/virtual-networks-overview.md) fornece redes seguras e privadas para os seus recursos Azure e no local. Ao limitar o acesso ao seu registo de contentores Azure privado saem de uma rede virtual Azure, garante que apenas os recursos na rede virtual acedem ao registo. Para cenários transversais, também pode configurar regras de firewall para permitir o acesso ao registo apenas a partir de endereços IP específicos.

Este artigo mostra dois cenários para configurar as regras de acesso à rede de entrada num registo de contentores: a partir de uma máquina virtual implantada numa rede virtual, ou a partir de um endereço IP público de um VM.

> [!IMPORTANT]
> Esta funcionalidade encontra-se atualmente em pré-visualização, e [aplicam-se algumas limitações.](#preview-limitations) As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).
>

Se em vez disso precisar de estabelecer regras de acesso para os recursos para chegar a um registo de contentores por detrás de uma firewall, consulte [as regras do Configure para aceder a um registo de contentores Azure atrás de uma firewall](container-registry-firewall-access-rules.md).


## <a name="preview-limitations"></a>Limitações de pré-visualização

* Apenas um registo de **contentores Premium** pode ser configurado com regras de acesso à rede. Para obter informações sobre os níveis de serviço de registo, consulte o Registo de [Contentores Azure SKUs](container-registry-skus.md). 

* Apenas um cluster [de serviço Azure Kubernetes](../aks/intro-kubernetes.md) ou [uma máquina virtual](../virtual-machines/linux/overview.md) Azure podem ser usados como hospedeiro para aceder a um registo de contentores numa rede virtual. *Outros serviços azure, incluindo as instâncias de contentores azure, não são atualmente suportados.*

* As operações da [ACR Tasks](container-registry-tasks-overview.md) não são atualmente suportadas num registo de contentores acedido numa rede virtual.

* Cada registo suporta um máximo de 100 regras de rede virtual.

## <a name="prerequisites"></a>Pré-requisitos

* Para utilizar os passos Do CLI Azure neste artigo, é necessária a versão Azure CLI 2.0.58 ou posterior. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

* Se ainda não tiver um registo de contentores, crie um (SKU Premium `hello-world` necessário) e empurre uma imagem de amostra como o Docker Hub. Por exemplo, utilize o [portal Azure][quickstart-portal] ou o [Azure CLI][quickstart-cli] para criar um registo. 

* Se pretender restringir o acesso ao registo utilizando uma rede virtual numa subscrição azure diferente, tem de registar o fornecedor de recursos para o Registo de Contentores Azure nessa subscrição. Por exemplo:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>Sobre as regras da rede para um registo de contentores

Um registo de contentores Azure por padrão aceita ligações através da internet a partir de anfitriões em qualquer rede. Com uma rede virtual, só pode permitir que recursos Azure, como um cluster AKS ou Azure VM, acedam de forma segura ao registo, sem ultrapassar um limite de rede. Também pode configurar regras de firewall de rede para permitir apenas intervalos específicos de endereços IP da Internet pública. 

Para limitar o acesso a um registo, altere primeiro a ação predefinida do registo de modo a negar todas as ligações de rede. Em seguida, adicione as regras de acesso à rede. Os clientes que tenham acesso através das regras da rede devem continuar a [autenticar o registo de contentores](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) e ser autorizados a aceder aos dados.

### <a name="service-endpoint-for-subnets"></a>Ponto final de serviço para subredes

Para permitir o acesso a partir de uma subnet numa rede virtual, é necessário adicionar um [ponto final](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço para o serviço de Registo de Contentores Azure. 

Os serviços multi-inquilinos, como o Registo de Contentores azure, utilizam um único conjunto de endereços IP para todos os clientes. Um ponto final de serviço atribui um ponto final para aceder a um registo. Este ponto final proporciona ao tráfego uma rota ideal para o recurso sobre a rede de espinha dorsal Azure. As identidades da rede virtual e da subnet também são transmitidas a cada pedido.

### <a name="firewall-rules"></a>Regras da firewall

Para as regras da rede IP, forneça gamas de endereços de internet permitidas utilizando notação CIDR, tais como *16.17.18.0/24* ou endereços IP individuais como *16.17.18.19*. As regras da rede IP só são permitidas para endereços IP da internet *pública.* As gamas de endereços IP reservadas para redes privadas (tal como definidas no RFC 1918) não são permitidas nas regras ip.

## <a name="create-a-docker-enabled-virtual-machine"></a>Criar uma máquina virtual ativada pelo Docker

Para este artigo, utilize um Ubuntu VM ativado pelo Docker para aceder a um registo de contentores Azure. Para utilizar a autenticação do Diretório Ativo Azure no registo, instale também o [Azure CLI][azure-cli] no VM. Se já tem uma máquina virtual Azure, ignore este passo de criação.

Pode utilizar o mesmo grupo de recursos para a sua máquina virtual e o seu registo de contentores. Esta configuração simplifica a limpeza no final, mas não é necessária. Se optar por criar um grupo de recursos separado para a máquina virtual e rede virtual, executar grupo [Az criar][az-group-create]. O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* na localização *central-oeste:*

```azurecli
az group create --name myResourceGroup --location westus
```

Agora implemente uma máquina virtual Ubuntu Azure padrão com [az vm criar][az-vm-create]. O exemplo seguinte cria um VM chamado *myDockerVM:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Demora alguns minutos até que a VM seja criada. Quando o comando estiver concluído, `publicIpAddress` tome nota do mostrado pelo Azure CLI. Utilize este endereço para efazer ligações SSH ao VM e opcionalmente para posterior configuração de regras de firewall.

### <a name="install-docker-on-the-vm"></a>Instale docker no VM

Depois de o VM estar em execução, faça uma ligação SSH ao VM. Substitua *publicamente IpAddress* pelo endereço IP público do seu VM.

```bash
ssh azureuser@publicIpAddress
```

Executar o seguinte comando para instalar Docker no Ubuntu VM:

```bash
sudo apt install docker.io -y
```

Após a instalação, execute o seguinte comando para verificar se o Docker está a funcionar corretamente no VM:

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

Siga os passos em [Instalação Azure CLI com aptidão](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) para instalar o Azure CLI na sua máquina virtual Ubuntu. Para este artigo, certifique-se de que instala a versão 2.0.58 ou posterior.

Saia da ligação SSH.

## <a name="allow-access-from-a-virtual-network"></a>Permitir o acesso a partir de uma rede virtual

Nesta secção, configure o registo do seu contentor para permitir o acesso a partir de uma subnet numa rede virtual Azure. São fornecidos passos equivalentes utilizando o portal Azure CLI e Azure.

### <a name="allow-access-from-a-virtual-network---cli"></a>Permitir o acesso a partir de uma rede virtual - CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Adicione um ponto final de serviço a uma subnet

Quando cria um VM, o Azure por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual baseia-se no nome da máquina virtual. Por exemplo, se nomear a sua máquina virtual *myDockerVM,* o nome de rede virtual padrão é *myDockerVMVNET,* com uma sub-rede chamada *myDockerVMSubnet*. Verifique isto no portal Azure ou utilizando o comando da lista vnet da [rede Az:][az-network-vnet-list]

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

Utilize o comando de atualização de subnet da [rede az][az-network-vnet-subnet-update] para adicionar um ponto final do serviço **Microsoft.ContainerRegistry** à sua subnet. Substitua os nomes da sua rede virtual e sub-rede no seguinte comando:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Utilize o comando de [sub-rede az net vnet][az-network-vnet-subnet-show] para recuperar o ID de recurso da sub-rede. Precisa disto num passo posterior para configurar uma regra de acesso à rede.

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

#### <a name="change-default-network-access-to-registry"></a>Alterar o acesso padrão da rede ao registo

Por predefinição, um registo de contentores Azure permite ligações dos anfitriões em qualquer rede. Para limitar o acesso a uma rede selecionada, altere a ação predefinida para negar o acesso. Substitua o nome do seu registo no seguinte comando de [atualização az acr:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Adicionar regra de rede ao registo

Utilize o comando de adição de uma regra de [rede az acr][az-acr-network-rule-add] para adicionar uma regra de rede ao seu registo que permite o acesso a partir da subnet do VM. Substitua o nome do registo do contentor e a identificação do recurso da subnet no seguinte comando: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Continuar a [verificar o acesso ao registo](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Permitir o acesso a partir de uma rede virtual - portal

#### <a name="add-service-endpoint-to-subnet"></a>Adicione ponto final de serviço à subnet

Quando cria um VM, o Azure por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual baseia-se no nome da máquina virtual. Por exemplo, se nomear a sua máquina virtual *myDockerVM,* o nome de rede virtual padrão é *myDockerVMVNET,* com uma sub-rede chamada *myDockerVMSubnet*.

Para adicionar um ponto final de serviço para o Registo de Contentores Azure a uma subrede:

1. Na caixa de pesquisa no topo do [portal Azure,][azure-portal]introduza *redes virtuais.* Quando as **redes Virtuais** aparecerem nos resultados da pesquisa, selecione-as.
1. A partir da lista de redes virtuais, selecione a rede virtual onde a sua máquina virtual está implantada, como o *myDockerVMVVNET*.
1. Em **Definições,** selecione **Sub-redes**.
1. Selecione a sub-rede onde a sua máquina virtual está implantada, como o *myDockerVMSubnet*.
1. Em **pontos finais de serviço**, selecione **Microsoft.ContainerRegistry**.
1. Selecione **Guardar**.

![Adicione ponto final de serviço à subnet][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Configurar o acesso à rede para registo

Por predefinição, um registo de contentores Azure permite ligações dos anfitriões em qualquer rede. Para limitar o acesso à rede virtual:

1. No portal, navegue para o seu registo de contentores.
1. Em **Definições,** selecione **Firewall e redes virtuais**.
1. Para negar o acesso por padrão, opte por permitir o acesso a partir de **redes Selecionadas**. 
1. Selecione **Adicionar rede virtual existente,** e selecione a rede virtual e a sub-rede configurada com um ponto final de serviço. Selecione **Adicionar**.
1. Selecione **Guardar**.

![Configure rede virtual para registo de contentores][acr-vnet-portal]

Continuar a [verificar o acesso ao registo](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Permitir o acesso a partir de um endereço IP

Nesta secção, configure o registo do seu contentor para permitir o acesso a partir de um endereço ou intervalo IP específico. São fornecidos passos equivalentes utilizando o portal Azure CLI e Azure.

### <a name="allow-access-from-an-ip-address---cli"></a>Permitir o acesso a partir de um endereço IP - CLI

#### <a name="change-default-network-access-to-registry"></a>Alterar o acesso padrão da rede ao registo

Se ainda não o fez, atualize a configuração do registo para negar o acesso por defeito. Substitua o nome do seu registo no seguinte comando de [atualização az acr:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Remover a regra da rede do registo

Se tiver adicionado anteriormente uma regra de rede para permitir o acesso a partir da subnet vM, remova o ponto final do serviço da subnet e a regra da rede. Substitua o nome do registo de contentores e a identificação de recursos da subnet que recuperou num passo anterior no comando de remoção da regra da [rede az acr:][az-acr-network-rule-remove] 

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

#### <a name="add-network-rule-to-registry"></a>Adicionar regra de rede ao registo

Utilize o comando de adição de uma regra de [rede az acr][az-acr-network-rule-add] para adicionar uma regra de rede ao seu registo que permite o acesso a partir do endereço IP do VM. Substitua o nome do registo de contentores e o endereço IP público do VM no seguinte comando.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Continuar a [verificar o acesso ao registo](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Permitir o acesso a partir de um endereço IP - portal

#### <a name="remove-existing-network-rule-from-registry"></a>Remover a regra de rede existente do registo

Se tiver adicionado anteriormente uma regra de rede para permitir o acesso a partir da subnet vM, remova a regra existente. Ignore esta secção se quiser aceder ao registo a partir de um VM diferente.

* Atualize as definições da subnet para remover o ponto final de serviço da subnet para o Registo de Contentores Azure. 

  1. No [portal Azure,][azure-portal]navegue para a rede virtual onde a sua máquina virtual está implantada.
  1. Em **Definições,** selecione **Sub-redes**.
  1. Selecione a sub-rede onde a sua máquina virtual está implantada.
  1. Em **pontos finais de serviço,** remova a caixa de verificação para **Microsoft.ContainerRegistry**. 
  1. Selecione **Guardar**.

* Remova a regra da rede que permite ao sub-rede aceder ao registo.

  1. No portal, navegue para o seu registo de contentores.
  1. Em **Definições,** selecione **Firewall e redes virtuais**.
  1. Em **redes Virtuais,** selecione o nome da rede virtual e, em seguida, selecione **Remover**.
  1. Selecione **Guardar**.

#### <a name="add-network-rule-to-registry"></a>Adicionar regra de rede ao registo

1. No portal, navegue para o seu registo de contentores.
1. Em **Definições,** selecione **Firewall e redes virtuais**.
1. Se ainda não o fez, opte por permitir o acesso a partir de **redes Selecionadas**. 
1. Nas **redes Virtuais,** certifique-se de que nenhuma rede é selecionada.
1. Em **Firewall,** insira o endereço IP público de um VM. Ou, insira uma gama de endereços na notação CIDR que contenha o endereço IP do VM.
1. Selecione **Guardar**.

![Configure a regra da firewall para o registo de contentores][acr-vnet-firewall-portal]

Continuar a [verificar o acesso ao registo](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Verifique o acesso ao registo

Depois de esperar alguns minutos para que a configuração seja atualizada, verifique se o VM pode aceder ao registo do contentor. Faça uma ligação SSH ao seu VM e execute o comando de [login az acr][az-acr-login] para iniciar sessão no seu registo. 

```bash
az acr login --name mycontainerregistry
```

Pode efetuar operações `docker pull` de registo, tais como executar para retirar uma imagem de amostra do registo. Substitua uma imagem e valor de etiqueta adequados ao seu registo, pré-fixado com o nome do servidor de login de registo (todas as minúsculas):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker puxa com sucesso a imagem para o VM.

Este exemplo demonstra que pode aceder ao registo de contentores privados através da regra de acesso à rede. No entanto, o registo não pode ser acedido a partir de um anfitrião de login diferente que não tenha uma regra de acesso à rede configurada. Se tentar iniciar sessão a `az acr login` partir `docker login` de outro hospedeiro utilizando o comando ou comando, a saída é semelhante à seguinte:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Restaurar o acesso ao registo predefinido

Para restaurar o registo para permitir o acesso por defeito, remova quaisquer regras de rede configuradas. Em seguida, detete a ação predefinida para permitir o acesso. São fornecidos passos equivalentes utilizando o portal Azure CLI e Azure.

### <a name="restore-default-registry-access---cli"></a>Restaurar o acesso ao registo por defeito - CLI

#### <a name="remove-network-rules"></a>Remover as regras da rede

Para ver uma lista de regras de rede configuradas para o seu registo, execute o seguinte comando [de lista de regras de rede az acr:][az-acr-network-rule-list]

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Para cada regra configurada, execute o comando de remoção da regra da [rede az acr][az-acr-network-rule-remove] para removê-lo. Por exemplo:

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

Substitua o nome do seu registo no seguinte comando de [atualização az acr:][az-acr-update]
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Restaurar o acesso ao registo predefinido - portal


1. No portal, navegue para o seu registo de contentores e selecione **Firewall e redes virtuais**.
1. Em **redes Virtuais,** selecione cada rede virtual e, em seguida, selecione **Remover**.
1. Em **Firewall,** selecione cada intervalo de endereços e, em seguida, selecione o ícone Eliminar.
1. Em **permitir o acesso a partir de**, selecione Todas as **redes**. 
1. Selecione **Guardar**.

## <a name="clean-up-resources"></a>Limpar recursos

Se criou todos os recursos Do Azure no mesmo grupo de recursos e já não precisa deles, pode eliminar opcionalmente os recursos utilizando um único [grupo az eliminar](/cli/azure/group) o comando:

```azurecli
az group delete --name myResourceGroup
```

Para limpar os seus recursos no portal, navegue para o grupo de recursos myResourceGroup. Assim que o grupo de recursos estiver carregado, clique no **grupo de recursos Delete** para remover o grupo de recursos e os recursos armazenados lá.

## <a name="next-steps"></a>Passos seguintes

Vários recursos e funcionalidades de rede virtuais foram discutidos neste artigo, embora brevemente. A documentação da Rede Virtual Azure aborda estes tópicos extensivamente:

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
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
