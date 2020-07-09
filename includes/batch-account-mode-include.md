---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/04/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 51b687dc2a6264eb12362616429746c9b182c3b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "68323899"
---
> [!NOTE]
> Quando criar uma conta do Batch, pode escolher entre dois modos de *alocação de conjuntos*: **subscrição de utilizador** e **serviço Batch**. Na maioria dos casos, deve utilizar o serviço Batch predefinido, no qual os conjuntos são alocados em segundo plano nas subscrições geridas do Azure. No modo de subscrição de utilizador alternativo, as VMs do Batch e outros recursos são criados diretamente na sua subscrição quando é criado um conjunto. O modo de subscrição de utilizador é necessário se quiser criar conjuntos do Batch com o [Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/). Para criar uma conta do Batch no modo de subscrição de utilizador, também tem de registar a sua subscrição no Azure Batch e associar a conta ao Azure Key Vault.