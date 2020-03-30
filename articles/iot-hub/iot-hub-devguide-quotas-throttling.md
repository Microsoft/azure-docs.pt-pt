---
title: Compreenda as quotas do Hub Azure IoT e o estrangulamento Microsoft Docs
description: Guia de desenvolvedores - descrição das quotas aplicáveis ao IoT Hub e ao comportamento de estrangulamento esperado.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 71a5737434e78bc39bccdfeb950e0dbc32ed0052
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284697"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referência - Quotas ioT hub e estrangulamento

Este artigo explica as quotas para um Hub IoT, e fornece informações para ajudá-lo a entender como funciona a aceleração.

## <a name="quotas-and-throttling"></a>Quotas e limitação

Cada subscrição azure pode ter no máximo 50 hubs IoT, e no máximo 1 hub gratuito.

Cada hub IoT é aprovisionado com um determinado número de unidades num escalão específico. O nível e o número de unidades determinam a quota diária máxima de mensagens que pode enviar. O tamanho da mensagem utilizado para calcular a quota diária é de 0,5 KB para um hub de nível livre e 4KB para todos os outros níveis. Para mais informações, consulte o Preço do [Hub Azure IoT](https://azure.microsoft.com/pricing/details/iot-hub/).

O nível também determina os limites de estrangulamento que o IoT Hub impõe em todas as operações.

### <a name="iot-plug-and-play"></a>IoT Plug and Play

Durante a pré-visualização pública, os dispositivos IoT Plug e Play enviarão mensagens separadas por interface, o que pode aumentar o número de mensagens contadas para a sua quota de mensagem.

## <a name="operation-throttles"></a>Aceleradores de operação

Os aceleradores de funcionamento são limitações de taxas que são aplicadas em intervalos de minutos e destinam-se a prevenir abusos. Também estão sujeitos a formação de [trânsito.](#traffic-shaping)

A tabela seguinte mostra os aceleradores forçados. Os valores referem-se a um centro individual.

| Limitação | Grátis, B1 e S1 | B2 e S2 | B3 e S3 | 
| -------- | ------- | ------- | ------- |
| [Operações de registo de identidade](#identity-registry-operations-throttle) (criar, recuperar, listar, atualizar, excluir) | 1.67/seg/unidade (100/min/unidade) | 1.67/seg/unidade (100/min/unidade) | 83,33/seg/unidade (5.000/min/unidade) |
| [Novas ligações de dispositivos](#device-connections-throttle) (este limite aplica-se à taxa de _novas ligações,_ não ao número total de ligações) | Superior a 100/seg ou 12/seg/unidade <br/> Por exemplo, duas unidades\*S1 são 2 12 = 24 novas ligações/seg, mas tem pelo menos 100 novas ligações/seg em todas as suas unidades. Com nove unidades S1, tem 108 novas\*ligações/seg (9 12) em todas as suas unidades. | 120 novas ligações/sec/unidade | 6.000 novas ligações/sec/unidade |
| Envios do dispositivo para a cloud | Maior de 100 operações de envio/seg ou 12 operações de envio/sec/unidade <br/> Por exemplo, duas unidades\*S1 são 2 12 = 24/seg, mas tem pelo menos 100 operações de envio/seg através das suas unidades. Com nove unidades S1, tem 108\*operações de envio/seg (9 12) através das suas unidades. | 120 enviar operações/sec/unidade | 6.000 enviar operações/sec/unidade |
| Cloud-to-device envia<sup>1</sup> | 1.67 Enviar operações/seg/unidade (100 mensagens/min/unidade) | 1.67 Enviar operações/seg/unidade (100 operações de envio/min/unidade) | 83.33 Enviar operações/sec/unidade (5.000 operações de envio/min/unidade) |
| Cloud-to-device recebe<sup>1</sup> <br/> (apenas quando o dispositivo utiliza HTTPS)| 16.67 receber operações/sec/unidade (1.000 receção de operações/min/unidade) | 16.67 receber operações/sec/unidade (1.000 receção de operações/min/unidade) | 833.33 receber operações/sec/unidade (50.000 a receber operações/min/unidade) |
| Upload de ficheiros | 1.67 iniciações de upload de ficheiros/sec/unidade (100/min/unidade) | 1.67 iniciações de upload de ficheiros/sec/unidade (100/min/unidade) | 83.33 iniciações de upload de ficheiros/sec/unidade (5.000/min/unidade) |
| Métodos diretos<sup>1</sup> | 160KB/seg/unidade<sup>2</sup> | 480KB/seg/unidade<sup>2</sup> | 24MB/seg/unidade<sup>2</sup> | 
| Consultas | 20/min/unidade | 20/min/unidade | 1.000/min/unidade |
| Twin (dispositivo e módulo) lê<sup>1</sup> | 100/seg | Superior a 100/seg ou 10/seg/unidade | 500/seg/unidade |
| Atualizações duplas (dispositivo e módulo)<sup>1</sup> | 50/seg | Superior a 50/seg ou 5/seg/unidade | 250/seg/unidade |
| Operações de emprego<sup>1</sup> <br/> (criar, atualizar, listar, eliminar) | 1.67/seg/unidade (100/min/unidade) | 1.67/seg/unidade (100/min/unidade) | 83,33/seg/unidade (5.000/min/unidade) |
| Operações de dispositivos de emprego<sup>1</sup> <br/> (atualizar twin, invocar método direto) | 10/seg | Superior de 10/seg ou 1/seg/unidade | 50/seg/unidade |
| Configurações e implementações de<sup>bordas 1</sup> <br/> (criar, atualizar, listar, eliminar) | 0,33/seg/unidade (20/min/unidade) | 0,33/seg/unidade (20/min/unidade) | 0,33/seg/unidade (20/min/unidade) |
| Taxa de iniciação do fluxo do dispositivo<sup>1</sup> | 5 novos fluxos/seg | 5 novos fluxos/seg | 5 novos fluxos/seg |
| Número máximo de correntes de dispositivos simultaneamente ligados<sup>1</sup> | 50 | 50 | 50 |
| Transferência máxima de dados de fluxo de<sup>dispositivos 1</sup> (volume agregado por dia) | 300 MB | 300 MB | 300 MB |

<sup>1</sup> Esta funcionalidade não está disponível no nível básico do IoT Hub. Para mais informações, consulte [Como escolher o Hub IoT certo](iot-hub-scaling.md). <br/><sup>2</sup> O tamanho do medidor de estrangulamento é de 4 KB.

### <a name="throttling-details"></a>Detalhes de estrangulamento

* O tamanho do medidor determina em que incrementos o seu limite de estrangulamento é consumido. Se a carga útil da sua chamada direta estiver entre 0 e 4 KB, é contada como 4 KB. Pode fazer até 40 chamadas por segundo por unidade antes de atingir o limite de 160 KB/seg/unidade.

   Da mesma forma, se a sua carga útil estiver entre 4 KB e 8 KB, cada chamada conta 8 KB e pode fazer até 20 chamadas por segundo por unidade antes de atingir o limite máximo.

   Finalmente, se o seu tamanho de carga útil estiver entre 156KB e 160 KB, poderá fazer apenas 1 chamada por segundo por unidade no seu hub antes de atingir o limite de 160 KB/seg/unidade.

*  Para operações de *dispositivos Jobs (atualizar twin, invocar método direto)* para o nível S2, 50/seg/unidade só se aplica quando invoca métodos utilizando empregos. Se invocar diretamente métodos diretos, aplica-se o limite de estrangulamento original de 24 MB/seg/unidade (para S2).

*  **Quota** é o número agregado de mensagens que pode enviar no seu hub *por dia*. Pode encontrar o limite de quota do seu hub sob a coluna **Número total de mensagens /dia** na página de [preços do IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

*  Os seus aceleradores cloud-to-device e dispositivo-to-cloud determinam a *taxa* máxima a que pode enviar mensagens - número de mensagens independentemente de 4 pedaços KB. Cada mensagem pode ter até 256 KB, que é o [tamanho máximo](iot-hub-devguide-quotas-throttling.md#other-limits)da mensagem .

*  É uma boa prática acelerar as chamadas para que não atinjas/excedes os limites de estrangulamento. Se atingir o limite, o IoT Hub responde com o código de erro 429 e o cliente deve recuar e voltar a tentar. Estes limites são por hub (ou em alguns casos por hub/unidade). Para mais informações, consulte a [Manage conectividade e padrões de mensagens/retry fiáveis.](iot-hub-reliability-features-in-sdks.md#retry-patterns)

### <a name="traffic-shaping"></a>Formação de tráfego

Para acomodar o tráfego de rutura, o IoT Hub aceita pedidos acima do acelerador por um tempo limitado. Os primeiros pedidos são processados imediatamente. No entanto, se o número de pedidos continuar a violar o acelerador, o IoT Hub começa a colocar os pedidos numa fila e processado à taxa limite. Este efeito chama-se formação de *tráfego.* Além disso, o tamanho desta fila é limitado. Se a violação do acelerador continuar, eventualmente a fila se enche, `429 ThrottlingException`e o IoT Hub começa a rejeitar pedidos com .

Por exemplo, utiliza um dispositivo simulado para enviar 200 mensagens dispositivo-cloud por segundo para o seu Hub S1 IoT (que tem um limite de 100/seg D2C envia). Durante o primeiro ou dois minutos, as mensagens são processadas imediatamente. No entanto, uma vez que o dispositivo continua a enviar mais mensagens do que o limite do acelerador, o IoT Hub começa a processar apenas 100 mensagens por segundo e coloca o resto numa fila. Começas a notar um aumento da latência. Eventualmente, começa-se a ficar à medida `429 ThrottlingException` que a fila se enche, e o "número de erros de aceleração" nas [métricas do IoT Hub](iot-hub-metrics.md) começa a aumentar.

### <a name="identity-registry-operations-throttle"></a>Acelerador de operações de registo de identidade

As operações de registo de identidade do dispositivo destinam-se a utilização em tempo de execução em cenários de gestão e provisionamento de dispositivos. A leitura ou atualização de um grande número de identidades de dispositivos é apoiada através de postos de [trabalho de importação e exportação.](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)

### <a name="device-connections-throttle"></a>Acelerador de ligações do dispositivo

As *ligações* do dispositivo regulam a taxa a que as novas ligações do dispositivo podem ser estabelecidas com um hub IoT. O acelerador de *ligações* do dispositivo não rege o número máximo de dispositivos ligados simultaneamente. O acelerador da taxa de *ligação* do dispositivo depende do número de unidades que são aprovisionadas para o hub IoT.

Por exemplo, se comprar uma única unidade S1, obtém-se um acelerador de 100 ligações por segundo. Portanto, para ligar 100.000 dispositivos, leva pelo menos 1.000 segundos (aproximadamente 16 minutos). No entanto, pode ter tantos dispositivos conectados simultaneamente como tem dispositivos registados no seu registo de identidade.

## <a name="other-limits"></a>Outros limites

O IoT Hub impõe outros limites operacionais:

| Operação | Limite |
| --------- | ----- |
| Dispositivos | O número total de dispositivos mais módulos que podem ser registados num único hub IoT está limitado a 1.000.000. A única forma de aumentar este limite é contactar o [Microsoft Support](https://azure.microsoft.com/support/options/).|
| Uploads de ficheiros | 10 uploads simultâneos de ficheiros por dispositivo. |
| Empregos<sup>1</sup> | O máximo de trabalhos simultâneos é 1 (para Free e S1), 5 (para S2) e 10 (para S3). No entanto, o número máximo de postos de trabalho de [importação/exportação](iot-hub-bulk-identity-mgmt.md) de dispositivos é de 1 para todos os níveis. <br/>O histórico de emprego supor até 30 dias. |
| Pontos finais adicionais | Os centros SKU pagos podem ter 10 pontos finais adicionais. Os centros SKU gratuitos podem ter um ponto final adicional. |
| Consultas de encaminhamento de mensagens | Os centros SKU pagos podem ter 100 consultas de encaminhamento. Os centros SKU gratuitos podem ter cinco consultas de encaminhamento. |
| Melhoramentos das mensagens | Os centros SKU pagos podem ter até 10 enriquecimentos de mensagens. Os centros SKU gratuitos podem ter até 2 enriquecimentos de mensagens.|
| Mensagens dispositivo-a-nuvem | Tamanho máximo da mensagem 256 KB |
| Mensagens cloud-to-device<sup>1</sup> | Tamanho máximo da mensagem 64 KB. As mensagens pendentes máximas para entrega são de 50 por dispositivo. |
| Método direto<sup>1</sup> | O tamanho máximo da carga direta do método é de 128 KB. |
| Configurações automáticas de dispositivos e módulos<sup>1</sup> | 100 configurações por centro SKU pago. 20 configurações por hub SKU gratuito. |
| Implantações automáticas IoT Edge<sup>1</sup> | 50 módulos por implantação. 100 implantações (incluindo implantações em camadas) por centro SKU pago. 10 implantações por centro SKU gratuito. |
| Gémeos<sup>1</sup> | O tamanho máximo das propriedades desejadas e as secções de propriedades reportadas são de 32 KB cada. O tamanho máximo da secção de etiquetas é de 8 KB. |
| Políticas de acesso partilhado | Número máximo de políticas de acesso partilhado é de 16 |

<sup>1</sup> Esta funcionalidade não está disponível no nível básico do IoT Hub. Para mais informações, consulte [Como escolher o Hub IoT certo](iot-hub-scaling.md).

## <a name="increasing-the-quota-or-throttle-limit"></a>Aumentar o limite de quota ou aceleração

A qualquer momento, pode aumentar as quotas ou os limites de aceleração [aumentando o número de unidades aprovisionadas num hub IoT](iot-hub-upgrade.md).

## <a name="latency"></a>Latência

O IoT Hub esforça-se por fornecer baixa latência para todas as operações. No entanto, devido às condições de rede e a outros fatores imprevisíveis, não pode garantir uma certa latência. Ao desenhar a sua solução, deve:

* Evite fazer suposições sobre a latência máxima de qualquer operação do IoT Hub.
* Forme o seu hub IoT na região de Azure mais próximo dos seus dispositivos.
* Considere utilizar o Azure IoT Edge para efetuar operações sensíveis à latência no dispositivo ou numa porta de entrada próxima do dispositivo.

Várias unidades do IoT Hub afetam o estrangulamento como descrito anteriormente, mas não fornecem quaisquer benefícios ou garantias adicionais de latência.

Se vir aumentos inesperados na latência de operação, contacte o Suporte da [Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Passos seguintes

Para uma discussão aprofundada sobre o comportamento acelerado do IoT Hub, consulte o blog post [IoT Hub a estrangular e você](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

Outros tópicos de referência neste guia de desenvolvimento do IoT Hub incluem:

* [Pontos finais do Hub IoT](iot-hub-devguide-endpoints.md)
