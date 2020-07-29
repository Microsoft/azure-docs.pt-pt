---
title: Faça o download de um Linux VHD de Azure
description: Faça o download de um Linux VHD utilizando o CLI Azure e o portal Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/21/2019
ms.author: cynthn
ms.openlocfilehash: 6254be55ae2a1ba6d178d330a41903585da2e50a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289785"
---
# <a name="download-a-linux-vhd-from-azure"></a>Faça o download de um Linux VHD de Azure

Neste artigo, você aprende a baixar um ficheiro de disco rígido virtual Linux (VHD) a partir de Azure usando o portal Azure CLI e Azure. 

Se ainda não o fez, instale [o Azure CLI](/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Parar a VM

Um VHD não pode ser descarregado do Azure se estiver ligado a um VM em execução. Tens de parar o VM para descarregar um VHD. Se pretender utilizar um VHD como [imagem](tutorial-custom-images.md) para criar outros VMs com discos novos, tem de desprovisionar e generalizar o sistema operativo contido no ficheiro e parar o VM. Para utilizar o VHD como disco para uma nova instância de um VM ou disco de dados existente, basta parar e negociar o VM.

Para utilizar o VHD como imagem para criar outros VMs, complete estes passos:

1. Utilize o SSH, o nome da conta e o endereço IP público do VM para ligar e desprovisioná-lo. Pode encontrar o endereço IP público com [o programa público-ip da rede Az](/cli/azure/network/public-ip#az-network-public-ip-show). O parâmetro +user também remove a última conta de utilizador a provisionada. Se estiver a cozer credenciais de conta no VM, deixe de fora este parâmetro +utilizador. O exemplo a seguir remove a última conta de utilizador a provisionada:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Inicie sessão na sua conta Azure com [login az](/cli/azure/reference-index).
3. Pare e transloja o VM.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Generalizar o VM. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Para utilizar o VHD como disco para uma nova instância de um VM ou disco de dados existente, complete estes passos:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com/).
2.  No menu esquerdo, selecione **Máquinas Virtuais.**
3.  Selecione o VM da lista.
4.  Na página para o VM, **selecione Stop**.

    ![Parar VM](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Gerar URL SAS

Para descarregar o ficheiro VHD, é necessário gerar um URL [de assinatura de acesso partilhado (SAS).](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) Quando o URL é gerado, um tempo de validade é atribuído ao URL.

1.  No menu da página para o VM, selecione **Discos**.
2.  Selecione o disco do sistema operativo para o VM e, em seguida, selecione **A Exportação de Discos**.
3.  **Selecione Gerar URL**.

    ![Gerar URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Baixar VHD

1.  Sob o URL que foi gerado, **selecione Descarregue o ficheiro VHD**.
**
    ![Baixar VHD](./media/download-vhd/export-download.png)

2.  Poderá necessitar de selecionar **Guardar** no navegador para iniciar o download. O nome predefinido para o ficheiro VHD é *abcd*.

    ![Selecione Guardar no navegador](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [carregar e criar um Linux VM a partir de disco personalizado com o Azure CLI](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Gerir discos Azure o Azure CLI](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
