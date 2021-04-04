---
title: Crie piscinas de recursos com mapeamento de subscrição Azure
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve como criar piscinas de recursos para a sua Nuvem Privada através do mapeamento de subscrição Azure
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7be4a4c601d3f33972c1e52596ef623116dcadd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97897081"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Crie piscinas de recursos para a sua Nuvem Privada com mapeamento de subscrição Azure
O mapeamento de subscrição Azure permite-lhe criar piscinas de recursos para a sua Nuvem Privada a partir das piscinas de recursos disponíveis vSphere. No portal CloudSimple, pode ver e gerir a subscrição do Azure para as suas Nuvens Privadas.

> [!NOTE]
> Mapear uma piscina de recursos também mapeia quaisquer piscinas de recursos para crianças. Um conjunto de recursos dos pais não pode ser mapeado se alguma piscina de recursos para crianças já estiver mapeada.

1. [Aceda ao portal CloudSimple](access-cloudsimple-portal.md).
2. Abra a página **Recursos** e selecione **o mapeamento de subscrições Azure**.  
3. Clique **em Editar Mapeamento de subscrição Azure**.  
4. Para mapear os conjuntos de recursos disponíveis, selecione-os à esquerda e clique na seta virada para a direita. 
5. Para remover mapeamentos, selecione-os à direita e clique na seta virada para a esquerda. 

    ![Subscrições do Azure](media/resources-azure-mapping.png)

6. Clique em **OK**.
