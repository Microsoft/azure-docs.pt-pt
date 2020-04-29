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
ms.openlocfilehash: 5680bd1c3fadab8edcde1634c708ca8683c0e99c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76021120"
---
## <a name="create-base-resources"></a>Criar recursos base

Antes de configurar o encaminhamento de mensagens, precisa de criar um hub IoT, uma conta de armazenamento e uma fila de ônibus de serviço. Estes recursos podem ser criados usando um dos quatro artigos disponíveis para a Parte 1 deste tutorial: o portal Azure, um modelo de Gestor de Recursos Azure, o Azure CLI ou Azure PowerShell.

Utilize o mesmo grupo de recursos e a mesma localização para todos os recursos. Em seguida, no final, você pode remover todos os recursos em um passo, eliminando o grupo de recursos.

Abaixo está um resumo dos passos a serem realizados nas seguintes secções: 

1. Criar um [grupo de recursos](../articles/azure-resource-manager/management/overview.md).

2. Crie um hub IoT na camada S1. Adicione um grupo de consumidores ao hub IoT. O grupo de consumidores é utilizado pelo Azure Stream Analytics durante a obtenção dos dados.

   > [!NOTE]
   > Você deve usar um hub de muito em um nível pago para completar este tutorial. O nível livre só lhe permite configurar um ponto final, e este tutorial requer vários pontos finais.
   > 

3. Crie uma conta de armazenamento standard V1 com replicação Standard_LRS.

4. Crie um espaço de nomes e uma fila do Service Bus.

5. Crie uma identidade para o dispositivo simulado que envia mensagens para o hub. Guarde a chave para a fase de teste. (Se criar um modelo de Gestor de Recursos, isto é feito após a implementação do modelo.)