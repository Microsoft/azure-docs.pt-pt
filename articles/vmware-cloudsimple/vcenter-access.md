---
title: Solução Azure VMware by CloudSimple - Cliente vSphere de acesso
description: Descreve como aceder ao vCenter da sua Nuvem Privada.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 18d9463bc512257034860e1188372879524924f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022669"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>Aceda ao seu portal vCenter Cloud Privado

Pode lançar o seu portal VCenter Cloud Privado a partir do portal Azure ou do portal CloudSimple.  o portal vCenter permite-lhe gerir a infraestrutura VMware na sua Nuvem Privada.

## <a name="before-you-begin"></a>Antes de começar

A ligação de rede deve ser estabelecida e a resolução de nomes DNS deve ser ativada para aceder ao portal vCenter.  Pode estabelecer a ligação de rede à sua Cloud Privada utilizando qualquer uma das opções abaixo.

* [Ligue-se a partir das instalações para a CloudSimple usando o ExpressRoute](on-premises-connection.md)
* [Configure uma ligação VPN à sua CloudSimple Private Cloud](set-up-vpn.md)

Para configurar a resolução de nome DNS dos seus componentes de infraestrutura DeCloud VMware privados, consulte [Configure DNS para resolução de nome sintetizar](on-premises-dns-setup.md) o acesso do vCenter em nuvem privada a partir de estações de trabalho no local

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no portal [https://portal.azure.com](https://portal.azure.com)Azure em .

## <a name="access-vcenter-from-azure-portal"></a>Acesso vCenter do portal Azure

Pode lançar o portal vCenter da sua Cloud Privada a partir do portal Azure.

1. Selecione **Todos os serviços**.

2. Pesquisa por **Serviços CloudSimple**.

3. Selecione o serviço CloudSimple da sua Cloud Privada a que pretende ligar.

4. Na página **de visão geral,** clique em **Ver Nuvens Privadas VMware**

    ![Visão geral do serviço CloudSimple](media/cloudsimple-service-overview.png)

5. Selecione a Nuvem Privada da lista de Nuvens Privadas e clique em **Lançar vSphere Client**.

    ![Lançar cliente vSphere](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>Aceder vCenter do portal CloudSimple

Pode lançar o portal vCenter da sua Cloud Privada a partir do portal CloudSimple.

1. Aceda ao seu [portal CloudSimple](access-cloudsimple-portal.md).

2. A partir dos **Recursos** selecione a Cloud Privada, a que pretende aceder e clique no **Cliente De Lançamento vSphere**.

    ![Lançar vSphere Client - Recursos](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Também pode lançar o portal vCenter a partir de um ecrã resumo da sua Cloud Privada.

    ![Lançar vSphere Client - Resumo](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Passos seguintes

* [Crie e gerencie VLANs/subnets para as suas Nuvens Privadas](create-vlan-subnet.md)
* [CloudSimple Private Cloud modelo de permissão vCenter vCenter](learn-private-cloud-permissions.md)