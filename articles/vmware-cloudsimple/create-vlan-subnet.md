---
title: Criar VLANs/sub-redes - Azure VMware Solution by CloudSimple
description: Azure VMware Solutions by CloudSimple - Descreve como criar e gerir VLANs/subnets para as suas Nuvens Privadas e, em seguida, aplicar regras de firewall.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0160513eb9abca54adbc3819b982348dc202c90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77566000"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>Crie e gere VLANs/sub-redes para as suas Nuvens Privadas

Abra o separador VLANs/Subnets na página Rede para criar e gerir VLANs/sub-redes para as suas Nuvens Privadas. Depois de criar uma sub-rede VLAN/, pode aplicar regras de firewall.

## <a name="create-a-vlansubnet"></a>Criar uma VLAN/sub-rede

1. [Aceda ao portal CloudSimple](access-cloudsimple-portal.md) e selecione **Rede** no menu lateral.
2. Selecione **VLANs/sub-redes**.
3. Clique **em Criar VLAN/sub-rede**.

    ![Página VLAN/sub-rede](media/vlan-subnet-page.png)

4. Selecione a Nuvem Privada para a nova VLAN/sub-rede.
5. Introduza uma identificação VLAN.
6. Introduza o nome da sub-rede.
7. Para ativar o encaminhamento na sub-rede VLAN, especifique a gama CIDR da sub-rede. Certifique-se de que a gama CIDR não se sobrepõe a nenhuma das suas sub-redes no local, sub-redes Azure ou sub-redes gateway.
8. Clique **em Submeter.**

    ![Criar VLAN/sub-rede](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> Há uma quota de 30 VLANs por nuvem privada. Estes limites podem ser aumentados [através do contacto com o suporte.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>Utilize informações VLAN para criar um grupo portuário distribuído na vSphere

Para criar um grupo portuário distribuído em vSphere, siga as instruções do tópico VMware 'Adicionar um grupo de portas distribuído' no <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">vSphere Networking Guide</a>. Ao configurar o grupo porta distribuído, forneça as informações VLAN a partir da configuração CloudSimple.

![Grupo Portuário Distribuído](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Selecione uma tabela de firewall

As tabelas de firewall e as regras associadas são definidas na página **de tabelas network > Firewall.** Para selecionar a tabela de firewall para aplicar na sub-rede VLAN/uma Nuvem Privada, selecione o anexo de tabela VLAN/sub-rede clique em **Firewall** na página **VLANs/Subnets.** Consulte [as tabelas de firewall](firewall.md) para obter instruções sobre a configuração das tabelas de firewall e regras de definição.

![Link de tabela de firewall](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Uma sub-rede pode ser associada a uma tabela de firewall. Uma tabela de firewall pode ser associada a várias sub-redes.

## <a name="edit-a-vlansubnet"></a>Editar uma VLAN/sub-rede

Para editar as definições de um VLAN/Subnet, selecione-o na página **VLANs/Subnets** e clique no ícone **Editar.** Faça alterações e clique em **Submet**.

## <a name="delete-a-vlansubnet"></a>Eliminar uma VLAN/sub-rede

Para eliminar uma VLAN/Subneta, selecione-a na página **VLANs/Subnets** e clique no ícone **Eliminar.** Clique **em Eliminar** para confirmar.
