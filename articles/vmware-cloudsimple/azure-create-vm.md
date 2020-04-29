---
title: Solução Azure VMware by CloudSimple - Crie uma máquina virtual em Azure com modelos VM
description: Descreve como criar máquinas virtuais em Azure usando modelos VM na infraestrutura VMware para a sua CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee3029de9826aee17dc76d0e69f08b3c1068423b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79244696"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Crie uma máquina virtual em Azure usando modelos VM na infraestrutura VMware

Pode criar uma máquina virtual no portal Azure utilizando modelos VM na infraestrutura VMware que o seu administrador CloudSimple ativou para a sua subscrição.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-cloudsimple-virtual-machine"></a>Criar a máquina virtual CloudSimple

1. Selecione **Todos os serviços**.

2. Pesquisa por **CloudSimple Virtual Machines**.

3. Clique em **Adicionar**.

    ![Criar a máquina virtual CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Introduza informações básicas clique em **Next:Size**.

    > [!NOTE]
    > A criação de máquinavirtual CloudSimple no Azure requer um modelo VM.  Este modelo VM deve existir no seu vCenter De Nuvem Privada.  Crie uma máquina virtual na sua Nuvem Privada a partir do VCenter UI com o sistema operativo pretendido e configurações.  Utilizando instruções em [Clone uma Máquina Virtual para um modelo no Cliente Web da vSphere,](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html)crie um modelo.

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
    | Nome de utilizador | Nome de utilizador do administrador VM (para modelos Windows)|
    | Palavra-passe <br>Confirmar palavra-passe | Palavra-passe para o administrador VM (para modelos Windows).  |

5. Selecione o número de núcleos e capacidade de memória para o VM e clique em **Seguinte:Configurações**. Selecione a caixa de verificação se pretender expor a virtualização completa do CPU ao sistema operativo do hóspede para que as aplicações que requerem virtualização de hardware possam funcionar em máquinas virtuais sem tradução binária ou paravirtualização. Para mais informações, consulte o artigo vMware [Expondo a Virtualização Assistida de Hardware VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

    ![Criar cloudSimple máquina virtual - tamanho](media/create-cloudsimple-virtual-machine-size.png)

6. Configure as interfaces e discos de rede conforme descrito nas tabelas seguintes e clique em **Rever + criar**.

    ![Criar cloudSimple máquina virtual - configurações](media/create-cloudsimple-virtual-machine-configurations.png)

    Para interfaces de rede, clique em **Adicionar interface de rede** e configurar as seguintes definições.

    | Controlo | Descrição |
    | ------------ | ------------- |
    | Nome | Introduza um nome para identificar a interface.  |
    | Rede | Selecione na lista de grupos portuários distribuídos configurados na sua nuvem privada vSphere.  |
    | Adaptador | Selecione um adaptador vSphere da lista de tipos disponíveis configurados para o VM. Para mais informações, consulte o artigo base de conhecimento VMware [Escolhendo um adaptador](https://kb.vmware.com/s/article/1001805)de rede para a sua máquina virtual . |
    | Poder ligado no Boot | Escolha se ativa o hardware NIC quando o VM é iniciado. O predefinido é **Ativar**. |

    Para os discos, clique em **Adicionar disco** e configurar as seguintes definições.

    | Item | Descrição |
    | ------------ | ------------- |
    | Nome | Introduza um nome para identificar o disco.  |
    | Tamanho | Selecione um dos tamanhos disponíveis.  |
    | Controlador SCSI | Selecione um controlador SCSI para o disco.  |
    | Modo | Determina como o disco participa em instantâneos. Escolha uma destas opções: <br> - Persistente independente: Todos os dados escritos ao disco são escritos permanentemente.<br> - Independente não persistente: As alterações escritas no disco são descartadas quando se desliga ou reinstala a máquina virtual.  O modo independente não persistente permite reiniciar sempre o VM no mesmo estado. Para mais informações, consulte a [documentação vMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. Assim que a validação estiver concluída, reveja as definições e clique em **Criar**. Para efazer quaisquer alterações, clique nos separadores na parte superior ou clique.

    ![Criar cloudSimple máquina virtual - revisão](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>Ver lista de máquinas virtuais CloudSimple

1. Selecione **Todos os serviços**.

2. Pesquisa por **CloudSimple Virtual Machines**.

3. Selecione o em que a sua Nuvem Privada foi criada.

    ![Lista de Máquinas Virtuais CloudSimple](media/list-cloudsimple-virtual-machines.png)

A lista de máquinas virtuais CloudSimple inclui máquinas virtuais criadas a partir do portal Azure.  As máquinas virtuais criadas no vCenter de Cloud Privada no conjunto de recursos vCenter mapeado serão mostradas na lista.  
