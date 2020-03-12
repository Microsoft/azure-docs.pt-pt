---
title: Configurar link privado
description: Criar um ponto final privado num registo de contentores e permitir um link privado numa rede virtual local
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: b7dcf2d1eb1a77ea8b9660318ed2a7d4ec183b42
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128396"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Configure Link Privado Azure para um registo de contentores Azure 

Pode configurar um [ponto final privado](../private-link/private-endpoint-overview.md) para o seu registo de contentores Azure para que os clientes de uma rede virtual Azure acedam de forma segura ao registo por uma [ligação privada](../private-link/private-link-overview.md). O ponto final privado utiliza um endereço IP do espaço de endereço da rede virtual para o seu registo. O tráfego de rede entre os clientes da rede virtual e o registo atravessa a rede virtual e uma ligação privada na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública.

Pode [configurar as definições de DNS](../private-link/private-endpoint-overview.md#dns-configuration) para o seu ponto final privado, de modo a que as definições se resolvam no endereço IP privado atribuído ao registo. Com a configuração dNS, os clientes e serviços na rede podem continuar a aceder ao registo com o nome de domínio totalmente qualificado do registo, como *myregistry.azurecr.io*.

Esta funcionalidade está disponível no nível de serviço de registo de contentores **Premium.** Para obter informações sobre os níveis e limites de serviço de registo, consulte o [Registo de Contentores Azure SKUs](container-registry-skus.md).

> [!IMPORTANT]
> Esta funcionalidade encontra-se atualmente em pré-visualização e aplicam-se [algumas limitações.](#preview-limitations) As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="preview-limitations"></a>Limitações de pré-visualização

* Atualmente, não é possível estabelecer uma ligação privada com um ponto final privado num [registo geo-replicado.](container-registry-geo-replication.md) 

## <a name="prerequisites"></a>Pré-requisitos

* Para utilizar os passos Do CLI Azure neste artigo, recomenda-se a versão 2.2.0 do Azure CLI. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli]. Ou correr em [Azure Cloud Shell.](../cloud-shell/quickstart.md)
* Se ainda não tiver um registo de contentores, crie um (nível Premium necessário) e empurre uma imagem de amostra como `hello-world` do Docker Hub. Por exemplo, utilize o [portal Azure][quickstart-portal] ou o [Azure CLI][quickstart-cli] para criar um registo. 

Os exemplos do Azure CLI neste artigo utilizam as seguintes variáveis ambientais. Substitua os valores adequados ao seu ambiente. Todos os exemplos são formatados para a concha bash:

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>Criar uma máquina virtual ativada pelo Docker

Para efeitos de teste, utilize um Ubuntu VM ativado pelo Docker para aceder a um registo de contentores Azure. Para utilizar a autenticação do Diretório Ativo Azure no registo, instale também o [Azure CLI][azure-cli] no VM. Se já tem uma máquina virtual Azure, ignore este passo de criação.

Pode utilizar o mesmo grupo de recursos para a sua máquina virtual e o seu registo de contentores. Esta configuração simplifica a limpeza no final, mas não é necessária. Se optar por criar um grupo de recursos separado para a máquina virtual e rede virtual, executar grupo [Az criar:][az-group-create]

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

Agora implemente uma máquina virtual Ubuntu Azure padrão com [az vm criar][az-vm-create]. O exemplo seguinte cria um VM chamado *myDockerVM*.

```azurecli
az vm create \
  --resource-group $resourceGroup \
  --name $vmName \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Demora alguns minutos até que a VM seja criada. Quando o comando estiver concluído, tome nota da `publicIpAddress` exibida pelo Azure CLI. Utilize este endereço para efazer ligações SSH ao VM.

### <a name="install-docker-on-the-vm"></a>Instale docker no VM

Depois de o VM estar em execução, faça uma ligação SSH ao VM. Substitua *publicamente IpAddress* pelo endereço IP público do seu VM.

```bash
ssh azureuser@publicIpAddress
```

Executar os seguintes comandos para instalar docker no Ubuntu VM:

```bash
sudo apt-get update
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

Siga os passos em [Instalação Azure CLI com aptidão](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) para instalar o Azure CLI na sua máquina virtual Ubuntu. Por exemplo:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Saia da ligação SSH.

## <a name="set-up-private-link---cli"></a>Configurar link privado - CLI

### <a name="get-network-and-subnet-names"></a>Obtenha nomes de rede e sub-rede

Se ainda não os tiver, precisará dos nomes de uma rede virtual e de uma sub-rede para configurar um link privado. Neste exemplo, utiliza a mesma sub-rede para o VM e o ponto final privado do registo. No entanto, em muitos cenários, você configuraria o ponto final numa sub-rede separada. 

Quando cria um VM, o Azure por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual baseia-se no nome da máquina virtual. Por exemplo, se nomear a sua máquina virtual *myDockerVM,* o nome de rede virtual padrão é *myDockerVMVNET,* com uma sub-rede chamada *myDockerVMSubnet*. Desestabeleça estes valores em variáveis ambientais executando o comando da [lista vnet da rede Az:][az-network-vnet-list]

```azurecli
networkName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Name: name}' --output tsv)

subnetName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo networkName=$networkName
echo subnetName=$subnetName
```

### <a name="disable-network-policies-in-subnet"></a>Desativar as políticas de rede na sub-rede

[Desativar as políticas](../private-link/disable-private-endpoint-network-policy.md) de rede, tais como grupos de segurança de rede na subnet para o ponto final privado. Atualize a configuração da sua subnet com [a az rede vnet atualização de sub-rede:][az-network-vnet-subnet-update]

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Configure a zona privada de DNS

Crie uma zona Privada de DNS para o domínio de registo de contentores Priviate Azure. Em etapas posteriores, cria registos DNS para o seu domínio de registo dentro desta zona DNS.

Para utilizar uma zona privada para anular a resolução padrão de DNS para o seu registo de contentores Azure, a zona deve ser nomeada **privatelink.azurecr.io**. Executar a seguinte zona de dns da [rede AZ criar][az-network-private-dns-zone-create] comando para criar a zona privada:

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Criar uma ligação de associação

Executar a [rede az private-dns link vnet criar][az-network-private-dns-link-vnet-create] para associar a sua zona privada com a rede virtual. Este exemplo cria uma ligação chamada *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Criar um ponto final de registo privado

Nesta secção, crie o ponto final privado do registo na rede virtual. Primeiro, obtenha a identificação de recursos do seu registo:

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

Executar a [rede az private-endpoint criar][az-network-private-endpoint-create] comando para criar o ponto final privado do registo.

O exemplo seguinte cria o ponto final *myPrivateEndpoint* e a ligação de serviço *myConnection*. Para especificar um recurso de registo de contentores para o ponto final, passe `--group-ids registry`:

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $resourceGroup \
    --vnet-name $networkName \
    --subnet $subnetName \
    --private-connection-resource-id $registryID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>Obtenha endereços IP privados

Executar a [rede az private point show][az-network-private-endpoint-show] para consultar o ponto final para o ID da interface de rede:

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Associados à interface de rede estão dois endereços IP privados para o registo de contentores: um para o próprio registo e outro para o ponto final dos dados do registo. Executar os seguintes comandos [de recurso az][az-resource-show] para obter os endereços IP privados para o registo de contentores e o ponto final de dados do registo:

```azurecli
privateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

dataEndpointPrivateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

### <a name="create-dns-records-in-the-private-zone"></a>Criar registos DNS na zona privada

Os seguintes comandos criam registos DNS na zona privada para o ponto final do registo e o seu ponto final de dados. Por exemplo, se tiver um registo chamado *myregistry* na região da *Europa Ocidental,* os nomes finais são `myregistry.azurecr.io` e `myregistry.westeurope.data.azurecr.io`. 

Primeiro executar a [rede az private-dns estabeleceu uma criação][az-network-private-dns-record-set-a-create] para criar conjuntos de recordes vazios A para o ponto final do registo e ponto final de dados:

```azurecli
az network private-dns record-set a create \
  --name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup
```

Executar a [rede az private dns registr-set um][az-network-private-dns-record-set-a-add-record] comando de registo adicionais para criar os registos A para o ponto final do registo e ponto final de dados:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $privateIP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $dataEndpointPrivateIP
```

O link privado está agora configurado e pronto a ser utilizado.

## <a name="set-up-private-link---portal"></a>Configurar link privado - portal

Os seguintes passos assumem que já tem uma rede virtual e uma subnet configurada com um VM para testes. Também pode [criar uma nova rede virtual e sub-rede.](../virtual-network/quick-create-portal.md)

### <a name="create-a-private-endpoint"></a>Criar um ponto final privado

1. No portal, navegue para o seu registo de contentores.
1. Em **Definições,** selecione **ligações de ponto final privados (Pré-visualização)** .
1. Selecione **+ Ponto final privado**.
1. No separador **Basics,** introduza ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Insira o nome de um grupo existente ou crie um novo.|
    | **Detalhes da instância** |  |
    | Nome | Introduza um nome único. |
    |Região|Selecione uma região.|
    |||
5. Selecione **Seguinte: Recurso**.
6. Insira ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    |Método de ligação  | Selecione **Ligar a um recurso Azure no meu diretório**.|
    | Subscrição| Selecione a sua subscrição. |
    | Tipo de recurso | Selecione **Microsoft.ContainerRegistry/registos**. |
    | Recurso |Selecione o nome do seu registo|
    |Subrecurso-alvo |Selecione **registo**|
    |||
7. Selecione **Seguinte: Configuração**.
8. Insira ou selecione as informações:

    | Definição | Valor |
    | ------- | ----- |
    |**Redes**| |
    | Rede virtual| Selecione a rede virtual onde a sua máquina virtual está implantada, como o *myDockerVMVNET*. |
    | Subrede | Selecione uma sub-rede, como o *myDockerVMSubnet* onde a sua máquina virtual está implantada. |
    |**Integração Privada de DNS**||
    |Integrar com zona privada de DNS |Selecione **Sim**. |
    |Zona Privada de DNS |Selecione *(Novo) privatelink.azurecr.io* |
    |||

1. Selecione **Rever + criar**. É levado para o **Review + criar** página onde o Azure valida a sua configuração. 
2. Quando vir a **mensagem de validação passada,** selecione **Criar**.

Após a criação do ponto final privado, as definições de DNS na zona privada aparecem na página de **visão geral** do ponto final.

![Definições dNS de ponto final](./media/container-registry-private-link/private-endpoint-overview.png)

O seu link privado está agora configurado e pronto a ser utilizado.

## <a name="validate-private-link-connection"></a>Validar a ligação de ligação privada

Deve validar que os recursos dentro da subnet do ponto final privado se ligam ao seu registo sobre um endereço IP privado, e ter a integração correta da zona DNS privada.

Para validar a ligação de ligação privada, SSH à máquina virtual que configura na rede virtual.

Execute o comando `nslookup` para resolver o endereço IP do seu registo sobre o link privado:

```bash
nslookup $registryName.azurecr.io
```

A saída de exemplo mostra o endereço IP do registo no espaço de endereço da sub-rede:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Compare este resultado com o endereço IP público na produção `nslookup` para o mesmo registo sobre um ponto final público:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Operações de registo sobre ligação privada

Verifique também se pode efetuar operações de registo a partir da máquina virtual na sub-rede. Faça uma ligação SSH à sua máquina virtual e faça [login az acr][az-acr-login] para iniciar sessão no seu registo. Dependendo da configuração do VM, poderá ter de pré-fixar os seguintes comandos com `sudo`.

```bash
az acr login --name $registryName
```

Efetuar operações de registo como `docker pull` para retirar uma amostra de imagem do registo. Substitua `hello-world:v1` por uma imagem e etiqueta apropriadas para o seu registo, pré-fixada com o nome do servidor de login de registo (todas as minúsculas):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker puxa com sucesso a imagem para o VM.

## <a name="manage-private-endpoint-connections"></a>Gerir ligações de ponto final privado

Gerencie as ligações de ponto final privado de um registo utilizando o portal Azure, ou utilizando comandos no grupo de comando de [ligação az acr private-endpoint.][az-acr-private-endpoint-connection] As operações incluem aprovar, excluir, listar, rejeitar ou mostrar detalhes das ligações de ponto final privado de um registo.

Por exemplo, para listar as ligações de ponto final privado de um registo, executar o comando da lista de ligação de [pontos privados az acr.][az-acr-private-endpoint-connection-list] Por exemplo:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

Quando cria uma ligação de ponto final privado utilizando as etapas deste artigo, o registo aceita automaticamente ligações de clientes e serviços que tenham permissões RBAC no registo. Pode configurar o ponto final para exigir a aprovação manual das ligações. Para obter informações sobre como aprovar e rejeitar ligações de ponto final privados, consulte [Gerir uma ligação de ponto final privado](../private-link/manage-private-endpoint.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se criou todos os recursos Do Azure no mesmo grupo de recursos e já não precisa deles, pode eliminar opcionalmente os recursos utilizando um único [grupo az eliminar](/cli/azure/group) o comando:

```azurecli
az group delete --name $resourceGroup
```

Para limpar os seus recursos no portal, navegue para o seu grupo de recursos. Assim que o grupo de recursos estiver carregado, clique no **grupo de recursos Delete** para remover o grupo de recursos e os recursos armazenados lá.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o Private Link, consulte a documentação [do Link Privado Azure.](../private-link/private-link-overview.md)
* Uma alternativa ao link privado é criar regras de acesso à rede para restringir o acesso ao registo. Para saber mais, consulte Restringir o acesso a um registo de [contentores Azure utilizando uma rede virtual Azure ou regras de firewall](container-registry-vnet.md).

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms
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
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns-zone/create#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns-link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns-record/set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns-record/set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
