---
title: Baixe um Linux VHD de Azure
description: Baixe um Linux VHD utilizando o Azure CLI e o portal Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 08/21/2019
ms.author: cynthn
ms.openlocfilehash: 02c3ee483e6a31960fd5123070a49f568ac4c690
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78968787"
---
# <a name="download-a-linux-vhd-from-azure"></a>Baixe um Linux VHD de Azure

Neste artigo, aprende-se a descarregar um ficheiro de disco rígido virtual Linux (VHD) do Azure utilizando o portal Azure CLI e Azure. 

Se ainda não o fez, [instale o Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Parar a VM

Um VHD não pode ser descarregado do Azure se estiver ligado a um VM em execução. Tens de parar o VM para descarregar um VHD. Se pretender utilizar um VHD como [imagem](tutorial-custom-images.md) para criar outros VMs com discos novos, precisa de desprovisionar e generalizar o sistema operativo contido no ficheiro e parar o VM. Para utilizar o VHD como um disco para uma nova instância de um VM ou disco de dados existente, basta parar e desalojar o VM.

Para utilizar o VHD como imagem para criar outros VMs, complete estes passos:

1. Utilize o SSH, o nome da conta e o endereço IP público do VM para ligá-lo e desfortá-lo. Pode encontrar o endereço IP público com [o programa ip público da rede Az.](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show) O parâmetro +utilizador também remove a última conta de utilizador provisionada. Se estiver a assar credenciais de conta no VM, deixe de fora este parâmetro +utilizador. O exemplo que se segue remove a última conta de utilizador provisionada:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Inicie sessão na sua conta Azure com [login az](https://docs.microsoft.com/cli/azure/reference-index).
3. Pare e desloque o VM.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Generalize o VM. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Para utilizar o VHD como um disco para uma nova instância de um VM ou disco de dados existente, complete estes passos:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com/).
2.  No menu esquerdo, selecione **Máquinas Virtuais**.
3.  Selecione o VM da lista.
4.  Na página para o VM, selecione **Stop**.

    ![Parar VM](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Gerar URL SAS

Para descarregar o ficheiro VHD, é necessário gerar um URL de assinatura de [acesso partilhado (SAS).](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Quando o URL é gerado, um tempo de validade é atribuído ao URL.

1.  No menu da página para o VM, selecione **Discos**.
2.  Selecione o disco do sistema operativo para o VM e, em seguida, selecione **A Exportação**de Discos .
3.  **Selecione Gerar URL**.

    ![Gerar URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Baixar VHD

1.  Sob o URL gerado, selecione **Descarregue o ficheiro VHD**.
**
    ![Baixar VHD](./media/download-vhd/export-download.png)

2.  Poderá ser necessário selecionar **Guardar** no navegador para iniciar o download. O nome predefinido para o ficheiro VHD é *abcd*.

    ![Selecione Guardar no navegador](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Passos seguintes

- Aprenda a [carregar e crie um VM Linux a partir de disco personalizado com o Azure CLI](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Gerir discos Azure o Azure CLI](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

