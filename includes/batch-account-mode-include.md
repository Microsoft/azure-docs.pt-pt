---
title: incluir ficheiro
description: incluir ficheiro
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 05/04/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: b5bb5da2b68bae36353b81a04993814b395ce67b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184613"
---
> [!NOTE]
> Quando criar uma conta do Batch, pode escolher entre dois modos de *alocação de conjuntos*: **subscrição de utilizador** e **serviço Batch**. Na maioria dos casos, deve utilizar o serviço Batch predefinido, no qual os conjuntos são alocados em segundo plano nas subscrições geridas do Azure. No modo de subscrição de utilizador alternativo, as VMs do Batch e outros recursos são criados diretamente na sua subscrição quando é criado um conjunto. O modo de subscrição de utilizador é necessário se quiser criar conjuntos do Batch com o [Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/). Para criar uma conta do Batch no modo de subscrição de utilizador, também tem de registar a sua subscrição no Azure Batch e associar a conta ao Azure Key Vault.