---
title: Descarregue um Windows VHD a partir de Azure
description: Faça o download de um Windows VHD utilizando o portal Azure.
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
ms.topic: article
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: d1c98fa4f3572c40279978d787b1719746478a06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75940443"
---
# <a name="download-a-windows-vhd-from-azure"></a>Descarregue um Windows VHD a partir de Azure

Neste artigo, aprende-se a descarregar um ficheiro de disco rígido virtual do Windows (VHD) do Azure utilizando o portal Azure.

## <a name="optional-generalize-the-vm"></a>Opcional: Generalizar o VM

Se pretender utilizar o VHD como [imagem](tutorial-custom-images.md) para criar outros VMs, deve utilizar o [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) para generalizar o sistema operativo. 

Para utilizar o VHD como uma imagem para criar outros VMs, generalize o VM.

1. Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. [Ligue-se ao VM](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3. No VM, abra a janela Command Prompt como administrador.
4. Mude o diretório para *%windir%\system32\sysprep* e run sysprep.exe.
5. Na caixa de diálogo da ferramenta de preparação do sistema, selecione **Enter System Out-of-Box Experience (OOBE) e certifique-se**de que a **Generalização** é selecionada.
6. Nas Opções de Encerramento, selecione **Shutdown**, e, em seguida, clique **EM OK**. 


## <a name="stop-the-vm"></a>Parar a VM

Um VHD não pode ser descarregado do Azure se estiver ligado a um VM em execução. Tens de parar o VM para descarregar um VHD. 

1. No menu Hub no portal Azure, clique em **Máquinas Virtuais**.
1. Selecione o VM da lista.
1. Na lâmina para o VM, clique em **Parar**.


## <a name="generate-download-url"></a>Gerar URL de descarregamento

Para descarregar o ficheiro VHD, é necessário gerar um URL de assinatura de [acesso partilhado (SAS).](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Quando o URL é gerado, um tempo de validade é atribuído ao URL.

1. Na página do VM, clique em **Discos** no menu esquerdo.
1. Selecione o disco do sistema operativo para o VM.
1. Na página do disco, selecione **Disk Export** a partir do menu esquerdo.
1. O tempo de validade padrão do URL é de *3600* segundos. Aumente isto para **36000** para discos Windows OS.
1. Clique em **gerar URL**.

> [!NOTE]
> O tempo de validade é aumentado a partir do predefinido para fornecer tempo suficiente para descarregar o grande ficheiro VHD para um sistema operativo Windows Server. Pode esperar que um ficheiro VHD que contenha o sistema operativo Windows Server deleve várias horas a descarregar dependendo da sua ligação. Se estiver a descarregar um VHD para um disco de dados, o tempo de predefinição é suficiente. 
> 
> 

## <a name="download-vhd"></a>Baixar VHD

1. No âmbito do URL gerado, clique em Baixar o ficheiro VHD.
1. Poderá ter de clicar em **Guardar** no seu navegador para iniciar o download. O nome predefinido para o ficheiro VHD é *abcd*.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [enviar um ficheiro VHD para o Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Crie discos geridos](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)a partir de discos não geridos numa conta de armazenamento .
- [Gerir discos Azure com PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

