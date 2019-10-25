---
title: incluir ficheiro
description: incluir ficheiro
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: c97c8231265cf87f52333a56d21d6fb13180c554
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808826"
---
## <a name="create-base-resources"></a>Criar recursos base

Antes de configurar o roteamento de mensagens, você precisa criar um hub IoT, uma conta de armazenamento e uma fila do barramento de serviço. Esses recursos podem ser criados usando um dos quatro artigos que estão disponíveis para a parte 1 deste tutorial: o portal do Azure, um modelo de Azure Resource Manager, o CLI do Azure ou Azure PowerShell.

Utilize o mesmo grupo de recursos e a mesma localização para todos os recursos. Em seguida, no final, você pode remover todos os recursos em uma etapa excluindo o grupo de recursos.

Veja abaixo um resumo das etapas a serem executadas nas seguintes seções: 

1. Crie um [grupo de recursos](../articles/azure-resource-manager/resource-group-overview.md).

2. Crie um hub IoT na camada S1. Adicione um grupo de consumidores ao hub IoT. O grupo de consumidores é utilizado pelo Azure Stream Analytics durante a obtenção dos dados.

   > [!NOTE]
   > Você deve usar um hub IOT em uma camada paga para concluir este tutorial. A camada gratuita só permite que você configure um ponto de extremidade, e este tutorial requer vários pontos de extremidades.
   > 

3. Crie uma conta de armazenamento standard V1 com replicação Standard_LRS.

4. Crie um espaço de nomes e uma fila do Service Bus.

5. Crie uma identidade para o dispositivo simulado que envia mensagens para o hub. Guarde a chave para a fase de teste. (Se estiver criando um modelo do Resource Manager, isso é feito após a implantação do modelo.)