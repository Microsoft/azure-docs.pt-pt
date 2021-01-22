---
title: Tutorial - Use uma imagem VM personalizada em uma escala definida com Azure CLI
description: Saiba como utilizar a CLI do Azure para criar uma imagem de VM personalizada que pode utilizar para implementar um conjunto de dimensionamento de máquinas virtuais
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: b12715e299f523d7ace56a72b0098b5d7ffac0ab
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683059"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Tutorial: Criar e utilizar uma imagem personalizada para conjuntos de dimensionamento de máquinas virtuais com a CLI do Azure
Quando cria um conjunto de dimensionamento, tem de especificar uma imagem a ser utilizada quando as instâncias de VM são implementadas. Para reduzir o número de tarefas após as instâncias de VM serem implementadas, pode utilizar uma imagem de VM personalizada. Esta imagem de VM personalizada inclui instalações ou configurações de aplicações obrigatórias. Quaisquer instâncias de VM criadas no conjunto de dimensionamento utilizam a imagem de VM personalizada e estão prontas para apresentar o seu tráfego de aplicações. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um Shared Image Gallery
> * Criar uma definição de imagem especializada
> * Criar uma versão de imagem
> * Criar um conjunto de escala a partir de uma imagem especializada
> * Partilhar uma galeria de imagens


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.4.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="overview"></a>Descrição geral

Uma [Galeria de Imagens Partilhadas](../virtual-machines/shared-image-galleries.md) simplifica a partilha de imagens personalizadas em toda a sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. As imagens personalizadas podem ser utilizadas para configurações do programa de arranque do sistema, como o pré-carregamento de aplicações, configurações de aplicação e outras configurações do SO. 

A Galeria de Imagens Partilhada permite-lhe partilhar as suas imagens VM personalizadas com outras. Escolha quais as imagens que pretende partilhar, em que regiões quer disponibilizá-las e com quem quer partilhá-las. 

## <a name="create-and-configure-a-source-vm"></a>Criar e configurar uma VM de origem

Em primeiro lugar, crie um grupo de recursos com [az group create](/cli/azure/group) e, em seguida, crie uma VM com [az vm create](/cli/azure/vm). Este VM é então usado como fonte para a imagem. O exemplo seguinte cria uma VM com o nome *myVM* no grupo de recursos com o nome *myResourceGroup*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

> [!IMPORTANT]
> O **ID** do seu VM é mostrado na saída do comando [az vm criar.](/cli/azure/vm) Copie este lugar seguro para que possa usá-lo mais tarde neste tutorial.

O endereço IP público do seu VM também é mostrado na saída do comando [az vm criar.](/cli/azure/vm) Execute o SSH ao endereço IP público da VM, da seguinte forma:

```console
ssh azureuser@<publicIpAddress>
```

Para personalizar a sua VM, vamos instalar um servidor Web básico. Quando a instância de VM no conjunto de dimensionamento for implementada, terá todos os pacotes necessários para executar uma aplicação Web. Utilize `apt-get` para instalar o *NGINX*, da seguinte forma:

```bash
sudo apt-get install -y nginx
```

Quando terminar, `exit` escreva para desligar a ligação SSH.

## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens 

Uma galeria de imagens é o principal recurso utilizado para permitir a partilha de imagens. 

Os caracteres permitidos para o nome da Galeria são letras maiúsculas ou minúsculas, dígitos, pontos e períodos. O nome da galeria não pode conter traços.   Os nomes das galerias devem ser únicos dentro da sua subscrição. 

Crie uma galeria de imagens utilizando [a az sig create](/cli/azure/sig#az-sig-create). O exemplo a seguir cria um grupo de recursos chamado *myGalleryRG* in *East US,* e uma galeria chamada *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Criar uma definição de imagem

As definições de imagem criam um agrupamento lógico para imagens. São utilizados para gerir informações sobre as versões de imagem que são criadas dentro delas. 

Os nomes da definição de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e períodos. 

Certifique-se de que a definição de imagem é o tipo certo. Se generalizou o VM (utilizando o Sysprep para Windows, ou desprovisionamento waagent para o Linux), então deve criar uma definição de imagem generalizada utilizando `--os-state generalized` . Se pretender utilizar o VM sem remover as contas de utilizador existentes, crie uma definição de imagem especializada utilizando `--os-state specialized` .

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, consulte [definições de imagem](../virtual-machines/shared-image-galleries.md#image-definitions).

Crie uma definição de imagem na galeria utilizando [a az sig definição de imagem criar](/cli/azure/sig/image-definition#az-sig-image-definition-create).

Neste exemplo, a definição de imagem chama-se *myImageDefinition*, e [destina-se a](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) uma imagem especializada do Linux OS. Para criar uma definição para imagens que utilizem um SISTEMA Windows, utilize `--os-type Windows` . 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

> [!IMPORTANT]
> O **ID** da sua definição de imagem é mostrado na saída do comando. Copie este lugar seguro para que possa usá-lo mais tarde neste tutorial.


## <a name="create-the-image-version"></a>Criar a versão de imagem

Crie uma versão de imagem a partir do VM utilizando [a az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create).  

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Menorversão.* *Patch*.

Neste exemplo, a versão da nossa imagem é *1.0.0* e vamos criar 1 réplica na região *sul-americana* e 1 réplica na região *leste dos EUA 2.* As regiões de replicação devem incluir a região onde se situa a VM de origem.

Substitua o valor `--managed-image` deste exemplo pelo ID do seu VM do passo anterior.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1" \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> É necessário esperar que a versão de imagem termine completamente de ser construída e replicada antes de poder utilizar a mesma imagem gerida para criar outra versão de imagem.
>
> Também pode armazenar a sua imagem no armazenamento Premium através de um armazenamento `--storage-account-type  premium_lrs` , ou [Armazenamento Redundante zona,](../storage/common/storage-redundancy.md) adicionando `--storage-account-type  standard_zrs` quando cria a versão de imagem.
>




## <a name="create-a-scale-set-from-the-image"></a>Criar um conjunto de escala a partir da imagem
Crie um conjunto de escala a partir da imagem especializada utilizando [`az vmss create`](/cli/azure/vmss#az-vmss-create) . 

Crie o conjunto de escala [`az vmss create`](/cli/azure/vmss#az-vmss-create) utilizando o parâmetro especializado para indicar que a imagem é uma imagem especializada. 

Utilize o ID de definição de imagem `--image` para criar as instâncias definidas na escala a partir da versão mais recente da imagem que está disponível. Também pode criar as instâncias definidas em escala a partir de uma versão específica, fornecendo o ID da versão de imagem para `--image` . 

Crie um conjunto de escala chamado *myScaleSet* a versão mais recente da imagem *myImageDefinition* que criamos anteriormente.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```

A criação e configuração de todas as VMs e recursos do conjunto de dimensionamento demora alguns minutos.


## <a name="test-your-scale-set"></a>Testar o seu conjunto de dimensionamento
Para permitir que o tráfego alcance o conjunto de dimensionamento e verificar se o servidor Web funciona corretamente, crie um balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule). O exemplo seguinte cria uma regra com o nome *myLoadBalancerRuleWeb*, que permite tráfego na porta *TCP**80*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Para ver o seu conjunto de dimensionamento em ação, obtenha o endereço IP público do seu balanceador de carga com [az network public-ip show](/cli/azure/network/public-ip). O exemplo seguinte obtém o endereço IP para *myScaleSetLBPublicIP*, criado como parte do conjunto de dimensionamento:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Escreva o endereço IP público no seu browser. A página Web NGINX predefinida é apresentada, conforme mostrado no seguinte exemplo:

![Nginx executado da imagem de VM personalizada](media/tutorial-use-custom-image-cli/default-nginx-website.png)



## <a name="share-the-gallery"></a>Partilhe a galeria

Pode partilhar imagens através de subscrições utilizando o controlo de acesso baseado em funções Azure (Azure RBAC). Pode partilhar imagens na galeria, definição de imagem ou versão de imagem. Qualquer utilizador que tenha lido permissões para uma versão de imagem, mesmo através de subscrições, poderá implementar um VM utilizando a versão de imagem.

Recomendamos que partilhe com outros utilizadores ao nível da galeria. Para obter a identificação do objeto da sua galeria, use [a az sig show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Use o ID do objeto como um âmbito, juntamente com um endereço de e-mail e [uma atribuição de funções az](/cli/azure/role/assignment#az-role-assignment-create) criar para dar a um utilizador acesso à galeria de imagens partilhada. Substitua `<email-address>` e pela sua própria `<gallery iD>` informação.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Para obter mais informações sobre como partilhar recursos usando o Azure RBAC, consulte [Adicionar ou remover atribuições de funções Azure usando Azure CLI](../role-based-access-control/role-assignments-cli.md).


## <a name="clean-up-resources"></a>Limpar os recursos
Para remover o seu conjunto de dimensionamento e recursos adicionais, elimine o grupo de recursos e todos os respetivos recursos com [az group delete](/cli/azure/group). O parâmetro `--no-wait` devolve o controlo à linha de comandos, sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que pretende eliminar os recursos sem uma linha de comandos adicional para fazê-lo.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a criar e a utilizar uma imagem de VM personalizada nos seus conjuntos de dimensionamento com a CLI do Azure:

> [!div class="checklist"]
> * Criar um Shared Image Gallery
> * Criar uma definição de imagem especializada
> * Criar uma versão de imagem
> * Criar um conjunto de escala a partir de uma imagem especializada
> * Partilhar uma galeria de imagens

Prossiga para o próximo tutorial para saber como implementar aplicações no seu conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Implementar aplicações nos seus conjuntos de dimensionamento](tutorial-install-apps-cli.md)