---
title: 'Quickstart: Consumir VMware VMs no Azure'
titleSuffix: Azure VMware Solution by CloudSimple
description: Saiba como configurar e consumir VMware VMs do portal Azure usando a Solução Azure VMware by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ab613c251bc43a025e0381046805ec998a04227
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77019558"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Quickstart - Consumir VMs VMware no Azure

Para criar uma máquina virtual no portal Azure, utilize modelos de máquinas virtuais que o seu administrador CloudSimple ativou para a sua subscrição. Os modelos VM são encontrados na infraestrutura VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>A criação de CloudSimple VM no Azure requer um modelo VM

Crie uma máquina virtual na sua Nuvem Privada a partir do vCenter UI. Para criar um modelo, siga as instruções em [Clone a Virtual Machine até um modelo no cliente web da vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Guarde o modelo VM no seu vCenter De Nuvem Privada.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Criar uma máquina virtual no Portal do Azure

1. Selecione **Todos os serviços**.

2. Pesquisa por **CloudSimple Virtual Machines**.

3. Clique em **Adicionar**.

    ![Criar a máquina virtual CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Introduza informações básicas e clique em **Next:Size**.

    ![Criar cloudSimple máquina virtual - básicos](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Campo | Descrição |
    | ------------ | ------------- |
    | Subscrição | Assinatura Azure associada à sua Nuvem Privada.  |
    | Grupo de Recursos | Grupo de recursos para o qual o VM será atribuído. Pode selecionar um grupo existente ou criar um novo. |
    | Nome | Nome para identificar o VM.  |
    | Localização | Região azul na qual este VM está hospedado.  |
    | Cloud Privada | CloudSimple Private Cloud onde pretende criar a máquina virtual. |
    | Piscina de Recursos | Piscina de recursos mapeado para o VM. Selecione a partir dos recursos disponíveis. |
    | modelo de vSphere | modelo vSphere para o VM.  |
    | Nome de utilizador | Nome de utilizador do administrador VM (para modelos Windows).|
    | Palavra-passe |  Palavra-passe para o administrador VM (para modelos Windows). |
    | Confirmar palavra-passe | Confirme a palavra-passe. |

5. Selecione o número de núcleos e capacidade de memória para o VM e clique em **Seguinte:Configurações**. Selecione a caixa de verificação se pretender expor a virtualização completa do CPU ao sistema operativo do hóspede. Aplicações que requerem virtualização de hardware podem funcionar em máquinas virtuais sem tradução binária ou paravirtualização. Para mais informações, consulte o artigo vMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">Expondo a Virtualização Assistida de Hardware VMware</a>.

    ![Criar cloudSimple máquina virtual - tamanho](media/create-cloudsimple-virtual-machine-size.png)

6. Configure as interfaces e discos de rede conforme descrito nas tabelas seguintes e clique em **Rever + criar**.

    ![Criar cloudSimple máquina virtual - configurações](media/create-cloudsimple-virtual-machine-configurations.png)

    Para interfaces de rede, clique em **Adicionar interface de rede** e configurar as seguintes definições.

    | Controlo | Descrição |
    | ------------ | ------------- |
    | Nome | Introduza um nome para identificar a interface.  |
    | Rede | Selecione na lista de grupos portuários distribuídos configurados na sua nuvem privada vSphere.  |
    | Adaptador | Selecione um adaptador vSphere da lista de tipos disponíveis configurados para o VM. Para mais informações, consulte o artigo base de conhecimento VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">Escolhendo um adaptador</a>de rede para a sua máquina virtual . |
    | Poder ligado no Boot | Escolha se ativa o hardware NIC quando o VM é iniciado. O predefinido é **Ativar**. |

    Para os discos, clique em **Adicionar disco** e configurar as seguintes definições.

    | Item | Descrição |
    | ------------ | ------------- |
    | Nome | Introduza um nome para identificar o disco.  |
    | Tamanho | Selecione um dos tamanhos disponíveis.  |
    | Controlador SCSI | Selecione um controlador SCSI para o disco.  |
    | Modo | Determina como o disco participa em instantâneos. Escolha uma destas opções: <br> - Persistente independente: Todos os dados escritos ao disco são escritos permanentemente.<br> - Independente não persistente: As alterações escritas no disco são descartadas quando se desliga ou reinstala a máquina virtual.  O modo independente não persistente permite reiniciar sempre o VM no mesmo estado. Para mais informações, consulte a <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">documentação vMware</a>.

7. Quando a validação estiver concluída, reveja as definições e clique em **Criar**. Para efazer quaisquer alterações, clique nos separadores na parte superior ou clique.

    ![Criar cloudSimple máquina virtual - revisão](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Passos seguintes

* [Ver lista de máquinas virtuais CloudSimple](azure-create-vm.md#view-list-of-cloudsimple-virtual-machines)
* [Gerir a máquina virtual CloudSimple a partir de Azure](azure-manage-vm.md)
