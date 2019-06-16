---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 104849557a8580e16fa1860b7919d1c0252debe9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66238508"
---
A tabela seguinte lista os limites associados com os escalões de serviço diferentes S1, S2, S3 e F1. Para obter informações sobre o custo de cada *unidade* em cada escalão, consulte [preços do IoT Hub do Azure](https://azure.microsoft.com/pricing/details/iot-hub/).

| Resource | S1 Standard | S2 Standard | S3 Standard | F1 Gratuito |
| --- | --- | --- | --- | --- |
| Mensagens/dia |400,000 |6,000,000 |300,000,000 |8,000 |
| Máximo de unidades |200 |200 |10 |1 |

> [!NOTE]
> Se prevê utilizar mais de 200 unidades com um hub de escalão S1 ou S2 ou 10 unidades com um hub de escalão S3, contacte o Support da Microsoft.
> 
> 

A tabela seguinte lista os limites que se aplicam a recursos do IoT Hub.

| Resource | Limite |
| --- | --- |
| Máximo de hubs IoT pagos por subscrição do Azure |50 |
| Máximo de hubs IoT gratuitos por subscrição do Azure |1 |
| Número máximo de carateres de um ID de dispositivo | 128 |
| Número máximo de identidades de dispositivos<br/> devolvidas numa única chamada |1,000 |
| Retenção máxima de mensagem do Hub IoT para mensagens do dispositivo para a cloud |7 dias |
| Tamanho máximo da mensagem do dispositivo para a cloud |256 KB |
| Tamanho máximo do batch do dispositivo para a cloud |AMQP e HTTP: 256 KB para o lote inteiro <br/>MQTT: 256 KB por cada mensagem |
| Máximo de mensagens no batch do dispositivo para a cloud |500 |
| Tamanho máximo da mensagem da cloud para o dispositivo |64 KB |
| TTL máximo das mensagens da cloud para o dispositivo |2 dias |
| Contagem máxima de entrega da cloud para o dispositivo <br/> mensagens |100 |
| Contagem máxima de entrega para mensagens de comentários <br/> em resposta a uma mensagem da cloud para o dispositivo |100 |
| TTL máximo para mensagens de comentários em <br/> resposta a uma mensagem da cloud para o dispositivo |2 dias |
| [Tamanho máximo do dispositivo duplo](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) <br/> (etiquetas, propriedades comunicadas e propriedades pretendidas) | 8 KB |
| Tamanho máximo do valor da cadeia do dispositivo duplo | 4 KB |
| [Profundidade máxima do objeto no dispositivo duplo](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 5 |
| Tamanho máximo do payload de método direto | 128 KB |
| Retenção máxima de histórico de tarefas | 30 dias |
| Máximo de tarefas simultâneas | 10 (para S3), 5 (para S2), 1 (para S1) |
| Máximo de pontos finais adicionais | 10 (para S1, S2 e S3) |
| Regras de encaminhamento máximo de mensagens | 100 (para S1, S2 e S3) |
| Número máximo de fluxos de dispositivos ligados em simultâneo | 50 (para S1, S2, S3 e F1 apenas) |
| Transferência de dados de transmissão máxima de dispositivo | 300 MB por dia (para S1, S2, S3 e F1 apenas) |

> [!NOTE]
> Se precisar de mais de 50 hubs IoT pagos numa subscrição do Azure, contacte Support da Microsoft.

> [!NOTE]
> Atualmente, o número máximo de dispositivos, que pode ligar a um hub IoT individual é 1.000.000. Se deseja aumentar este limite, contacte [Support da Microsoft](https://azure.microsoft.com/support/options/).

IoT Hub limita os pedidos quando as quotas seguintes são ultrapassadas.

| Limitação | Valor por hub |
| --- | --- |
| Operações de registo de identidade <br/> (criar, obter, listar, atualizar e eliminar), <br/> importar/exportar de forma individual ou em massa |83.33/SEC/Unit (5.000/min/unidade) (para S3). <br/> 1.67/SEC/Unit (100/min/unidade) (para S1 e S2). |
| Ligações do dispositivo |6000/seg/unidade (para S3), 120/seg/unidade (para S2), 12/seg/unidade (para S1). <br/>Mínimo de 100/seg. |
| Envios do dispositivo para a cloud |6000/seg/unidade (para S3), 120/seg/unidade (para S2), 12/seg/unidade (para S1). <br/>Mínimo de 100/seg. |
| Envios da cloud para o dispositivo | 83.33/SEC/Unit (5.000/min/unidade) (para S3), 1.67/sec/unit (100/min/unidade) (para S1 e S2). |
| Receções da cloud para o dispositivo |833.33/SEC/Unit (50 000/min/unidade) (para S3), 16.67/sec/unit (1.000/min/unidade) (para S1 e S2). |
| Operações de carregamento de ficheiros |carregamento de ficheiros 83.33 notificações/seg/unidade (5.000/min/unidade) (para S3), ficheiros 1.67 carregamento notificações/seg/unidade (100/min/unidade) (para S1 e S2). <br/> 10 000 SAS URIs podem ficar fora de uma conta de armazenamento do Azure de uma só vez.<br/> 10 SAS URIs/dispositivo podem ficar fora de uma só vez. |
| Métodos diretos | 24 MB/seg/unidade (para S3), 480 KB/seg/unidade (para S2), 160 KB/seg/unidade (para S1).<br/> Com base no tamanho do medidor da limitação 8 KB. |
| Leituras de dispositivo duplo | 500/seg/unidade (para S3), máximo de 100/seg ou 10/seg/unidade (para S2), 100/seg (para S1) |
| Atualizações de dispositivo duplo | 250/seg/unidade (para S3), máximo de 50 por segundo ou 5/seg/unidade (para S2), 50/seg (para S1) |
| Operações de tarefas <br/> (criar, atualizar, lista e eliminar) | 83.33/SEC/Unit (5.000/min/unidade) (para S3), 1.67/sec/unit (100/min/unidade) (para S2), 1.67/sec/unit (100/min/unidade) (para S1). |
| Débito de operação tarefas por dispositivo | máximo de 50/seg/unidade (para S3), de 10 por segundo ou 1/seg/unidade (para S2), 10/seg (para S1). |
| Taxa de inicialização de fluxo de dispositivo | 5 novos fluxos de bytes/s (para S1, S2, S3 e apenas F1). |
