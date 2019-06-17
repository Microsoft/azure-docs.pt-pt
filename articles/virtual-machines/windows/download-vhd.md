---
title: Transferir um VHD do Windows do Azure | Documentos da Microsoft
description: Transferir um VHD do Windows com o portal do Azure.
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
ms.openlocfilehash: 3d44a4a723c39bf9780475a2ac3088da94285f6e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61076354"
---
# <a name="download-a-windows-vhd-from-azure"></a>Transferir um VHD do Windows do Azure

Neste artigo, irá aprender a transferir um ficheiro de disco rígido virtual (VHD) do Windows Azure utilizando o portal do Azure.

## <a name="stop-the-vm"></a>Parar a VM

Não é possível transferir um VHD do Azure se este estiver ligado a uma VM em execução. Terá de parar a VM para transferir um VHD. Se pretender utilizar um VHD como um [imagem](tutorial-custom-images.md) para criar outras VMs com novos discos, utilize [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) para generalizar o sistema de operativo contido no ficheiro e, em seguida, pare a VM. Para utilizar o VHD como um disco para uma nova instância de uma VM existente ou um disco de dados, apenas terá de parar e desalocar a VM.

Para usar o VHD como uma imagem para criar outras VMs, conclua estes passos:

1.  Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com/).
2.  [Ligar à VM](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  Na VM, abra a janela de linha de comandos como administrador.
4.  Altere o diretório para *%windir%\system32\sysprep* e execute sysprep.exe.
5.  Na caixa de diálogo Ferramenta de preparação do sistema, selecione **Enter System Out-of-Box Experience (OOBE)** e certifique-se de que **Generalize** está selecionada.
6.  Nas opções de encerramento, selecione **encerramento**e, em seguida, clique em **OK**. 

Para utilizar o VHD como um disco para uma nova instância de uma VM existente ou um disco de dados, conclua estes passos:

1.  No menu Hub no portal do Azure, clique em **máquinas virtuais**.
2.  Selecione a VM a partir da lista.
3.  No painel da VM, clique em **parar**.

    ![Parar VM](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Gerar o URL de SAS

Para transferir o ficheiro VHD, terá de gerar um [assinatura de acesso partilhado (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL. Quando o URL é gerado, é atribuído um prazo de expiração para o URL.

1.  No menu do painel para a VM, clique em **discos**.
2.  Selecione o disco do sistema operativo para a VM e, em seguida, clique em **exportar**.
3.  Definir a hora de expiração do URL para *36000*.
4.  Clique em **gerar URL**.

    ![Gerar URL](./media/download-vhd/export-generate.png)

> [!NOTE]
> A hora de expiração é aumentada do padrão para fornecer tempo suficiente para transferir o ficheiro VHD de grandes para um sistema operativo Windows Server. Pode esperar um arquivo VHD que contém o sistema operativo do Windows Server pode levar várias horas para transferir consoante a ligação. Se estiver a transferir um VHD para um disco de dados, o tempo predefinido é suficiente. 
> 
> 

## <a name="download-vhd"></a>Baixe o VHD

1.  Em URL que foi gerado, clique em transferir o ficheiro VHD.

    ![Baixe o VHD](./media/download-vhd/export-download.png)

2.  Poderá ter de clicar em **guardar** no browser para iniciar o download. É o nome predefinido para o ficheiro VHD *abcd*.

    ![Clique em Guardar no browser](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [carregar um ficheiro VHD para o Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Criar discos geridos a partir de discos não geridos numa conta de armazenamento](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Gerir discos do Azure com o PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

