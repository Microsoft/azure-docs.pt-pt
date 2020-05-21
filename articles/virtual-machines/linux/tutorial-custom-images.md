---
title: Tutorial - Crie imagens VM personalizadas com o Azure CLI
description: Neste tutorial, vai aprender a utilizar a CLI do Azure para criar uma imagem de máquina virtual personalizada no Azure
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: mvc
ms.reviewer: akjosh
ms.openlocfilehash: bed65754dd872d51d4cbd1bccc673373e8e96846
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652990"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Tutorial: Criar uma imagem personalizada de uma VM do Azure com a CLI do Azure

As imagens personalizadas são como imagens do marketplace, mas são criadas por si. As imagens personalizadas podem ser utilizadas para configurações do programa de arranque do sistema, como o pré-carregamento de aplicações, configurações de aplicação e outras configurações do SO. Neste tutorial, vai criar a sua imagem personalizada de uma máquina virtual do Azure. Saiba como:

> [!div class="checklist"]
> * Criar um Shared Image Gallery
> * Criar uma definição de imagem
> * Criar uma versão de imagem
> * Criar um VM a partir de uma imagem 
> * Partilhar uma galeria de imagens


Este tutorial utiliza o CLI dentro da [Cloud Shell Azure,](https://docs.microsoft.com/azure/cloud-shell/overview)que é constantemente atualizada para a versão mais recente. Para abrir a Cloud Shell, selecione **Experimente a** partir do topo de qualquer bloco de código.

Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão 2.4.0 do Azure CLI ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="overview"></a>Descrição geral

Uma [Galeria de Imagem Partilhada](shared-image-galleries.md) simplifica a partilha de imagens personalizadas em toda a sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. As imagens personalizadas podem ser utilizadas para configurações do programa de arranque do sistema, como o pré-carregamento de aplicações, configurações de aplicação e outras configurações do SO. 

A Galeria de Imagem Partilhada permite-lhe partilhar as suas imagens VM personalizadas com outras. Escolha quais as imagens que pretende partilhar, quais as regiões em que as quer disponibilizar e com quem as quer partilhar. 

A funcionalidade Da Galeria de Imagem Partilhada tem vários tipos de recursos:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="before-you-begin"></a>Antes de começar

Os passos abaixo detalham como tornar uma VM existente numa imagem personalizada reutilizável que pode utilizar para criar novas instâncias da VM.

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente. Se necessário, pode ver o [CLI quickstart](quick-create-cli.md) para criar um VM para usar para este tutorial. Ao trabalhar através do tutorial, substitua os nomes de recursos sempre que necessário.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell em um separado separado browser, indo para [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens 

Uma galeria de imagens é o recurso principal usado para permitir a partilha de imagens. 

Os caracteres permitidos para o nome da Galeria são letras maiúsculas ou minúsculas, dígitos, pontos e períodos. O nome da galeria não pode conter traços.   Os nomes das galerias devem ser únicos dentro da sua subscrição. 

Crie uma galeria de imagens usando [az sig criar.](/cli/azure/sig#az-sig-create) O exemplo seguinte cria um grupo de recursos chamado *myGalleryRG* no *Leste dos EUA,* e uma galeria chamada *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="get-infornation-about-the-vm"></a>Infornation sobre o VM

Pode ver uma lista de VMs disponíveis através [da lista Az Vm](/cli/azure/vm#az-vm-list). 

```azurecli-interactive
az vm list --output table
```

Assim que souber o nome VM e em que grupo de recursos se encontra, obtenha a identificação do VM usando [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```

Copie a identificação do seu VM para usar mais tarde.

## <a name="create-an-image-definition"></a>Criar uma definição de imagem

As definições de imagem criam um agrupamento lógico para imagens. São utilizados para gerir informação sobre as versões de imagem que são criadas dentro delas. 

Os nomes de definição de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e períodos. 

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, consulte [definições](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)de imagem .

Crie uma definição de imagem na galeria utilizando a [criação de definição de imagem az sig](/cli/azure/sig/image-definition#az-sig-image-definition-create). 

Neste exemplo, a definição de imagem é denominada *myImageDefinition*, e é para uma imagem o Linux OS [especializada.](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) 

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

Copie a identificação da definição de imagem da saída para utilizar mais tarde.

## <a name="create-the-image-version"></a>Criar a versão de imagem

Crie uma versão de imagem a partir do VM utilizando a imagem [da galeria az criar-versão de imagem.](/cli/azure/sig/image-version#az-sig-image-version-create)  

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Versão menor.* *Patch*.

Neste exemplo, a versão da nossa imagem é *de 1.0.0* e vamos criar 2 réplicas na região *centro-oeste dos EUA,* 1 réplica na região *centro-sul dos EUA* e 1 réplica na região leste dos EUA *2* utilizando armazenamento redundante. As regiões de replicação devem incluir a região onde se encontra a fonte VM.

Substitua o valor `--managed-image` deste exemplo pela identificação do seu VM do passo anterior.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> É preciso esperar que a versão de imagem termine completamente de ser construída e replicada antes de poder utilizar a mesma imagem gerida para criar outra versão de imagem.
>
> Também pode armazenar a sua imagem no armazenamento Premiun através de um armazenamento de adição `--storage-account-type  premium_lrs` ou [Zona Redundante](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) adicionando `--storage-account-type  standard_zrs` quando criar a versão de imagem.
>

 
## <a name="create-the-vm"></a>Crie a VM

Criar o VM usando [az vm criar](/cli/azure/vm#az-vm-create) usando o parâmetro --especializado para indicar que a imagem é uma imagem especializada. 

Utilize o ID de definição de imagem para `--image` criar o VM a partir da versão mais recente da imagem que está disponível. Também pode criar o VM a partir de uma versão específica, fornecendo o ID da versão de imagem para `--image` . 

Neste exemplo, estamos a criar um VM a partir da versão mais recente da imagem *myImageDefinition.*

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

## <a name="share-the-gallery"></a>Partilhar a galeria

Pode partilhar imagens através de subscrições utilizando o Controlo de Acesso Baseado em Funções (RBAC). Pode partilhar imagens na galeria, definição de imagem ou versão de imagem. Qualquer utilizador que tenha lido permissões para uma versão de imagem, mesmo através de subscrições, será capaz de implementar um VM utilizando a versão de imagem.

Recomendamos que partilhe com outros utilizadores ao nível da galeria. Para obter a identificação do objeto da sua galeria, use [az sig show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Utilize o ID do objeto como âmbito, juntamente com um endereço de e-mail e [uma atribuição de função az criar](/cli/azure/role/assignment#az-role-assignment-create) para dar a um utilizador acesso à galeria de imagens partilhadas. Substitua `<email-address>` e com a sua própria `<gallery iD>` informação.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Para obter mais informações sobre como partilhar recursos usando o RBAC, consulte [Gerir o acesso utilizando o RBAC e o Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).

## <a name="azure-image-builder"></a>Azure Image Builder

A Azure também oferece um serviço, construído em Packer, [Azure VM Image Builder.](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview) Basta descrever as suas personalizações num modelo, e tratará da criação de imagem. 

## <a name="next-steps"></a>Próximos passos

Neste tutorial, criou uma imagem de VM personalizada. Aprendeu a:

> [!div class="checklist"]
> * Criar um Shared Image Gallery
> * Criar uma definição de imagem
> * Criar uma versão de imagem
> * Criar um VM a partir de uma imagem 
> * Partilhar uma galeria de imagens

Avance para o próximo tutorial para saber mais sobre máquinas virtuais de elevada disponibilidade.

> [!div class="nextstepaction"]
> [Criar VMs de elevada disponibilidade](tutorial-availability-sets.md)

