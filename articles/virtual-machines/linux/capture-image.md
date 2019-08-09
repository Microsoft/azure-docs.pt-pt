---
title: Capturar uma imagem de uma VM do Linux no Azure usando CLI do Azure | Microsoft Docs
description: Capture uma imagem de uma VM do Azure para usar em implantações em massa usando o CLI do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
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
ms.openlocfilehash: 328748b9dd81834b9c69f81bc0bda60c9ad12cb0
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879963"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Como criar uma imagem de uma máquina virtual ou VHD

<!-- generalize, image - extended version of the tutorial-->

Para criar várias cópias de uma VM (máquina virtual) para uso no Azure, Capture uma imagem da VM ou do VHD do sistema operacional. Para criar uma imagem para implantação, você precisará remover informações pessoais da conta. Nas etapas a seguir, você desprovisiona uma VM existente, a desaloca e cria uma imagem. Você pode usar essa imagem para criar VMs em qualquer grupo de recursos dentro de sua assinatura.

Para criar uma cópia de sua VM do Linux existente para backup ou depuração, ou para carregar um VHD do Linux especializado de uma VM local, consulte [carregar e criar uma VM do Linux de uma imagem de disco personalizada](upload-vhd.md).  

Você pode usar o serviço do **Construtor de imagem de VM do Azure (visualização pública)** para criar sua imagem personalizada, sem necessidade de aprender sobre as ferramentas ou instalar pipelines de compilação, simplesmente fornecendo uma configuração de imagem, e o construtor de imagem criará a imagem. Para obter mais informações, consulte [introdução com o construtor de imagem de VM do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

Além disso, use o Packer para criar sua configuração personalizada. Para obter mais informações, consulte [como usar o Packer para criar imagens de máquina virtual do Linux no Azure](build-image-with-packer.md).

Você precisará dos seguintes itens antes de criar uma imagem:

* Uma VM do Azure criada no modelo de implantação do Gerenciador de recursos que usa discos gerenciados. Se você ainda não criou uma VM do Linux, poderá usar o [portal](quick-create-portal.md), o [CLI do Azure](quick-create-cli.md)ou os [modelos do Resource Manager](create-ssh-secured-vm-from-template.md). Configure a VM conforme necessário. Por exemplo, [adicione discos de dados](add-disk.md), aplique atualizações e instale aplicativos. 

* As [CLI do Azure](/cli/azure/install-az-cli2) mais recentes instaladas e conectadas a uma conta do Azure com [AZ login](/cli/azure/reference-index#az-login).

## <a name="prefer-a-tutorial-instead"></a>Prefere um tutorial?

Para obter uma versão simplificada deste artigo e para testar, avaliar ou aprender sobre VMs no Azure, consulte [criar uma imagem personalizada de uma VM do Azure usando a CLI](tutorial-custom-images.md).  Caso contrário, continue lendo aqui para obter o panorama completo.


## <a name="step-1-deprovision-the-vm"></a>Passo 1: Desaprovisionar a VM
Primeiro, você desprovisionará a VM usando o agente de VM do Azure para excluir dados e arquivos específicos do computador. Use o `waagent` comando com o `-deprovision+user` parâmetro em sua VM Linux de origem. Para obter mais informações, veja o [Guia de utilizador do Agente Linux do Azure](../extensions/agent-linux.md).

1. Conecte-se à sua VM do Linux com um cliente SSH.
2. Na janela SSH, digite o seguinte comando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Só execute esse comando em uma VM que você capturará como uma imagem. Esse comando não garante que a imagem esteja desmarcada de todas as informações confidenciais ou seja adequada para redistribuição. O `+user` parâmetro também remove a última conta de usuário provisionada. Para manter as credenciais da conta de usuário na VM, `-deprovision`Use apenas.
 
3. Digite **y** para continuar. Você pode adicionar o `-force` parâmetro para evitar esta etapa de confirmação.
4. Após a conclusão do comando, digite **Exit** para fechar o cliente SSH.  A VM ainda estará em execução neste ponto.

## <a name="step-2-create-vm-image"></a>Passo 2: Criar imagem de VM
Use o CLI do Azure para marcar a VM como generalizada e capturar a imagem. Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo pelos seus próprios valores. Os nomes de parâmetrode exemplo incluem MyResource, *myVnet*e *myVM*.

1. Desaloque a VM desprovisionada com [AZ VM DEALLOCATE](/cli/azure/vm). O exemplo a seguir Desaloca a VM denominada *myVM* no grupo de recursoschamado MyResource Group.  
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```
    
    Aguarde até que a VM seja completamente desalocada antes de prosseguir. Isso pode levar alguns minutos para ser concluído.  A VM é desligada durante a desalocação.

2. Marque a VM como generalizada com [AZ VM generalize](/cli/azure/vm). O exemplo a seguir marca a VM chamada *myVM* no grupo de recursos chamado MyResource Group como generalizado.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

    Uma VM que foi generalizada não pode mais ser reiniciada.

3. Crie uma imagem do recurso da VM com [AZ Image Create](/cli/azure/image#az-image-create). O exemplo a seguir cria uma imagem chamada MyImage no grupo de recursos chamado MyResource Group usando o recurso de VM chamado *myVM*.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > A imagem é criada no mesmo grupo de recursos que a VM de origem. Você pode criar VMs em qualquer grupo de recursos dentro de sua assinatura a partir desta imagem. De uma perspectiva de gerenciamento, talvez você queira criar um grupo de recursos específico para seus recursos e imagens de VM.
   >
   > Se você quiser armazenar a imagem em um armazenamento resistente a zona, precisará criá-la em uma região que dê suporte a [zonas de disponibilidade](../../availability-zones/az-overview.md) e `--zone-resilient true` inclua o parâmetro.
   
Esse comando retorna JSON que descreve a imagem da VM. Salve essa saída para referência posterior.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Passo 3: Criar uma VM a partir da imagem capturada
Crie uma VM usando a imagem que você criou com [AZ VM Create](/cli/azure/vm). O exemplo a seguir cria uma VM chamada *myVMDeployed* a partir da imagem chamada MyImage.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Criando a VM em outro grupo de recursos 

Você pode criar VMs de uma imagem em qualquer grupo de recursos dentro de sua assinatura. Para criar uma VM em um grupo de recursos diferente da imagem, especifique a ID de recurso completo para a imagem. Use a [lista de imagens AZ](/cli/azure/image#az-image-list) para exibir uma lista de imagens. O resultado será semelhante ao seguinte exemplo.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

O exemplo a seguir usa [AZ VM Create](/cli/azure/vm#az-vm-create) para criar uma VM em um grupo de recursos diferente da imagem de origem, ESPECIFICANDO a ID de recurso de imagem.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Passo 4: Verificar a implementação

SSH na máquina virtual que você criou para verificar a implantação e começar a usar a nova VM. Para se conectar via SSH, localize o endereço IP ou o FQDN de sua VM com [AZ VM show](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Passos Seguintes
Você pode criar várias VMs a partir de sua imagem de VM de origem. Para fazer alterações na imagem: 

- Crie uma VM com base em sua imagem.
- Faça atualizações ou alterações de configuração.
- Siga as etapas novamente para desprovisionar, desalocar, generalizar e criar uma imagem.
- Use essa nova imagem para implantações futuras. Você pode excluir a imagem original.

Para obter mais informações sobre como gerenciar suas VMs com a CLI, consulte [CLI do Azure](/cli/azure).
