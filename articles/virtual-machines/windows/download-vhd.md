---
title: Faça o download de um Windows VHD a partir de Azure
description: Faça o download de um VHD do Windows utilizando o portal Azure.
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: e87f2436f69abed4c0d0ee415226f99b52f2e804
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82085370"
---
# <a name="download-a-windows-vhd-from-azure"></a>Faça o download de um Windows VHD a partir de Azure

Neste artigo, aprende-se a descarregar um ficheiro virtual do Windows (VHD) a partir do Azure utilizando o portal Azure.

## <a name="optional-generalize-the-vm"></a>Opcional: Generalizar o VM

Se pretender utilizar o VHD como [imagem](tutorial-custom-images.md) para criar outros VMs, deve utilizar [o Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) para generalizar o sistema operativo. 

Para utilizar o VHD como imagem para criar outros VMs, generalize o VM.

1. Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. [Ligue-se ao VM](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3. No VM, abra a janela de aviso de comando como administrador.
4. Mude o diretório para *%windir%\system32\sysprep* e executar sysprep.exe.
5. Na caixa de diálogo de ferramentas de preparação do sistema, selecione **Enter System Out-of-Box Experience (OOBE) e certifique-se**de que a **Generalização** está selecionada.
6. Nas Opções de Encerramento, **selecione Desligar**e, em seguida, clique **em OK**. 


## <a name="stop-the-vm"></a>Parar a VM

Um VHD não pode ser descarregado do Azure se estiver ligado a um VM em execução. Tens de parar o VM para descarregar um VHD. 

1. No menu Hub no portal Azure, clique em **Máquinas Virtuais.**
1. Selecione o VM da lista.
1. Na lâmina para o VM, clique em **Stop**.


## <a name="generate-download-url"></a>Gerei URL de descarregamento

Para descarregar o ficheiro VHD, é necessário gerar um URL [de assinatura de acesso partilhado (SAS).](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Quando o URL é gerado, um tempo de validade é atribuído ao URL.

1. Na página para o VM, clique em **Discos** no menu esquerdo.
1. Selecione o disco do sistema operativo para o VM.
1. Na página para o disco, selecione **'Exportação** de Disco' a partir do menu esquerdo.
1. O tempo de validade padrão do URL é de *3600* segundos. Aumente isto para **36000** para discos WINDOWS OS.
1. Clique **em Gerar URL**.

> [!NOTE]
> O tempo de validade é aumentado a partir do padrão para fornecer tempo suficiente para descarregar o grande ficheiro VHD para um sistema operativo Windows Server. Pode esperar que um ficheiro VHD que contenha o sistema operativo Windows Server descodua várias horas para ser descarregado dependendo da sua ligação. Se estiver a descarregar um VHD para um disco de dados, o tempo predefinido é suficiente. 
> 
> 

## <a name="download-vhd"></a>Baixar VHD

1. Sob o URL que foi gerado, clique em Baixar o ficheiro VHD.
1. Pode ter de clicar em **Guardar** no seu browser para iniciar o download. O nome predefinido para o ficheiro VHD é *abcd*.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [enviar um ficheiro VHD para o Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Criar discos geridos a partir de discos não geridos numa conta de armazenamento](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Gerir discos Azure com PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

