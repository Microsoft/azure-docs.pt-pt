---
title: Crie piscinas de recursos com mapeamento de subscrição Azure
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve como criar piscinas de recursos para a sua Nuvem Privada através do mapeamento de subscrição Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 474ef03d482288b6bf7b5a8b1c224349a8e2d3a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77014968"
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
