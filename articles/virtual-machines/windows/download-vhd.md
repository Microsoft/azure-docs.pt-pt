---
title: Baixar um VHD do Windows do Azure | Microsoft Docs
description: Baixe um VHD do Windows usando o portal do Azure.
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
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: ebcc8301fa3693880974e45b594be218905e8311
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775392"
---
# <a name="download-a-windows-vhd-from-azure"></a>Baixar um VHD do Windows do Azure

Neste artigo, você aprenderá a baixar um arquivo de VHD (disco rígido virtual) do Windows do Azure usando o portal do Azure.

## <a name="stop-the-vm"></a>Parar a VM

Não será possível baixar um VHD do Azure se ele estiver anexado a uma VM em execução. Você precisa parar a VM para baixar um VHD. Se você quiser usar um VHD como uma [imagem](tutorial-custom-images.md) para criar outras VMs com novos discos, use o [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) para generalizar o sistema operacional contido no arquivo e, em seguida, parar a VM. Para usar o VHD como um disco para uma nova instância de uma VM ou um disco de dados existente, você só precisa parar e desalocar a VM.

Para usar o VHD como uma imagem para criar outras VMs, conclua estas etapas:

1.  Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com/).
2.  [Conecte-se à VM](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  Na VM, abra a janela de prompt de comando como administrador.
4.  Altere o diretório para *%windir%\system32\sysprep* e execute Sysprep. exe.
5.  Na caixa de diálogo ferramenta de preparação do sistema, selecione **entrar no OOBE (experiência inicial do sistema)** e certifique-se de que generalizar está selecionado.
6.  Em opções de desligamento, selecione **desligar**e clique em **OK**. 

Para usar o VHD como um disco para uma nova instância de uma VM ou um disco de dados existente, conclua estas etapas:

1.  No menu Hub na portal do Azure, clique em **máquinas virtuais**.
2.  Selecione a VM na lista.
3.  Na folha da VM, clique em **parar**.

    ![Parar VM](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Gerar URL SAS

Para baixar o arquivo VHD, você precisa gerar uma URL de [assinatura de acesso compartilhado (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Quando a URL é gerada, um tempo de expiração é atribuído à URL.

1.  No menu da folha da VM, clique em **discos**.
2.  Selecione o disco do sistema operacional para a VM e clique em **exportação de disco**.
3.  Defina o tempo de expiração da URL como *36000*.
4.  Clique em **gerar URL**.

    ![Gerar URL](./media/download-vhd/export-generate-new.png)

> [!NOTE]
> O tempo de expiração é aumentado do padrão para fornecer tempo suficiente para baixar o arquivo VHD grande para um sistema operacional Windows Server. Você pode esperar que um arquivo VHD que contenha o sistema operacional Windows Server demore várias horas para baixar, dependendo da sua conexão. Se você estiver baixando um VHD para um disco de dados, o tempo padrão será suficiente. 
> 
> 

## <a name="download-vhd"></a>Baixar VHD

1.  Na URL que foi gerada, clique em baixar o arquivo VHD.

    ![Baixar VHD](./media/download-vhd/export-download.png)

2.  Talvez seja necessário clicar em **salvar** no navegador para iniciar o download. O nome padrão para o arquivo VHD é *ABCD*.

    ![Clique em salvar no navegador](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [carregar um arquivo VHD no Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Crie discos gerenciados de discos não gerenciados em uma conta de armazenamento](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Gerenciar discos do Azure com o PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

