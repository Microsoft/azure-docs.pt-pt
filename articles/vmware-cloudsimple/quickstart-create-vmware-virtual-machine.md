---
title: Solução do Azure VMware por CloudSimple início rápido – consumir VMs VMware no Azure
description: Neste guia de início rápido, você aprenderá a configurar e consumir VMs VMware do portal do Azure usando a solução VMware do Azure da CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: quickstart
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dd4faf6df54d478654c59ffc18bf8c5873d576b9
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816665"
---
# <a name="quickstart-consume-vmware-vms-on-azure"></a>Início rápido: Consumir VMs de VMware no Azure

Para criar uma máquina virtual no portal do Azure, você pode usar modelos de máquina virtual disponíveis em seu vCenter de nuvem privada. Um administrador do vCenter pode criar modelos adicionais na nuvem privada.

## <a name="create-a-vm-template"></a>Criar um modelo de VM

Primeiro, crie uma máquina virtual em sua nuvem privada usando a interface do usuário do vCenter. Para criar um modelo, siga as instruções no artigo do VMware [clonar uma máquina virtual para um modelo no cliente Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Armazene o modelo de VM em seu vCenter de nuvem privada.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Criar uma máquina virtual no portal do Azure

1. Selecione **Todos os serviços**.

2. Procure **máquinas virtuais CloudSimple**.

3. Selecione **Adicionar**.

    ![Selecione Adicionar](media/create-cloudsimple-virtual-machine.png)

4. Insira as seguintes informações sobre a máquina virtual e, em **seguida, selecione Avançar: Tamanho**.

    ![Criar máquina virtual CloudSimple-noções básicas](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Campo | Descrição |
    | ------------ | ------------- |
    | **Subscrição** | A assinatura do Azure associada à sua nuvem privada.  |
    | **Grupo de recursos** | O grupo de recursos ao qual a VM será atribuída. Você pode selecionar um grupo existente ou criar um novo. |
    | **Name** | Um nome para identificar a VM.  |
    | **Location** | A região do Azure na qual a VM está hospedada.  |
    | **Nuvem privada** | A nuvem privada do CloudSimple na qual você deseja criar a VM. |
    | **ResourcePool** | Um pool de recursos mapeado para a VM. Selecione os pools de recursos disponíveis. |
    | **Modelo vSphere** | O modelo vSphere para a VM.  |
    | **Nome de utilizador** | O nome de usuário do administrador da VM (para modelos do Windows).|
    | **Palavra-passe** |  A senha do administrador da VM (para modelos do Windows). |
    | **Confirmar palavra-passe** | A senha fornecida no campo anterior. |

5. Selecione o número de núcleos e a capacidade de memória para a VM. Selecione **expor ao SO convidado** se você quiser expor a virtualização de CPU completa para o sistema operacional convidado. Aplicativos que exigem virtualização de hardware podem ser executados em máquinas virtuais sem conversão binária ou paravirtualização. Para obter mais informações, consulte o artigo do VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">expor virtualização assistida de hardware VMware</a>. Quando terminar, selecione **avançar: Configurações**.

    ![Criar máquina virtual CloudSimple-tamanho](media/create-cloudsimple-virtual-machine-size.png)

6. Configure os discos e os adaptadores de rede conforme descrito nas tabelas a seguir e selecione revisar **+ criar**.

    ![Criar máquina virtual CloudSimple-configurações](media/create-cloudsimple-virtual-machine-configurations.png)

    Para interfaces de rede, selecione **Adicionar interface de rede** e defina as seguintes configurações:
    
    | Definição | Descrição |
    | ------------ | ------------- |
    | **Name** | Insira um nome para identificar a interface.  |
    | **Rede** | Selecione na lista de grupos de portas distribuídas configuradas em sua nuvem privada vSphere.  |
    | **Placas** | Selecione um adaptador vSphere na lista de tipos disponíveis configurados para a VM. Para obter mais informações, consulte o artigo do VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">escolhendo um adaptador de rede para sua máquina virtual</a>. |
    | **Ligar na inicialização** | Escolha se deseja habilitar o hardware NIC quando a VM for inicializada. O padrão é **habilitar**. |

    Para discos, selecione **adicionar disco** e defina as seguintes configurações:

    | Definição | Descrição |
    | ------------ | ------------- |
    | **Name** | Insira um nome para identificar o disco.  |
    | **Tamanho** | Selecione um dos tamanhos disponíveis.  |
    | **Controlador SCSI** | Selecione um controlador SCSI para o disco.  |
    | **Moda** | O modo especifica como o disco participa de instantâneos. Escolha uma destas opções: <br> **Persistente independente**: Todas as alterações gravadas no disco são gravadas permanentemente.<br> **Independente não persistente**: As alterações gravadas no disco são descartadas quando você desliga ou redefine a máquina virtual. O modo não persistente independente permite que você sempre reinicie a VM no mesmo estado. Para obter mais informações, consulte a <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">documentação do VMware</a>.

7. Após a conclusão da validação, examine as configurações e selecione **criar**. Para fazer alterações, selecione as guias na parte superior ou selecione **anterior: Configurações**.

    ![Criar máquina virtual CloudSimple-examinar + criar](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Passos Seguintes

* [Exibir a lista de máquinas virtuais CloudSimple](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [Gerenciar máquinas virtuais CloudSimple do Azure](https://docs.azure.cloudsimple.com/azure-manage-vm/)
