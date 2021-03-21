---
title: 'Quickstart: Consumir VMware VMs em Azure'
titleSuffix: Azure VMware Solution by CloudSimple
description: Saiba como configurar e consumir VMware VMs do portal Azure usando Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ab613c251bc43a025e0381046805ec998a04227
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "77019558"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Quickstart - Consumir VMware VMs em Azure

Para criar uma máquina virtual no portal Azure, utilize modelos de máquinas virtuais que o seu administrador CloudSimple tenha ativado para a sua subscrição. Os modelos VM encontram-se na infraestrutura VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>CloudSimple VM criação em Azure requer um modelo VM

Crie uma máquina virtual na sua Nuvem Privada a partir do uI do vCenter. Para criar um modelo, siga as instruções em [Clone a Virtual Machine para um Modelo no vSphere Web Client](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Guarde o modelo VM no seu VCenter Private Cloud.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Criar uma máquina virtual no Portal do Azure

1. Selecione **Todos os serviços**.

2. Procure por **Máquinas Virtuais CloudSimple**.

3. Clique em **Adicionar**.

    ![Criar máquina virtual CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Introduza informações básicas e clique **em Seguinte:Tamanho**.

    ![Criar máquina virtual CloudSimple - básicos](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Campo | Descrição |
    | ------------ | ------------- |
    | Subscrição | Subscrição Azure associada à sua Nuvem Privada.  |
    | Grupo de Recursos | Grupo de recursos ao qual o VM será atribuído. Pode selecionar um grupo existente ou criar um novo. |
    | Name | Nome para identificar o VM.  |
    | Localização | Região de Azure em que este VM está hospedado.  |
    | Cloud Privada | CloudSimple Private Cloud onde pretende criar a máquina virtual. |
    | Piscina de Recursos | Conjunto de recursos mapeado para o VM. Selecione entre os pools de recursos disponíveis. |
    | vSphere Modelo | vSphere modelo para o VM.  |
    | Nome de utilizador | Nome de utilizador do administrador VM (para modelos Windows).|
    | Palavra-passe |  Palavra-passe para o administrador VM (para modelos Windows). |
    | Confirmar palavra-passe | Confirme a palavra-passe. |

5. Selecione o número de núcleos e capacidade de memória para o VM e clique em **Seguinte:Configurações**. Selecione a caixa de verificação se pretender expor a virtualização total do CPU ao sistema operativo do hóspede. As aplicações que requerem virtualização de hardware podem funcionar em máquinas virtuais sem tradução binária ou paravirtualização. Para mais informações, consulte o artigo VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">Expoe a Virtualização Assistida de Hardware VMware.</a>

    ![Criar máquina virtual CloudSimple - tamanho](media/create-cloudsimple-virtual-machine-size.png)

6. Configure interfaces de rede e discos conforme descrito nas seguintes tabelas e clique em **Rever + criar**.

    ![Criar máquina virtual CloudSimple - configurações](media/create-cloudsimple-virtual-machine-configurations.png)

    Para interfaces de rede, clique em **Adicionar interface de rede** e configufique as seguintes definições.

    | Controlar | Descrição |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar a interface.  |
    | Rede | Selecione a partir da lista de grupos de portas distribuídos configurados na sua Nuvem Privada vSphere.  |
    | Adaptador | Selecione um adaptador vSphere da lista de tipos disponíveis configurados para o VM. Para obter mais informações, consulte o artigo base de conhecimento VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">Escolher um adaptador de rede para a sua máquina virtual.</a> |
    | Energia ligado no Boot | Escolha se ativa o hardware NIC quando o VM for iniciado. O padrão é **Ativar**. |

    Para discos, clique **em Adicionar disco** e configurar as seguintes definições.

    | Item | Descrição |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar o disco.  |
    | Tamanho | Selecione um dos tamanhos disponíveis.  |
    | Controlador SCSI | Selecione um controlador SCSI para o disco.  |
    | Modo | Determina como o disco participa em instantâneos. Escolha uma destas opções: <br> - Persistente independente: Todos os dados escritos no disco são escritos permanentemente.<br> - Independente não persistente: As alterações escritas no disco são descartadas quando desliga ou repõe a máquina virtual.  O modo independente não persistente permite-lhe reiniciar sempre o VM no mesmo estado. Para mais informações, consulte a <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">documentação VMware</a>.

7. Quando a validação estiver concluída, reveja as definições e clique em **Criar**. Para escoar quaisquer alterações, clique nos separadores na parte superior ou clique.

    ![Create CloudSimple virtual machine - revisão](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Passos seguintes

* [Ver lista de máquinas virtuais CloudSimple](azure-create-vm.md#view-list-of-cloudsimple-virtual-machines)
* [Gerir a máquina virtual CloudSimple a partir de Azure](azure-manage-vm.md)
