---
title: Solução de VMware do Azure por guia de introdução do CloudSimple - consumir VMs de VMware no Azure
description: Neste início rápido, irá aprender como configurar e consumir as VMs de VMware no portal do Azure com o Azure VMware solução por CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: quickstart
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b430efbc931d72de4b095a7eac4c1e7ca496b1b9
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393501"
---
# <a name="quickstart-consume-vmware-vms-on-azure"></a>Início rápido: Consumir VMs de VMware no Azure

Para criar uma máquina virtual no portal do Azure, pode utilizar modelos de máquina virtual disponíveis no seu vCenter de nuvem privada. Um administrador do vCenter pode criar modelos adicionais na cloud privada.

## <a name="create-a-vm-template"></a>Criar um modelo de VM

Primeiro, crie uma máquina virtual na sua nuvem privada utilizando o vCenter da interface do Usuário. Para criar um modelo, siga as instruções no artigo VMware [clonar uma Máquina Virtual para um modelo no vSphere Web Client](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Store o modelo de VM no seu vCenter de nuvem privada.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Criar uma máquina virtual no portal do Azure

1. Selecione **Todos os serviços**.

2. Procure **máquinas de virtuais de CloudSimple**.

3. Selecione **Adicionar**.

    ![Selecione adicionar](media/create-cloudsimple-virtual-machine.png)

4. Introduza as seguintes informações sobre a máquina virtual e, em seguida, selecione **seguinte: Tamanho**.

    ![Criar Máquina Virtual de CloudSimple - Noções básicas](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Campo | Descrição |
    | ------------ | ------------- |
    | **Subscrição** | A subscrição do Azure associada à sua nuvem privada.  |
    | **Grupo de recursos** | O grupo de recursos para o qual a VM será atribuída. Pode selecionar um grupo existente ou crie um novo. |
    | **Nome** | Um nome para identificar a VM.  |
    | **Localização** | A região do Azure na qual a VM está alojada.  |
    | **Nuvem privada** | CloudSimple nuvem privada na qual pretende criar a VM. |
    | **ResourcePool** | Um agrupamento de recursos mapeadas para a VM. Selecione os agrupamentos de recursos disponíveis. |
    | **Modelo do vSphere** | O modelo do vSphere para a VM.  |
    | **Nome de utilizador** | O nome de utilizador de admin de VM (para modelos do Windows).|
    | **Palavra-passe** |  A palavra-passe de admin de VM (para modelos do Windows). |
    | **Confirmar palavra-passe** | A palavra-passe fornecida no campo anterior. |

5. Selecione o número de núcleos e a capacidade de memória para a VM. Selecione **expor ao SO convidado** se quiser expor a virtualização de CPU total para o sistema operativo convidado. Aplicações que requerem a virtualização de hardware podem ser executadas em máquinas virtuais sem conversão binária ou de paravirtualização. Para obter mais informações, consulte o artigo do VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">expor Hardware assistido virtualização do VMware</a>. Quando tiver terminado, selecione **seguinte: Configurações**.

    ![Criar Máquina Virtual de CloudSimple - tamanho](media/create-cloudsimple-virtual-machine-size.png)

6. Configure discos e interfaces de rede, conforme descrito nas tabelas seguintes e, em seguida, selecione **rever + criar**.

    ![Criar Máquina Virtual de CloudSimple - configurações](media/create-cloudsimple-virtual-machine-configurations.png)

    Para interfaces de rede, selecione **interface de rede de Add** e, em seguida, configure as seguintes definições:
    
    | Definição | Descrição |
    | ------------ | ------------- |
    | **Nome** | Introduza um nome para identificar a interface.  |
    | **Rede** | Selecione na lista de grupos de portas de distribuída configurado no vSphere sua nuvem privada.  |
    | **Placa** | Selecione um adaptador de vSphere na lista de tipos disponíveis configurado para a VM. Para obter mais informações, consulte o artigo do VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">escolher um adaptador de rede para a máquina virtual</a>. |
    | **Ligar no arranque** | Escolha se pretende ativar o hardware do NIC quando arranca a VM. A predefinição é **ativar**. |

    Para discos, selecione **adicionar disco** e, em seguida, configure as seguintes definições:

    | Definição | Descrição |
    | ------------ | ------------- |
    | **Nome** | Introduza um nome para identificar o disco.  |
    | **Tamanho** | Selecione um dos tamanhos disponíveis.  |
    | **Controlador SCSI** | Selecione um controlador de SCSI para o disco.  |
    | **modo** | O modo Especifica como o disco participa de instantâneos. Escolha uma destas opções: <br> **Independente de persistente**: Todas as alterações escritas no disco são escritas permanentemente.<br> **Independente não persistentes**: As alterações escritas no disco são eliminadas quando desligar ou repor a máquina virtual. Modo de não persistentes independente permite-lhe sempre de reiniciar a VM no mesmo Estado. Para obter mais informações, consulte a <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">documentação do VMware</a>.

7. Depois de concluída a validação, reveja as definições e selecione **criar**. Para fazer alterações, selecione nos separadores na parte superior ou selecione **anterior: Configurações**.

    ![Criar Máquina Virtual de CloudSimple - revisão + criar](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Passos Seguintes

* [Ver a lista de máquinas de virtuais de CloudSimple](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [Gerir CloudSimple máquinas de virtuais do Azure](https://docs.azure.cloudsimple.com/azure-manage-vm/)
