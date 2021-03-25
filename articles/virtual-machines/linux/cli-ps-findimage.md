---
title: Localizar e utilizar informações do plano de compra do mercado utilizando o CLI
description: Aprenda a usar o CLI Azure para encontrar URNs de imagem e parâmetros de plano de compra, como a editora, oferta, SKU, e versão, para imagens Marketplace VM.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.collection: linux
ms.custom: contperf-fy21q3-portal
ms.openlocfilehash: 70cb4cc54c6f9a376d3bd38dc8bb6cd3a059a20c
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022853"
---
# <a name="find-azure-marketplace-image-information-using-the-azure-cli"></a>Encontre informações de imagem do Azure Marketplace utilizando o Azure CLI

Este tópico descreve como usar o CLI Azure para encontrar imagens VM no Mercado Azure. Utilize estas informações para especificar uma imagem do Marketplace quando criar um VM programáticamente com os modelos CLI, Gestor de Recursos ou outras ferramentas.

Também pode navegar nas imagens disponíveis e nas ofertas utilizando o [Azure Marketplace](https://azuremarketplace.microsoft.com/) ou  [o Azure PowerShell](../windows/cli-ps-findimage.md). 

## <a name="terminology"></a>Terminologia

Uma imagem de Marketplace em Azure tem os seguintes atributos:

* **Editora**: A organização que criou a imagem. Exemplos: Canonical, MicrosoftWindowsServer
* **Oferta**: O nome de um grupo de imagens relacionadas criadas por uma editora. Exemplos: UbuntuServer, WindowsServer
* **SKU**: Um caso de oferta, como uma grande libertação de uma distribuição. Exemplos: 18.04-LTS, 2019-Datacenter
* **Versão**: O número de versão de uma imagem SKU. 

Estes valores podem ser passados individualmente ou como *URN* de imagem, combinando os valores separados pelo cólon (:). Por exemplo: *Editor*:*Oferta*:*Sku*:*Versão*. Pode substituir o número de versão na URN `latest` para utilizar a versão mais recente da imagem. 

Se o editor de imagem fornecer licença e termos de compra adicionais, então deve aceitá-los antes de poder usar a imagem.  Para mais informações, consulte [a informação do plano de compra.](#check-the-purchase-plan-information)



## <a name="list-popular-images"></a>Listar imagens populares

Execute o comando [da lista de imagens az vm,](/cli/azure/vm/image) sem `--all` opção, para ver uma lista de imagens VM populares no Mercado Azure. Por exemplo, executar o seguinte comando para exibir uma lista em cache de imagens populares em formato de mesa:

```azurecli
az vm image list --output table
```

A saída inclui a URN de imagem. Também pode utilizar o *UrnAlias,* que é uma versão abreviada criada para imagens populares como *UbuntuLTS*.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
debian-10      Debian                  10                  Debian:debian-10:10:latest                                      Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-LVM               RedHat:RHEL:7-LVM:latest                                        RHEL                 latest
SLES           SUSE                    15                  SUSE:SLES:15:latest                                             SLES                 latest
UbuntuServer   Canonical               18.04-LTS           Canonical:UbuntuServer:18.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2019-Datacenter     MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest     Win2019Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

## <a name="find-specific-images"></a>Encontrar imagens específicas

Para encontrar uma imagem VM específica no Marketplace, utilize o `az vm image list` comando com a `--all` opção. Esta versão do comando demora algum tempo a ser concluída e pode devolver uma saída longa, por isso normalmente filtra a lista por `--publisher` ou por outro parâmetro. 

Por exemplo, o comando que se segue exibe todas as ofertas da Debian (lembre-se que sem o `--all` interruptor, apenas procura a cache local de imagens comuns):

```azurecli
az vm image list --offer Debian --all --output table 
```

Saída parcial: 

```output
Offer                                    Publisher                         Sku                                      Urn                                                                                                   Version
---------------------------------------  --------------------------------  ---------------------------------------  ----------------------------------------------------------------------------------------------------  --------------
apache-solr-on-debian                    apps-4-rent                       apache-solr-on-debian                    apps-4-rent:apache-solr-on-debian:apache-solr-on-debian:1.0.0                                         1.0.0
atomized-h-debian10-v1                   atomizedinc1587939464368          hdebian10plan                            atomizedinc1587939464368:atomized-h-debian10-v1:hdebian10plan:1.0.0                                   1.0.0
atomized-h-debian9-v1                    atomizedinc1587939464368          hdebian9plan                             atomizedinc1587939464368:atomized-h-debian9-v1:hdebian9plan:1.0.0                                     1.0.0
atomized-r-debian10-v1                   atomizedinc1587939464368          rdebian10plan                            atomizedinc1587939464368:atomized-r-debian10-v1:rdebian10plan:1.0.0                                   1.0.0
atomized-r-debian9-v1                    atomizedinc1587939464368          rdebian9plan                             atomizedinc1587939464368:atomized-r-debian9-v1:rdebian9plan:1.0.0                                     1.0.0
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.7                         1.0.7
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.8                         1.0.8
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.9                         1.0.9
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.18                          1.0.18
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.19                          1.0.19
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.20                          1.0.20
apache-web-server-with-debian-10         cognosys                          apache-web-server-with-debian-10         cognosys:apache-web-server-with-debian-10:apache-web-server-with-debian-10:1.2019.1008                1.2019.1008
docker-ce-with-debian-10                 cognosys                          docker-ce-with-debian-10                 cognosys:docker-ce-with-debian-10:docker-ce-with-debian-10:1.2019.0710                                1.2019.0710
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201602010                                                                       8.0.201602010
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201603020                                                                       8.0.201603020
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201604050                                                                       8.0.201604050
...
```


## <a name="look-at-all-available-images"></a>Veja todas as imagens disponíveis
 
Outra forma de encontrar uma imagem num local é executar os [editores de listas de imagens az vm](/cli/azure/vm/image), [az vm lista-ofertas](/cli/azure/vm/image)- e [a az vm list-skus](/cli/azure/vm/image) comandos em sequência. Com estes comandos, determina-se estes valores:

1. Listar os editores de imagem para uma localização. Neste exemplo, estamos a olhar para a região *oeste dos EUA.*
    
    ```azurecli
    az vm image list-publishers --location westus --output table
    ```

1. Para um determinado publicador, liste as respetivas ofertas. Neste exemplo, adicionamos *canonical* como editor.
    
    ```azurecli
    az vm image list-offers --location westus --publisher Canonical --output table
    ```

1. Para uma determinada oferta, liste as respetivas SKUs. Neste exemplo, adicionamos *ubuntuServer* como a oferta.
    ```azurecli
    az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
    ```

1. Para uma determinada editora, oferta e SKU, mostrar todas as versões da imagem. Neste exemplo, adicionamos *18.04-LTS* como o SKU.

    ```azurecli
    az vm image list \
        --location westus \
        --publisher Canonical \  
        --offer UbuntuServer \    
        --sku 18.04-LTS \
        --all --output table
    ```

Passe este valor da coluna URN com o `--image` parâmetro quando criar um VM com o comando [az vm criar.](/cli/azure/vm) Também pode substituir o número de versão na URN por "mais recente", para simplesmente utilizar a versão mais recente da imagem. 

Se implementar um VM com um modelo de Gestor de Recursos, define os parâmetros de imagem individualmente nas `imageReference` propriedades. Veja a [referência de modelo](/azure/templates/microsoft.compute/virtualmachines).


## <a name="check-the-purchase-plan-information"></a>Verifique as informações do plano de compra

Algumas imagens VM no Azure Marketplace têm licença e termos de compra adicionais que deve aceitar antes de poder implantá-las programáticamente.  

Para implementar um VM a partir de tal imagem, terá de aceitar os termos da imagem na primeira vez que a utilizar, uma vez por subscrição. Também terá de especificar os parâmetros do plano de *compra* para implementar um VM a partir dessa imagem

Para visualizar as informações do plano de compra de uma imagem, execute o comando de exibição de [imagem az vm](/cli/azure/image) com a URN da imagem. Se a `plan` propriedade na saída não `null` estiver, a imagem tem termos que deve aceitar antes da implementação programática.

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

Executar um comando semelhante para a imagem RabbitMQ Certified by Bitnami mostra as `plan` seguintes propriedades: `name` , e `product` `publisher` . (Algumas imagens também têm uma `promotion code` propriedade.) 

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

Para implementar esta imagem, tem de aceitar os termos e fornecer os parâmetros do plano de compra quando implementar um VM utilizando essa imagem.

## <a name="accept-the-terms"></a>Aceitar os termos

Para visualizar e aceitar os termos da licença, utilize o comando [de aceitação de termos de aceitação de imagem az vm.](/cli/azure/vm/image/terms) Quando aceita os termos, ativa a implementação programática na sua subscrição. Só precisa de aceitar termos uma vez por subscrição para a imagem. Por exemplo:

```azurecli
az vm image terms show --urn bitnami:rabbitmq:rabbitmq:latest
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

Para aceitar os termos, escreva:

```azurecli
az vm image terms accept --urn bitnami:rabbitmq:rabbitmq:latest
``` 

## <a name="deploy-a-new-vm-using-the-image-parameters"></a>Implementar um novo VM utilizando os parâmetros de imagem

Com informações sobre a imagem, pode implantá-la usando o `az vm create` comando. 

Para implementar uma imagem que não tenha informações de plano, como a mais recente imagem do Ubuntu Server 18.04 da Canonical, passe a URN `--image` para:

```azurecli-interactive
az group create --name myURNVM --location westus
az vm create \
   --resource-group myURNVM \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image Canonical:UbuntuServer:18.04-LTS:latest 
```


Para uma imagem com parâmetros de plano de compra, como o RabbitMQ Certificado pela imagem Bitnami, você passa a URN para `--image` e também fornece os parâmetros do plano de compra:

```azurecli
az group create --name myPurchasePlanRG --location westus

az vm create \
   --resource-group myPurchasePlanRG \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image bitnami:rabbitmq:rabbitmq:latest \
   --plan-name rabbitmq \
   --plan-product rabbitmq \
   --plan-publisher bitnami
```

Se receber uma mensagem sobre a aceitação dos termos da imagem, reveja a secção [Aceite os termos](#accept-the-terms). Certifique-se de que a saída das `az vm image accept-terms` devoluções mostra o valor `"accepted": true,` que aceitou os termos da imagem.


## <a name="using-an-existing-vhd-with-purchase-plan-information"></a>Usando um VHD existente com informações do plano de compra

Se tiver um VHD existente a partir de um VM que foi criado usando uma imagem de Azure Marketplace paga, poderá ter de fornecer informações sobre o plano de compra quando criar um novo VM a partir desse VHD. 

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


## <a name="next-steps"></a>Passos seguintes
Para criar uma máquina virtual rapidamente utilizando as informações de imagem, consulte [Criar e Gerir VMs Linux com o Azure CLI](tutorial-manage-vm.md).
