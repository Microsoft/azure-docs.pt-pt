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
ms.openlocfilehash: 710bb8cba7fbbe4bc9b9fdc52b0767c96f97fe72
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630906"
---
## <a name="create-base-resources"></a>Criar recursos de bases

Antes de poder configurar o encaminhamento de mensagens, terá de criar um hub IoT, uma conta de armazenamento e uma fila do Service Bus. Estes recursos podem ser criados com um dos quatro artigos que estão disponíveis para a parte 1 deste Tutorial: a CLI do Azure, Azure PowerShell, portal do Azure ou um modelo Azure Resource Manager.

Utilize o mesmo grupo de recursos e a mesma localização para todos os recursos. Em seguida, no final, pode remover todos os recursos num único passo ao eliminar o grupo de recursos.

As secções seguintes descrevem os passos para ser executada.

1. Crie um [grupo de recursos](../articles/azure-resource-manager/resource-group-overview.md).

2. Crie um hub IoT na camada S1. Adicione um grupo de consumidores ao hub IoT. O grupo de consumidores é utilizado pelo Azure Stream Analytics durante a obtenção dos dados.

   > [!NOTE]
   > Tem de utilizar um hub Iot num escalão pago para concluir este tutorial. O escalão gratuito permite-lhe apenas configurar um ponto final e requer vários pontos de extremidade.
   > 

3. Crie uma conta de armazenamento standard V1 com replicação Standard_LRS.

4. Crie um espaço de nomes e uma fila do Service Bus.

5. Crie uma identidade para o dispositivo simulado que envia mensagens para o hub. Guarde a chave para a fase de teste. (Se criar um modelo do Resource Manager, isso é feito depois de implementar o modelo.)