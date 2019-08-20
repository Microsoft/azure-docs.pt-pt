---
title: Solução do Azure VMware por CloudSimple-criar uma máquina virtual no Azure com modelos de VM
description: Descreve como criar máquinas virtuais no Azure usando modelos de VM na infraestrutura do VMware para sua nuvem privada do CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee3029de9826aee17dc76d0e69f08b3c1068423b
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576839"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Criar uma máquina virtual no Azure usando modelos de VM na infra-estrutura do VMware

Você pode criar uma máquina virtual no portal do Azure usando modelos de VM na infra-estrutura do VMware que o administrador do CloudSimple habilitou para sua assinatura.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-cloudsimple-virtual-machine"></a>Criar máquina virtual CloudSimple

1. Selecione **Todos os serviços**.

2. Procure **máquinas virtuais CloudSimple**.

3. Clique em **Adicionar**.

    ![Criar máquina virtual CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Inserir informações básicas clique em **Avançar: tamanho**.

    > [!NOTE]
    > A criação de máquina virtual CloudSimple no Azure requer um modelo de VM.  Esse modelo de VM deve existir em seu vCenter de nuvem privada.  Crie uma máquina virtual em sua nuvem privada da interface do usuário do vCenter com o sistema operacional e as configurações desejadas.  Usando as instruções em [clonar uma máquina virtual para um modelo no cliente Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html), crie um modelo.

    ![Criar máquina virtual CloudSimple-noções básicas](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Campo | Descrição |
    | ------------ | ------------- |
    | Subscription | Assinatura do Azure associada à sua nuvem privada.  |
    | Grupo de Recursos | Grupo de recursos ao qual a VM será atribuída. Você pode selecionar um grupo existente ou criar um novo. |
    | Name | Nome para identificar a VM.  |
    | Location | Região do Azure na qual essa VM está hospedada.  |
    | Cloud Privada | CloudSimple nuvem privada em que você deseja criar a máquina virtual. |
    | Agrupamento de recursos | Pool de recursos mapeados para a VM. Selecione os pools de recursos disponíveis. |
    | Modelo vSphere | modelo de vSphere para a VM.  |
    | Nome de utilizador | Nome de usuário do administrador da VM (para modelos do Windows)|
    | Palavra-passe <br>Confirmar palavra-passe | Senha para o administrador da VM (para modelos do Windows).  |

5. Selecione o número de núcleos e a capacidade de memória para a VM e clique em **Avançar: configurações**. Marque a caixa de seleção se você quiser expor a virtualização de CPU completa para o sistema operacional convidado para que os aplicativos que exigem a virtualização de hardware possam ser executados em máquinas virtuais sem conversão binária ou paravirtualização. Para obter mais informações, consulte o artigo do VMware [expor virtualização assistida de hardware VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

    ![Criar máquina virtual CloudSimple-tamanho](media/create-cloudsimple-virtual-machine-size.png)

6. Configure os discos e os adaptadores de rede conforme descrito nas tabelas a seguir e clique em revisar **+ criar**.

    ![Criar máquina virtual CloudSimple-configurações](media/create-cloudsimple-virtual-machine-configurations.png)

    Para interfaces de rede, clique em **Adicionar interface de rede** e defina as configurações a seguir.

    | Controlar | Descrição |
    | ------------ | ------------- |
    | Name | Insira um nome para identificar a interface.  |
    | Rede | Selecione na lista de grupos de portas distribuídas configuradas em sua nuvem privada vSphere.  |
    | Placas | Selecione um adaptador vSphere na lista de tipos disponíveis configurados para a VM. Para obter mais informações, consulte o artigo da base de dados de conhecimento do VMware [escolhendo um adaptador de rede para sua máquina virtual](https://kb.vmware.com/s/article/1001805). |
    | Ligar na inicialização | Escolha se deseja habilitar o hardware NIC quando a VM for inicializada. O padrão é **habilitar**. |

    Para discos, clique em **adicionar disco** e defina as configurações a seguir.

    | Item | Descrição |
    | ------------ | ------------- |
    | Name | Insira um nome para identificar o disco.  |
    | Size | Selecione um dos tamanhos disponíveis.  |
    | Controlador SCSI | Selecione um controlador SCSI para o disco.  |
    | Modo | Determina como o disco participa em instantâneos. Escolha uma destas opções: <br> – Persistente independente: Todos os dados gravados no disco são gravados permanentemente.<br> -Não persistente independente: As alterações gravadas no disco são descartadas quando você desliga ou redefine a máquina virtual.  O modo não persistente independente permite que você sempre reinicie a VM no mesmo estado. Para obter mais informações, consulte a [documentação do VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. Após a conclusão da validação, examine as configurações e clique em **criar**. Para fazer alterações, clique nas guias na parte superior ou clique em.

    ![Criar máquina virtual CloudSimple-examinar](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>Exibir a lista de máquinas virtuais CloudSimple

1. Selecione **Todos os serviços**.

2. Procure **máquinas virtuais CloudSimple**.

3. Selecione o em que sua nuvem privada foi criada.

    ![Lista de máquinas virtuais CloudSimple](media/list-cloudsimple-virtual-machines.png)

A lista de máquinas virtuais CloudSimple inclui máquinas virtuais criadas a partir de portal do Azure.  As máquinas virtuais criadas no vCenter de nuvem privada no pool de recursos do vCenter mapeado serão mostradas na lista.  
