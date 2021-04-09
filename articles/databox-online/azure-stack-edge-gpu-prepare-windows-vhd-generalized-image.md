---
title: Crie imagens VM a partir de imagem generalizada do Windows VHD para o seu dispositivo GPU Azure Stack Edge Pro
description: Descreve como imagens VM a partir de imagens generalizadas a partir de um VHD do Windows ou de um VHDX. Utilize esta imagem generalizada para criar imagens VM para utilizar com VMs implantados no seu dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: 978099accd57d15c750a27f77b2e220f569a2dd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962611"
---
# <a name="use-generalized-image-from-windows-vhd-to-create-a-vm-image-for-your-azure-stack-edge-pro-device"></a>Utilize uma imagem generalizada a partir do Windows VHD para criar uma imagem VM para o seu dispositivo Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Para implementar VMs no seu dispositivo Azure Stack Edge Pro, precisa de ser capaz de criar imagens VM personalizadas que pode utilizar para criar VMs. Este artigo descreve os passos necessários para preparar um Windows VHD ou VHDX para criar uma imagem generalizada. Esta imagem generalizada é então usada para criar uma imagem VM para o seu dispositivo Azure Stack Edge Pro. 

## <a name="about-preparing-windows-vhd"></a>Sobre a preparação do Windows VHD

Um VHD ou VHDX do Windows pode ser usado para criar uma imagem *generalizada* ou uma imagem *especializada.* A tabela seguinte resume as principais diferenças entre as imagens *generalizadas* e as *especializadas.*


|Tipo de imagem  |Generalizada  |Especializada  |
|---------|---------|---------|
|Destino     |Implantado em qualquer sistema         | Direcionado para um sistema específico        |
|Configuração após arranque     | Configuração necessária no primeiro arranque do VM.          | A armação não é necessária. <br> A plataforma liga o VM.        |
|Configuração     |Nome de anfitrião, utilizador de administração e outras definições específicas de VM necessárias.         |Completamente pré-configurado.         |
|Usado quando     |Criar vários novos VMs a partir da mesma imagem.         |Migrar uma máquina específica ou restaurar um VM de cópias de segurança anteriores.         |


Este artigo abrange os passos necessários para ser implantado a partir de uma imagem generalizada. Para implementar a partir de uma imagem especializada, consulte [utilizar o Windows VHD especializado](azure-stack-edge-placeholder.md) para o seu dispositivo.

> [!IMPORTANT]
> Este procedimento não abrange casos em que o VHD de origem esteja configurado com configurações e configurações personalizadas. Por exemplo, podem ser necessárias ações adicionais para generalizar um VHD que contenha regras de firewall personalizadas ou configurações de procuração. Para obter mais informações sobre estas ações adicionais, consulte [Prepare um VHD do Windows para fazer o upload para Azure - Azure Virtual Machines](../virtual-machines/windows/prepare-for-upload-vhd-image.md).


## <a name="vm-image-workflow"></a>Fluxo de trabalho de imagem VM

O fluxo de trabalho de alto nível para preparar um VHD do Windows para utilização como imagem generalizada tem os seguintes passos:

1. Converta a fonte VHD ou VHDX num VHD de tamanho fixo.
1. Crie um VM em Hiper-V utilizando o VHD fixo.
1. Ligue-se ao Hiper-VM.
1. Generalize o VHD utilizando o utilitário *sysprep.* 
1. Copie a imagem generalizada para o armazenamento blob.
1. Utilize uma imagem generalizada para implantar VMs no seu dispositivo. Para obter mais informações, consulte como [implementar um VM através do portal Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md) ou [implementar um VM via PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).


## <a name="prerequisites"></a>Pré-requisitos

Antes de preparar um VHD do Windows para ser usado como uma imagem generalizada no Azure Stack Edge, certifique-se de que:

- Tem um VHD ou um VHDX contendo uma versão suportada do Windows. Consulte [sistemas operativos de hóspedes suportados]() para o seu Azure Stack Edge Pro. 
- Tem acesso a um cliente Windows com Hiper-V Manager instalado. 
- Tem acesso a uma conta de armazenamento Azure Blob para armazenar o seu VHD depois de preparado.

## <a name="prepare-a-generalized-windows-image-from-vhd"></a>Prepare uma imagem generalizada do Windows a partir de VHD

## <a name="convert-to-a-fixed-vhd"></a>Converter para um VHD fixo 

Para o seu dispositivo, necessitará de VHDs de tamanho fixo para criar imagens VM. Terá de converter a sua fonte Windows VHD ou VHDX num VHD fixo. Siga estes passos:

1. Abra o Hyper-V Manager no seu sistema de clientes. Ir para **Editar Disco.**

    ![Gestor de Hiper-V aberto](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-1.png)

1. Na página **Antes de começar,** selecione **Next>**.

1. Na página **de disco rígido virtual Localizar,** navegue até à localização da fonte Windows VHD ou VHDX que pretende converter. Selecione **Next>**.

    ![Localizar página de disco rígido virtual](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-2.png)

1. Na página **de ação Escolha,** selecione **Converter** e selecione **Next>**.

    ![Escolha a página de ação](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-3.png)

1. Na página do **formato do disco Escolha,** selecione o formato **VHD** e, em seguida, selecione **Next>**.

   ![Escolha a página do formato do disco](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-4.png)


1. Na página **do tipo de disco Escolha,** escolha tamanho **fixo** e selecione **Next>**.

   ![Escolha a página do tipo de disco](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-5.png)


1. Na página do **disco configurar,** navegue para a localização e especifique um nome para o disco VHD de tamanho fixo. Selecione **Next>**.

   ![Configure a página do disco](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-6.png)


1. Reveja o resumo e **selecione Acabamento**. A conversão VHD ou VHDX demora alguns minutos. O tempo de conversão depende do tamanho do disco de origem. 

<!--
1. Run PowerShell on your Windows client.
1. Run the following command:

    ```powershell
    Convert-VHD -Path <source VHD path> -DestinationPath <destination-path.vhd> -VHDType Fixed 
    ```
-->
Você usará este VHD fixo para todos os passos seguintes neste artigo.


## <a name="create-a-hyper-v-vm-from-fixed-vhd"></a>Criar um VM Hiper-V a partir de VHD fixo

1. No **Hyper-V Manager,** no painel de âmbito, clique com o botão direito para abrir o menu de contexto e, em seguida, selecione **Nova**  >  **Máquina Virtual**.

    ![Selecione nova máquina virtual no painel de âmbito](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-1.png)

1. Na **página antes de começar** a página do Novo Assistente de Máquinas Virtuais, selecione **Seguinte**.

1. Na página **de nome e localização do Especifique,** forneça um **Nome** e **localização** para a sua máquina virtual. Selecione **Seguinte**.

    ![Especifique o nome e a localização do seu VM](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-2.png)

1. Na página **de geração Especificar,** escolha **a Geração 1** para o tipo de imagem do dispositivo .vhd e, em seguida, selecione **Seguinte**.    

    ![Especificar geração](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-3.png)

1. Atribua a memória desejada e as configurações de networking.

1. Na página **de disco rígido virtual Connect,** escolha Utilize um disco rígido virtual **existente**, especifique a localização do VHD fixo do Windows que criámos anteriormente e, em seguida, selecione **Next**.

    ![Conecte a página de disco rígido virtual](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-4.png)

1. Reveja o **Resumo** e, em seguida, selecione **Finish** para criar a máquina virtual.

A máquina virtual demora vários minutos a criar.
     

## <a name="connect-to-the-hyper-v-vm"></a>Ligue-se ao Hiper-VM

O VM mostra na lista das máquinas virtuais do seu sistema cliente. 


1. Selecione o VM e, em seguida, clique à direita e **selecione Iniciar**. 

    ![Selecione VM e inicie-o](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-2.png)

2. O VM mostra como **Running**. Selecione o VM e, em seguida, clique à direita e selecione **Connect**.

    ![Ligar à VM](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-4.png)

Depois de ligado ao VM, preencha o assistente de configuração da Máquina e, em seguida, inscreva-se no VM.


## <a name="generalize-the-vhd"></a>Generalizar o VHD  

Utilize o utilitário *sysprep* para generalizar o VHD. 

1. Dentro do VM, abra um aviso de comando.
1. Executar o seguinte comando para generalizar o VHD. 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    Para mais informações, consulte [a visão geral do Sysprep (preparação do sistema).](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)
1.  Depois do comando estar completo, o VM desligar-se-á. **Não reinicie a VM**.

## <a name="upload-the-vhd-to-azure-blob-storage"></a>Faça o upload do armazenamento VHD para Azure Blob

O seu VHD pode agora ser usado para criar uma imagem generalizada no Azure Stack Edge. 

1. Faça o upload do VHD para o armazenamento de bolhas Azure. Consulte as instruções detalhadas no [Upload a VHD utilizando o Azure Storage Explorer](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md).
1. Depois de o upload estar concluído, pode utilizar a imagem carregada para criar imagens VM e VMs. 

<!-- this should be added to deploy VM articles - If you experience any issues creating VMs from your new image, you can use VM console access to help troubleshoot. For information on console access, see [link].-->



## <a name="next-steps"></a>Passos seguintes

Dependendo da natureza da implantação, pode escolher um dos seguintes procedimentos.

- [Implementar um VM a partir de uma imagem generalizada através do portal Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Implementar um VM a partir de uma imagem generalizada via Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
