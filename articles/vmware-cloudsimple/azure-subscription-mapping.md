---
title: Criar pools de recursos com o mapeamento de assinatura do Azure
description: Descreve como criar pools de recursos para sua nuvem privada por meio do mapeamento de assinatura do Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ea102ad8377da70b88a0e59834ebe3a09866632
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563213"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Criar pools de recursos para sua nuvem privada com o mapeamento de assinatura do Azure
O mapeamento de assinatura do Azure permite que você crie pools de recursos para sua nuvem privada dos pools de recursos do vSphere disponíveis. No portal do CloudSimple, você pode exibir e gerenciar a assinatura do Azure para suas nuvens privadas.

> [!NOTE]
> O mapeamento de um pool de recursos também mapeia os pools de recursos filho. Um pool de recursos pai não poderá ser mapeado se algum pool de recursos filho já estiver mapeado.

1. [Acesse o portal do CloudSimple](access-cloudsimple-portal.md).
2. Abra a página **recursos** e selecione **mapeamento de assinaturas do Azure**.  
3. Clique em **Editar mapeamento de assinatura do Azure**.  
4. Para mapear os pools de recursos disponíveis, selecione-os à esquerda e clique na seta para a direita. 
5. Para remover mapeamentos, selecione-os à direita e clique na seta para a esquerda. 

    ![Subscrições do Azure](media/resources-azure-mapping.png)

6. Clique em **OK**.
