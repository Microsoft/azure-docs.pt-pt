---
title: Azure VMware Solution by CloudSimple - Cliente de acesso vSphere
description: Descreve como aceder ao vCenter da sua Nuvem Privada.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2e62042a84f7ac2615762e3d9b036e4340bd8c73
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899121"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>Aceda ao seu portal Private Cloud vCenter

Pode lançar o seu portal Private Cloud vCenter a partir do portal Azure ou do portal CloudSimple.  o portal vCenter permite-lhe gerir a infraestrutura VMware na sua Nuvem Privada.

## <a name="before-you-begin"></a>Antes de começar

A ligação à rede tem de ser estabelecida e a resolução do nome DNS deve ser ativada para aceder ao portal vCenter.  Pode estabelecer a ligação de rede à sua Nuvem Privada utilizando qualquer uma das opções abaixo.

* [Ligue-se de instalações para CloudSimple usando ExpressRoute](on-premises-connection.md)
* [Configure uma ligação VPN à sua CloudSimple Private Cloud](set-up-vpn.md)

Para configurar a resolução de nome DNS dos seus componentes de infraestrutura Private Cloud VMware, consulte [o Configure DNS para resolução de nomes para acesso private Cloud vCenter a partir de estações de trabalho no local](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Acesso vCenter do portal Azure

Pode lançar o portal vCenter da sua Cloud Privada a partir do portal Azure.

1. Selecione **Todos os serviços**.

2. Procure por **Serviços CloudSimple**.

3. Selecione o serviço CloudSimple da sua Cloud Privada ao qual pretende ligar.

4. Na página **'Vista Geral',** clique **em Ver VMware Private Clouds**

    ![Visão geral do serviço CloudSimple](media/cloudsimple-service-overview.png)

5. Selecione a Nuvem Privada da lista de Nuvens Privadas e clique em **Lançamento vSphere Client**.

    ![Lançamento vSphere Cliente](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>Acesso vCenter do portal CloudSimple

Pode lançar o portal vCenter da sua Cloud Privada a partir do portal CloudSimple.

1. Aceda ao seu [portal CloudSimple](access-cloudsimple-portal.md).

2. A partir dos **Recursos** selecione a Nuvem Privada, a que pretende aceder e clique no **Launch vSphere Client.**

    ![Lançamento vSphere Cliente - Recursos](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Também pode lançar o portal vCenter a partir do ecrã sumário da sua Nuvem Privada.

    ![Lançamento vSphere Cliente - Resumo](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Passos seguintes

* [Crie e gere VLANs/sub-redes para as suas Nuvens Privadas](create-vlan-subnet.md)
* [CloudSimple Modelo de permissão em nuvem privada de VMware vCenter](learn-private-cloud-permissions.md)