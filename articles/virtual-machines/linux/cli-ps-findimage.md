---
title: Selecione imagens Linux VM com o CLI Azure
description: Saiba como usar o Azure CLI para determinar a editora, oferta, SKU e versão para imagens Marketplace VM.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 01/25/2019
ms.author: cynthn
ms.collection: linux
ms.openlocfilehash: efa0b91c9c0e43104f36017c3b1a1f3167190d63
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562814"
---
# <a name="find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Localizar imagens de VM do Linux no Azure Marketplace com o CLI do Azure

Este tópico descreve como usar o CLI Azure para encontrar imagens VM no Mercado Azure. Utilize estas informações para especificar uma imagem do Marketplace quando criar um VM programáticamente com os modelos CLI, Gestor de Recursos ou outras ferramentas.

Navegue também nas imagens disponíveis e nas ofertas utilizando a montra do [Azure Marketplace,](https://azuremarketplace.microsoft.com/) o [portal Azure](https://portal.azure.com), ou  [Azure PowerShell](../windows/cli-ps-findimage.md). 

Certifique-se de que está a iniciar sessão numa conta Azure `az login` ().

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="deploy-from-a-vhd-using-purchase-plan-parameters"></a>Implementar a partir de um VHD usando parâmetros do plano de compra

Se tiver um VHD existente que foi criado usando uma imagem paga do Azure Marketplace, poderá ter de fornecer informações sobre o plano de compra quando criar um novo VM a partir desse VHD. 

Se ainda tiver o VM original, ou outro VM criado usando a mesma imagem de mercado, pode obter o nome do plano, editor e informações do produto usando [a visão de exemplo de exemplo az vm.](/cli/azure/vm#az_vm_get_instance_view) Este exemplo obtém um VM nomeado *myVM* no grupo de recursos *myResourceGroup* e, em seguida, exibe as informações do plano de compra.

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

Se não tiver a informação do plano antes da vm original ser eliminada, pode apresentar um pedido de [apoio](https://ms.portal.azure.com/#create/Microsoft.Support). Precisarão do nome VM, ID de subscrição e da hora da operação de eliminação.

Uma vez que tenha a informação do plano, pode criar o novo VM usando o `--attach-os-disk` parâmetro para especificar o VHD.

```azurecli-interactive
az vm create \
   --resource-group myResourceGroup \
  --name myNewVM \
  --nics myNic \
  --size Standard_DS1_v2 --os-type Linux \
  --attach-os-disk myVHD \
  --plan-name planName \
  --plan-publisher planPublisher \
  --plan-product planProduct 
```

## <a name="deploy-a-new-vm-using-purchase-plan-parameters"></a>Implementar um novo VM utilizando parâmetros do plano de compra

Se já tiver informações sobre a imagem, pode implantá-la usando o `az vm create` comando. Neste exemplo, implementamos um VM com o RabbitMQ Certificado pela imagem Bitnami:

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami
```

Se receber uma mensagem sobre a aceitação dos termos da imagem, consulte a secção [Aceite os termos](#accept-the-terms) mais tarde neste artigo.

## <a name="list-popular-images"></a>Listar imagens populares

Execute o comando [da lista de imagens az vm,](/cli/azure/vm/image) sem `--all` opção, para ver uma lista de imagens VM populares no Mercado Azure. Por exemplo, executar o seguinte comando para exibir uma lista em cache de imagens populares em formato de mesa:

```azurecli
az vm image list --output table
```

A saída inclui a URN de imagem (o valor na coluna *Urna).* Ao criar um VM com uma destas imagens populares do Marketplace, pode especificar alternativamente as *UrnAlias,* uma forma encurtada como *UbuntuLTS*.

```output
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-RAW               RedHat:RHEL:7-RAW:latest                                        RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Encontrar imagens específicas

Para encontrar uma imagem VM específica no Marketplace, utilize o `az vm image list` comando com a `--all` opção. Esta versão do comando demora algum tempo a ser concluída e pode devolver uma saída longa, por isso normalmente filtra a lista por `--publisher` ou por outro parâmetro. 

Por exemplo, o comando que se segue exibe todas as ofertas da Debian (lembre-se que sem o `--all` interruptor, apenas procura a cache local de imagens comuns):

```azurecli
az vm image list --offer Debian --all --output table 

```

Saída parcial: 

```output
Offer              Publisher    Sku                  Urn                                                    Version
-----------------  -----------  -------------------  -----------------------------------------------------  --------------
Debian             credativ     7                    credativ:Debian:7:7.0.201602010                        7.0.201602010
Debian             credativ     7                    credativ:Debian:7:7.0.201603020                        7.0.201603020
Debian             credativ     7                    credativ:Debian:7:7.0.201604050                        7.0.201604050
Debian             credativ     7                    credativ:Debian:7:7.0.201604200                        7.0.201604200
Debian             credativ     7                    credativ:Debian:7:7.0.201606280                        7.0.201606280
Debian             credativ     7                    credativ:Debian:7:7.0.201609120                        7.0.201609120
Debian             credativ     7                    credativ:Debian:7:7.0.201611020                        7.0.201611020
Debian             credativ     7                    credativ:Debian:7:7.0.201701180                        7.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201602010                        8.0.201602010
Debian             credativ     8                    credativ:Debian:8:8.0.201603020                        8.0.201603020
Debian             credativ     8                    credativ:Debian:8:8.0.201604050                        8.0.201604050
Debian             credativ     8                    credativ:Debian:8:8.0.201604200                        8.0.201604200
Debian             credativ     8                    credativ:Debian:8:8.0.201606280                        8.0.201606280
Debian             credativ     8                    credativ:Debian:8:8.0.201609120                        8.0.201609120
Debian             credativ     8                    credativ:Debian:8:8.0.201611020                        8.0.201611020
Debian             credativ     8                    credativ:Debian:8:8.0.201701180                        8.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201703150                        8.0.201703150
Debian             credativ     8                    credativ:Debian:8:8.0.201704110                        8.0.201704110
Debian             credativ     8                    credativ:Debian:8:8.0.201704180                        8.0.201704180
Debian             credativ     8                    credativ:Debian:8:8.0.201706190                        8.0.201706190
Debian             credativ     8                    credativ:Debian:8:8.0.201706210                        8.0.201706210
Debian             credativ     8                    credativ:Debian:8:8.0.201708040                        8.0.201708040
Debian             credativ     8                    credativ:Debian:8:8.0.201710090                        8.0.201710090
Debian             credativ     8                    credativ:Debian:8:8.0.201712040                        8.0.201712040
Debian             credativ     8                    credativ:Debian:8:8.0.201801170                        8.0.201801170
Debian             credativ     8                    credativ:Debian:8:8.0.201803130                        8.0.201803130
Debian             credativ     8                    credativ:Debian:8:8.0.201803260                        8.0.201803260
Debian             credativ     8                    credativ:Debian:8:8.0.201804020                        8.0.201804020
Debian             credativ     8                    credativ:Debian:8:8.0.201804150                        8.0.201804150
Debian             credativ     8                    credativ:Debian:8:8.0.201805160                        8.0.201805160
Debian             credativ     8                    credativ:Debian:8:8.0.201807160                        8.0.201807160
Debian             credativ     8                    credativ:Debian:8:8.0.201901221                        8.0.201901221
...
```

Aplicar filtros semelhantes com o `--location` `--publisher` , e `--sku` opções. Pode realizar partidas parciais num filtro, como procurar `--offer Deb` encontrar todas as imagens Debian.

Se não especificar um determinado local com a `--location` opção, os valores para a localização predefinida são devolvidos. (Desafine uma localização padrão diferente executando `az configure --defaults location=<location>` .)

Por exemplo, o seguinte comando lista todos os SKUs Debian 8 na localização da Europa Ocidental:

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Saída parcial:

```output
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
Debian   credativ     8                  credativ:Debian:8:8.0.201710090                  8.0.201710090
Debian   credativ     8                  credativ:Debian:8:8.0.201712040                  8.0.201712040
Debian   credativ     8                  credativ:Debian:8:8.0.201801170                  8.0.201801170
Debian   credativ     8                  credativ:Debian:8:8.0.201803130                  8.0.201803130
Debian   credativ     8                  credativ:Debian:8:8.0.201803260                  8.0.201803260
Debian   credativ     8                  credativ:Debian:8:8.0.201804020                  8.0.201804020
Debian   credativ     8                  credativ:Debian:8:8.0.201804150                  8.0.201804150
Debian   credativ     8                  credativ:Debian:8:8.0.201805160                  8.0.201805160
Debian   credativ     8                  credativ:Debian:8:8.0.201807160                  8.0.201807160
Debian   credativ     8                  credativ:Debian:8:8.0.201901221                  8.0.201901221
...
```

## <a name="navigate-the-images"></a>Navegue pelas imagens
 
Outra forma de encontrar uma imagem num local é executar os [editores de listas de imagens az vm](/cli/azure/vm/image), [az vm lista-ofertas](/cli/azure/vm/image)- e [a az vm list-skus](/cli/azure/vm/image) comandos em sequência. Com estes comandos, determina-se estes valores:

1. Listar os publicadores de imagem.
2. Para um determinado publicador, liste as respetivas ofertas.
3. Para uma determinada oferta, liste as respetivas SKUs.

Em seguida, para um SKU selecionado, pode escolher uma versão para implementar.

Por exemplo, o seguinte comando lista os editores de imagem na localização dos EUA Ocidentais:

```azurecli
az vm image list-publishers --location westus --output table
```

Saída parcial:

```output
Location    Name
----------  ----------------------------------------------------
westus      128technology
westus      1e
westus      4psa
westus      5nine-software-inc
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      accessdata-group
westus      accops
westus      Acronis
westus      Acronis.Backup
westus      actian-corp
westus      actian_matrix
westus      actifio
westus      activeeon
westus      advantech-webaccess
westus      aerospike
westus      affinio
westus      aiscaler-cache-control-ddos-and-url-rewriting-
westus      akamai-technologies
westus      akumina
...
```

Utilize estas informações para encontrar ofertas de um editor específico. Por exemplo, para a editora *canónica* na localização dos EUA ocidentais, encontre ofertas executando `azure vm image list-offers` . Passe a localização e a editora como no seguinte exemplo:

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

Resultado:

```output
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
```
Como vê, na região oeste dos EUA, a Canonical publica a oferta *da UbuntuServer* sobre a Azure. Mas que SKUs? Para obter esses valores, corra `azure vm image list-skus` e desateia a localização, editora e oferta que descobriu:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Resultado:

```output
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-LTS
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      18.04-DAILY-LTS
westus      18.04-LTS
westus      18.10
westus      18.10-DAILY
westus      19.04-DAILY
```

Finalmente, use o `az vm image list` comando para encontrar uma versão específica do SKU que deseja, por exemplo, *18.04-LTS:*

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 18.04-LTS --all --output table
```

Saída parcial:

```output
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201804262  18.04.201804262
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805170  18.04.201805170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805220  18.04.201805220
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806130  18.04.201806130
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806170  18.04.201806170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201807240  18.04.201807240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808060  18.04.201808060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808080  18.04.201808080
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808140  18.04.201808140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808310  18.04.201808310
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201809110  18.04.201809110
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810030  18.04.201810030
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810240  18.04.201810240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810290  18.04.201810290
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201811010  18.04.201811010
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812031  18.04.201812031
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812040  18.04.201812040
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812060  18.04.201812060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901140  18.04.201901140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901220  18.04.201901220
...
```

Agora pode escolher precisamente a imagem que pretende utilizar tomando nota do valor URN. Passe este valor com o `--image` parâmetro quando criar um VM com o comando [az vm criar.](/cli/azure/vm) Lembre-se que pode substituir opcionalmente o número de versão na URN por "mais recente". Esta versão é sempre a versão mais recente da imagem. 

Se implementar um VM com um modelo de Gestor de Recursos, define os parâmetros de imagem individualmente nas `imageReference` propriedades. Veja a [referência de modelo](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Ver propriedades do plano

Para ver as informações do plano de compra de uma imagem, execute o comando [de exibição de imagem az vm.](/cli/azure/image) Se a `plan` propriedade na saída não `null` estiver, a imagem tem termos que deve aceitar antes da implementação programática.

Por exemplo, a imagem Canonical Ubuntu Server 18.04 LTS não tem termos adicionais, porque a `plan` informação `null` é:

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

Resultado:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

Executar um comando semelhante para a imagem RabbitMQ Certified by Bitnami mostra as `plan` seguintes propriedades: `name` , e `product` `publisher` . (Algumas imagens também têm uma `promotion code` propriedade.) Para implementar esta imagem, consulte as seguintes secções para aceitar os termos e permitir a implementação programática.

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Resultado:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

## <a name="accept-the-terms"></a>Aceitar os termos

Para visualizar e aceitar os termos da licença, utilize o comando [de aceitação de termos de aceitação de imagem az vm.](/cli/azure/vm/image?) Quando aceita os termos, ativa a implementação programática na sua subscrição. Só precisa de aceitar termos uma vez por subscrição para a imagem. Por exemplo:

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
``` 

A saída inclui um `licenseTextLink` nos termos da licença, e indica que o valor `accepted` `true` de:

```output
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

## <a name="next-steps"></a>Passos seguintes
Para criar uma máquina virtual rapidamente utilizando as informações de imagem, consulte [Criar e Gerir VMs Linux com o Azure CLI](tutorial-manage-vm.md).
