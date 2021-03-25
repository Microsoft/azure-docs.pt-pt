---
title: Como gerir interfaces de rede VMs no seu Azure Stack Edge Pro através do portal Azure
description: Saiba como gerir interfaces de rede em VMs que são implantados no seu GPU Azure Stack Edge Pro através do portal Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/23/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage network interfaces on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: 84077f174fabd02afcd5171e8d365e8cbd3a52c2
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027738"
---
# <a name="use-the-azure-portal-to-manage-network-interfaces-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Utilize o portal Azure para gerir interfaces de rede nos VMs no gpu Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Pode criar e gerir máquinas virtuais (VMs) num dispositivo Azure Stack Edge utilizando o portal Azure, modelos, cmdlets Azure PowerShell e através de scripts Azure CLI/Python. Este artigo descreve como gerir as interfaces de rede num VM em execução no seu dispositivo Azure Stack Edge utilizando o portal Azure. 

Quando cria um VM, especifica uma interface de rede virtual a criar. É melhor adicionar uma ou mais interfaces de rede à máquina virtual depois de esta ser criada. Também pode querer alterar as definições de interface de rede padrão para uma interface de rede existente.

Este artigo explica como adicionar uma interface de rede a um VM existente, alterar as definições existentes, como o tipo IP (estático vs. dinâmico), e finalmente remover ou desprender uma interface existente. 

        
## <a name="about-network-interfaces-on-vms"></a>Sobre interfaces de rede em VMs

Uma interface de rede permite que uma máquina virtual (VM) em funcionamento no seu dispositivo Azure Stack Edge Pro comunique com o Azure e os recursos no local. Quando ativa uma porta para a rede de computação no seu dispositivo, é criado um interruptor virtual nessa interface de rede. Este interruptor virtual é então utilizado para implementar cargas de trabalho computacional, tais como VMs ou aplicações contentorizadas no seu dispositivo. 

O seu dispositivo suporta apenas um interruptor virtual, mas várias interfaces de rede virtuais. Cada interface de rede no seu VM tem um endereço IP estático ou dinâmico atribuído a ele. Com endereços IP atribuídos a múltiplas interfaces de rede no seu VM, determinadas capacidades estão ativadas no seu VM. Por exemplo, o seu VM pode hospedar vários websites ou serviços com diferentes endereços IP e certificados SSL num único servidor. Um VM no seu dispositivo pode servir como um aparelho virtual de rede, como uma firewall ou um equilibrador de carga. <!--Is it possible to do that on ASE?-->

<!--There is a limit to how many virtual network interfaces can be created on the virtual switch on your device. See the Azure Stack Edge Pro limits article for details.--> 


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a gerir VMs no seu dispositivo através do portal Azure, certifique-se de que:

1. Ativou uma interface de rede para calcular o seu dispositivo. Esta ação cria um interruptor virtual na interface de rede no seu VM. 
    1. Na UI local do seu dispositivo, vá ao **Compute.** Selecione a interface de rede que utilizará para criar um interruptor virtual.

        > [!IMPORTANT] 
        > Só é possível configurar uma porta para o cálculo.

    1. Ativar o cálculo na interface de rede. A Azure Stack Edge Pro GPU cria e gere um interruptor virtual correspondente a essa interface de rede.

1. Tem pelo menos um VM implantado no seu dispositivo. Para criar este VM, consulte as instruções em [Implementar VM no seu Azure Stack Edge Pro através do portal Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).

1. O seu VM deve estar em **estado de paragem.** Para parar o seu VM, vá a **máquinas virtuais > visão geral** e selecione o VM que pretende parar. Na página de propriedades VM, selecione **Stop** e, em seguida, selecione **Sim** quando solicitado para confirmação. Antes de adicionar, editar ou apagar interfaces de rede, tem de parar o VM.

    ![Pare o VM da página de propriedades VM](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="add-a-network-interface"></a>Adicionar uma interface de rede

Siga estes passos para adicionar uma interface de rede a uma máquina virtual implantada no seu dispositivo. 

1. Vá à máquina virtual que parou e, em seguida, vá para a página **VM Properties.** Selecione **Rede**.
    
    ![Selecione networking na página de propriedades VM](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-1.png)

2. Na lâmina **de rede,** a partir da barra de comando, selecione **+ Adicionar interface de rede**.

    ![Selecione adicionar interface de rede](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-2.png)

3. Na lâmina de **interface de rede Add,** introduza os seguintes parâmetros:

    
    |Coluna1  |Coluna2  |
    |---------|---------|
    |Name     | Um nome único dentro do grupo de recursos. O nome não pode ser alterado após a criação da interface de rede. Para gerir facilmente várias interfaces de rede, utilize as sugestões fornecidas nas [convenções de Nomeação.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)     |
    |Rede virtual| A rede virtual associada ao interruptor virtual criado no seu dispositivo quando ativou o cálculo na interface de rede. Existe apenas uma rede virtual associada ao seu dispositivo.         |         
    |Sub-rede     | Uma sub-rede dentro da rede virtual selecionada. Este campo é automaticamente povoado com a sub-rede associada à interface de rede na qual ativou o cálculo.         |       
    |Atribuição de IP   | Um IP estático ou dinâmico para a sua interface de rede. O IP estático deve ser um IP disponível e gratuito da gama de sub-redes especificada. Escolha a dinâmica se existe um servidor DHCP no ambiente.        | 

    ![Adicione uma lâmina de interface de rede](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-3.png)

4. Verá uma notificação de que a criação da interface de rede está em andamento.

    ![Notificação quando a interface de rede está a ser criada](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-4.png)

5.  Após a criação da interface de rede com sucesso, a lista de interfaces de rede atualiza-se para exibir a interface recém-criada.

    ![Lista atualizada de interfaces de rede](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-5.png)


## <a name="edit-a-network-interface"></a>Editar uma interface de rede

Siga estes passos para editar uma interface de rede associada a uma máquina virtual implantada no seu dispositivo.

1. Vá à máquina virtual que parou e vá à página **VM Properties.** Selecione **Rede**.

1. Na lista de interfaces de rede, selecione a interface que pretende editar. Na extrema direita da interface de rede selecionada, selecione o ícone de edição (lápis).  

    ![Selecione uma interface de rede para editar](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-1.png)

1. Na lâmina de **interface de rede Editar,** só é possível alterar a atribuição ip da interface de rede. O nome, rede virtual e sub-rede associados à interface de rede não podem ser alterados uma vez que são criados. Altere a **atribuição de IP** para estática e guarde as alterações.

    ![Alterar atribuição IP para a interface de rede](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-2.png)

1. A lista de atualizações de interface de rede para exibir a interface de rede atualizada.


## <a name="detach-a-network-interface"></a>Desprender uma interface de rede

Siga estes passos para desprender ou remova uma interface de rede associada a uma máquina virtual implantada no seu dispositivo.

1. Vá à máquina virtual que parou e vá à página **VM Properties.** Selecione **Rede**.

1. Na lista de interfaces de rede, selecione a interface que pretende editar. Na extrema direita da interface de rede selecionada, selecione o ícone de desprendimento (desligar a ficha).  

    ![Selecione uma interface de rede para desprender](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-1.png)

1. Depois de a interface estar completamente separada, a lista de interfaces de rede é atualizada para exibir as restantes interfaces.

## <a name="next-steps"></a>Passos seguintes

Para aprender a implementar máquinas virtuais no seu dispositivo Azure Stack Edge Pro, consulte [implementar máquinas virtuais através do portal Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
