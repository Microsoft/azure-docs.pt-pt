---
title: Baixar um VHD do Linux do Azure | Microsoft Docs
description: Baixe um VHD do Linux usando o CLI do Azure e o portal do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/21/2019
ms.author: cynthn
ms.openlocfilehash: 21720ead788a505f86c4d35eb5a39943ff4a2e12
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981010"
---
# <a name="download-a-linux-vhd-from-azure"></a>Baixar um VHD do Linux do Azure

Neste artigo, você aprende a baixar um arquivo de VHD (disco rígido virtual) do Linux do Azure usando o CLI do Azure e portal do Azure. 

Se você ainda não fez isso, instale o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Parar a VM

Não será possível baixar um VHD do Azure se ele estiver anexado a uma VM em execução. Você precisa parar a VM para baixar um VHD. Se você quiser usar um VHD como uma [imagem](tutorial-custom-images.md) para criar outras VMs com novos discos, será necessário desprovisionar e generalizar o sistema operacional contido no arquivo e parar a VM. Para usar o VHD como um disco para uma nova instância de uma VM ou um disco de dados existente, você só precisa parar e desalocar a VM.

Para usar o VHD como uma imagem para criar outras VMs, conclua estas etapas:

1. Use SSH, o nome da conta e o endereço IP público da VM para se conectar a ela e desprovisioná-la. Você pode encontrar o endereço IP público com [AZ Network Public-IP show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). O parâmetro + User também remove a última conta de usuário provisionada. Se você estiver trazendo credenciais de conta na VM, deixe esse parâmetro + usuário. O exemplo a seguir remove a última conta de usuário provisionada:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Entre em sua conta do Azure com [AZ login](https://docs.microsoft.com/cli/azure/reference-index).
3. Pare e desaloque a VM.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Generalizar a VM. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Para usar o VHD como um disco para uma nova instância de uma VM ou um disco de dados existente, conclua estas etapas:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com/).
2.  No menu à esquerda, selecione **máquinas virtuais**.
3.  Selecione a VM na lista.
4.  Na página da VM, selecione **parar**.

    ![Parar VM](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Gerar URL SAS

Para baixar o arquivo VHD, você precisa gerar uma URL de [assinatura de acesso compartilhado (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Quando a URL é gerada, um tempo de expiração é atribuído à URL.

1.  No menu da página da VM, selecione **discos**.
2.  Selecione o disco do sistema operacional para a VM e, em seguida, selecione **exportação de disco**.
3.  Selecione **gerar URL**.

    ![Gerar URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Baixar VHD

1.  Na URL que foi gerada, selecione **baixar o arquivo VHD**.
**
    ![Baixar VHD](./media/download-vhd/export-download.png)

2.  Talvez seja necessário selecionar **salvar** no navegador para iniciar o download. O nome padrão para o arquivo VHD é *ABCD*.

    ![Selecione salvar no navegador](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [carregar e criar uma VM do Linux de um disco personalizado com o CLI do Azure](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Gerenciar discos do Azure o CLI do Azure](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

