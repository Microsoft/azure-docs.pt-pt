---
title: Solução de VMware do Azure por guia de introdução do CloudSimple - consumir as VMs de VMware no Azure
description: Saiba como configurar e consumir VMs de VMware a partir do portal do Azure com a solução de VMware do Azure por CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3940adfaa42de8ac9c3f32a9eadc8f6d643ce3ce
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149557"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Início rápido - consumir as VMs de VMware no Azure

Para criar uma máquina virtual no portal do Azure, utilize modelos de Máquina Virtual que o administrador de CloudSimple tiver ativado para a sua subscrição. Estes modelos VM são encontrados na infraestrutura de VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>A criação da CloudSimple VM no Azure requer um modelo de VM

Crie uma máquina virtual na sua nuvem privada a partir do vCenter da interface do Usuário. Para criar um modelo, siga as instruções em [clonar uma Máquina Virtual para um modelo no vSphere Web Client](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Store o modelo de VM no seu vCenter de nuvem privada.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Criar uma máquina virtual no portal do Azure

1. Selecione **Todos os serviços**.

2. Procure **máquinas de virtuais de CloudSimple**.

3. Clique em **Adicionar**.

    ![Criar máquina virtual de CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Introduza o clique de informações básicas **próxima: tamanho**.

    ![Criar máquina virtual de CloudSimple - Noções básicas](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Campo | Descrição |
    | ------------ | ------------- |
    | Subscrição | Subscrição do Azure associada a sua nuvem privada.  |
    | Grupo de Recursos | Grupo de recursos para o qual a VM será atribuída. Pode selecionar um grupo existente ou crie um novo. |
    | Name | Nome para identificar a VM.  |
    | Location | Região do Azure na qual esta VM está alojada.  |
    | Nuvem privada | Nuvem privada CloudSimple onde pretende criar a máquina virtual. |
    | Agrupamento de recursos | Mapear o agrupamento de recursos para a VM. Selecione os agrupamentos de recursos disponíveis. |
    | Modelo do vSphere | modelo do vSphere para a VM.  |
    | Nome de utilizador | Nome de utilizador do administrador VM (para modelos do Windows)|
    | Palavra-passe |  Palavra-passe para o administrador VM (para modelos do Windows). |
    | Confirmar palavra-passe | Confirme a palavra-passe |

5. Selecione o número de núcleos e a capacidade de memória da VM e clique em **próxima: configurações**. Selecione a caixa de verificação se quiser expor virtualização total de CPU para o sistema operativo convidado. Aplicações que requerem a virtualização de hardware podem ser executadas em máquinas virtuais sem conversão binária ou de paravirtualização. Para obter mais informações, consulte o artigo do VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">expor Hardware assistido virtualização do VMware</a>.

    ![Criar máquina virtual de CloudSimple - tamanho](media/create-cloudsimple-virtual-machine-size.png)

6. Configurar discos e interfaces de rede, conforme descrito nas tabelas seguintes e clique em **rever + criar**.

    ![Criar máquina virtual de CloudSimple - configurações](media/create-cloudsimple-virtual-machine-configurations.png)

    Para interfaces de rede, clique em **interface de rede de Add** e configure as seguintes definições.
    
    | Controlo | Descrição |
    | ------------ | ------------- |
    | Name | Introduza um nome para identificar a interface.  |
    | Rede | Selecione na lista do grupo de porta distribuída configurada no vSphere sua nuvem privada.  |
    | Placa | Selecione um adaptador de vSphere na lista de tipos disponíveis configurado para a VM. Para obter mais informações, consulte o artigo da base de dados de conhecimento VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">escolher um adaptador de rede para a máquina virtual</a>. |
    | Ligar no arranque | Escolha se pretende ativar o hardware do NIC quando arranca a VM. A predefinição é **ativar**. |

    Para discos, clique em **adicionar disco** e configure as seguintes definições.

    | Item | Descrição | 
    | ------------ | ------------- | 
    | Name | Introduza um nome para identificar o disco.  | 
    | Tamanho | Selecione um dos tamanhos disponíveis.  | 
    | Controlador SCSI | Selecione um controlador de SCSI para o disco.  |
    | Modo | Determina a forma como o disco participa de instantâneos. Escolha uma destas opções: <br> -Independente persistente: Todos os dados escritos no disco é escrito permanentemente.<br> -Independente não persistentes: As alterações escritas no disco são eliminadas quando desligar ou repor a máquina virtual.  Modo de não persistentes independente permite-lhe sempre de reiniciar a VM no mesmo Estado. Para obter mais informações, consulte a <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">documentação do VMware</a>.

7. Uma vez concluída a validação, reveja as definições e clique em **criar**. Para fazer alterações, clique nos separadores na parte superior ou clique em.

    ![Criar a máquina virtual de CloudSimple - rever](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Passos Seguintes

* [Ver a lista de máquinas de virtuais de CloudSimple](https://docs.azure.cloudsimple.com/azure-manage-vm/)
* [Gerir a máquina de virtual de CloudSimple do Azure](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)