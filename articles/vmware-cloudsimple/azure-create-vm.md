---
title: Azure VMware Solution by CloudSimple - Criar uma máquina virtual em Azure com modelos VM
description: Descreve como criar máquinas virtuais em Azure usando modelos VM na infraestrutura VMware para a sua CloudSimple Private Cloud
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b01afe60a78a746eb0dc5f03cc7b45989f8cf81e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898764"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Crie uma máquina virtual em Azure usando modelos VM na infraestrutura VMware

Pode criar uma máquina virtual no portal Azure utilizando modelos VM na infraestrutura VMware que o seu administrador CloudSimple ativou para a sua subscrição.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-cloudsimple-virtual-machine"></a>Criar máquina virtual CloudSimple

1. Selecione **Todos os serviços**.

2. Procure por **Máquinas Virtuais CloudSimple**.

3. Clique em **Adicionar**.

    ![Criar máquina virtual CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Introduza informações básicas clique **em Seguinte:Tamanho**.

    > [!NOTE]
    > A criação de máquinas virtuais CloudSimple no Azure requer um modelo VM.  Este modelo VM deve existir no seu VCenter Private Cloud.  Crie uma máquina virtual na sua Nuvem Privada a partir do VCenter UI com o sistema operativo e configurações desejadas.  Utilizando instruções em [Clone a Virtual Machine para um Modelo no vSphere Web Client,](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html)crie um modelo.

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
    | Nome de utilizador | Nome de utilizador do administrador VM (para modelos Windows)|
    | Palavra-passe <br>Confirmar palavra-passe | Palavra-passe para o administrador VM (para modelos Windows).  |

5. Selecione o número de núcleos e capacidade de memória para o VM e clique em **Seguinte:Configurações**. Selecione a caixa de verificação se pretender expor a virtualização total do CPU ao sistema operativo dos hóspedes para que as aplicações que requeiram virtualização de hardware possam funcionar em máquinas virtuais sem tradução binária ou paravirtualização. Para mais informações, consulte o artigo VMware [Expoe a Virtualização Assistida de Hardware VMware.](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)

    ![Criar máquina virtual CloudSimple - tamanho](media/create-cloudsimple-virtual-machine-size.png)

6. Configure interfaces de rede e discos conforme descrito nas seguintes tabelas e clique em **Rever + criar**.

    ![Criar máquina virtual CloudSimple - configurações](media/create-cloudsimple-virtual-machine-configurations.png)

    Para interfaces de rede, clique em **Adicionar interface de rede** e configufique as seguintes definições.

    | Controlar | Descrição |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar a interface.  |
    | Rede | Selecione a partir da lista de grupos de portas distribuídos configurados na sua Nuvem Privada vSphere.  |
    | Adaptador | Selecione um adaptador vSphere da lista de tipos disponíveis configurados para o VM. Para obter mais informações, consulte o artigo base de conhecimento VMware [Escolher um adaptador de rede para a sua máquina virtual.](https://kb.vmware.com/s/article/1001805) |
    | Energia ligado no Boot | Escolha se ativa o hardware NIC quando o VM for iniciado. O padrão é **Ativar**. |

    Para discos, clique **em Adicionar disco** e configurar as seguintes definições.

    | Item | Descrição |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar o disco.  |
    | Tamanho | Selecione um dos tamanhos disponíveis.  |
    | Controlador SCSI | Selecione um controlador SCSI para o disco.  |
    | Modo | Determina como o disco participa em instantâneos. Escolha uma destas opções: <br> - Persistente independente: Todos os dados escritos no disco são escritos permanentemente.<br> - Independente não persistente: As alterações escritas no disco são descartadas quando desliga ou repõe a máquina virtual.  O modo independente não persistente permite-lhe reiniciar sempre o VM no mesmo estado. Para mais informações, consulte a [documentação VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. Assim que a validação estiver concluída, reveja as definições e clique em **Criar**. Para escoar quaisquer alterações, clique nos separadores na parte superior ou clique.

    ![Create CloudSimple virtual machine - revisão](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>Ver lista de máquinas virtuais CloudSimple

1. Selecione **Todos os serviços**.

2. Procure por **Máquinas Virtuais CloudSimple**.

3. Selecione a nuvem privada em que foi criada a sua Nuvem Privada.

    ![Lista de máquinas virtuais cloudSimple](media/list-cloudsimple-virtual-machines.png)

A lista de máquinas virtuais CloudSimple inclui máquinas virtuais criadas a partir do portal Azure.  Máquinas virtuais criadas no Private Cloud vCenter no pool de recursos do vCenter mapeado serão mostradas na lista.  
