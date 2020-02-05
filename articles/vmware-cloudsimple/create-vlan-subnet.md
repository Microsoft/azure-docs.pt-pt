---
title: Criar VLANs/subnets
description: Azure VMware Solutions (AVS) - Descreve como criar e gerir VLANs/subredes para as suas Nuvens Privadas AVS e, em seguida, aplicar regras de firewall.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 249c48500dbcd75f62f856b3345b3a2c02502d1a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024777"
---
# <a name="create-and-manage-vlanssubnets-for-your-avs-private-clouds"></a>Crie e gerencie VLANs/subnets para as suas Nuvens Privadas AVS

Abra o separador VLANs/Subnets na página da Rede para criar e gerir VLANs/subredes para as suas Nuvens Privadas AVS. Depois de criar uma VLAN/subnet, pode aplicar regras de firewall.

## <a name="create-a-vlansubnet"></a>Criar uma VLAN/subnet

1. [Aceda ao portal AVS](access-cloudsimple-portal.md) e selecione **Rede** no menu lateral.
2. Selecione **VLANs/subnets**.
3. Clique em **criar VLAN/sub-rede**.

    ![Página VLAN/sub-rede](media/vlan-subnet-page.png)

4. Selecione a Nuvem Privada AVS para a nova VLAN/subnet.
5. Introduza uma identificação VLAN.
6. Introduza o nome da sub-rede.
7. Para ativar o encaminhamento no VLAN (sub-rede), especifique a gama CIDR da sub-rede. Certifique-se de que a gama CIDR não se sobrepõe a nenhuma das suas subredes no local, subredes Azure ou sub-rede gateway.
8. Clique em **Submit** (Submeter).

    ![Criar VLAN/subnet](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> Há uma quota de 30 VLANs por nuvem privada. Estes limites podem ser aumentados [contactando](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)o suporte .

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>Utilize informações VLAN para criar um grupo portuário distribuído na vSphere

Para criar um grupo portuário distribuído na vSphere, siga as instruções do tópico VMware 'Adicione um grupo portuário distribuído' no Guia de <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">Rede vSphere</a>. Ao configurar o grupo portuário distribuído, forneça as informações VLAN a partir da configuração AVS.

![Grupo Portuário Distribuído](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Selecione uma tabela de firewall

As tabelas de firewall e as regras associadas são definidas na página de **tabelas Network > Firewall.** Para selecionar a tabela de firewall para aplicar à VLAN/subnet para uma Nuvem Privada AVS, selecione o anexo de **tabela De firewall** VLAN/subnet na página **VLANs/Subnets.** Consulte [as tabelas de firewall](firewall.md) para obter instruções sobre a configuração de tabelas de firewall e regras de definição.

![Link de tabela firewall](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Uma sub-rede pode ser associada a uma mesa de firewall. Uma tabela de firewall pode ser associada a várias subredes.

## <a name="edit-a-vlansubnet"></a>Editar uma VLAN/subnet

Para editar as definições para uma VLAN/Subnet, selecione-as na página **VLANs/Subnets** e clique no ícone **Editar.** Faça alterações e clique em **Submet**.

## <a name="delete-a-vlansubnet"></a>Eliminar uma VLAN/subnet

Para eliminar um VLAN/Subnet, selecione-o na página **VLANs/Subnets** e clique no ícone **Eliminar.** Clique em **Apagar** para confirmar.
