---
title: Mapear a assinatura do Azure para pools de recursos na solução VMware do Azure por CloudSimple
description: Descreve como mapear um pool de recursos na solução VMware do Azure de CloudSimple para sua assinatura do Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1bf721f35500d2ff1344996e7750c5e574f40f31
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816265"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>Mapear pools de recursos de sua nuvem privada para sua assinatura do Azure

O mapeamento de assinatura do Azure permite mapear pools de recursos de sua nuvem privada vCenter para sua assinatura do Azure. Você pode mapear apenas a assinatura em que criou o serviço CloudSimple.  A criação de uma máquina virtual VMware por meio do portal do Azure implanta a máquina virtual no pool de recursos mapeado.  No portal do CloudSimple, você pode exibir e gerenciar a assinatura do Azure para suas nuvens privadas.

Uma assinatura pode ser mapeada para vários pools de recursos do vCenter de uma nuvem privada.  Você precisa mapear pools de recursos de cada nuvem privada.  Somente os pools de recursos mapeados estarão disponíveis para a criação de uma máquina virtual VMware a partir da portal do Azure.

> [!IMPORTANT]
> O mapeamento de um pool de recursos também mapeia os pools de recursos filho. Um pool de recursos pai não poderá ser mapeado se algum pool de recursos filho já estiver mapeado.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um serviço CloudSimple e uma nuvem privada em sua assinatura.  Para criar um serviço CloudSimple, consulte [início rápido-criar serviço](quickstart-create-cloudsimple-service.md).  Se você precisar criar uma nuvem privada, consulte [início rápido-configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md).

Você pode mapear o cluster do vCenter (pool de recursos raiz) para sua assinatura.  Se você quiser criar um novo pool de recursos, consulte [o artigo criar um pool de recursos](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html) no site de documentação do VMware.

## <a name="default-resource-group"></a>Grupo de recursos padrão

A criação de uma nova máquina virtual CloudSimple de portal do Azure permite que você selecione o grupo de recursos.  Uma máquina virtual criada no vCenter de nuvem privada em um pool de recursos mapeado será visível no portal do Azure.  A máquina virtual descoberta será colocada no grupo de recursos padrão do Azure.  Você pode alterar o nome do grupo de recursos padrão.

## <a name="map-azure-subscription"></a>Mapear assinatura do Azure

1. Acesse o [portal do CloudSimple](access-cloudsimple-portal.md).

2. Abra a página **recursos** e selecione a nuvem privada que você deseja mapear.

3. Selecione **mapeamento de assinaturas do Azure**.

4. Clique em **Editar mapeamento de assinatura do Azure**.

5. Para mapear os pools de recursos disponíveis, selecione-os à esquerda e clique na seta para a direita.

6. Para remover mapeamentos, selecione-os à direita e clique na seta para a esquerda.

    ![Subscrições do Azure](media/resources-azure-mapping.png)

7. Clique em **OK**.

## <a name="change-default-resource-group-name"></a>Alterar o nome do grupo de recursos padrão

1. Acesse o [portal do CloudSimple](access-cloudsimple-portal.md).

2. Abra a página **recursos** e selecione a nuvem privada que você deseja mapear.

3. Selecione **mapeamento de assinaturas do Azure**.

4. Clique em **Editar** em nome do grupo de recursos do Azure.

    ![Editar nome do grupo de recursos](media/resources-edit-resource-group-name.png)

5. Insira um novo nome para o grupo de recursos e clique em **Enviar**.

    ![Insira o novo nome do grupo de recursos](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>Passos Seguintes

* [Consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre as [máquinas virtuais CloudSimple](cloudsimple-virtual-machines.md)