---
title: Soluções Azure VMware (AVS) Quickstart - Criar VMware VMware vMware no Azure
description: Saiba como criar e configurar VMware VMs do portal Azure utilizando soluções Azure VMware (AVS)
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 575d59d60ebfcfdbe4d234d608e8d988006773c2
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019558"
---
# <a name="quickstart---create-vmware-vms-on-azure"></a>Quickstart - Crie VMware VMs no Azure

Para criar uma máquina virtual no portal Azure, utilize modelos de máquinas virtuais que o seu administrador AVS ativou para a sua subscrição. Os modelos de VM são encontrados na infra-estrutura do VMware.

## <a name="avs-vm-creation-on-azure-requires-a-vm-template"></a>A criação de VM AVS em Azure requer um modelo VM

Crie uma máquina virtual na sua Nuvem Privada AVS a partir do vCenter UI. Para criar um modelo, siga as instruções em [clonar uma máquina virtual para um modelo no cliente Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Guarde o modelo VM no seu VCenter De Nuvem Privada AVS.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Criar uma máquina virtual no portal Azure

1. Selecione **Todos os serviços**.

2. Pesquisa de **Máquinas Virtuais AVS**.

3. Clique em **Adicionar**.

    ![Criar máquina virtual AVS](media/create-cloudsimple-virtual-machine.png)

4. Insira informações básicas e clique em **Avançar: tamanho**.

    ![Criar máquina virtual AVS - básico](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Campo | Descrição |
    | ------------ | ------------- |
    | Subscrição | Assinatura Azure associada à sua Nuvem Privada AVS. |
    | Grupo de Recursos | Grupo de recursos ao qual a VM será atribuída. Você pode selecionar um grupo existente ou criar um novo. |
    | Nome | Nome para identificar a VM.  |
    | Localização | Região do Azure na qual essa VM está hospedada.  |
    | Nuvem Privada AVS | AVS Private Cloud onde você quer criar a máquina virtual. |
    | Agrupamento de recursos | Pool de recursos mapeados para a VM. Selecione os pools de recursos disponíveis. |
    | Modelo vSphere | modelo de vSphere para a VM.  |
    | Nome de utilizador | Nome de usuário do administrador da VM (para modelos do Windows).|
    | Palavra-passe |  Senha para o administrador da VM (para modelos do Windows). |
    | Confirmar palavra-passe | Confirme a palavra-passe. |

5. Selecione o número de núcleos e a capacidade de memória para a VM e clique em **Avançar: configurações**. Marque a caixa de seleção se desejar expor a virtualização completa da CPU para o sistema operacional convidado. Aplicativos que exigem virtualização de hardware podem ser executados em máquinas virtuais sem conversão binária ou paravirtualização. Para obter mais informações, consulte o artigo do VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">expor virtualização assistida de hardware VMware</a>.

    ![Criar máquina virtual AVS - tamanho](media/create-cloudsimple-virtual-machine-size.png)

6. Configure os discos e os adaptadores de rede conforme descrito nas tabelas a seguir e clique em **revisar + criar**.

    ![Criar máquina virtual AVS - configurações](media/create-cloudsimple-virtual-machine-configurations.png)

    Para interfaces de rede, clique em **Adicionar interface de rede** e defina as configurações a seguir.

    | Controlo | Descrição |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar a interface.  |
    | Rede | Selecione na lista de grupos portuários distribuídos configurados na sua Nuvem Privada AVS vSphere. |
    | Adaptador | Selecione um adaptador vSphere na lista de tipos disponíveis configurados para a VM. Para obter mais informações, consulte o artigo da base de dados de conhecimento do VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">escolhendo um adaptador de rede para sua máquina virtual</a>. |
    | Ligar na inicialização | Escolha se deseja habilitar o hardware NIC quando a VM for inicializada. O padrão é **habilitar**. |

    Para discos, clique em **adicionar disco** e defina as configurações a seguir.

    | Item | Descrição |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar o disco. |
    | Tamanho | Selecione um dos tamanhos disponíveis. |
    | Controlador SCSI | Selecione um controlador SCSI para o disco. |
    | Modo | Determina como o disco participa em instantâneos. Escolha uma destas opções: <br> – Persistente independente: todos os dados gravados no disco são gravados permanentemente.<br> – Não persistente independente: as alterações gravadas no disco são descartadas quando você desliga ou redefine a máquina virtual. O modo não persistente independente permite que você sempre reinicie a VM no mesmo estado. Para obter mais informações, consulte a <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">documentação do VMware</a>.

7. Quando a validação for concluída, examine as configurações e clique em **criar**. Para fazer alterações, clique nas guias na parte superior ou clique em.

    ![Criar máquina virtual AVS - revisão](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Passos seguintes

* [Ver lista de máquinas virtuais AVS](azure-create-vm.md#view-list-of-avs-virtual-machines)
* [Gerir a máquina virtual AVS a partir de Azure](azure-manage-vm.md)
