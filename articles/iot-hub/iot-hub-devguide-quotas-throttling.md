---
title: Compreenda as quotas do Azure IoT Hub e o estrangulamento Microsoft Docs
description: Guia do desenvolvedor - descrição das quotas que se aplicam ao IoT Hub e ao comportamento de estrangulamento esperado.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.custom:
- 'Role: Cloud Development'
- 'Role: Operations'
- 'Role: Technical Support'
ms.openlocfilehash: 5a5b20efbf804c2ea1097f905da1cfd62727ff15
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410696"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referência - IoT Hub quotas e estrangulamento

Este artigo explica as quotas para um Hub IoT, e fornece informações para ajudá-lo a entender como funciona o estrangulamento.

## <a name="quotas-and-throttling"></a>Quotas e limitação

Cada subscrição do Azure pode ter no máximo 50 hubs IoT e, no máximo, 1 hub gratuito.

Cada hub IoT é aprovisionado com um determinado número de unidades num escalão específico. O nível e o número de unidades determinam a quota máxima diária de mensagens que pode enviar. O tamanho da mensagem utilizado para calcular a quota diária é de 0,5 KB para um hub de nível livre e 4KB para todos os outros níveis. Para mais informações, consulte [o Azure IoT Hub Pricing](https://azure.microsoft.com/pricing/details/iot-hub/).

O nível também determina os limites de estrangulamento que o IoT Hub impõe em todas as operações.

## <a name="iot-plug-and-play"></a>IoT Plug and Play

Os dispositivos IoT Plug e Play enviam pelo menos uma mensagem de telemetria para cada interface, incluindo a raiz, que pode aumentar o número de mensagens contadas para a sua quota de mensagem.

## <a name="operation-throttles"></a>Aceleradores de operação

Os aceleradores de operação são limitações de taxa que são aplicadas em intervalos de minutos e destinam-se a prevenir abusos. Também estão sujeitos à [formação de trânsito.](#traffic-shaping)

A tabela seguinte mostra os aceleradores forçados. Os valores referem-se a um centro individual.

| Limitação | Grátis, B1 e S1 | B2 e S2 | B3 e S3 | 
| -------- | ------- | ------- | ------- |
| [Operações de registo de identidade](#identity-registry-operations-throttle) (criar, recuperar, listar, atualizar, excluir) | 1,67/seg/unidade (100/min/unidade) | 1,67/seg/unidade (100/min/unidade) | 83.33/seg/unidade (5.000/min/unidade) |
| [Novas ligações do dispositivo](#device-connections-throttle) (este limite aplica-se à taxa de _novas ligações_ , não ao número total de ligações) | Superior de 100/seg ou 12/seg/unidade <br/> Por exemplo, duas unidades S1 são 2 \* 12 = 24 novas ligações/seg, mas tem pelo menos 100 novas ligações/seg através das suas unidades. Com nove unidades S1, tem 108 novas ligações/seg (9 \* 12) através das suas unidades. | 120 novas ligações/seg/unidade | 6.000 novas ligações/seg/unidade |
| Envios do dispositivo para a cloud | Mais de 100 operações de envio/seg ou 12 operações de envio/seg/unidade <br/> Por exemplo, duas unidades S1 são 2 \* 12 = 24/seg, mas tem pelo menos 100 operações de envio/seg através das suas unidades. Com nove unidades S1, tem 108 operações de envio/seg (9 \* 12) através das suas unidades. | 120 operações de envio/seg/unidade | 6.000 operações de envio/seg/unidade |
| Nuvem-para-dispositivo envia<sup>1</sup> | 1.67 Enviar operações/seg/unidade (100 mensagens/min/unidade) | 1.67 enviar operações/seg/unidade (100 operações de envio/min/unidade) | 83.33 operações de envio/seg/unidade (5.000 operações de envio/min/unidade) |
| Cloud-to-device recebe<sup>1</sup> <br/> (apenas quando o dispositivo utiliza HTTPS)| 16.67 receber operações/seg/unidade (1.000 operações de receção/min/unidade) | 16.67 receber operações/seg/unidade (1.000 operações de receção/min/unidade) | 833.33 receber operações/seg/unidade (50.000 operações de receção/min/unidade) |
| Upload de arquivo | 1.67 iniciações de upload de ficheiros/sec/unidade (100/min/unidade) | 1.67 iniciações de upload de ficheiros/sec/unidade (100/min/unidade) | 83.33 iniciações de upload de ficheiros/sec/unidade (5.000/min/unidade) |
| Métodos diretos<sup>1</sup> | 160KB/seg/unidade<sup>2</sup> | 480KB/seg/unidade<sup>2</sup> | 24MB/seg/unidade<sup>2</sup> | 
| Consultas | 20/min/unidade | 20/min/unidade | 1.000/min/unidade |
| Twin (dispositivo e módulo) lê<sup>1</sup> | 100/seg | Superior de 100/seg ou 10/seg/unidade | 500/seg/unidade |
| Duas atualizações (dispositivo e módulo)<sup>1</sup> | 50/seg | Superior de 50/seg ou 5/seg/unidade | 250/seg/unidade |
| Operações de emprego<sup>1</sup> <br/> (criar, atualizar, listar, eliminar) | 1,67/seg/unidade (100/min/unidade) | 1,67/seg/unidade (100/min/unidade) | 83.33/seg/unidade (5.000/min/unidade) |
| Operações de dispositivos de<sup>emprego 1</sup> <br/> (atualizar gémeos, invocar o método direto) | 10/seg | Superior de 10/seg ou 1/seg/unidade | 50/seg/unidade |
| Configurações e implementações de borda<sup>1</sup> <br/> (criar, atualizar, listar, eliminar) | 0,33/seg/unidade (20/min/unidade) | 0,33/seg/unidade (20/min/unidade) | 0,33/seg/unidade (20/min/unidade) |
| Taxa de iniciação do fluxo do dispositivo<sup>1</sup> | 5 novos fluxos/seg | 5 novos fluxos/seg | 5 novos fluxos/seg |
| Número máximo de fluxos de dispositivos ligados simultaneamente<sup>1</sup> | 50 | 50 | 50 |
| Transferência máxima de dados de fluxo de<sup>dispositivos 1</sup> (volume agregado por dia) | 300 MB | 300 MB | 300 MB |

<sup>1</sup> Esta funcionalidade não está disponível no nível básico do IoT Hub. Para mais informações, consulte [Como escolher o IoT Hub certo.](iot-hub-scaling.md) <br/><sup>2</sup> O tamanho do medidor de estrangulamento é de 4 KB. O estrangulamento baseia-se apenas no tamanho da carga útil do pedido.

### <a name="throttling-details"></a>Detalhes de estrangulamento

* O tamanho do medidor determina em que incrementos o seu limite de estrangulamento é consumido. Se a carga útil da sua chamada direta for entre 0 e 4 KB, é contada como 4 KB. Pode fazer até 40 chamadas por segundo por unidade antes de atingir o limite de 160 KB/seg/unidade.

   Da mesma forma, se a sua carga útil estiver entre 4 KB e 8 KB, cada chamada representa 8 KB e pode fazer até 20 chamadas por segundo por unidade antes de atingir o limite máximo.

   Finalmente, se o seu tamanho de carga útil for entre 156KB e 160 KB, poderá fazer apenas 1 chamada por segundo por unidade no seu hub antes de atingir o limite de 160 KB/seg/unidade.

*  Para *as operações do dispositivo Jobs (atualizar twin, invocar o método direto)* para o nível S3, 50/seg/unidade só se aplica quando invoca métodos que utilizam trabalhos. Se invocar métodos diretos diretamente, aplica-se o limite de estrangulamento original de 24 MB/seg/unidade (para S3).

*  **Quota** é o número agregado de mensagens que pode enviar no seu hub *por dia.* Pode encontrar o limite de quota do seu hub sob a coluna **Número total de mensagens/dia** na página de preços do [IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

*  Os seus aceleradores nuvem-a-dispositivo e dispositivo-a-nuvem determinam a *velocidade* máxima a que pode enviar mensagens -- número de mensagens independentemente de 4 pedaços de KB. Cada mensagem pode chegar a 256 KB, que é o tamanho máximo da [mensagem](iot-hub-devguide-quotas-throttling.md#other-limits).

*  É uma boa prática acelerar as suas chamadas para que não atinja/exceda os limites de estrangulamento. Se atingir o limite, o IoT Hub responde com o código de erro 429 e o cliente deve recuar e tentar novamente. Estes limites são por hub (ou em alguns casos por hub/unidade). Para obter mais informações, consulte [gerir a conectividade e padrões de mensagens/retrips fiáveis](iot-hub-reliability-features-in-sdks.md#retry-patterns).

### <a name="traffic-shaping"></a>Formação de tráfego

Para acomodar o tráfego de rajadas, o IoT Hub aceita pedidos acima do acelerador por um tempo limitado. Os primeiros pedidos são processados imediatamente. No entanto, se o número de pedidos continuar a violar o acelerador, o IoT Hub começa a colocar os pedidos numa fila e processado à taxa limite. Este efeito chama-se *formação de tráfego.* Além disso, o tamanho desta fila é limitado. Se a violação do acelerador continuar, eventualmente a fila enche-se, e o IoT Hub começa a rejeitar pedidos com `429 ThrottlingException` .

Por exemplo, utiliza um dispositivo simulado para enviar 200 mensagens de dispositivo para nuvem por segundo ao seu Hub S1 IoT (que tem um limite de 100/seg D2C envia). Durante o primeiro minuto ou dois, as mensagens são processadas imediatamente. No entanto, uma vez que o dispositivo continua a enviar mais mensagens do que o limite do acelerador, o IoT Hub começa a processar apenas 100 mensagens por segundo e coloca o resto numa fila. Começas a notar um aumento da latência. Eventualmente, começa-se a ficar à medida que `429 ThrottlingException` a fila se enche, e a [métrica do "Número de erros de estrangulamento" do IoT Hub](monitor-iot-hub-reference.md#device-telemetry-metrics) começa a aumentar. Para aprender a criar alertas e gráficos com base em métricas, consulte [o Monitor IoT Hub](monitor-iot-hub.md).

### <a name="identity-registry-operations-throttle"></a>Acelerador de operações de registo de identidade

As operações de registo de identidade do dispositivo destinam-se a utilização a tempo de funcionamento em cenários de gestão e provisionamento de dispositivos. A leitura ou a atualização de um grande número de identidades de dispositivos é apoiada através de postos de [trabalho de importação e exportação.](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)

### <a name="device-connections-throttle"></a>Acelerador de ligações ao dispositivo

O acelerador *de ligações* do dispositivo regula a velocidade a que novas ligações do dispositivo podem ser estabelecidas com um hub IoT. O acelerador *de ligações do dispositivo* não rege o número máximo de dispositivos ligados simultaneamente. A taxa de aceleração das *ligações* do dispositivo depende do número de unidades que estão previstas para o hub IoT.

Por exemplo, se comprar uma única unidade S1, obtém-se um acelerador de 100 ligações por segundo. Portanto, para ligar 100.000 dispositivos, leva pelo menos 1.000 segundos (aproximadamente 16 minutos). No entanto, pode ter tantos dispositivos ligados simultaneamente como os dispositivos registados no seu registo de identidade.

## <a name="other-limits"></a>Outros limites

O IoT Hub impõe outros limites operacionais:

| Operação | Limite |
| --------- | ----- |
| Dispositivos | O número total de dispositivos mais módulos que podem ser registados num único hub IoT está limitado a 1.000.000. A única forma de aumentar este limite é contactar o [Microsoft Support.](https://azure.microsoft.com/support/options/)|
| Uploads de ficheiros | 10 uploads de ficheiros simultâneos por dispositivo. |
| Empregos<sup>1</sup> | Os empregos máximos simultâneos são 1 (para Grátis e S1), 5 (para S2) e 10 (para S3). No entanto, o máximo simultâneo [de importação/exportação de postos de trabalho](iot-hub-bulk-identity-mgmt.md) é 1 para todos os níveis. <br/>O histórico de empregos mantém-se até 30 dias. |
| Pontos finais adicionais | Os centros SKU pagos podem ter 10 pontos finais adicionais. Os hubs SKU gratuitos podem ter um ponto final adicional. |
| Consultas de encaminhamento de mensagens | Os centros SKU pagos podem ter 100 consultas de encaminhamento. Os centros SKU gratuitos podem ter cinco consultas de encaminhamento. |
| Melhoramentos das mensagens | Os centros SKU pagos podem ter até 10 enriquecimentos de mensagens. Os centros SKU gratuitos podem ter até 2 enriquecimentos de mensagens.|
| Mensagens dispositivo-a-nuvem | Tamanho máximo da mensagem 256 KB |
| Mensagens nuvem-para-dispositivo<sup>1</sup> | Mensagem máxima tamanho 64 KB. As mensagens máximas pendentes para entrega são de 50 por dispositivo. |
| Método direto<sup>1</sup> | O tamanho máximo da carga útil do método direto é de 128 KB. |
| Configurações automáticas de dispositivo e módulo<sup>1</sup> | 100 configurações por hub SKU pago. 20 configurações por hub SKU gratuito. |
| Implementações automáticas IoT Edge<sup>1</sup> | 50 módulos por implantação. 100 implementações (incluindo implementações em camadas) por hub SKU pago. 10 implementações por hub SKU gratuito. |
| Gémeos<sup>1</sup> | O tamanho máximo das propriedades desejadas e as secções de propriedades reportadas são de 32 KB cada. O tamanho máximo da secção de etiquetas é de 8 KB. |
| Políticas de acesso partilhado | O número máximo de políticas de acesso partilhado é de 16. |
| certificados ca x509 | O número máximo de certificados de CA x509 que podem ser registados no IoT Hub é de 25. |

<sup>1</sup> Esta funcionalidade não está disponível no nível básico do IoT Hub. Para mais informações, consulte [Como escolher o IoT Hub certo.](iot-hub-scaling.md)

## <a name="increasing-the-quota-or-throttle-limit"></a>Aumento do limite de quota ou aceleração

A qualquer momento, pode aumentar as quotas ou os limites [de aceleração, aumentando o número de unidades a provisionadas num hub IoT](iot-hub-upgrade.md).

## <a name="latency"></a>Latência

O IoT Hub esforça-se por fornecer baixa latência para todas as operações. No entanto, devido às condições da rede e a outros fatores imprevisíveis, não pode garantir uma certa latência. Ao desenhar a sua solução, deve:

* Evite fazer suposições sobre a latência máxima de qualquer operação do Hub IoT.
* Adquir o seu hub IoT na região Azure mais próxima dos seus dispositivos.
* Considere utilizar o Azure IoT Edge para realizar operações sensíveis à latência no dispositivo ou num porta de entrada próximo do dispositivo.

Várias unidades IoT Hub afetam o estrangulamento como descrito anteriormente, mas não fornecem quaisquer benefícios ou garantias adicionais de latência.

Se vir aumentos inesperados na latência da operação, contacte [o Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Passos seguintes

Para uma discussão aprofundada sobre o comportamento de estrangulamento do IoT Hub, consulte o blog post [IoT Hub throttling e você](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

Outros tópicos de referência neste guia de desenvolvimento do IoT Hub incluem:

* [Pontos finais do Hub IoT](iot-hub-devguide-endpoints.md)
* [Monitorizar o Hub IoT](monitor-iot-hub.md)
