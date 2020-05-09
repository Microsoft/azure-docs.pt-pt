---
title: Configurar link privado
description: Criar um ponto final privado num registo de contentores e permitir o acesso a um link privado numa rede virtual local
ms.topic: article
ms.date: 05/07/2020
ms.openlocfilehash: 46ec816d85a528fd3208026ef76dff8470154767
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982455"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Configure Link Privado Azure para um registo de contentores Azure 

Limite o acesso a um registo atribuindo endereços IP privados da rede virtual aos pontos finais do registo utilizando o [Azure Private Link](../private-link/private-link-overview.md). O tráfego de rede entre os clientes da rede virtual e o registo atravessa a rede virtual e uma ligação privada na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública.

Pode [configurar as definições de DNS](../private-link/private-endpoint-overview.md#dns-configuration) para o seu ponto final privado, de modo a que as definições se resolvam no endereço IP privado atribuído ao registo. Com a configuração dNS, os clientes e serviços na rede podem continuar a aceder ao registo com o nome de domínio totalmente qualificado do registo, como *myregistry.azurecr.io*.

Esta funcionalidade está disponível no nível de serviço de registo de contentores **Premium.** Para obter informações sobre os níveis e limites de serviço de registo, consulte os níveis de registo de [contentores de Azure](container-registry-skus.md).

## <a name="things-to-know"></a>Aspetos importantes

* Atualmente, a digitalização de imagens utilizando o Azure Security Center não está disponível num registo configurado com um ponto final privado.

## <a name="prerequisites"></a>Pré-requisitos

* Para utilizar os passos Do CLI Azure neste artigo, recomenda-se a versão 2.2.0 do Azure CLI. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli]. Ou correr em [Azure Cloud Shell.](../cloud-shell/quickstart.md)
* Se ainda não tiver um registo de contentores, crie um (nível `hello-world` Premium necessário) e [importe](container-registry-import-images.md) uma imagem de amostra como o Docker Hub. Por exemplo, utilize o [portal Azure][quickstart-portal] ou o [Azure CLI][quickstart-cli] para criar um registo.
* Para configurar o acesso ao registo utilizando um link privado numa subscrição azure diferente, é necessário registar o fornecedor de recursos para o Registo de Contentores Azure nessa subscrição. Por exemplo:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Os exemplos do Azure CLI neste artigo utilizam as seguintes variáveis ambientais. Substitua os valores adequados ao seu ambiente. Todos os exemplos são formatados para a concha bash:

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>Configurar link privado - CLI

### <a name="get-network-and-subnet-names"></a>Obtenha nomes de rede e sub-rede

Se ainda não os tiver, precisará dos nomes de uma rede virtual e de uma sub-rede para configurar um link privado. Neste exemplo, utiliza a mesma sub-rede para o VM e o ponto final privado do registo. No entanto, em muitos cenários, você configuraria o ponto final numa sub-rede separada. 

Quando cria um VM, o Azure por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual baseia-se no nome da máquina virtual. Por exemplo, se nomear a sua máquina virtual *myDockerVM,* o nome de rede virtual padrão é *myDockerVMVNET,* com uma sub-rede chamada *myDockerVMSubnet*. Desestabeleça estes valores em variáveis ambientais executando o comando da [lista vnet da rede Az:][az-network-vnet-list]

```azurecli
NETWORK_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Name: name}' --output tsv)

SUBNET_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo NETWORK_NAME=$NETWORK_NAME
echo SUBNET_NAME=$SUBNET_NAME
```

### <a name="disable-network-policies-in-subnet"></a>Desativar as políticas de rede na sub-rede

[Desativar as políticas](../private-link/disable-private-endpoint-network-policy.md) de rede, tais como grupos de segurança de rede na subnet para o ponto final privado. Atualize a configuração da sua subnet com [a az rede vnet atualização de sub-rede:][az-network-vnet-subnet-update]

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Configure a zona privada de DNS

Crie uma zona privada de DNS para o domínio privado de registo de contentores Azure. Em etapas posteriores, cria registos DNS para o seu domínio de registo nesta zona DNS.

Para utilizar uma zona privada para anular a resolução padrão de DNS para o seu registo de contentores Azure, a zona deve ser nomeada **privatelink.azurecr.io**. Executar a seguinte zona de dns da [rede AZ criar][az-network-private-dns-zone-create] comando para criar a zona privada:

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Criar uma ligação de associação

Executar a [rede az private-dns link vnet criar][az-network-private-dns-link-vnet-create] para associar a sua zona privada com a rede virtual. Este exemplo cria uma ligação chamada *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Criar um ponto final de registo privado

Nesta secção, crie o ponto final privado do registo na rede virtual. Primeiro, obtenha a identificação de recursos do seu registo:

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

Executar a [rede az private-endpoint criar][az-network-private-endpoint-create] comando para criar o ponto final privado do registo.

O exemplo seguinte cria o ponto final *myPrivateEndpoint* e a ligação de serviço *myConnection*. Para especificar um recurso de registo de `--group-ids registry`contentores para o ponto final, passe:

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>Obtenha endereços IP privados

Executar a [rede az private point show][az-network-private-endpoint-show] para consultar o ponto final para o ID da interface de rede:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Associados à interface de rede neste exemplo estão dois endereços IP privados para o registo de contentores: um para o próprio registo e outro para o ponto final de dados do registo. Os seguintes comandos de exposição de [recursos az][az-resource-show] obtêm os endereços IP privados para o registo de contentores e o ponto final dos dados do registo:

```azurecli
PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

> [!NOTE]
> Se o seu registo for [geo-replicado,](container-registry-geo-replication.md)consulta-se pelo ponto final adicional dos dados para cada réplica do registo.

### <a name="create-dns-records-in-the-private-zone"></a>Criar registos DNS na zona privada

Os seguintes comandos criam registos DNS na zona privada para o ponto final do registo e o seu ponto final de dados. Por exemplo, se tiver um registo chamado *myregistry* na região `myregistry.azurecr.io` da `myregistry.westeurope.data.azurecr.io` *Europa Ocidental,* os nomes finais são e . 

> [!NOTE]
> Se o seu registo for [geo-replicado,](container-registry-geo-replication.md)crie registos de DNS additonais para o IP final de cada réplica.

Primeiro executar a [rede az private-dns estabeleceu uma criação][az-network-private-dns-record-set-a-create] para criar conjuntos de recordes vazios A para o ponto final do registo e ponto final de dados:

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

Executar a [rede az private dns registr-set um][az-network-private-dns-record-set-a-add-record] comando de registo adicionais para criar os registos A para o ponto final do registo e ponto final de dados:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

O link privado está agora configurado e pronto a ser utilizado.

## <a name="set-up-private-link---portal"></a>Configurar link privado - portal

Crie um link privado quando criar um registo, ou adicione um link privado a um registo existente. Os seguintes passos assumem que já tem uma rede virtual e uma subnet configurada com um VM para testes. Também pode [criar uma nova rede virtual e sub-rede.](../virtual-network/quick-create-portal.md)

### <a name="create-a-private-endpoint---new-registry"></a>Criar um ponto final privado - novo registo

1. Ao criar um registo no portal, no separador **Basics,** em **SKU,** selecione **Premium**.
1. Selecione o separador **Networking.**
1. Na **conectividade da rede,** selecione **ponto** > final privado **+ adicionar**.
1. Insira ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Insira o nome de um grupo existente ou crie um novo.|
    | Name | Introduza um nome exclusivo. |
    | Subrecurso |Selecione **registo**|
    | **Redes** | |
    | Rede virtual| Selecione a rede virtual onde a sua máquina virtual está implantada, como o *myDockerVMVNET*. |
    | Subrede | Selecione uma sub-rede, como o *myDockerVMSubnet* onde a sua máquina virtual está implantada. |
    |**Integração Privada de DNS**||
    |Integrar com zona privada de DNS |Selecione **Sim**. |
    |Zona Privada de DNS |Selecione *(Novo) privatelink.azurecr.io* |
    |||
1. Configure as definições restantes do registo e, em seguida, selecione **Rever + Criar**.

  ![Criar registo com ponto final privado](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>Criar um ponto final privado - registo existente

1. No portal, navegue para o seu registo de contentores.
1. Em **Definições,** selecione **Networking**.
1. No separador **pontos finais privados,** **selecione + ponto final privado**.
1. No separador **Basics,** introduza ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Insira o nome de um grupo existente ou crie um novo.|
    | **Detalhes da instância** |  |
    | Name | Insira um nome. |
    |Região|selecione uma região.|
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

Após a criação do ponto final privado, as definições de DNS na zona privada aparecem na página **de pontos finais privados** no portal:

1. No portal, navegue para o registo do seu contentor e selecione **Definições > Networking**.
1. No separador **pontos finais privados,** selecione o ponto final privado que criou.
1. Na página **'Visão Geral',** reveja as definições de link e as definições personalizadas de DNS.

  ![Definições dNS de ponto final](./media/container-registry-private-link/private-endpoint-overview.png)

O seu link privado está agora configurado e pronto a ser utilizado.

## <a name="disable-public-access"></a>Desativar o acesso público

Para muitos cenários, desativar o acesso ao registo das redes públicas. Esta configuração impede que os clientes fora da rede virtual cheguem aos pontos finais do registo. Para desativar o acesso do público através do portal:

1. No portal, navegue para o registo do seu contentor e selecione **Definições > Networking**.
1. No separador de **acesso público,** em **Permitir o acesso do público,** selecione **Disabled**. Em seguida, selecione **Guardar**.

## <a name="validate-private-link-connection"></a>Validar a ligação de ligação privada

Deve validar que os recursos dentro da subnet do ponto final privado se ligam ao seu registo sobre um endereço IP privado, e ter a integração correta da zona DNS privada.

Para validar a ligação de ligação privada, SSH à máquina virtual que configura na rede virtual.

Executar `nslookup` o comando para resolver o endereço IP do seu registo sobre o link privado:

```bash
nslookup $REGISTRY_NAME.azurecr.io
```

A saída de exemplo mostra o endereço IP do registo no espaço de endereço da sub-rede:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Compare este resultado com o `nslookup` endereço IP público na produção para o mesmo registo sobre um ponto final público:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Operações de registo sobre ligação privada

Verifique também se pode efetuar operações de registo a partir da máquina virtual na sub-rede. Faça uma ligação SSH à sua máquina virtual e faça [login az acr][az-acr-login] para iniciar sessão no seu registo. Dependendo da configuração do VM, poderá ter de `sudo`pré-fixar os seguintes comandos com .

```bash
az acr login --name $REGISTRY_NAME
```

Efetuar operações de registo, de modo `docker pull` a retirar uma amostra de imagem do registo. Substitua `hello-world:v1` por uma imagem e etiqueta apropriadas para o seu registo, pré-fixada com o nome do servidor de login de registo (todas as minúsculas):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker puxa com sucesso a imagem para o VM.

## <a name="manage-private-endpoint-connections"></a>Gerir ligações de ponto final privado

Gerencie as ligações de ponto final privado de um registo utilizando o portal Azure, ou utilizando comandos no grupo de comando de [ligação az acr private-endpoint.][az-acr-private-endpoint-connection] As operações incluem aprovar, excluir, listar, rejeitar ou mostrar detalhes das ligações de ponto final privado de um registo.

Por exemplo, para listar as ligações de ponto final privado de um registo, executar o comando da lista de ligação de [pontos privados az acr.][az-acr-private-endpoint-connection-list] Por exemplo:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

Quando cria uma ligação de ponto final privado utilizando as etapas deste artigo, o registo aceita automaticamente ligações de clientes e serviços que tenham permissões RBAC no registo. Pode configurar o ponto final para exigir a aprovação manual das ligações. Para obter informações sobre como aprovar e rejeitar ligações de ponto final privados, consulte [Gerir uma ligação de ponto final privado](../private-link/manage-private-endpoint.md).

## <a name="add-zone-records-for-replicas"></a>Adicione registos de zona para réplicas

Como mostrado neste artigo, quando se adiciona uma ligação de ponto final `privatelink.azurecr.io` privado a um registo, são criados registos de DNS na zona para o registo e os seus pontos finais de dados nas regiões onde o registo é [replicado](container-registry-geo-replication.md). 

Se adicionar mais tarde uma nova réplica, terá de adicionar manualmente um novo registo de zona para o ponto final dos dados naquela região. Por exemplo, se criar uma réplica do *meu registo* na localidade do norte da *Europa,* adicione um recorde de zona para `myregistry.northeurope.data.azurecr.io`. Para passos, consulte [Create DNS registos na zona privada](#create-dns-records-in-the-private-zone) neste artigo.

## <a name="clean-up-resources"></a>Limpar recursos

Se criou todos os recursos Do Azure no mesmo grupo de recursos e já não precisa deles, pode eliminar opcionalmente os recursos utilizando um único [grupo az eliminar](/cli/azure/group) o comando:

```azurecli
az group delete --name $RESOURCE_GROUP
```

Para limpar os seus recursos no portal, navegue para o seu grupo de recursos. Assim que o grupo de recursos estiver carregado, clique no **grupo de recursos Delete** para remover o grupo de recursos e os recursos armazenados lá.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o Private Link, consulte a documentação [do Link Privado Azure.](../private-link/private-link-overview.md)
* Se precisar de configurar regras de acesso ao registo por detrás de uma firewall de cliente, consulte [as regras do Configure para aceder a um registo de contentores Azure atrás](container-registry-firewall-access-rules.md)de uma firewall .

<!-- LINKS - external -->
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
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
