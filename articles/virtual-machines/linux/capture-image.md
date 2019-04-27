---
title: Capturar uma imagem de VM do Linux no Azure com a CLI do Azure | Documentos da Microsoft
description: Capture uma imagem de VM do Azure para utilizar para implementações em massa com a CLI do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 5022d765b5dfa4f1f973b7fb4370d5314bb887b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542877"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Como criar uma imagem de uma máquina virtual ou VHD

<!-- generalize, image - extended version of the tutorial-->

Para criar várias cópias de uma máquina virtual (VM) para utilização no Azure, capture uma imagem da VM ou do VHD do SO. Para criar uma imagem para a implementação, terá de remover as informações de conta pessoal. Nos passos seguintes, desaprovisionar uma VM existente, desalocá-lo e criar uma imagem. Pode utilizar esta imagem para criar VMs em qualquer grupo de recursos na sua subscrição.

Para criar uma cópia da sua VM do Linux existente para a cópia de segurança ou de depuração ou para carregar um VHD especializado do Linux a partir de uma VM no local, consulte [carregar e criar uma VM do Linux a partir de imagem de disco personalizada](upload-vhd.md).  

Também pode utilizar **Packer** para criar a configuração personalizada. Para obter mais informações, consulte [como utilizar o Packer para criar imagens de máquinas virtuais do Linux no Azure](build-image-with-packer.md).

Terá dos seguintes itens antes de criar uma imagem:

* Uma VM do Azure criada no modelo de implementação do Resource Manager que utiliza discos geridos. Se ainda não criou uma VM do Linux, pode utilizar o [portal](quick-create-portal.md), o [CLI do Azure](quick-create-cli.md), ou [modelos do Resource Manager](create-ssh-secured-vm-from-template.md). Configure a VM conforme necessário. Por exemplo, [adicionar discos de dados](add-disk.md), aplicar atualizações e instalar aplicações. 

* A versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) instalado e de ter sessão iniciada numa conta do Azure com [início de sessão az](/cli/azure/reference-index#az-login).

## <a name="quick-commands"></a>Comandos rápidos

Para uma versão simplificada deste artigo e teste, avaliar ou aprender sobre as VMs no Azure, veja [criar uma imagem personalizada de uma VM do Azure com a CLI](tutorial-custom-images.md).


## <a name="step-1-deprovision-the-vm"></a>Passo 1: Desaprovisionar a VM
Primeiro vou desaprovisionar a VM ao utilizar o agente de VM do Azure para eliminar ficheiros específicas da máquina e dados. Utilize o `waagent` comando com o `-deprovision+user` parâmetro na sua VM do Linux de origem. Para obter mais informações, veja o [Guia de utilizador do Agente Linux do Azure](../extensions/agent-linux.md).

1. Ligar à VM do Linux com um cliente SSH.
2. Na janela de SSH, introduza o seguinte comando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Apenas execute este comando numa VM que vai capturar como uma imagem. Este comando não garante que a imagem será removida de todas as informações confidenciais ou se é adequada para redistribuição. O `+user` parâmetro também remove a última conta de utilizador aprovisionado. Para manter as credenciais da conta de utilizador na VM, utilize apenas `-deprovision`.
 
3. Introduza **y** para continuar. Pode adicionar o `-force` parâmetro para evitar esta etapa de confirmação.
4. Depois do comando for concluído, introduza **sair** para fechar o cliente SSH.

## <a name="step-2-create-vm-image"></a>Passo 2: Criar imagem de VM
Utilize a CLI do Azure para marcar a VM como generalizado e capturar a imagem. Nos exemplos a seguir, substitua os nomes de parâmetros de exemplo pelos seus próprios valores. Os nomes de parâmetros de exemplo incluem *myResourceGroup*, *myVnet*, e *myVM*.

1. Desaloque a VM que desaprovisionado com [az vm deallocate](/cli/azure/vm). O exemplo seguinte desaloca a VM com o nome *myVM* no grupo de recursos com o nome *myResourceGroup*.
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. Marque a VM como generalizado com [az vm generalize](/cli/azure/vm). O exemplo seguinte marca a VM com o nome *myVM* no grupo de recursos com o nome *myResourceGroup* como generalizada.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. Criar uma imagem do recurso VM com o [criar imagem de az](/cli/azure/image#az-image-create). O exemplo seguinte cria uma imagem designada *myImage* no grupo de recursos com o nome *myResourceGroup* usando o recurso da VM com o nome *myVM*.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > A imagem é criada no mesmo grupo de recursos como a VM de origem. Pode criar VMs em qualquer grupo de recursos na sua subscrição a partir desta imagem. Da perspectiva de gestão, pode pretender criar um grupo de recursos específicos para os seus recursos da VM e imagens.
   >
   > Se gostaria de armazenar a imagem no armazenamento resiliente para a zona, terá de criá-la numa região que suporta [zonas de disponibilidade](../../availability-zones/az-overview.md) e incluir o `--zone-resilient true` parâmetro.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Passo 3: Criar uma VM a partir da imagem capturada
Criar uma VM com a imagem criada com [az vm criar](/cli/azure/vm). O exemplo seguinte cria uma VM com o nome *myVMDeployed* da imagem com o nome *myImage*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Criar a VM em outro grupo de recursos 

Pode criar VMs a partir de uma imagem em qualquer grupo de recursos na sua subscrição. Para criar uma VM no grupo de recursos diferente que a imagem, especifica o ID de recurso completo para a sua imagem. Uso [lista de imagens de az](/cli/azure/image#az-image-list) para ver uma lista de imagens. O resultado será semelhante ao seguinte exemplo.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

O exemplo seguinte utiliza [az vm criar](/cli/azure/vm#az-vm-create) para criar uma VM num grupo de recursos que não seja a imagem de origem, especificando o ID de recurso de imagem.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Passo 4: Verificar a implementação

SSH para a máquina virtual que criou para verificar a implementação e comece a utilizar a nova VM. Para ligar através de SSH, encontrar o endereço IP ou FQDN da VM com [show de vm de az](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Passos Seguintes
Pode criar várias VMs a partir da imagem de VM de origem. Para fazer alterações à sua imagem: 

- Crie uma VM a partir da sua imagem.
- Torne as atualizações ou alterações de configuração.
- Siga os passos novamente para desaprovisionar, desalocar, generalize e criar uma imagem.
- Utilize esta imagem de novo para as futuras Implantações. Pode eliminar a imagem original.

Para obter mais informações sobre como gerir as suas VMs com a CLI, veja [CLI do Azure](/cli/azure).
