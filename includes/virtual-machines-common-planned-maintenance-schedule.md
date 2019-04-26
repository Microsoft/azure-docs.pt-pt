---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: c7fe0d6f8e03501cca7a8b98f95286b6a21c0476
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232799"
---
## <a name="multi-and-single-instance-vms"></a>Múltiplas e VMs de instância única
Muitos clientes executando em conta a contagem do Azure-críticos que pode agendar quando as respetivas VMs entrar em manutenção planeada devido ao tempo de inatividade, cerca de 15 minutos, que ocorre durante a manutenção. Pode utilizar os conjuntos de disponibilidade para ajudar a controlar quando as VMs aprovisionadas recebem a manutenção planeada.

Existem duas configurações possíveis para as VMs em execução no Azure. VMs ou estão configuradas como várias instâncias ou de instância única. Se as VMs num conjunto de disponibilidade, em seguida, eles são configurados como várias instâncias. Tenha em atenção, até mesmo único VMs podem ser implementadas num conjunto de disponibilidade, para que eles são tratados como várias instâncias. Se não forem VMs num conjunto de disponibilidade, em seguida, eles são configurados como instância única.  Para obter detalhes sobre os conjuntos de disponibilidade, consulte [gerir a disponibilidade das suas máquinas de virtuais do Windows](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [gerir a disponibilidade das suas máquinas de virtuais do Linux](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Atualizações de manutenção planeada para VMs de instância única e várias instâncias acontecem em separado. Ao reconfigurar as suas VMs para ser a única instância (se eles estiverem várias instâncias) ou para ser várias instâncias (se forem de instância única), pode controlar quando as respetivas VMs recebem a manutenção planeada. Ver [a manutenção de máquinas virtuais do Linux do Azure planeada](../articles/virtual-machines/linux/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [manutenção para máquinas de virtuais do Windows Azure planeada](../articles/virtual-machines/windows/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter detalhes sobre a manutenção planeada para VMs do Azure.

## <a name="for-multi-instance-configuration"></a>Para a configuração de várias instâncias
Pode selecionar o tempo de manutenção planeada afeta as VMs que são implementadas numa configuração de conjunto de disponibilidade ao remover estas VMs de conjuntos de disponibilidade.

1. É enviado um e-mail para si sete dias antes da manutenção planeada para as VMs na configuração de várias instâncias. Os nomes das VMs com várias instâncias afetados e IDs de subscrição estão incluídos no corpo da mensagem de e-mail.
2. Durante os sete dias, pode escolher o tempo que as instâncias são atualizadas ao remover as VMs de várias instâncias nessa região de seu conjunto de disponibilidade. Esta alteração na configuração faz com que uma reinicialização, como a Máquina Virtual está a mudar de um anfitrião físico, destinado a manutenção, para outro anfitrião físico que não está direcionado para manutenção.
3. Pode remover a VM do seu conjunto de disponibilidade no portal do Azure.

   1. No portal, selecione a VM para remover do conjunto de disponibilidade.  

   2. Sob **configurações**, clique em **conjunto de disponibilidade**.

      ![Seleção de conjunto de disponibilidade](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

   3. No conjunto de disponibilidade menu pendente, selecione "Não faz parte de um conjunto de disponibilidade."

      ![Remover do conjunto](./media/virtual-machines-planned-maintenance-schedule/availabilitysetwarning.png)

   4. Na parte superior, clique em **guardar**. Clique em **Sim** para confirmar que esta ação reinicia a VM.

   >[!TIP]
   >Pode reconfigurar a VM para várias instâncias mais tarde, selecionando um dos conjuntos de disponibilidade listadas.

4. Removido de conjuntos de disponibilidade de VMs são movidas para anfitriões de instância única e não são atualizadas durante a manutenção planeada para configurações de conjunto de disponibilidade.
5. Quando a atualização para VMs do conjunto de disponibilidade é concluída (de acordo com a agenda descrita no e-mail original), deve adicionar as VMs volta em seus conjuntos de disponibilidade. Tornar-se parte de um conjunto de disponibilidade reconfigura as VMs como várias instâncias e resulta numa reinicialização. Normalmente, uma vez que todas as atualizações de várias instâncias forem concluídas em todo o ambiente do Azure, a manutenção de instância única segue.

Remover uma VM do conjunto de disponibilidade pode também ser possível com o Azure PowerShell:

```
Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM
```

## <a name="for-single-instance-configuration"></a>Para a configuração de instância única
Pode selecionar o tempo de manutenção planeada afeta VMs numa configuração de instância única, adicionando estas VMs em conjuntos de disponibilidade.

Passo-a-passo

1. É enviado um e-mail para si sete dias antes da manutenção planeada para VMs numa configuração de instância única. Os IDs de subscrição e os nomes das VMs de instância única afetados são incluídos no corpo da mensagem de e-mail.
2. Durante os sete dias, pode escolher o tempo de que sua instância for reiniciado, adicionando as VMs de instância única para um conjunto de disponibilidade na mesma região. Esta alteração na configuração faz com que uma reinicialização, como a Máquina Virtual está a mudar de um anfitrião físico, destinado a manutenção, para outro anfitrião físico que não está direcionado para manutenção.
3. Siga as instruções aqui para adicionar VMs existentes em conjuntos de disponibilidade com o portal do Azure e o Azure PowerShell. (Consulte o exemplo do PowerShell do Azure que se segue estes passos.)
4. Assim que estas VMs são reconfiguradas como várias instâncias, eles são excluídos da manutenção planeada para VMs de instância única.
5. Uma vez concluída a atualização VM de instância única (de acordo com a agenda no e-mail original), pode devolver as VMs para instância única ao remover as VMs de seus conjuntos de disponibilidade.

Adicionar uma VM a um conjunto de disponibilidade pode ser obtido com o Azure PowerShell:

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/
