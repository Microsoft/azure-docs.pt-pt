---
title: Desempenho e afinação da memória do editor da Microsoft OPC
description: Neste tutorial, aprende-se a sintonizar a performance e a memória da Editora OPC.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 89e288d1186efd405019d6474dcbd332e7925d67
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787843"
---
# <a name="tutorial-tune-the-opc-publisher-performance-and-memory"></a>Tutorial: Sintonize o desempenho e a memória da Editora OPC

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ajuste o desempenho
> * Ajustar o fluxo de mensagem para os recursos de memória

Ao executar o OPC Publisher em configurações de produção, devem ser considerados os requisitos de desempenho da rede (produção e latência) e os recursos de memória. A OPC Publisher expõe os seguintes parâmetros de linha de comando para ajudar a satisfazer estes requisitos:

* Capacidade de fila de mensagens `mq` (para a versão 2.5 e abaixo, não disponível na versão 2.6, `om` para a versão 2.7)
* Intervalo de envio do IoT Hub `si` ()
* Tamanho da mensagem IoT Hub `ms` ()

## <a name="adjusting-iot-hub-send-interval-and-iot-hub-message-size"></a>Ajustar o intervalo de envio do Hub IoT e o tamanho da mensagem IoT Hub

O `mq/om` parâmetro controla o limite superior da capacidade da fila interna da mensagem. Esta fila tampona todas as mensagens antes de serem enviadas para o IoT Hub. O tamanho padrão da fila é de até 2 MB para a versão 2.5 e inferior a 4000 IoT Hub para a versão 2.7 (isto é, se a definição para o tamanho da mensagem IoT Hub for de 256 KB, o tamanho da fila será de até 1 GB). Se o OPC Publisher não conseguir enviar mensagens para o IoT Hub com rapidez suficiente, o número de itens nesta fila aumenta. Se isto acontecer durante os ensaios, um ou ambos os seguintes podem ser feitos para atenuar:

* diminuir o intervalo de envio do Hub IoT `si` ()

* aumentar o tamanho da mensagem IoT Hub `ms` (, o máximo que pode ser definido é de 256 KB)

Se a fila continuar a crescer, mesmo que os `si` parâmetros e `ms` os parâmetros tenham sido ajustados, eventualmente a capacidade máxima da fila será alcançada e as mensagens serão perdidas. Isto deve-se ao facto de tanto o parâmetro como o `si` `ms` parâmetro terem limites físicos e a ligação à Internet entre o OPC Publisher e o IoT Hub não ser suficientemente rápida para o número de mensagens que devem ser enviadas num determinado cenário. Nesse caso, apenas a criação de vários editores paralelos da OPC ajudará. O `mq/om` parâmetro também tem o maior impacto no consumo de memória pela OPC Publisher. 

O `si` parâmetro obriga o Editor OPC a enviar mensagens para o IoT Hub no intervalo especificado. Uma mensagem é enviada quando o tamanho máximo da mensagem IoT Hub de 256 KB de dados está disponível (desencadeando o intervalo de envio para reset) ou quando o tempo de intervalo especificado tiver passado.

O `ms` parâmetro permite o lote de mensagens enviadas para o IoT Hub. Na maioria das configurações de rede, a latência de enviar uma única mensagem para o IoT Hub é elevada, em comparação com o tempo que leva para transmitir a carga útil. Isto deve-se principalmente aos requisitos de Qualidade de Serviço (QoS), uma vez que as mensagens só são reconhecidas depois de processadas pelo IoT Hub. Portanto, se um atraso para que os dados cheguem ao IoT Hub for aceitável, o Editor OPC deve ser configurado para utilizar o tamanho máximo da mensagem de 256 KB, definindo o `ms` parâmetro para 0. É também a forma mais rentável de utilizar o Editor OPC.

A configuração predefinida envia dados para o IoT Hub a cada 10 segundos ( `si=10` ) ou quando estão disponíveis 256 KB de dados de mensagem IoT Hub ( `ms=0` ). Isto adiciona um atraso máximo de 10 segundos, mas tem baixa probabilidade de perder dados devido ao grande tamanho da mensagem. A métrica `monitored item notifications enqueue failure`  na versão 2.5 e abaixo da `messages lost` OPC Publisher mostra quantas mensagens foram perdidas.

Quando ambos `si` e `ms` parâmetros estão definidos para 0, o OPC Publisher envia uma mensagem para o IoT Hub assim que os dados estão disponíveis. Isto resulta num tamanho médio de mensagem IoT Hub de pouco mais de 200 bytes. No entanto, a vantagem desta configuração é que a OPC Publisher envia os dados do ativo conectado sem demora. O número de mensagens perdidas será elevado para casos de utilização em que uma grande quantidade de dados deve ser publicado e, portanto, isso não é recomendado para estes cenários.

Para medir o desempenho do OPC Publisher, o `di` parâmetro pode ser utilizado para imprimir métricas de desempenho ao registo de vestígios no intervalo especificado (em segundos).

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu a afinar o desempenho e a memória da Editora OPC, pode consultar o repositório da OPC Publisher GitHub para obter mais recursos:

> [!div class="nextstepaction"]
> [Repositório da OPC Editor GitHub](https://github.com/Azure/Industrial-IoT)