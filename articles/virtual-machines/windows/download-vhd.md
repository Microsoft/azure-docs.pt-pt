---
title: Baixar um VHD do Windows do Azure
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
ms.topic: article
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: d1c98fa4f3572c40279978d787b1719746478a06
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940443"
---
# <a name="download-a-windows-vhd-from-azure"></a>Baixar um VHD do Windows do Azure

Neste artigo, você aprenderá a baixar um arquivo de VHD (disco rígido virtual) do Windows do Azure usando o portal do Azure.

## <a name="optional-generalize-the-vm"></a>Opcional: generalizar a VM

Se você quiser usar o VHD como uma [imagem](tutorial-custom-images.md) para criar outras VMS, deverá usar o [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) para generalizar o sistema operacional. 

Para usar o VHD como uma imagem para criar outras VMs, generalizar a VM.

1. Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. [Conecte-se à VM](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3. Na VM, abra a janela de prompt de comando como administrador.
4. Altere o diretório para *%windir%\system32\sysprep* e execute Sysprep. exe.
5. Na caixa de diálogo ferramenta de preparação do sistema, selecione **entrar no OOBE (experiência inicial do sistema)** e certifique-se de que **generalizar** está selecionado.
6. Em opções de desligamento, selecione **desligar**e clique em **OK**. 


## <a name="stop-the-vm"></a>Parar a VM

Não será possível baixar um VHD do Azure se ele estiver anexado a uma VM em execução. Você precisa parar a VM para baixar um VHD. 

1. No menu Hub na portal do Azure, clique em **máquinas virtuais**.
1. Selecione a VM na lista.
1. Na folha da VM, clique em **parar**.


## <a name="generate-download-url"></a>Gerar URL de download

Para baixar o arquivo VHD, você precisa gerar uma URL de [assinatura de acesso compartilhado (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Quando a URL é gerada, um tempo de expiração é atribuído à URL.

1. Na página da VM, clique em **discos** no menu à esquerda.
1. Selecione o disco do sistema operacional para a VM.
1. Na página do disco, selecione exportação de **disco** no menu à esquerda.
1. O tempo de expiração padrão da URL é de *3600* segundos. Aumente isso para **36000** para discos do sistema operacional Windows.
1. Clique em **gerar URL**.

> [!NOTE]
> O tempo de expiração é aumentado do padrão para fornecer tempo suficiente para baixar o arquivo VHD grande para um sistema operacional Windows Server. Você pode esperar que um arquivo VHD que contenha o sistema operacional Windows Server demore várias horas para baixar, dependendo da sua conexão. Se você estiver baixando um VHD para um disco de dados, o tempo padrão será suficiente. 
> 
> 

## <a name="download-vhd"></a>Baixar VHD

1. Na URL que foi gerada, clique em baixar o arquivo VHD.
1. Talvez seja necessário clicar em **salvar** no navegador para iniciar o download. O nome padrão para o arquivo VHD é *ABCD*.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [carregar um arquivo VHD no Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Crie discos gerenciados de discos não gerenciados em uma conta de armazenamento](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Gerenciar discos do Azure com o PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

