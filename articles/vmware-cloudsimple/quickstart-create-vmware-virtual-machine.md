---
title: 'Início rápido: consumir VMs VMware no Azure'
titleSuffix: Azure VMware Solution by CloudSimple
description: Saiba como configurar e consumir VMs VMware de portal do Azure usando a solução Azure VMware da CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ab613c251bc43a025e0381046805ec998a04227
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452360"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Início rápido – consumir VMs VMware no Azure

Para criar uma máquina virtual no portal do Azure, use modelos de máquina virtual que o administrador do CloudSimple habilitou para sua assinatura. Os modelos de VM são encontrados na infra-estrutura do VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>A criação de VM CloudSimple no Azure requer um modelo de VM

Crie uma máquina virtual em sua nuvem privada na interface do usuário do vCenter. Para criar um modelo, siga as instruções em [clonar uma máquina virtual para um modelo no cliente Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Armazene o modelo de VM em seu vCenter de nuvem privada.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Criar uma máquina virtual no Portal do Azure

1. Selecione **Todos os serviços**.

2. Procure **máquinas virtuais CloudSimple**.

3. Clique em **Adicionar**.

    ![Criar máquina virtual CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Insira informações básicas e clique em **Avançar: tamanho**.

    ![Criar máquina virtual CloudSimple-noções básicas](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Campo | Descrição |
    | ------------ | ------------- |
    | Subscrição | Assinatura do Azure associada à sua nuvem privada.  |
    | Grupo de Recursos | Grupo de recursos ao qual a VM será atribuída. Você pode selecionar um grupo existente ou criar um novo. |
    | Nome | Nome para identificar a VM.  |
    | Localização | Região do Azure na qual essa VM está hospedada.  |
    | Cloud Privada | CloudSimple nuvem privada em que você deseja criar a máquina virtual. |
    | Agrupamento de recursos | Pool de recursos mapeados para a VM. Selecione os pools de recursos disponíveis. |
    | Modelo vSphere | modelo de vSphere para a VM.  |
    | Nome de utilizador | Nome de usuário do administrador da VM (para modelos do Windows).|
    | Palavra-passe |  Senha para o administrador da VM (para modelos do Windows). |
    | Confirmar palavra-passe | Confirme a palavra-passe. |

5. Selecione o número de núcleos e a capacidade de memória para a VM e clique em **Avançar: configurações**. Marque a caixa de seleção se desejar expor a virtualização completa da CPU para o sistema operacional convidado. Aplicativos que exigem virtualização de hardware podem ser executados em máquinas virtuais sem conversão binária ou paravirtualização. Para obter mais informações, consulte o artigo do VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">expor virtualização assistida de hardware VMware</a>.

    ![Criar máquina virtual CloudSimple-tamanho](media/create-cloudsimple-virtual-machine-size.png)

6. Configure os discos e os adaptadores de rede conforme descrito nas tabelas a seguir e clique em **revisar + criar**.

    ![Criar máquina virtual CloudSimple-configurações](media/create-cloudsimple-virtual-machine-configurations.png)

    Para interfaces de rede, clique em **Adicionar interface de rede** e defina as configurações a seguir.

    | Controlo | Descrição |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar a interface.  |
    | Rede | Selecione na lista de grupos de portas distribuídas configuradas em sua nuvem privada vSphere.  |
    | Adaptador | Selecione um adaptador vSphere na lista de tipos disponíveis configurados para a VM. Para obter mais informações, consulte o artigo da base de dados de conhecimento do VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">escolhendo um adaptador de rede para sua máquina virtual</a>. |
    | Ligar na inicialização | Escolha se deseja habilitar o hardware NIC quando a VM for inicializada. O padrão é **habilitar**. |

    Para discos, clique em **adicionar disco** e defina as configurações a seguir.

    | Item | Descrição |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar o disco.  |
    | Tamanho | Selecione um dos tamanhos disponíveis.  |
    | Controlador SCSI | Selecione um controlador SCSI para o disco.  |
    | Modo | Determina como o disco participa em instantâneos. Escolha uma destas opções: <br> – Persistente independente: todos os dados gravados no disco são gravados permanentemente.<br> – Não persistente independente: as alterações gravadas no disco são descartadas quando você desliga ou redefine a máquina virtual.  O modo não persistente independente permite que você sempre reinicie a VM no mesmo estado. Para obter mais informações, consulte a <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">documentação do VMware</a>.

7. Quando a validação for concluída, examine as configurações e clique em **criar**. Para fazer alterações, clique nas guias na parte superior ou clique em.

    ![Criar máquina virtual CloudSimple-examinar](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Passos seguintes

* [Exibir a lista de máquinas virtuais CloudSimple](azure-create-vm.md#view-list-of-cloudsimple-virtual-machines)
* [Gerenciar a máquina virtual CloudSimple do Azure](azure-manage-vm.md)
