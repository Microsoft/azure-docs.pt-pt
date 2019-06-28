---
title: Mapear a subscrição do Azure para agrupamentos de recursos no Azure VMware solução por CloudSimple
description: Descreve como mapear um agrupamento de recursos no Azure VMware solução por CloudSimple à sua subscrição do Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efda996e03d46a2f97d19558f7c2930b623a639e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333347"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>Mapear agrupamentos de recursos a partir da nuvem privada da sua subscrição do Azure

Mapeamento de subscrição do Azure permite-lhe mapear agrupamentos de recursos do vCenter sua nuvem privada para a sua subscrição do Azure. Pode mapear apenas a subscrição onde criou o serviço de CloudSimple.  Criar uma máquina virtual VMware a partir do portal do Azure implementa a máquina virtual no agrupamento de recursos mapeadas.  No portal do CloudSimple, pode ver e gerir a subscrição do Azure de suas nuvens privadas.

Uma subscrição pode ser mapeada para vários conjuntos de recursos do vCenter de uma nuvem privada.  Tem de mapear agrupamentos de recursos de cada nuvem privada.  Apenas os agrupamentos de recursos mapeadas estarão disponíveis para criar uma máquina virtual VMware a partir do portal do Azure.

> [!IMPORTANT]
> Também é um agrupamento de recursos de mapeamento mapeia quaisquer agrupamentos de recursos subordinados. Não é possível mapear um agrupamento de recursos de principal se quaisquer agrupamentos de recursos subordinados já estão mapeados.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem um serviço de CloudSimple e a nuvem privada na sua subscrição.  Para criar um serviço de CloudSimple, veja [início rápido - criar serviço](quickstart-create-cloudsimple-service.md).  Se precisar de criar uma nuvem privada, veja [início rápido - configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md).

Pode mapear o cluster do vCenter (agrupamento de recursos de raiz) à sua subscrição.  Se quiser criar um novo conjunto de recursos, veja [criar um agrupamento de recursos](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html) artigo no site de documentação do VMware.

## <a name="default-resource-group"></a>Grupo de recursos predefinido

Criar uma nova máquina virtual de CloudSimple a partir do portal do Azure permite-lhe selecionar o grupo de recursos.  Uma máquina virtual criada no vCenter de nuvem privada num agrupamento de recursos mapeadas ficará visível no portal do Azure.  A máquina virtual detetada será colocada no grupo de recursos do Azure padrão.  Pode alterar o nome do grupo de recursos predefinido.

## <a name="map-azure-subscription"></a>Mapear a subscrição do Azure

1. Acesso a [CloudSimple portal](access-cloudsimple-portal.md).

2. Abra o **recursos** página e selecione a nuvem privada que pretende mapear.

3. Selecione **subscrições do Azure, mapeamento**.

4. Clique em **mapeamento de subscrição do Azure editar**.

5. Para mapear agrupamentos de recursos disponíveis, selecioná-los no lado esquerdo e clique na seta de voltados para a direita.

6. Para remover os mapeamentos, selecioná-los à direita e clique na seta de voltados para a esquerda.

    ![Subscrições do Azure](media/resources-azure-mapping.png)

7. Clique em **OK**.

## <a name="change-default-resource-group-name"></a>Nome do grupo de recursos de padrão de alteração

1. Acesso a [CloudSimple portal](access-cloudsimple-portal.md).

2. Abra o **recursos** página e selecione a nuvem privada que pretende mapear.

3. Selecione **subscrições do Azure, mapeamento**.

4. Clique em **editar** em nome do grupo de recursos do Azure.

    ![Editar o nome do grupo de recursos](media/resources-edit-resource-group-name.png)

5. Introduza um novo nome para o grupo de recursos e clique em **submeter**.

    ![Introduza o nome do grupo de recursos novo](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>Passos Seguintes

* [Consumir as VMs de VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [CloudSimple máquinas de virtuais](cloudsimple-virtual-machines.md)