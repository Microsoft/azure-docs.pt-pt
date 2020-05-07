---
title: Tutorial - Use uma imagem VM personalizada em um conjunto de escala com Azure CLI
description: Saiba como utilizar a CLI do Azure para criar uma imagem de VM personalizada que pode utilizar para implementar um conjunto de dimensionamento de máquinas virtuais
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc
ms.reviewer: akjosh
ms.openlocfilehash: 22f3fd44fbeb3d951d4add7b90a0e9aebd863ebf
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792858"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Tutorial: Criar e utilizar uma imagem personalizada para conjuntos de dimensionamento de máquinas virtuais com a CLI do Azure
Quando cria um conjunto de dimensionamento, tem de especificar uma imagem a ser utilizada quando as instâncias de VM são implementadas. Para reduzir o número de tarefas após as instâncias de VM serem implementadas, pode utilizar uma imagem de VM personalizada. Esta imagem de VM personalizada inclui instalações ou configurações de aplicações obrigatórias. Quaisquer instâncias de VM criadas no conjunto de dimensionamento utilizam a imagem de VM personalizada e estão prontas para apresentar o seu tráfego de aplicações. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma Galeria de Imagem Partilhada
> * Criar uma definição de imagem especializada
> * Criar uma versão de imagem
> * Criar um conjunto de escala a partir de uma imagem especializada
> * Partilhar uma galeria de imagens


Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão 2.4.0 do Azure CLI ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="overview"></a>Descrição geral

Uma [Galeria de Imagem Partilhada](shared-image-galleries.md) simplifica a partilha de imagens personalizadas em toda a sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. As imagens personalizadas podem ser utilizadas para configurações do programa de arranque do sistema, como o pré-carregamento de aplicações, configurações de aplicação e outras configurações do SO. 

A Galeria de Imagem Partilhada permite-lhe partilhar as suas imagens VM personalizadas com outras. Escolha quais as imagens que pretende partilhar, quais as regiões em que as quer disponibilizar e com quem as quer partilhar. 

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
> A **identificação** do seu VM é mostrada na saída do comando [az vm criar.](/cli/azure/vm) Copie isto em algum lugar seguro para que possa usá-lo mais tarde neste tutorial.

O endereço IP público do seu VM também é mostrado na saída do comando [az vm criar.](/cli/azure/vm) Execute o SSH ao endereço IP público da VM, da seguinte forma:

```console
ssh azureuser@<publicIpAddress>
```

Para personalizar a sua VM, vamos instalar um servidor Web básico. Quando a instância de VM no conjunto de dimensionamento for implementada, terá todos os pacotes necessários para executar uma aplicação Web. Utilize `apt-get` para instalar o *NGINX*, da seguinte forma:

```bash
sudo apt-get install -y nginx
```

Quando terminar, digite `exit` para desligar a ligação SSH.

## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens 

Uma galeria de imagens é o recurso principal usado para permitir a partilha de imagens. 

Os caracteres permitidos para o nome da Galeria são letras maiúsculas ou minúsculas, dígitos, pontos e períodos. O nome da galeria não pode conter traços.   Os nomes das galerias devem ser únicos dentro da sua subscrição. 

Crie uma galeria de imagens usando [az sig criar.](/cli/azure/sig#az-sig-create) O exemplo seguinte cria um grupo de recursos chamado *myGalleryRG* no *Leste dos EUA,* e uma galeria chamada *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Criar uma definição de imagem

As definições de imagem criam um agrupamento lógico para imagens. São utilizados para gerir informação sobre as versões de imagem que são criadas dentro delas. 

Os nomes de definição de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e períodos. 

Certifique-se de que a sua definição de imagem é do tipo certo. Se tiver generalizado o VM (utilizando sysprep para Windows, ou waagent -deprovisionamento para Linux), então deve criar uma definição de imagem generalizada utilizando `--os-state generalized`. Se pretender utilizar o VM sem remover as contas de utilizador `--os-state specialized`existentes, crie uma definição de imagem especializada utilizando .

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, consulte [definições](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)de imagem .

Crie uma definição de imagem na galeria utilizando a [criação de definição de imagem az sig](/cli/azure/sig/image-definition#az-sig-image-definition-create).

Neste exemplo, a definição de imagem é denominada *myImageDefinition*, e é para uma imagem o Linux OS [especializada.](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) Para criar uma definição para imagens utilizando um Sistema operativo Windows, utilize. `--os-type Windows` 

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
> A **identificação** da sua definição de imagem é mostrada na saída do comando. Copie isto em algum lugar seguro para que possa usá-lo mais tarde neste tutorial.


## <a name="create-the-image-version"></a>Criar a versão de imagem

Crie uma versão de imagem a partir do VM utilizando a imagem [da galeria az criar-versão de imagem.](/cli/azure/sig/image-version#az-sig-image-version-create)  

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Versão menor.* *Patch*.

Neste exemplo, a versão da nossa imagem é *1.0.0* e vamos criar uma réplica na região *centro-sul dos EUA* e uma réplica na região *leste dos EUA 2.* As regiões de replicação devem incluir a região onde se encontra a fonte VM.

Substitua o `--managed-image` valor deste exemplo pela identificação do seu VM do passo anterior.

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
> É preciso esperar que a versão de imagem termine completamente de ser construída e replicada antes de poder utilizar a mesma imagem gerida para criar outra versão de imagem.
>
> Também pode armazenar a sua imagem `--storage-account-type  premium_lrs`no armazenamento Premium através `--storage-account-type  standard_zrs` de um armazenamento de adição ou [De si](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) mesmo, adicionando quando cria a versão de imagem.
>




## <a name="create-a-scale-set-from-the-image"></a>Criar um conjunto de escala a partir da imagem
Criar um conjunto de escala [`az vmss create`](/cli/azure/vmss#az-vmss-create)a partir da imagem especializada utilizando . 

Crie o [`az vmss create`](/cli/azure/vmss#az-vmss-create) conjunto de escala utilizando o parâmetro --especializado para indicar que a imagem é uma imagem especializada. 

Utilize o ID `--image` de definição de imagem para criar as instâncias de conjunto de escala a partir da versão mais recente da imagem que está disponível. Também pode criar as instâncias de conjunto de escala a `--image`partir de uma versão específica, fornecendo o ID da versão de imagem para . 

Crie um conjunto de escala sem nome *myScaleSet* a versão mais recente da imagem *myImageDefinition* que criamos anteriormente.

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



## <a name="share-the-gallery"></a>Partilhar a galeria

Pode partilhar imagens através de subscrições utilizando o Controlo de Acesso Baseado em Funções (RBAC). Pode partilhar imagens na galeria, definição de imagem ou versão de imagem. Qualquer utilizador que tenha lido permissões para uma versão de imagem, mesmo através de subscrições, será capaz de implementar um VM utilizando a versão de imagem.

Recomendamos que partilhe com outros utilizadores ao nível da galeria. Para obter a identificação do objeto da sua galeria, use [az sig show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Utilize o ID do objeto como âmbito, juntamente com um endereço de e-mail e [uma atribuição de função az criar](/cli/azure/role/assignment#az-role-assignment-create) para dar a um utilizador acesso à galeria de imagens partilhadas. `<email-address>` Substitua `<gallery iD>` e com a sua própria informação.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Para obter mais informações sobre como partilhar recursos usando o RBAC, consulte [Gerir o acesso utilizando o RBAC e o Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="clean-up-resources"></a>Limpar recursos
Para remover o seu conjunto de dimensionamento e recursos adicionais, elimine o grupo de recursos e todos os respetivos recursos com [az group delete](/cli/azure/group). O parâmetro `--no-wait` devolve o controlo à linha de comandos, sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que pretende eliminar os recursos sem uma linha de comandos adicional para fazê-lo.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a criar e a utilizar uma imagem de VM personalizada nos seus conjuntos de dimensionamento com a CLI do Azure:

> [!div class="checklist"]
> * Criar uma Galeria de Imagem Partilhada
> * Criar uma definição de imagem especializada
> * Criar uma versão de imagem
> * Criar um conjunto de escala a partir de uma imagem especializada
> * Partilhar uma galeria de imagens

Prossiga para o próximo tutorial para saber como implementar aplicações no seu conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Implementar aplicações nos seus conjuntos de dimensionamento](tutorial-install-apps-cli.md)
