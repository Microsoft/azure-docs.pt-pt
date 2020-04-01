---
title: Tutorial - Crie imagens VM personalizadas com o Azure CLI
description: Neste tutorial, vai aprender a utilizar a CLI do Azure para criar uma imagem de máquina virtual personalizada no Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e629c605c0ffd3a7e0e1e53c3d661642b9dd01b7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74034502"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Tutorial: Criar uma imagem personalizada de uma VM do Azure com a CLI do Azure

As imagens personalizadas são como imagens do marketplace, mas são criadas por si. As imagens personalizadas podem ser utilizadas para configurações do programa de arranque do sistema, como o pré-carregamento de aplicações, configurações de aplicação e outras configurações do SO. Neste tutorial, vai criar a sua imagem personalizada de uma máquina virtual do Azure. Saiba como:

> [!div class="checklist"]
> * Desaprovisionar e generalizar VMs
> * Criar uma imagem personalizada
> * Criar uma VM a partir de uma imagem personalizada
> * Listar todas as imagens na sua subscrição
> * Eliminar uma imagem

Este tutorial utiliza o CLI dentro da [Cloud Shell Azure,](https://docs.microsoft.com/azure/cloud-shell/overview)que é constantemente atualizada para a versão mais recente. Para abrir a Cloud Shell, selecione **Experimente a** partir do topo de qualquer bloco de código.

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

Os passos abaixo detalham como tornar uma VM existente numa imagem personalizada reutilizável que pode utilizar para criar novas instâncias da VM.

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente. Se necessário, esta amostra de [guião](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) pode criar uma para si. Quando trabalhar no tutorial, substitua o grupo de recursos e os nomes da VM sempre que preciso.

## <a name="create-a-custom-image"></a>Criar uma imagem personalizada

Para criar uma imagem de uma máquina virtual, tem de preparar a VM ao desaprovisionar, desalocar e, em seguida, marcar a VM de origem como generalizada. Assim que a VM tiver sido preparada, pode criar uma imagem.

### <a name="deprovision-the-vm"></a>Desaprovisionar a VM 

O desaprovisionamento generaliza a VM ao remover informações específicas do computador. Esta generalização torna possível implementar várias VMs a partir de uma única imagem. Durante o desaprovisionamento, o nome do anfitrião é reposto para *localhost.localdomain*. Também são eliminadas as chaves de anfitrião do SSH, as configurações do servidor de nomes, a palavra-passe de raiz e as concessões DHCP em cache.

> [!WARNING]
> A desprovisionamento e a marcação do VM como generalizado tornarão a fonte VM inutilizável, não podendo ser reiniciada. 

Para desaprovisionar a VM, utilize o agente da VM do Azure (waagent). O agente da VM do Azure é instalado na VM e gere o aprovisionamento e a interação com o Controlador dos Recursos de Infraestrutura do Azure. Para obter mais informações, veja o [Guia de utilizador do Agente Linux do Azure](../extensions/agent-linux.md).

Ligue à VM atual através do SSH e execute o comando para desaprovisionar a VM. Com o argumento `+user`, a última conta de utilizador aprovisionada e quaisquer dados associados também são eliminados. Substitua o endereço IP de exemplo pelo endereço IP público da VM.

SSH para a VM.
```bash
ssh azureuser@52.174.34.95
```
Desaprovisione a VM.

```bash
sudo waagent -deprovision+user -force
```
Feche a sessão SSH.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Desaloque e marque a VM como generalizada

Para criar uma imagem, a VM tem de ser desalocada. Desaloque a VM com [az vm deallocate](/cli//azure/vm). 
   
```azurecli-interactive 
az vm deallocate --resource-group myResourceGroup --name myVM
```

Por fim, defina o estado da VM como generalizado com [az vm generalize](/cli//azure/vm), para que a plataforma do Azure saiba que a VM foi generalizada. Pode criar apenas uma imagem a partir de uma VM generalizada.
   
```azurecli-interactive 
az vm generalize --resource-group myResourceGroup --name myVM
```

### <a name="create-the-image"></a>Criar a imagem

Já pode criar uma imagem da VM com [az image create](/cli//azure/image). O exemplo seguinte cria uma imagem designada *myImage* a partir de uma VM designada *myVM*.
   
```azurecli-interactive 
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>Criar VMs a partir da imagem

Agora que tem uma imagem, pode criar uma ou mais VMs novas a partir da imagem com [az vm create](/cli/azure/vm). O exemplo seguinte cria uma VM denominada *myVMfromImage* a partir de uma imagem denominada *myImage*.

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Recomendamos que limite o número de implementações simultâneas a 20 VMs de uma única imagem. Se estiver a planear implementações em larga escala e simultâneas de mais de 20 VMs a partir da mesma imagem personalizada, deve utilizar uma Galeria de [Imagem Partilhada](shared-image-galleries.md) com múltiplas réplicas de imagem. 

## <a name="image-management"></a>Gestão das imagens 

Seguem-se alguns exemplos de tarefas comuns de gestão de imagens e como concluí-las com a CLI do Azure.

Liste todas as imagens por nome num formato de tabela.

```azurecli-interactive 
az image list \
    --resource-group myResourceGroup
```

Elimine uma imagem. Este exemplo elimina a imagem denominada *myOldImage* do *myResourceGroup*.

```azurecli-interactive 
az image delete \
    --name myOldImage \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma imagem de VM personalizada. Aprendeu a:

> [!div class="checklist"]
> * Desaprovisionar e generalizar VMs
> * Criar uma imagem personalizada
> * Criar uma VM a partir de uma imagem personalizada
> * Listar todas as imagens na sua subscrição
> * Eliminar uma imagem

Avance para o próximo tutorial para saber mais sobre máquinas virtuais de elevada disponibilidade.

> [!div class="nextstepaction"]
> [Criar VMs altamente disponíveis.](tutorial-availability-sets.md)

