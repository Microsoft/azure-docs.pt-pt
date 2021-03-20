---
title: Configurar link privado
description: Crie um ponto final privado num registo de contentores e permita o acesso a uma ligação privada numa rede virtual local. O acesso a ligações privadas é uma característica do nível de serviço Premium.
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 3193c65a2021d29f03bd9ae6cbc00fd6c349d9bf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93342305"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>Conecte-se privadamente a um registo de contentores Azure usando a Azure Private Link


Limitar o acesso a um registo atribuindo endereços IP privados de rede virtual aos pontos finais do registo e utilizando o [Azure Private Link](../private-link/private-link-overview.md). O tráfego de rede entre os clientes da rede virtual e os pontos finais privados do registo atravessa a rede virtual e uma ligação privada na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública. O Private Link também permite o acesso ao registo privado a partir do local através do [Azure ExpressRoute](../expressroute/expressroute-introduction.MD) ou de um [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

Pode [configurar as definições de DNS](../private-link/private-endpoint-overview.md#dns-configuration) para os pontos finais privados do registo, de modo a que as definições resolvam o endereço IP privado atribuído pelo registo. Com a configuração do DNS, os clientes e serviços na rede podem continuar a aceder ao registo no nome de domínio totalmente qualificado do registo, como *myregistry.azurecr.io*. 

Esta funcionalidade está disponível no nível de serviço de registo de contentores **Premium.** Atualmente, um máximo de 10 pontos finais privados podem ser criados para um registo. Para obter informações sobre os níveis e limites do serviço de registo, consulte [os níveis de registo do contentor Azure](container-registry-skus.md).

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Para utilizar os passos Azure CLI neste artigo, recomenda-se a versão 2.6.0 do Azure CLI. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli]. Ou correr em [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Se ainda não tiver um registo de contentores, crie um (nível Premium necessário) e [importe](container-registry-import-images.md) uma amostra de imagem pública, como `mcr.microsoft.com/hello-world` a partir do Registo de Contentores da Microsoft. Por exemplo, utilize o [portal Azure][quickstart-portal] ou o [CLI Azure][quickstart-cli] para criar um registo.
* Para configurar o acesso ao registo utilizando um link privado numa subscrição Azure diferente, é necessário registar o fornecedor de recursos do Registo de Contentores Azure nessa subscrição. Por exemplo:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Os exemplos do Azure CLI neste artigo utilizam as seguintes variáveis ambientais. Valores de substituição adequados ao seu ambiente. Todos os exemplos são formatados para a concha bash:

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>Configurar link privado - CLI

### <a name="get-network-and-subnet-names"></a>Obtenha nomes de rede e sub-redes

Se ainda não os tem, vai precisar dos nomes de uma rede virtual e de uma sub-rede para configurar um link privado. Neste exemplo, utiliza-se a mesma sub-rede para o VM e o ponto final privado do registo. No entanto, em muitos cenários, configuraria o ponto final numa sub-rede separada. 

Quando cria um VM, o Azure por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual baseia-se no nome da máquina virtual. Por exemplo, se nomear a sua máquina virtual *myDockerVM,* o nome de rede virtual padrão é *myDockerVMVNET,* com uma sub-rede chamada *myDockerVMSubnet*. Desaprova estes valores em variáveis ambientais executando o comando da [lista de vnets da rede az:][az-network-vnet-list]

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

[Desativar políticas de rede,](../private-link/disable-private-endpoint-network-policy.md) tais como grupos de segurança de rede na sub-rede para o ponto final privado. Atualize a configuração da sub-rede com [a atualização da sub-rede de rede Az:][az-network-vnet-subnet-update]

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Configure a zona privada do DNS

Crie uma [zona privada de DNS](../dns/private-dns-privatednszone.md) para o domínio privado do registo de contentores Azure. Em etapas posteriores, cria registos DNS para o seu domínio de registo nesta zona DE DNS.

Para utilizar uma zona privada para anular a resolução de DNS predefinida para o seu registo de contentores Azure, a zona deve ser nomeada **privatelink.azurecr.io**. Executar a seguinte [zona privada-dns da rede az criar][az-network-private-dns-zone-create] comando para criar a zona privada:

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Criar uma ligação de associação

Executar [a rede az rede private-dns link vnet criar][az-network-private-dns-link-vnet-create] para associar a sua zona privada com a rede virtual. Este exemplo cria um link chamado *myDNSLink*.

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

Executar o [ponto de terminação privado da rede az criar][az-network-private-endpoint-create] comando para criar o ponto final privado do registo.

O exemplo a seguir cria o ponto final *myPrivateEndpoint* e a ligação de serviço *myConnection*. Para especificar um recurso de registo de contentores para o ponto final, `--group-ids registry` passe:

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

Executar [o show de pontos finais privados da rede Az][az-network-private-endpoint-show] para consultar o ponto final para o ID da interface de rede:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Associados à interface de rede neste exemplo estão dois endereços IP privados para o registo do contentor: um para o registo em si e outro para o ponto final de dados do registo. Os seguintes comandos [de demonstração de recursos az][az-resource-show] obtêm os endereços IP privados para o registo do contentor e o ponto final de dados do registo:

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
> Se o seu registo for [geo-replicado,](container-registry-geo-replication.md)consultar o ponto final de dados adicional para cada réplica de registo.

### <a name="create-dns-records-in-the-private-zone"></a>Criar registos DNS na zona privada

Os seguintes comandos criam registos DNS na zona privada para o ponto final do registo e o seu ponto final de dados. Por exemplo, se tiver um registo chamado *miogressiário* na região de *Westeurope,* os nomes finais são `myregistry.azurecr.io` e `myregistry.westeurope.data.azurecr.io` . 

> [!NOTE]
> Se o seu registo for [geo-replicado,](container-registry-geo-replication.md)crie registos dns additonals para o ponto final de dados de cada réplica IP.

Primeira execução [a az rede private-dns record-set uma criação][az-network-private-dns-record-set-a-create] para criar conjuntos de recordes vazios A para o ponto final de registo e ponto final de dados:

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

Executar o [registo de registos da rede az private-dns][az-network-private-dns-record-set-a-add-record] para criar os registos A para o ponto final do registo e ponto final de dados:

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

Crie um link privado quando criar um registo ou adicionar um link privado a um registo existente. Os seguintes passos pressupõem que já tem uma rede virtual e sub-rede configurada com um VM para testes. Também pode [criar uma nova rede virtual e uma sub-rede.](../virtual-network/quick-create-portal.md)

### <a name="create-a-private-endpoint---new-registry"></a>Criar um ponto final privado - novo registo

1. Ao criar um registo no portal, no **separador Básico,** no **SKU,** selecione **Premium**.
1. Selecione o separador **Rede**.
1. Na **conectividade da rede**, selecione Private **endpoint**  >  **+ Add**.
1. Insira ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Insira o nome de um grupo existente ou crie um novo.|
    | Name | Introduza um nome exclusivo. |
    | Subresource |Selecione **registo**|
    | **Rede** | |
    | Rede virtual| Selecione a rede virtual onde a sua máquina virtual está implantada, como *o myDockerVMVNET*. |
    | Sub-rede | Selecione uma sub-rede, como *a myDockerVMSubnet* onde a sua máquina virtual está implantada. |
    |**Integração privada de DNS**||
    |Integrar com zona DNS privada |Selecione **Yes** (Sim). |
    |Zona DNS Privada |Selecione *(Nova) privatelink.azurecr.io* |
    |||
1. Configure as definições restantes do registo e, em seguida, selecione **Review + Create**.

  ![Criar registo com ponto final privado](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>Criar um ponto final privado - registo existente

1. No portal, navegue para o registo do seu contentor.
1. Em **Definições**, selecione **Networking**.
1. No **separador pontos finais privados,** selecione **+ ponto final privado**.
1. No separador **Básicos, insira** ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Insira o nome de um grupo existente ou crie um novo.|
    | **Detalhes da instância** |  |
    | Name | Insira um nome. |
    |Region|selecione uma região.|
    |||
5. Selecione **Seguinte: Recurso**.
6. Insira ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    |Método de ligação  | Selecione **Ligar a um recurso Azure no meu diretório**.|
    | Subscrição| Selecione a sua subscrição. |
    | Tipo de recurso | Selecione **Microsoft.ContainerRegistry/registries**. |
    | Recurso |Selecione o nome do seu registo|
    |Subresource-alvo |Selecione **registo**|
    |||
7. Selecione **Seguinte: Configuração**.
8. Insira ou selecione as informações:

    | Definição | Valor |
    | ------- | ----- |
    |**Rede**| |
    | Rede virtual| Selecione a rede virtual onde a sua máquina virtual está implantada, como *o myDockerVMVNET*. |
    | Sub-rede | Selecione uma sub-rede, como *a myDockerVMSubnet* onde a sua máquina virtual está implantada. |
    |**Integração privada de DNS**||
    |Integrar com zona DNS privada |Selecione **Yes** (Sim). |
    |Zona DNS Privada |Selecione *(Nova) privatelink.azurecr.io* |
    |||

1. Selecione **Rever + criar**. Acedeu à página **Rever + criar**, onde o Azure valida a sua configuração. 
2. Quando vir a mensagem **A validação passou**, selecione **Criar**.

Após a criação do ponto final privado, as definições de DNS na zona privada aparecem na página **de pontos finais privados** no portal:

1. No portal, navegue para o registo do seu contentor e selecione **Definições > Networking**.
1. No **separador pontos finais privados,** selecione o ponto final privado que criou.
1. Na página **'Vista Geral',** reveja as definições de link e as definições personalizadas de DNS.

  ![Definições de DNS de ponto final](./media/container-registry-private-link/private-endpoint-overview.png)

O seu link privado está agora configurado e pronto a ser utilizado.

## <a name="disable-public-access"></a>Desativar o acesso público

Para muitos cenários, desativar o acesso ao registo das redes públicas. Esta configuração impede que os clientes fora da rede virtual cheguem aos pontos finais do registo. 

### <a name="disable-public-access---cli"></a>Desativar o acesso público - CLI

Para desativar o acesso público utilizando o Azure CLI, execute [a atualização az acr][az-acr-update] e desative `--public-network-enabled` para `false` . 

> [!NOTE]
> O `public-network-enabled` argumento requer Azure CLI 2.6.0 ou mais tarde. 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```


### <a name="disable-public-access---portal"></a>Desativar o acesso público - portal

1. No portal, navegue para o registo do seu contentor e selecione **Definições > Networking**.
1. No separador **de acesso público,** em **Permitir o acesso à rede pública,** selecione **Disabled**. Em seguida, selecione **Guardar**.

## <a name="validate-private-link-connection"></a>Validar a ligação de ligação privada

Deve validar que os recursos dentro da sub-rede do ponto final privado se conectam ao seu registo sobre um endereço IP privado, e tem a integração de zona de DNS privada correta.

Para validar a ligação de ligação privada, o SSH à máquina virtual que instalou na rede virtual.

Executar uma utilidade como `nslookup` ou para procurar o endereço IP do seu registo sobre o link `dig` privado. Por exemplo:

```bash
dig $REGISTRY_NAME.azurecr.io
```

A saída de exemplo mostra o endereço IP do registo no espaço de endereço da sub-rede:

```console
[...]
; <<>> DiG 9.11.3-1ubuntu1.13-Ubuntu <<>> myregistry.azurecr.io
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52155
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;myregistry.azurecr.io.         IN      A

;; ANSWER SECTION:
myregistry.azurecr.io.  1783    IN      CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 10 IN A      10.0.0.7

[...]
```

Compare este resultado com o endereço IP público na `dig` produção para o mesmo registo em um ponto final público:

```console
[...]
;; ANSWER SECTION:
myregistry.azurecr.io.  2881    IN  CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 2881 IN CNAME xxxx.xx.azcr.io.
xxxx.xx.azcr.io.    300 IN  CNAME   xxxx-xxx-reg.trafficmanager.net.
xxxx-xxx-reg.trafficmanager.net. 300 IN CNAME   xxxx.westeurope.cloudapp.azure.com.
xxxx.westeurope.cloudapp.azure.com. 10  IN A 20.45.122.144

[...]
```

### <a name="registry-operations-over-private-link"></a>Operações de registo sobre ligação privada

Verifique também se pode efetuar operações de registo a partir da máquina virtual na sub-rede. Faça uma ligação SSH à sua máquina virtual e faça [login az acr][az-acr-login] para iniciar sessão no seu registo. Dependendo da configuração VM, poderá ter de pré-afixar os seguintes comandos com `sudo` .

```bash
az acr login --name $REGISTRY_NAME
```

Efetuar operações de registo, de modo `docker pull` a retirar uma imagem de amostra do registo. Substitua `hello-world:v1` por uma imagem e etiqueta apropriada para o seu registo, pré-fixado com o nome do servidor de login de registo (todos os minúsculos):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker puxa com sucesso a imagem para o VM.

## <a name="manage-private-endpoint-connections"></a>Gerir ligações privadas de ponto final

Gerir as ligações de ponto final privado de um registo utilizando o portal Azure, ou utilizando comandos no grupo de comando de [ligação az acr de ligação ao ponto privado.][az-acr-private-endpoint-connection] As operações incluem aprovar, eliminar, listar, rejeitar ou mostrar detalhes das ligações privadas de um registo.

Por exemplo, para listar as ligações de ponto final privado de um registo, executar o comando [da lista de ligações az acr de extremidade privada.][az-acr-private-endpoint-connection-list] Por exemplo:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

Quando configura uma ligação de ponto final privado utilizando os passos deste artigo, o registo aceita automaticamente ligações de clientes e serviços que tenham permissões Azure RBAC no registo. Pode configurar o ponto final para exigir a aprovação manual das ligações. Para obter informações sobre como aprovar e rejeitar ligações privadas de ponto final, consulte [Gerir uma Ligação de Pontos de Terminação Privadas](../private-link/manage-private-endpoint.md).

## <a name="add-zone-records-for-replicas"></a>Adicionar registos de zona para réplicas

Como mostrado neste artigo, quando se adiciona uma ligação de ponto final privado a um registo, cria-se registos DNS na `privatelink.azurecr.io` zona para o registo e os seus pontos finais de dados nas regiões onde o registo é [replicado.](container-registry-geo-replication.md) 

Se mais tarde adicionar uma nova réplica, tem de adicionar manualmente um novo registo de zona para o ponto final de dados nessa região. Por exemplo, se criar uma réplica de *miogitrismo* na localização *da northeurope,* adicione um registo de zona para `myregistry.northeurope.data.azurecr.io` . Para etapas, consulte [os registos do DNS na zona privada](#create-dns-records-in-the-private-zone) neste artigo.

## <a name="dns-configuration-options"></a>Opções de configuração de DNS

O ponto final privado neste exemplo integra-se com uma zona privada de DNS associada a uma rede virtual básica. Esta configuração utiliza o serviço DNS fornecido pelo Azure diretamente para resolver o FQDN público do registo no seu endereço IP privado na rede virtual. 

O link privado suporta cenários adicionais de configuração de DNS que utilizam a zona privada, incluindo com soluções DE DNS personalizadas. Por exemplo, pode ter uma solução DE DNS personalizada implantada na rede virtual, ou no local de uma rede que liga à rede virtual utilizando um gateway VPN ou Azure ExpressRoute. 

Para resolver o FQDN público do registo para o endereço IP privado nestes cenários, é necessário configurar um reencaminhador de nível de servidor para o serviço Azure DNS (168.63.129.16). As opções e passos de configuração exata dependem das redes existentes e do DNS. Por exemplo, consulte [a configuração do DNS do Ponto Final Privado Azure](../private-link/private-endpoint-dns.md).

> [!IMPORTANT]
> Se para uma elevada disponibilidade criou pontos finais privados em várias regiões, recomendamos que utilize um grupo de recursos separado em cada região e coloque a rede virtual e a zona privada de DNS associada. Esta configuração também impede uma resolução de DNS imprevisível causada pela partilha da mesma zona privada de DNS.

## <a name="clean-up-resources"></a>Limpar os recursos

Se criou todos os recursos Azure no mesmo grupo de recursos e já não precisa deles, pode eliminar opcionalmente os recursos utilizando um único comando [de eliminação do grupo AZ:](/cli/azure/group)

```azurecli
az group delete --name $RESOURCE_GROUP
```

Para limpar os seus recursos no portal, navegue para o seu grupo de recursos. Assim que o grupo de recursos estiver carregado, clique no **grupo de recursos Delete** para remover o grupo de recursos e os recursos aí armazenados.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o Private Link, consulte a documentação do [Azure Private Link.](../private-link/private-link-overview.md)
* Se precisar de configurar regras de acesso ao registo por detrás de uma firewall do cliente, consulte [as regras de Configuração para aceder a um registo de contentores Azure atrás de uma firewall](container-registry-firewall-access-rules.md).

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
