---
title: Transferir um VHD do Linux do Azure | Documentos da Microsoft
description: Transferir um VHD do Linux com a CLI do Azure e o portal do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: f72d49a3ab204ce64eb89d0f05630b640c138e0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61390368"
---
# <a name="download-a-linux-vhd-from-azure"></a>Transferir um VHD do Linux do Azure

Neste artigo, irá aprender a transferir um ficheiro de disco rígido virtual (VHD) do Linux do Azure com a CLI do Azure e o portal do Azure. 

Se ainda não o fez, instale [CLI do Azure](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Parar a VM

Não é possível transferir um VHD do Azure se este estiver ligado a uma VM em execução. Terá de parar a VM para transferir um VHD. Se pretender utilizar um VHD como um [imagem](tutorial-custom-images.md) para criar outras VMs com novos discos, precisa desaprovisionar e generalizar o sistema de operativo contido no ficheiro e pare a VM. Para utilizar o VHD como um disco para uma nova instância de uma VM existente ou um disco de dados, apenas terá de parar e desalocar a VM.

Para usar o VHD como uma imagem para criar outras VMs, conclua estes passos:

1. Utilize SSH, o nome da conta e o endereço IP público da VM para ligar à mesma e desaprovisioná-lo. Pode encontrar o endereço IP público com [show de public-ip de rede de az](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). A + parâmetro user também remove a última conta de utilizador aprovisionado. Se estiver implantando as credenciais da conta na VM, deixe esta + parâmetro user. O exemplo seguinte remove a última conta de utilizador aprovisionado:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Inicie sessão na sua conta do Azure com [início de sessão az](https://docs.microsoft.com/cli/azure/reference-index).
3. Parar e desalocar a VM.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Generalize a VM. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Para utilizar o VHD como um disco para uma nova instância de uma VM existente ou um disco de dados, conclua estes passos:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com/).
2.  No menu Hub, clique em **Virtual Machines**.
3.  Selecione a VM a partir da lista.
4.  No painel da VM, clique em **parar**.

    ![Parar VM](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Gerar o URL de SAS

Para transferir o ficheiro VHD, terá de gerar um [assinatura de acesso partilhado (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL. Quando o URL é gerado, é atribuído um prazo de expiração para o URL.

1.  No menu do painel para a VM, clique em **discos**.
2.  Selecione o disco do sistema operativo para a VM e, em seguida, clique em **exportar**.
3.  Clique em **gerar URL**.

    ![Gerar URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Baixe o VHD

1.  Em URL que foi gerado, clique em transferir o ficheiro VHD.

    ![Baixe o VHD](./media/download-vhd/export-download.png)

2.  Poderá ter de clicar em **guardar** no browser para iniciar o download. É o nome predefinido para o ficheiro VHD *abcd*.

    ![Clique em Guardar no browser](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [carregar e criar uma VM do Linux a partir de discos personalizados com a CLI do Azure](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Gerir discos do Azure, a CLI do Azure](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

