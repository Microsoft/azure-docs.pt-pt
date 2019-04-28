---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 32f533d06b7db0284459951e65f9c04fe0bb0285
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485369"
---
Um conjunto de disponibilidade ajuda a manter as máquinas virtuais disponíveis durante o período de inatividade, por exemplo, durante a manutenção. Colocar duas ou mais máquinas virtuais configuradas de forma semelhante num conjunto de disponibilidade cria a redundância necessários para manter a disponibilidade das aplicações ou serviços que sua máquina virtual é executada. Para obter detalhes sobre como isto funciona, consulte [gerir a disponibilidade das máquinas virtuais][Manage the availability of virtual machines].

É uma prática recomendada usar conjuntos de disponibilidade e pontos finais de balanceamento de carga para ajudar a garantir que seu aplicativo esteja sempre disponível e em execução com eficiência. Para obter mais informações sobre pontos finais com balanceamento de carga, veja [balanceamento de carga para serviços de infraestrutura do Azure][Load balancing for Azure infrastructure services].

Pode adicionar máquinas virtuais clássicas num conjunto de disponibilidade, através de uma das duas opções:

* [Opção 1: Criar uma máquina virtual e um conjunto, ao mesmo tempo de disponibilidade][Option 1: Create a virtual machine and an availability set at the same time]. Em seguida, adicione novas máquinas virtuais para o conjunto ao criar essas máquinas virtuais.
* [Opção 2: Adicionar uma máquina virtual existente a um conjunto de disponibilidade][Option 2: Add an existing virtual machine to an availability set].

> [!NOTE]
> No modelo clássico, as máquinas virtuais que pretende colocar no mesmo conjunto de disponibilidade têm de pertencer ao mesmo serviço cloud.
> 
> 

## <a id="createset"> </a>Opção 1: Criar uma máquina virtual e um conjunto, ao mesmo tempo de disponibilidade
Pode utilizar o portal do Azure ou comandos do Azure PowerShell para fazer isso.

Para utilizar o portal do Azure:

1. Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **criar um recurso** > **computação**.
3. Selecione a imagem de máquina virtual do Marketplace que deseja usar. Pode optar por criar uma máquina virtual Linux ou Windows.
4. Para a máquina virtual selecionada, certifique-se de que o modelo de implementação está definido como **clássica** e, em seguida, clique em **Create**
   
    ![Texto alternativo da imagem](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)
5. Introduza um nome da máquina virtual, o nome de utilizador e a palavra-passe (para máquinas do Windows) ou a chave pública de SSH (para máquinas do Linux). 
6. Escolha o tamanho da VM e, em seguida, clique em **selecione** para continuar.
7. Escolher **configuração opcional > conjunto de disponibilidade**, e selecione o conjunto de disponibilidade pretende adicionar a máquina virtual.
   
    ![Texto alternativo da imagem](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png) 
8. Reveja as definições de configuração. Quando tiver terminado, clique em **criar**.
9. Enquanto o Azure cria a máquina virtual, pode acompanhar o progresso em **máquinas virtuais** no hub menu.

Para utilizar comandos do Azure PowerShell para criar uma máquina virtual do Azure e adicioná-lo a um conjunto de disponibilidade de novo ou existente, consulte [utilizar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas no Windows](../articles/virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a id="addmachine"> </a>Opção 2: Adicionar uma máquina virtual existente a um conjunto de disponibilidade
No portal do Azure, pode adicionar máquinas virtuais clássicas existentes para uma disponibilidade existente definido ou criar um novo para os mesmos. (Tenha em atenção que as máquinas virtuais no mesmo conjunto de disponibilidade têm de pertencer ao mesmo serviço cloud). Os passos são quase os mesmos. Com o Azure PowerShell, pode adicionar a máquina virtual a um conjunto de disponibilidade existente.

1. Se ainda não o fez, inicie sessão para o [portal do Azure](https://portal.azure.com).
2. No menu da esquerda, clique em **máquinas virtuais (clássicas)**.
   
    ![Texto alternativo da imagem](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)
3. Na lista de máquinas virtuais, selecione o nome da máquina virtual que pretende adicionar ao conjunto.
4. Escolher **conjunto de disponibilidade** da máquina virtual **definições**.
   
    ![Texto alternativo da imagem](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)
5. Selecione o conjunto de disponibilidade que pretende adicionar a máquina virtual. A máquina virtual têm de pertencer ao mesmo serviço cloud como o conjunto de disponibilidade.
   
    ![Texto alternativo da imagem](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)
6. Clique em **Guardar**.

Para utilizar comandos do Azure PowerShell, abra uma sessão do PowerShell do Azure de nível de administrador e execute o seguinte comando. Para os marcadores de posição (tal como &lt;VmCloudServiceName&gt;), substituir tudo dentro das aspas, incluindo os < e > caracteres, com os nomes corretos.

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

> [!NOTE]
> A máquina virtual poderá ter de ser reiniciado para concluir a adicioná-lo para o conjunto de disponibilidade.
> 
> 

<!-- LINKS -->
[Option 1: Create a virtual machine and an availability set at the same time]: #createset
[Option 2: Add an existing virtual machine to an availability set]: #addmachine

[Load balancing for Azure infrastructure services]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Manage the availability of virtual machines]:../articles/virtual-machines/linux/manage-availability.md

[Create a virtual machine running Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Virtual Network overview]: ../articles/virtual-network/virtual-networks-overview.md

