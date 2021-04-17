---
title: Faça o download de um Windows VHD a partir de Azure
description: Faça o download de um VHD do Windows utilizando o portal Azure.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: 32b9753b79273ce747d00cba077dd8a5ee6d724d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565292"
---
# <a name="download-a-windows-vhd-from-azure"></a>Faça o download de um Windows VHD a partir de Azure

Neste artigo, aprende-se a descarregar um ficheiro virtual do Windows (VHD) a partir do Azure utilizando o portal Azure.

## <a name="optional-generalize-the-vm"></a>Opcional: Generalizar o VM

Se pretender utilizar o VHD como [imagem](tutorial-custom-images.md) para criar outros VMs, deve utilizar [o Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) para generalizar o sistema operativo. Caso contrário, terá de fazer uma cópia do disco para cada VM que pretende criar.

Para utilizar o VHD como imagem para criar outros VMs, generalize o VM.

1. Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. [Ligue-se ao VM](connect-logon.md). 
3. No VM, abra a janela de aviso de comando como administrador.
4. Mude o diretório para *%windir%\system32\sysprep* e executar sysprep.exe.
5. Na caixa de diálogo de ferramentas de preparação do sistema, selecione **Enter System Out-of-Box Experience (OOBE) e certifique-se** de que a **Generalização** está selecionada.
6. Nas Opções de Encerramento, **selecione Desligar** e, em seguida, clique **em OK**. 

Se não quiser generalizar o seu VM atual, ainda pode criar uma imagem generalizada fazendo primeiro [uma imagem do disco de SO,](#alternative-snapshot-the-vm-disk)criando um novo VM a partir do instantâneo e, em seguida, generalizando a cópia.

## <a name="stop-the-vm"></a>Parar a VM

Um VHD não pode ser descarregado do Azure se estiver ligado a um VM em execução. Se quiser manter o VM em funcionamento, pode [criar uma imagem instantânea e, em seguida, descarregar a fotografia](#alternative-snapshot-the-vm-disk).

1. No menu Hub no portal Azure, clique em **Máquinas Virtuais.**
1. Selecione o VM da lista.
1. Na lâmina para o VM, clique em **Stop**.

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

## <a name="generate-download-url"></a>Gerei URL de descarregamento

Para descarregar o ficheiro VHD, é necessário gerar um URL [de assinatura de acesso partilhado (SAS).](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) Quando o URL é gerado, um tempo de validade é atribuído ao URL.

1. Na página para o VM, clique em **Discos** no menu esquerdo.
1. Selecione o disco do sistema operativo para o VM.
1. Na página para o disco, selecione **'Exportação** de Disco' a partir do menu esquerdo.
1. O tempo de validade padrão do URL é de *3600* segundos (uma hora). Pode ser necessário aumentar isto para discos WINDOWS OS ou grandes discos de dados. **36000** segundos (10 horas) é geralmente suficiente.
1. Clique **em Gerar URL**.

> [!NOTE]
> O tempo de validade é aumentado a partir do padrão para fornecer tempo suficiente para descarregar o grande ficheiro VHD para um sistema operativo Windows Server. Os Grandes VHDs podem demorar até várias horas a descarregar dependendo da sua ligação e do tamanho do VM. 
> 
> 

## <a name="download-vhd"></a>Baixar VHD

1. Sob o URL que foi gerado, clique em Baixar o ficheiro VHD.
1. Pode ter de clicar em **Guardar** no seu browser para iniciar o download. O nome predefinido para o ficheiro VHD é *abcd*.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [enviar um ficheiro VHD para o Azure](upload-generalized-managed.md). 
- [Criar discos geridos a partir de discos não geridos numa conta de armazenamento](attach-disk-ps.md).
- [Gerir discos Azure com PowerShell](tutorial-manage-data-disk.md).
