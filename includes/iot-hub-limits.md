---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 689322614e2cea4e59a5a573b72bfeb2aafda847
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71148473"
---
A tabela a seguir lista os limites associados às diferentes camadas de serviço S1, S2, S3 e F1. Para obter informações sobre o custo de cada *unidade* em cada camada, consulte [preços do Hub IOT do Azure](https://azure.microsoft.com/pricing/details/iot-hub/).

| Resource | S1 Standard | S2 Standard | S3 Standard | F1 Gratuito |
| --- | --- | --- | --- | --- |
| Mensagens/dia |400,000 |6,000,000 |300,000,000 |8,000 |
| Máximo de unidades |200 |200 |10 |1 |

> [!NOTE]
> Se você antecipar o uso de mais de 200 unidades com um hub de camada S1 ou S2 ou 10 unidades com um hub de camada S3, entre em contato com Suporte da Microsoft.
> 
> 

A tabela a seguir lista os limites que se aplicam aos recursos do Hub IoT.

| Resource | Limite |
| --- | --- |
| Máximo de hubs IoT pagos por subscrição do Azure |50 |
| Máximo de hubs IoT gratuitos por subscrição do Azure |1 |
| Número máximo de caracteres em uma ID de dispositivo | 128 |
| Número máximo de identidades de dispositivos<br/> devolvidas numa única chamada |1,000 |
| Retenção máxima de mensagem do Hub IoT para mensagens do dispositivo para a cloud |7 dias |
| Tamanho máximo da mensagem do dispositivo para a cloud |256 KB |
| Tamanho máximo do batch do dispositivo para a cloud |AMQP e HTTP: 256 KB para todo o lote <br/>MQTT 256 KB para cada mensagem |
| Máximo de mensagens no batch do dispositivo para a cloud |500 |
| Tamanho máximo da mensagem da cloud para o dispositivo |64 KB |
| TTL máximo das mensagens da cloud para o dispositivo |2 dias |
| Contagem máxima de entrega da cloud para o dispositivo <br/> mensagens |100 |
| Profundidade máxima da fila da nuvem para o dispositivo por dispositivo |50 |
| Contagem máxima de entrega para mensagens de comentários <br/> em resposta a uma mensagem da cloud para o dispositivo |100 |
| TTL máximo para mensagens de comentários em <br/> resposta a uma mensagem da cloud para o dispositivo |2 dias |
| [Tamanho máximo do dispositivo n º](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) <br/> (etiquetas, propriedades comunicadas e propriedades pretendidas) | 8 KB |
| Tamanho máximo do valor da cadeia do dispositivo duplo | 4 KB |
| [Profundidade máxima do objeto no dispositivo.](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 5 |
| Tamanho máximo do payload de método direto | 128 KB |
| Retenção máxima de histórico de tarefas | 30 dias |
| Máximo de tarefas simultâneas | 10 (para S3), 5 (para S2), 1 (para S1) |
| Máximo de pontos finais adicionais | 10 (para S1, S2 e S3) |
| Regras de encaminhamento máximo de mensagens | 100 (para S1, S2 e S3) |
| Número máximo de fluxos de dispositivo conectados simultaneamente | 50 (somente para S1, S2, S3 e F1) |
| Transferência máxima de dados de fluxo de dispositivo | 300 MB por dia (somente para S1, S2, S3 e F1) |

> [!NOTE]
> Se você precisar de mais de 50 hubs IoT pagos em uma assinatura do Azure, entre em contato com Suporte da Microsoft.

> [!NOTE]
> Atualmente, o número máximo de dispositivos que você pode conectar a um único Hub IoT é 1 milhão. Se você quiser aumentar esse limite, entre em contato com [suporte da Microsoft](https://azure.microsoft.com/support/options/).

O Hub IoT limita as solicitações quando as seguintes cotas são excedidas.

| Limitação | Valor por hub |
| --- | --- |
| Operações de registo de identidade <br/> (criar, recuperar, listar, atualizar e excluir), <br/> importar/exportar de forma individual ou em massa |83,33/s/unidade (5000/min/unidade) (para S3). <br/> 1,67/s/unidade (100/min/unidade) (para S1 e S2). |
| Ligações do dispositivo |6000/s/unidade (para S3), 120/s/unidade (para S2), 12/s/unidade (para S1). <br/>Mínimo de 100/seg. |
| Envios do dispositivo para a cloud |6000/s/unidade (para S3), 120/s/unidade (para S2), 12/s/unidade (para S1). <br/>Mínimo de 100/seg. |
| Envios da cloud para o dispositivo | 83,33/s/unidade (5000/min/unidade) (para S3), 1,67/s/unidade (100/min/unidade) (para S1 e S2). |
| Receções da cloud para o dispositivo |833,33/s/unidade (50000/min/unidade) (para S3), 16,67/s/unidade (1000/mín/unidade) (para S1 e S2). |
| Operações de carregamento de ficheiros |83,33 notificações de upload de arquivo/s/unidade (5000/min/unidade) (para S3), 1,67 notificações de upload de arquivo/s/unidade (100/min/unidade) (para S1 e S2). <br/> 10.000 URIs SAS podem estar fora de uma conta de armazenamento do Azure ao mesmo tempo.<br/> 10 SAS URIs/dispositivo podem ficar fora de uma só vez. |
| Métodos diretos | 24 MB/s/unidade (para S3), 480 KB/s/unidade (para S2), 160 KB/s/unidade (para S1).<br/> Com base no tamanho do medidor de limitação de 8 KB. |
| Leituras de dispositivo duplo | 500/s/unidade (para S3), máximo de 100/s ou 10/s/unidade (para S2), 100/s (para S1) |
| Atualizações de dispositivo duplo | 250/s/unidade (para S3), máximo de 50/s ou 5/s/unidade (para S2), 50/s (para S1) |
| Operações de tarefas <br/> (criar, atualizar, listar e excluir) | 83,33/s/unidade (5000/min/unidade) (para S3), 1,67/s/unidade (100/min/unidade) (para S2), 1,67/s/unidade (100/min/unidade) (para S1). |
| Débito de operação tarefas por dispositivo | 50/s/unidade (para S3), máximo de 10/s ou 1/s/unidade (para S2), 10/s (para S1). |
| Taxa de inicialização de fluxo do dispositivo | 5 novos fluxos/s (somente para S1, S2, S3 e F1). |
