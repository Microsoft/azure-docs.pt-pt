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
ms.openlocfilehash: 58edadb553730b646f23f4981d6cbf1bdbfe76d5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577724"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Início rápido - consumir as VMs de VMware no Azure

Para criar uma máquina virtual no portal do Azure, utilize modelos de Máquina Virtual que o administrador de CloudSimple tiver ativado para a sua subscrição. Estes modelos VM são encontrados na infraestrutura de VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>A criação da CloudSimple VM no Azure requer um modelo de VM

Crie uma máquina virtual na sua nuvem privada a partir do vCenter da interface do Usuário. Para criar um modelo, siga as instruções em [clonar uma Máquina Virtual para um modelo no vSphere Web Client](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Store o modelo de VM no seu vCenter de nuvem privada.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Criar uma máquina virtual no portal do Azure

1. No menu da esquerda, clique em **+** ou **criar um recurso**.

2. No menu da esquerda, clique em **computação**e, em seguida, clique em **Máquina Virtual de CloudSimple**.

3. Clique em **confirmar** para verificar que deseja criar uma nova VM.

4. Definir a configuração básica, conforme descrito na tabela seguinte e, em seguida, clique em **seguinte: Tamanho**.

    | Campo | Descrição |
    | ------------ | ------------- |
    | Subscrição | Subscrição do Azure associada a sua implementação de nuvem privada.  |
    | Grupo de Recursos | Grupo de implementação para o qual a VM será atribuída. Pode selecionar um grupo existente ou crie um novo. |
    | Name | Nome para identificar a VM.  |
    | Location | Região do Azure na qual esta VM está alojada.  |
    | Agrupamento de recursos | Recursos físicos para a VM. Selecione os agrupamentos de recursos disponíveis. |
    | Modelo do vSphere | Tipo de modelo de sistema operativo para a VM.  |
    | Nome de utilizador | Nome de utilizador do administrador VM. |
    | Confirmar palavra-passe do palavra-passe | Palavra-passe para o administrador VM.  |

5. Selecione o número de núcleos e a capacidade de memória para a VM.

6. (Opcional) Se quiser expor virtualização total de CPU para o sistema operativo convidado, selecione o **expor ao SO convidado** caixa de verificação.
Esta seleção permite que as aplicações que requerem a virtualização de hardware para serem executadas em máquinas virtuais sem conversão binária ou de paravirtualização. Para obter mais informações, consulte o artigo do VMware [expor Hardware assistido virtualização do VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

7. Clique em **seguinte: Configuração**.

8. Configure as interfaces de rede e discos, conforme descrito nas tabelas seguintes.

    Para interfaces de rede, clique em **interface de rede de Add** e configure as seguintes definições.

    | Controlo | Descrição |
    | ------------ | ------------- |
    | Name | Introduza um nome para identificar a interface.  |
    | Rede | Selecione na lista de redes configuradas no vSphere sua nuvem privada.  |
    | Placa | Selecione um adaptador de vSphere na lista de tipos disponíveis configurado para a VM. Para obter mais informações, consulte o artigo da base de dados de conhecimento VMware [escolher um adaptador de rede para a máquina virtual](https://kb.vmware.com/s/article/1001805). |
    | Ligar no arranque | Escolha se pretende ativar o hardware do NIC quando arranca a VM. A predefinição é **ativar**. |

    Para discos, clique em **adicionar disco** e configure as seguintes definições.

    | Item | Descrição |
    | ------------ | ------------- |
    | Name | Introduza um nome para identificar o disco.  |
    | Tamanho | Selecione um dos tamanhos disponíveis.  |
    | Controlador SCSI | Selecione um controlador SCSI. Os controladores disponíveis variam para diferentes sistemas operativos suportados.  |
    | Modo | Determina a forma como o disco participa de instantâneos. Escolha uma destas opções: <br> -Independente persistente: Todos os dados escritos no disco é escrito permanentemente.<br> -Independente não persistentes: As alterações escritas no disco são eliminadas quando desligar ou repor a máquina virtual.  Modo de não persistentes independente permite-lhe sempre de reiniciar a VM no mesmo Estado. Para obter mais informações, consulte o [documentação do VMware.](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)

9. Reveja as definições. Para fazer alterações, clique nos separadores na parte superior.

10. Clique em **criar** para guardar as definições e criar a VM.

## <a name="next-steps"></a>Passos Seguintes

* [Ver a lista de máquinas de virtuais de CloudSimple](https://docs.azure.cloudsimple.com/azurelistvms/)
* [Gerir a máquina de virtual de CloudSimple do Azure](https://docs.azure.cloudsimple.com/azureoverviewpage/)