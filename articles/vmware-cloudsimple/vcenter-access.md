---
title: Azure VMware Solutions (AVS) - Cliente vSphere de acesso
description: Descreve como aceder ao vCenter a partir da sua Nuvem Privada AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ad018ea89b194d42ab1867a0569725c4c3680f7d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022669"
---
# <a name="access-your-avs-private-cloud-vcenter-portal"></a>Aceda ao seu portal AVS Private Cloud vCenter

Pode lançar o seu portal AVS Private Cloud vCenter a partir do portal Azure ou portal AVS. o portal vCenter permite-lhe gerir a infraestrutura VMware na sua Nuvem Privada AVS.

## <a name="before-you-begin"></a>Antes de começar

A ligação de rede deve ser estabelecida e a resolução de nomes DNS deve ser ativada para aceder ao portal vCenter. Pode estabelecer a ligação de rede à sua Nuvem Privada AVS utilizando qualquer uma das opções abaixo.

* [Ligue-se a partir de instalações para AVS usando ExpressRoute](on-premises-connection.md)
* [Configure uma ligação VPN à sua Nuvem Privada AVS](set-up-vpn.md)

Para configurar a resolução de nome DNS dos seus componentes de infraestrutura De VMware De Nuvem Privada AVS, consulte [Configure DNS para resolução de nome sintetizar o acesso vCenter da AVS Private Cloud a partir de estações de trabalho no local](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Acesso vCenter do portal Azure

Pode lançar o portal vCenter da sua Nuvem Privada AVS a partir do portal Azure.

1. Selecione **Todos os serviços**.

2. Pesquisa de **Serviços AVS**.

3. Selecione o serviço AVS da sua Nuvem Privada AVS a que pretende ligar.

4. Na página **de visão geral,** clique em **Ver VMware AVS Nuvens Privadas**

    ![Visão geral do serviço AVS](media/cloudsimple-service-overview.png)

5. Selecione a Nuvem Privada AVS da lista de Nuvens Privadas AVS e clique em **Lançar vSphere Client**.

    ![Iniciar cliente do vSphere](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-avs-portal"></a>Acesso vCenter do portal AVS

Pode lançar o portal vCenter do seu Portal AVS Private Cloud a partir do portal AVS.

1. Aceda ao seu [portal AVS.](access-cloudsimple-portal.md)

2. A partir dos **Recursos** selecione a Nuvem Privada AVS, a que pretende aceder e clicar no **Cliente De Lançamento vSphere**.

    ![Lançar vSphere Client - Recursos](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Também pode lançar o portal vCenter a partir de ecrã resumo da sua Nuvem Privada AVS.

    ![Lançar vSphere Client - Resumo](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Passos seguintes

* [Crie e gerencie VLANs/subnets para as suas Nuvens Privadas AVS](create-vlan-subnet.md)
* [Modelo de permissão de nuvem privada AVS do VMware vCenter](learn-private-cloud-permissions.md)