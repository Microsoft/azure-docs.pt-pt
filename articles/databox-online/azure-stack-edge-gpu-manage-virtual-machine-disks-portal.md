---
title: Gerir discos VMs em Azure Stack Edge Pro GPU, Pro R, Mini R via Portal Azure
description: Aprenda a gerir discos incluindo adicionar ou desvincular um disco de dados em VMs que são implantados no seu GPU Azure Stack Edge Pro, Azure Stack Edge Pro R e Azure Stack Edge Mini R através do portal Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage disks on a VM running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: dff3f4bafdb35d89e8f1792c1aca68824a9bc685
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080557"
---
# <a name="use-the-azure-portal-to-manage-disks-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Utilize o portal Azure para gerir discos nos VMs no gpu Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Pode fornecer discos nas máquinas virtuais (VMs) implantados no seu dispositivo Azure Stack Edge Pro utilizando o portal Azure. Os discos são aprovisionados no dispositivo através do Gestor de Recursos Azure local e consomem a capacidade do dispositivo. As operações como a adição de um disco, a desprendimento de um disco podem ser feitas através do portal Azure, que por sua vez faz chamadas para o Gestor de Recursos Azure local para o fornecimento do armazenamento. 

Este artigo explica como adicionar um disco de dados a um VM existente, separar um disco de dados e, finalmente, redimensionar o próprio VM através do portal Azure. 

        
## <a name="about-disks-on-vms"></a>Sobre discos em VMs

O seu VM pode ter um disco de so e um disco de dados. Todas as máquinas virtuais implantadas no seu dispositivo têm um disco de sistema operativo ligado. Este disco de oss tem um SISTEMA pré-instalado, que foi selecionado quando o VM foi criado. Este disco contém o volume de arranque.

> [!NOTE]
> Não é possível alterar o tamanho do disco DE para o VM no seu dispositivo. O tamanho do disco DE é determinado pelo tamanho VM que selecionou. 


Um disco de dados, por outro lado, é um disco gerido ligado ao VM em execução no seu dispositivo. Um disco de dados é usado para armazenar dados de aplicações. Os discos de dados são normalmente unidades SCSI. O tamanho do VM determina quantos discos de dados pode anexar a um VM. Por padrão, o armazenamento premium é usado para hospedar os discos.

Um VM implantado no seu dispositivo pode por vezes conter um disco temporário. O disco temporário fornece armazenamento de curto prazo para aplicações e processos, e destina-se apenas a armazenar dados como página ou ficheiros de troca. Os dados do disco temporário podem ser perdidos durante um evento de manutenção ou quando se recolocar um VM. Durante um reboot padrão bem sucedido do VM, os dados sobre o disco temporário persistirão. 


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a gerir os discos nos VMs em execução no seu dispositivo através do portal Azure, certifique-se de que:


1. Tem acesso a um dispositivo GPU Azure Stack Edge Pro ativado. Também ativou uma interface de rede para calcular o seu dispositivo. Esta ação cria um interruptor virtual na interface de rede no seu VM. 
    1. Na UI local do seu dispositivo, vá ao **Compute.** Selecione a interface de rede que utilizará para criar um interruptor virtual.

        > [!IMPORTANT] 
        > Só é possível configurar uma porta para o cálculo.

    1. Ativar o cálculo na interface de rede. A Azure Stack Edge Pro GPU cria e gere um interruptor virtual correspondente a essa interface de rede.

1. Tem pelo menos um VM implantado no seu dispositivo. Para criar este VM, consulte as instruções em [Implementar VM no seu Azure Stack Edge Pro através do portal Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).



## <a name="add-a-data-disk"></a>Adicionar um disco de dados

Siga estes passos para adicionar um disco a uma máquina virtual implantada no seu dispositivo. 

1. Vá à máquina virtual à qual pretende adicionar um disco de dados e, em seguida, vá para a página **'Vista Geral'.** Selecione os **Discos**.
    
    ![Selecione Discos na página overview](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-1.png)

1. Na lâmina **Discos,** em **Discos de Dados,** selecione **Criar e prenda um novo disco**.

    ![Criar e anexar um novo disco](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-2.png)

1. Na **criação de uma nova** lâmina de disco, introduza os seguintes parâmetros:

    
    |Campo  |Descrição  |
    |---------|---------|
    |Nome     | Um nome único dentro do grupo de recursos. O nome não pode ser alterado após a criação do disco de dados.     |
    |Tamanho| O tamanho do seu disco de dados em GiB. O tamanho máximo de um disco de dados é determinado pelo tamanho VM que selecionou. Ao a provisionar um disco, deve também considerar o espaço real no seu dispositivo e outras cargas de trabalho VM que estão a funcionar que consomem capacidade.  |         

    ![Criar uma nova lâmina de disco](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-3.png)

    Selecione **OK** e proceda.

1. Na página **'Vista Geral',** em **Discos,** verá uma entrada correspondente ao novo disco. Aceite o predefinido ou atribua um Número de Unidade Lógica válido (LUN) ao disco e **selecione Guardar**. Um LUN é um identificador único para um disco SCSI. Para mais informações, veja [o que é um LUN?](../virtual-machines/linux/azure-to-guest-disk-mapping.md#what-is-a-lun)

    ![Disco novo na página do visão geral](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-4.png)

1. Verá uma notificação de que a criação de discos está em andamento. Depois de o disco ser criado com sucesso, a máquina virtual é atualizada. 

    ![Notificação para criação de discos](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-5.png)

1. Volte para a página **geral.** A lista de atualizações de discos para exibir o disco de dados recém-criado.

    ![Lista atualizada de discos de dados](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-6.png)


## <a name="change-a-data-disk"></a>Alterar um disco de dados

Siga estes passos para alterar um disco associado a uma máquina virtual implantada no seu dispositivo.

1. Vá à máquina virtual que tem o disco de dados para alterar e vá para a página **'Vista Geral'.** Selecione os **Discos**.

1. Na lista de discos de dados, selecione o disco que pretende alterar. Na extrema direita do disco selecionado, selecione o ícone de edição (lápis).  

    ![Selecione um disco para alterar](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-1.png)

1. Na lâmina do **disco Change,** só é possível alterar o tamanho do disco. O nome associado ao disco não pode ser alterado uma vez que é criado. Altere o **Tamanho** e guarde as alterações.

    ![Alterar o tamanho do disco de dados](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-2.png)

    > [!NOTE]
    > Só se pode expandir um disco de dados, não se pode encolher o disco.

1. Na página **'Vista Geral',** a lista de discos atualiza-se para exibir o disco atualizado.


## <a name="attach-an-existing-disk"></a>Anexar um disco existente

Siga estes passos para anexar um disco existente à máquina virtual implantada no seu dispositivo.

1. Vá à máquina virtual à qual deseja anexar o disco existente e, em seguida, vá à página **'Vista Geral'.** Selecione os **Discos**.
    
    ![Selecione Discos ](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. Na lâmina **Discos,** em **Discos de Dados,** selecione **Fixe um disco existente**.

    ![Selecione anexar um disco existente](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-1.png)

1. Aceite a LUN predefinido ou atribua um LUN válido. Escolha um disco de dados existente na lista de abandono. Selecione Guardar.

    ![Selecione um disco existente](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-2.png)

    **Selecione Save** e proceda.

1. Verá uma notificação de que a máquina virtual está atualizada. Depois de atualizado o VM, volte à página **'Vista Geral'.** Refresque a página para ver o disco recém-anexado na lista de discos de dados.

    ![Ver lista atualizada de discos de dados na página de visão geral](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="detach-a-data-disk"></a>Desanexar um disco de dados

Siga estes passos para desprender ou remova um disco de dados associado a uma máquina virtual implantada no seu dispositivo.

> [!NOTE]
> - Pode remover um disco de dados enquanto o VM está em funcionamento. Certifique-se de que nada está a utilizar ativamente o disco antes de o separar do VM.
> - Se separar um disco, este não é apagado automaticamente.

1. Vá à máquina virtual a partir da qual deseja desprender um disco de dados e ir para a página **'Vista Geral'.** Selecione os **Discos**.

    ![Selecione Discos](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. Na lista de discos, selecione o disco que pretende desmontar. Na extrema direita do disco selecionado, selecione o ícone de desprendimento (cruz). A entrada selecionada será separada. Selecione **Guardar**. 

    ![Selecione um disco para desprender](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/detach-data-disk-1.png)

1. Depois do disco ser desligado, a máquina virtual é atualizada. Atualize a página **'Vista Geral'** para ver a lista atualizada de discos de dados.

    ![Selecione guardar](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="next-steps"></a>Passos seguintes

Para aprender a implementar máquinas virtuais no seu dispositivo Azure Stack Edge Pro, consulte [implementar máquinas virtuais através do portal Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
