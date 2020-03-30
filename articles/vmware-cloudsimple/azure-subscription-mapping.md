---
title: Criar piscinas de recursos com mapeamento de subscrição Azure
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve como criar piscinas de recursos para a sua Nuvem Privada através do mapeamento de subscrição do Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 474ef03d482288b6bf7b5a8b1c224349a8e2d3a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014968"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Crie piscinas de recursos para a sua Nuvem Privada com mapeamento de subscrição Azure
O mapeamento de subscrição Azure permite-lhe criar piscinas de recursos para a sua Nuvem Privada a partir dos conjuntos de recursos vSphere disponíveis. No portal CloudSimple, pode ver e gerir a subscrição Azure para as suas Nuvens Privadas.

> [!NOTE]
> Mapear um conjunto de recursos também mapeia quaisquer piscinas de recursos infantis. Um conjunto de recursos parentais não pode ser mapeado se algum conjunto de recursos infantis já estiver mapeado.

1. [Aceda ao portal CloudSimple](access-cloudsimple-portal.md).
2. Abra a página **Recursos** e selecione o mapeamento de **subscrições do Azure.**  
3. Clique em editar mapeamento de **subscrição Do Azure**.  
4. Para mapear os conjuntos de recursos disponíveis, selecione-os à esquerda e clique na seta virada para a direita. 
5. Para remover mapeamentos, selecione-os à direita e clique na seta virada para a esquerda. 

    ![Subscrições do Azure](media/resources-azure-mapping.png)

6. Clique em **OK**.
