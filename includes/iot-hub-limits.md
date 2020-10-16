---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 007c19a10db5e000770c8c80189453d4a80edec2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82204631"
---
A tabela a seguir enumera os limites associados aos diferentes níveis de serviço S1, S2, S3 e F1. Para obter informações sobre o custo de cada *unidade* em cada nível, consulte [os preços do Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Recurso | S1 Standard | S2 Standard | S3 Standard | F1 Gratuito |
| --- | --- | --- | --- | --- |
| Mensagens/dia |400,000 |6,000,000 |300,000,000 |8,000 |
| Máximo de unidades |200 |200 |10 |1 |

> [!NOTE]
> Se antecipar a utilização de mais de 200 unidades com um hub de nível S1 ou S2 ou 10 unidades com um hub de nível S3, contacte o Microsoft Support.
> 
> 

A tabela a seguir enumera os limites aplicáveis aos recursos do IoT Hub.

| Recurso | Limite |
| --- | --- |
| Máximo de hubs IoT pagos por subscrição do Azure |50 |
| Máximo de hubs IoT gratuitos por subscrição do Azure |1 |
| Número máximo de caracteres num ID do dispositivo | 128 |
| Número máximo de identidades de dispositivos<br/> devolvidas numa única chamada |1,000 |
| Retenção máxima de mensagem do Hub IoT para mensagens do dispositivo para a cloud |7 dias |
| Tamanho máximo da mensagem do dispositivo para a cloud |256 KB |
| Tamanho máximo do batch do dispositivo para a cloud |AMQP e HTTP: 256 KB para todo o lote <br/>MQTT: 256 KB para cada mensagem |
| Máximo de mensagens no batch do dispositivo para a cloud |500 |
| Tamanho máximo da mensagem da cloud para o dispositivo |64 KB |
| TTL máximo das mensagens da cloud para o dispositivo |2 dias |
| Contagem máxima de entrega da cloud para o dispositivo <br/> mensagens |100 |
| Profundidade máxima da fila nuvem-para-dispositivo por dispositivo |50 |
| Contagem máxima de entrega para mensagens de comentários <br/> em resposta a uma mensagem da cloud para o dispositivo |100 |
| TTL máximo para mensagens de comentários em <br/> resposta a uma mensagem da cloud para o dispositivo |2 dias |
| [Tamanho máximo do dispositivo duplo](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB para a secção de tags e 32 KB para secções de propriedades desejadas e reportadas cada |
| Comprimento máximo da chave de corda gémea do dispositivo | 1 KB |
| Comprimento máximo do valor da corda gémea do dispositivo | 4 KB |
| [Profundidade máxima do objeto no dispositivo duplo](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| Tamanho máximo do payload de método direto | 128 KB |
| Retenção máxima de histórico de tarefas | 30 dias |
| Máximo de tarefas simultâneas | 10 (para S3), 5 (para S2), 1 (para S1) |
| Máximo de pontos finais adicionais | 10 (para S1, S2 e S3) |
| Regras de encaminhamento máximo de mensagens | 100 (para S1, S2 e S3) |
| Número máximo de fluxos de dispositivos ligados simultaneamente | 50 (apenas para S1, S2, S3 e F1) |
| Transferência máxima de dados de fluxo de dispositivos | 300 MB por dia (apenas para S1, S2, S3 e F1) |

> [!NOTE]
> Se precisar de mais de 50 hubs IoT pagos numa subscrição Azure, contacte o Microsoft Support.

> [!NOTE]
> Atualmente, o número total de dispositivos mais módulos que podem ser registados num único hub IoT está limitado a 1.000.000. Se pretender aumentar este limite, contacte [o Microsoft Support](https://azure.microsoft.com/support/options/).

IoT Hub acelera os pedidos quando as seguintes quotas são ultrapassadas.

| Limitação | Valor por hub |
| --- | --- |
| Operações de registo de identidade <br/> (criar, recuperar, listar, atualizar e apagar), <br/> importar/exportar de forma individual ou em massa |83.33/seg/unidade (5.000/min/unidade) (para S3). <br/> 1,67/seg/unidade (100/min/unidade) (para S1 e S2). |
| Ligações do dispositivo |6.000/seg/unidade (para S3), 120/seg/unidade (para S2), 12/seg/unidade (para S1). <br/>Mínimo de 100/seg. |
| Envios do dispositivo para a cloud |6.000/seg/unidade (para S3), 120/seg/unidade (para S2), 12/seg/unidade (para S1). <br/>Mínimo de 100/seg. |
| Envios da cloud para o dispositivo | 83.33/seg/unidade (5.000/min/unidade) (para S3), 1,67/sec/unit (100/min/unidade) (para S1 e S2). |
| Receções da cloud para o dispositivo |833.33/seg/unidade (50.000/min/unidade) (para S3), 16.67/sec/unit (1.000/min/unit) (para S1 e S2). |
| Operações de carregamento de ficheiros |83.33 iniciações de upload de ficheiros/sec/unidade (5.000/min/unidade) (para S3), 1.67 iniciações de upload de ficheiros/sec/unidade (100/min/unidade) (para S1 e S2). <br/> 10.000 SAS URIs podem estar fora para uma conta de armazenamento Azure de uma só vez.<br/> 10 SAS URIs/dispositivo podem ficar fora de uma só vez. |
| Métodos diretos | 24 MB/seg/unidade (para S3), 480 KB/seg/unidade (para S2), 160 KB/seg/unidade (para S1).<br/> Baseado no tamanho do medidor de estrangulamento de 8 KB. |
| Leituras de dispositivo duplo | 500/seg/unidade (para S3), Máximo de 100/seg ou 10/seg/unidade (para S2), 100/seg (para S1) |
| Atualizações de dispositivo duplo | 250/seg/unidade (para S3), Máximo de 50/seg ou 5/seg/unidade (para S2), 50/seg (para S1) |
| Operações de tarefas <br/> (criar, atualizar, listar e eliminar) | 83.33/seg/unidade (5.000/min/unidade) (para S3), 1.67/sec/unit (100/min/unit) (para S2), 1,67/sec/unit (100/min/unit) (para S1). |
| Débito de operação tarefas por dispositivo | 50/seg/unidade (para S3), máximo de 10/seg ou 1/seg/unidade (para S2), 10/seg (para S1). |
| Taxa de iniciação do fluxo do dispositivo | 5 novos fluxos/seg (apenas para S1, S2, S3 e F1). |
