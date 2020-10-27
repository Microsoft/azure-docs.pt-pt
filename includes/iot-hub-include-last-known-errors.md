---
title: incluir ficheiro
description: incluir ficheiro
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a0b7a1c600fe81081fbfe8d33c3878f68e730888
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547368"
---
[Obtenha Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) na API REST dá o estado de saúde dos pontos finais, bem como o último erro conhecido, para identificar a razão pela qual um ponto final não é saudável. A tabela abaixo enumera os erros mais comuns.

|Último erro conhecido|Descrição/quando ocorre|Possível Mitigação|
|-----|-----|-----|
|Transitório|Ocorreu um erro transitório e o IoT Hub voltará a tentar a operação.|Observe [os registos de recursos das rotas.](https://docs.microsoft.com/azure/iot-hub/monitor-service-reference#routes)|
|InternalError|Ocorreu um erro ao entregar uma mensagem a um ponto final.|Esta é uma exceção interna, mas também observar os [registos de recursos das rotas.](https://docs.microsoft.com/azure/iot-hub/monitor-service-reference#routes)|
|Não autorizado|O IoT Hub não está autorizado a enviar mensagens para o ponto final especificado.|Validar que a cadeia de ligação está atualizada para o ponto final. Se tiver mudado, considere uma atualização no seu Hub IoT. Se o ponto final utilizar a identidade gerida, verifique se o diretor do IoT Hub tem as permissões necessárias no alvo.|
|Acelerador|O IoT Hub está a ser estrangulado enquanto escreve mensagens no ponto final.|Reveja os limites do acelerador para o ponto final afetado. Modificar as configurações para o ponto final para escalar, se necessário.|
|Tempo Limite|Tempo de operação.|Repita a operação.|
|Não encontrado|O recurso alvo não existe.|Certifique-se de que o recurso-alvo existe.|
|Recipiente não encontrado|O recipiente de armazenamento não existe.|Certifique-se de que o recipiente de armazenamento existe.|
|Contentor desativado|O recipiente de armazenamento está desativado.|Certifique-se de que o recipiente de armazenamento está ativado.|
|MaxMessageSizeExceeded|O encaminhamento de mensagens tem um limite de tamanho de mensagem de 256Kb.O tamanho da mensagem que está a ser encaminhado excedeu este limite.|Verifique se o tamanho da mensagem pode ser reduzido usando menos propriedades de aplicação ou menos enriquecimentos de mensagens.|
|PartitionAndDuplicateDetectionNotsuped|O autocarro de serviço pode não ter a deteção duplicada ativada.|Desative a deteção duplicada do Service Bus ou considere utilizar uma entidade sem duplicar a deteção.|
|Entidade De sessãoNotSuped|O autocarro de serviço pode não ter sessões ativadas.|Desative a sessão do Service Bus ou considere utilizar uma entidade sem sessões.|
|NoMatchingSubscriptionsForMessage|Não há nenhuma subscrição para escrever mensagem sobre o tópico do autocarro de serviço.|Crie uma subscrição para mensagens IoT Hub a serem encaminhadas para.|
|EndpointExternallyDisabled|O ponto final não está num estado ativo, pelo que o IoT Hub pode enviar-lhe mensagens.|Habilitar o ponto final para trazê-lo de volta ao estado ativo.|
|DeviceMaximumQueueDepthExceeded|O limite de tamanho do autocarro de serviço foi atingido.|Considere remover mensagens dos Centros de Eventos alvo para permitir que novas mensagens sejam ingeridas nos Centros de Eventos.|