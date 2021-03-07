---
title: Como implantar VMs no seu Azure Stack Edge Pro através do portal Azure
description: Saiba como criar e gerir VMs no seu Azure Stack Edge Pro através do portal Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro device so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 6054e7e79acaa6abf304508221c63143b9d14a45
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102436537"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>Implemente VMs no seu dispositivo GPU Azure Stack Edge Pro através do portal Azure

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Pode criar e gerir máquinas virtuais (VMs) num dispositivo Azure Stack Edge utilizando o portal Azure, modelos, cmdlets Azure PowerShell e através de scripts Azure CLI/Python. Este artigo descreve como criar e gerir um VM no seu dispositivo Azure Stack Edge utilizando o portal Azure. 

Este artigo aplica-se aos dispositivos Azure Stack Edge Pro GPU, Azure Stack Edge Pro R e Azure Stack Edge Mini R. 

> [!IMPORTANT] 
> Recomendamos que permita a autenticação multifactor para o utilizador que gere VMs que são implantados no seu dispositivo a partir da nuvem.
        
## <a name="vm-deployment-workflow"></a>Fluxo de trabalho de implantação VM

O resumo de alto nível do fluxo de trabalho de implantação é o seguinte:

1. Ativar uma interface de rede para calcular o seu dispositivo Azure Stack Edge. Isto cria um interruptor virtual na interface de rede especificada.
1. Permitir a gestão em nuvem de máquinas virtuais a partir do portal Azure.
1. Faça o upload de um VHD para uma conta de Armazenamento Azure utilizando o Storage Explorer. 
1. Utilize o VHD carregado para descarregar o VHD para o dispositivo e criar uma imagem VM a partir do VHD. 
1. Utilizar os recursos criados nos passos anteriores:
    1. Imagem VM que criaste.
    1. VSwitch associado à interface de rede em que ativou o cálculo.
    1. Sub-rede associada ao VSwitch.

    E criar ou especificar os seguintes recursos em linha:
    1. Nome VM, escolha um tamanho VM suportado, credenciais de inscrição para o VM. 
    1. Crie novos discos de dados ou anexe os discos de dados existentes.
    1. Configure IP estático ou dinâmico para o VM. Se fornecer um IP estático, escolha entre um IP gratuito na gama de sub-redes da interface de rede ativada para o cálculo.

    Use os recursos de cima para criar uma máquina virtual.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a criar e gerir VMs no seu dispositivo através do portal Azure, certifique-se de que:

1. Completou as definições de rede no seu dispositivo Azure Stack Edge Pro, conforme descrito no [dispositivo Step 1: Configure Azure Stack Edge Pro](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device).

    1. Ativou uma interface de rede para calcular. Este IP de interface de rede é utilizado para criar um interruptor virtual para a implementação de VM. Na UI local do seu dispositivo, vá ao **Compute.** Selecione a interface de rede que utilizará para criar um interruptor virtual.

        > [!IMPORTANT] 
        > Só é possível configurar uma porta para o cálculo.

    1. Ativar o cálculo na interface de rede. O Azure Stack Edge Pro cria e gere um interruptor virtual correspondente a essa interface de rede.

1. Tem acesso a um Windows ou Linux VHD que utilizará para criar a imagem VM para a máquina virtual que pretende criar.

## <a name="deploy-a-vm"></a>Implementar uma VM

Siga estes passos para criar uma máquina virtual no seu dispositivo Azure Stack Edge.

### <a name="add-a-vm-image"></a>Adicione uma imagem VM

1. Faça o upload de um VHD para uma conta de Armazenamento Azure. Siga os passos no [Upload a VHD utilizando o Azure Storage Explorer](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md).

1. No portal Azure, aceda ao recurso Azure Stack Edge para o seu dispositivo Azure Stack Edge. Vá ao **Edge compute > Máquinas Virtuais**.

    ![Adicionar imagem VM 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. Selecione **Máquinas Virtuais** para ir à página **'Vista Geral'.** **Ativar a** gestão virtual da nuvem de máquina.
    ![Adicionar imagem VM 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. O primeiro passo é adicionar uma imagem VM. Já carregou um VHD na conta de armazenamento no passo anterior. Utilizará este VHD para criar uma imagem VM.

    **Selecione Adicionar imagem** para descarregar o VHD da conta de armazenamento e adicionar ao dispositivo. O processo de descarregamento demora vários minutos dependendo do tamanho do VHD e da largura de banda da internet disponível para o download. 

    ![Adicionar imagem VM 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. Na lâmina **de imagem Adicionar,** introduza os seguintes parâmetros. Selecione **Adicionar**.


    |Parâmetro  |Descrição  |
    |---------|---------|
    |Download a partir de blob de armazenamento    |Navegue pela localização da bolha de armazenamento na conta de armazenamento onde carregou o VHD.         |
    |Baixar para    | Configurar automaticamente para o dispositivo atual onde está a implantar a máquina virtual.        |
    |Guardar imagem como      | O nome da imagem VM que está a criar a partir do VHD que carregou para a conta de armazenamento.        |
    |Tipo de SO     |Escolha entre Windows ou Linux como o sistema operativo do VHD que utilizará para criar a imagem VM.         |
   

    ![Adicionar imagem VM 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. O VHD é descarregado e a imagem VM é criada. A criação de imagem leva vários minutos para ser concluída. Vê uma notificação para a conclusão bem sucedida da imagem VM.

    ![Adicionar imagem VM 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. Após a criação da imagem VM com sucesso, é adicionada à lista de imagens na lâmina **Imagens.**
    ![Adicionar imagem VM 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    As atualizações da lâmina **de implementação** indicam o estado da implantação.

    ![Adicionar imagem VM 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    A imagem recém-adicionada também é exibida na página **'Vista Geral'.**
    ![Adicionar imagem VM 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>Adicionar uma VM

Siga estes passos para criar um VM depois de ter criado uma imagem VM.

1. Na página **'Vista Geral',** selecione **Adicionar máquina virtual**.

    ![Adicionar VM 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. No **separador Básicos,** introduza os seguintes parâmetros.


    |Parâmetro |Descrição  |
    |---------|---------|
    |Nome da máquina virtual     |         |
    |Imagem     | Selecione a partir das imagens VM disponíveis no dispositivo.        |
    |Tamanho     | Escolha entre os [tamanhos VM suportados.](azure-stack-edge-gpu-virtual-machine-sizes.md)        |
    |Nome de utilizador     | Utilize o nome de utilizador padrão *azureuser*.        |
    |Tipo de autenticação    | Escolha entre a chave pública SSH ou uma palavra-passe definida pelo utilizador.       |
    |Palavra-passe     | Introduza uma palavra-passe para iniciar sinsus na máquina virtual. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os requisitos de [Complexidade definidos](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).        |
    |Confirmar palavra-passe    | Introduza a senha novamente.        |


    ![Adicionar VM 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    Selecione **Seguinte: Discos**.

1. No separador **Discos,** irá anexar discos ao seu VM. 
    
    1. Pode optar por **criar e anexar um disco novo** ou **anexar um disco existente.**

        ![Adicionar VM 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. Selecione **Criar e anexar um novo disco.** Na lâmina de **disco nova,** forneça um nome para o disco e o tamanho em GiB.

        ![Adicionar VM 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1.  Repita o processo para adicionar mais discos. Depois de criados os discos, aparecem no **separador Discos.**

        ![Adicionar VM 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-3.png)

        Selecione **Seguinte: Rede**.

1. No **separador Networking,** configurará a conectividade da rede para o seu VM.

    
    |Parâmetro  |Descrição |
    |---------|---------|
    |Rede virtual    | A partir da lista de dropdown, selecione o interruptor virtual criado no seu dispositivo Azure Stack Edge quando ativar o cálculo na interface de rede.    |
    |Sub-rede     | Este campo é automaticamente povoado com a sub-rede associada à interface de rede na qual ativou o cálculo.         |
    |Endereço IP     | Forneça uma estática ou um IP dinâmico para o seu VM. O IP estático deve ser um IP disponível e gratuito da gama de sub-redes especificada.        |

    ![Adicionar VM 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    Selecione **Seguinte: Revisão + Criar**.

1. No **separador 'Rever + Criar',** revê as especificações do VM e selecione **Criar**.

    ![Adicionar VM 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. A criação de VM começa e pode demorar até 20 minutos. Pode ir a **Implementações** para monitorizar a criação de VM.

    ![Adicionar VM 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

    
1. Após a criação do VM com sucesso, a página **de visão** geral atualiza-se para exibir o novo VM.

    ![Adicionar VM 9](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. Selecione o VM recém-criado para ir a **máquinas Virtuais**.

    ![Adicionar VM 10](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    Selecione o VM para ver os detalhes. 

    ![Adicionar VM 11](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

## <a name="connect-to-a-vm"></a>Ligar a uma VM

Dependendo se criou um Windows ou um Linux VM, os passos para ligar podem ser diferentes. Não é possível ligar-se aos VM implantados no seu dispositivo através do portal Azure. Tem de tomar os seguintes passos para se ligar ao seu Linux ou Windows VM.

### <a name="connect-to-linux-vm"></a>Ligue-se ao Linux VM

Siga estes passos para ligar a um Linux VM.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>Ligue-se ao Windows VM

Siga estes passos para ligar a um VM do Windows.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>Passos seguintes

Para aprender a administrar o seu dispositivo Azure Stack Edge Pro, consulte[utilizar o UI web local para administrar um Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md).
