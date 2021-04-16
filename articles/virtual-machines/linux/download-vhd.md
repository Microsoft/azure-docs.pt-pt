---
title: Faça o download de um Linux VHD de Azure
description: Faça o download de um Linux VHD utilizando o CLI Azure e o portal Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 8def06990b72d6e08127e8c4f16e0dfd87905d4f
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565190"
---
# <a name="download-a-linux-vhd-from-azure"></a>Faça o download de um Linux VHD de Azure

Neste artigo, você aprende a descarregar um ficheiro de disco rígido virtual Linux (VHD) a partir de Azure usando o portal Azure. 

## <a name="stop-the-vm"></a>Parar a VM

Um VHD não pode ser descarregado do Azure se estiver ligado a um VM em execução. Se quiser manter o VM em funcionamento, pode [criar uma imagem instantânea e, em seguida, descarregar a fotografia](#alternative-snapshot-the-vm-disk).

Para parar o VM:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com/).
2.  No menu esquerdo, selecione **Máquinas Virtuais.**
3.  Selecione o VM da lista.
4.  Na página para o VM, **selecione Stop**.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="Mostra o botão do menu para parar o VM.":::

### <a name="alternative-snapshot-the-vm-disk"></a>Alternativa: Snapshot do disco VM

Tire uma foto do disco para descarregar.

1. Selecione o VM no [portal](https://portal.azure.com).
2. Selecione **Discos** no menu esquerdo e, em seguida, selecione o disco que pretende ser instantâneo. Os detalhes do disco serão apresentados.  
3. Selecione **Criar Snapshot** a partir do menu no topo da página. A página **'Criar instantâneo'** será aberta.
4. Em **Nome,** escreva um nome para o instantâneo. 
5. Para **o tipo Snapshot**, selecione **Full** ou **Incremental**.
6. Quando terminar, selecione **Rever + criar**.

O seu instantâneo será criado em breve e poderá ser usado para descarregar ou criar outro VM a partir de.

> [!NOTE]
> Se não parar primeiro o VM, a fotografia não estará limpa. O instantâneo será no mesmo estado que se o VM tivesse sido ligado ou acidentado no momento em que o instantâneo foi feito.  Embora normalmente seguro, pode causar problemas se as aplicações de execução que executam um tempo não forem resistentes a acidentes.
>  
> Este método só é recomendado para VMs com um único disco de SO. Os VMs com um ou mais discos de dados devem ser interrompidos antes do download ou antes de criar uma imagem instantânea para o disco de oss e cada disco de dados.

## <a name="generate-sas-url"></a>Gerar URL SAS

Para descarregar o ficheiro VHD, é necessário gerar um URL [de assinatura de acesso partilhado (SAS).](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) Quando o URL é gerado, um tempo de validade é atribuído ao URL.

1. No menu da página para o VM, selecione **Discos**.
2. Selecione o disco do sistema operativo para o VM e, em seguida, selecione **A Exportação de Discos**.
1. Se o necessário, atualize o valor do **URL expira em (segundos)** para lhe dar tempo suficiente para concluir o download. O predefinido é de 3600 segundos (uma hora).
3. **Selecione Gerar URL**.
 
      
## <a name="download-vhd"></a>Baixar VHD

1.  Sob o URL que foi gerado, **selecione Descarregue o ficheiro VHD**.

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="Mostra o botão para descarregar o VHD.":::

2.  Poderá necessitar de selecionar **Guardar** no navegador para iniciar o download. O nome predefinido para o ficheiro VHD é *abcd*.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [carregar e criar um Linux VM a partir de disco personalizado com o Azure CLI](upload-vhd.md). 
- [Gerir discos Azure o Azure CLI](tutorial-manage-disks.md).
