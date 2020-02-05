---
title: Azure VMware Solutions (AVS) - Crie uma máquina virtual em Azure com modelos VM
description: Descreve como criar máquinas virtuais em Azure usando modelos VM na infraestrutura VMware para a sua Nuvem Privada AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 533aaab13f1b957e709f66b23b511fc199ee0285
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015206"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Crie uma máquina virtual em Azure usando modelos VM na infraestrutura VMware

Pode criar uma máquina virtual no portal Azure utilizando modelos VM na infraestrutura VMware que o seu administrador AVS ativou para a sua subscrição.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="create-avs-virtual-machine"></a>Criar máquina virtual AVS

1. Selecione **Todos os serviços**.

2. Pesquisa de **Máquinas Virtuais AVS**.

3. Clique em **Adicionar**.

    ![Criar máquina virtual AVS](media/create-cloudsimple-virtual-machine.png)

4. Introduza informações básicas clique em **Next:Size**.

    > [!NOTE]
    > A criação de máquina virtual AVS em Azure requer um modelo VM. Este modelo VM deve existir no seu vCenter De Nuvem Privada. Crie uma máquina virtual na sua Nuvem Privada a partir do VCenter UI com o sistema operativo pretendido e configurações. Utilizando instruções em [Clone uma Máquina Virtual para um modelo no Cliente Web da vSphere,](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html)crie um modelo.

    ![Criar máquina virtual AVS - básico](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Campo | Descrição |
    | ------------ | ------------- |
    | Subscrição | Assinatura do Azure associada à sua nuvem privada.  |
    | Grupo de Recursos | Grupo de recursos ao qual a VM será atribuída. Você pode selecionar um grupo existente ou criar um novo. |
    | Nome | Nome para identificar a VM.  |
    | Localização | Região do Azure na qual essa VM está hospedada.  |
    | Cloud Privada | AVS Private Cloud onde você quer criar a máquina virtual. |
    | Agrupamento de recursos | Pool de recursos mapeados para a VM. Selecione os pools de recursos disponíveis. |
    | Modelo vSphere | modelo de vSphere para a VM.  |
    | Nome de utilizador | Nome de utilizador do administrador VM (para modelos Windows)|
    | Palavra-passe <br>Confirmar palavra-passe | Senha para o administrador da VM (para modelos do Windows).  |

5. Selecione o número de núcleos e a capacidade de memória para a VM e clique em **Avançar: configurações**. Selecione a caixa de verificação se pretender expor a virtualização completa do CPU ao sistema operativo do hóspede para que as aplicações que requerem virtualização de hardware possam funcionar em máquinas virtuais sem tradução binária ou paravirtualização. Para obter mais informações, consulte o artigo do VMware [expor virtualização assistida de hardware VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

    ![Criar máquina virtual AVS - tamanho](media/create-cloudsimple-virtual-machine-size.png)

6. Configure os discos e os adaptadores de rede conforme descrito nas tabelas a seguir e clique em **revisar + criar**.

    ![Criar máquina virtual AVS - configurações](media/create-cloudsimple-virtual-machine-configurations.png)

    Para interfaces de rede, clique em **Adicionar interface de rede** e defina as configurações a seguir.

    | Controlo | Descrição |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar a interface.  |
    | Rede | Selecione na lista de grupos de portas distribuídas configuradas em sua nuvem privada vSphere.  |
    | Adaptador | Selecione um adaptador vSphere na lista de tipos disponíveis configurados para a VM. Para obter mais informações, consulte o artigo da base de dados de conhecimento do VMware [escolhendo um adaptador de rede para sua máquina virtual](https://kb.vmware.com/s/article/1001805). |
    | Ligar na inicialização | Escolha se deseja habilitar o hardware NIC quando a VM for inicializada. O padrão é **habilitar**. |

    Para discos, clique em **adicionar disco** e defina as configurações a seguir.

    | Item | Descrição |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar o disco.  |
    | Tamanho | Selecione um dos tamanhos disponíveis.  |
    | Controlador SCSI | Selecione um controlador SCSI para o disco.  |
    | Modo | Determina como o disco participa em instantâneos. Escolha uma destas opções: <br> – Persistente independente: todos os dados gravados no disco são gravados permanentemente.<br> – Não persistente independente: as alterações gravadas no disco são descartadas quando você desliga ou redefine a máquina virtual. O modo não persistente independente permite que você sempre reinicie a VM no mesmo estado. Para obter mais informações, consulte a [documentação do VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. Assim que a validação estiver concluída, reveja as definições e clique em **Criar**. Para fazer alterações, clique nas guias na parte superior ou clique em.

    ![Criar máquina virtual AVS - revisão](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-avs-virtual-machines"></a>Ver lista de máquinas virtuais AVS

1. Selecione **Todos os serviços**.

2. Pesquisa de **Máquinas Virtuais AVS**.

3. Selecione o em que a sua Nuvem Privada foi criada.

    ![Lista de Máquinas Virtuais AVS](media/list-cloudsimple-virtual-machines.png)

A lista de máquinas virtuais AVS inclui máquinas virtuais criadas a partir do portal Azure.  As máquinas virtuais criadas no vCenter de Cloud Privada no conjunto de recursos vCenter mapeado serão mostradas na lista.  
