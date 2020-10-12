---
title: Faça o download de um Linux VHD de Azure
description: Faça o download de um Linux VHD utilizando o CLI Azure e o portal Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 897cae53e589f4058e5499c0e6e941d4f1d9bb2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87761071"
---
# <a name="download-a-linux-vhd-from-azure"></a>Faça o download de um Linux VHD de Azure

Neste artigo, você aprende a descarregar um ficheiro de disco rígido virtual Linux (VHD) a partir de Azure usando o portal Azure. 

## <a name="stop-the-vm"></a>Parar a VM

Um VHD não pode ser descarregado do Azure se estiver ligado a um VM em execução. Tens de parar o VM para descarregar o VHD. 

1.  Inicie sessão no [portal do Azure](https://portal.azure.com/).
2.  No menu esquerdo, selecione **Máquinas Virtuais.**
3.  Selecione o VM da lista.
4.  Na página para o VM, **selecione Stop**.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="Mostra o botão do menu para parar o VM.":::

## <a name="generate-sas-url"></a>Gerar URL SAS

Para descarregar o ficheiro VHD, é necessário gerar um URL [de assinatura de acesso partilhado (SAS).](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) Quando o URL é gerado, um tempo de validade é atribuído ao URL.

1. No menu da página para o VM, selecione **Discos**.
2. Selecione o disco do sistema operativo para o VM e, em seguida, selecione **A Exportação de Discos**.
1. Se o necessário, atualize o valor do **URL expira em (segundos)** para lhe dar tempo suficiente para concluir o download. O predefinido é de 3600 segundos (uma hora).
3. **Selecione Gerar URL**.
 
      
## <a name="download-vhd"></a>Baixar VHD

1.  Sob o URL que foi gerado, **selecione Descarregue o ficheiro VHD**.

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="Mostra o botão do menu para parar o VM.":::

2.  Poderá necessitar de selecionar **Guardar** no navegador para iniciar o download. O nome predefinido para o ficheiro VHD é *abcd*.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [carregar e criar um Linux VM a partir de disco personalizado com o Azure CLI](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Gerir discos Azure o Azure CLI](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
