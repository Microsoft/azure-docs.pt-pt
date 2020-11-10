---
title: Configure um portão de sinal para gravação de vídeo baseada em eventos - Azure
description: Este artigo fornece orientações sobre como configurar um portão de sinal num gráfico de mídia.
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: afcec7c03f1353f08b58311278f5a533e0c911bc
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410798"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>Configurar um portão de sinal para gravação de vídeo baseada em eventos

Dentro de um gráfico de mídia, um [nó do processador do portão](media-graph-concept.md#signal-gate-processor) de sinal permite-lhe encaminhar os meios de um nó para outro quando o portão é acionado por um evento. Quando é acionado, o portão abre-se e permite que os meios de comunicação fluam durante uma duração especificada. Na ausência de eventos para acionar o portão, o portão fecha-se e os meios de comunicação param de fluir. Pode utilizar o processador signal gate para a gravação de vídeo baseada em eventos.

Neste artigo, você vai aprender a configurar um processador de portão de sinal.

## <a name="suggested-prereading"></a>Pré-leitura sugerida
-   [Grafo do suporte de dados](media-graph-concept.md)
-   [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)


## <a name="problem"></a>Problema
Um utilizador pode querer começar a gravar num determinado momento antes ou depois de o portão ser acionado por um evento. O utilizador conhece a latência aceitável dentro do seu sistema. Então querem especificar a latência do processador do portão de sinal. Pretendem ainda especificar a duração mínima e máxima da sua gravação, independentemente do número de novos eventos recebidos.
 
### <a name="use-case-scenario"></a>Cenário de caso de utilização
Suponha que queira gravar o vídeo sempre que a porta da frente do seu prédio abrir. Quer que a gravação: 

- Inclua os segundos *X* antes da porta se abrir. 
- Duram pelo menos *y* segundos se a porta não estiver aberta de novo. 
- Duram no máximo segundos *Z* se a porta for aberta repetidamente. 
 
Sabes que o teu sensor da porta tem uma latência de segundos *K.* Para reduzir a probabilidade de os eventos serem ignorados como chegadas tardias, você quer permitir pelo menos *K* segundos para que os eventos cheguem.


## <a name="solution"></a>Solução

Para resolver o problema, modifique os parâmetros do processador do portão de sinal.

Para configurar um processador do portão de sinal, utilize estes quatro parâmetros:
- Janela de avaliação de ativação
- Compensação do sinal de ativação
- Janela de ativação mínima
- Janela de ativação máxima

Quando o processador do portão de sinal é acionado, permanece aberto durante o tempo mínimo de ativação. O evento de ativação começa no carimbo de hora para o evento mais cedo, mais o offset do sinal de ativação. 

Se o processador do portão de sinal for acionado novamente enquanto estiver aberto, o temporizador reinicia e o portão permanece aberto durante pelo menos o tempo mínimo de ativação. O processador do portão de sinal nunca permanece aberto por mais tempo do que o tempo máximo de ativação. 

Um evento (evento 1) que ocorre antes de outro evento (evento 2), baseado em carimbos de tempo de mídia, pode ser ignorado se o sistema ficar e o evento 1 chegar ao processador do portão de sinal após o evento 2. Se o evento 1 não chegar entre a chegada do evento 2 e a janela de avaliação de ativação, o evento 1 é ignorado. Não é passado através do processador do portão de sinalização. 

As identificações de correlação estão definidas para cada evento. Estes IDs são definidos a partir do evento inicial. São sequenciais para cada evento seguinte.

> [!IMPORTANT]
> O tempo de mídia baseia-se na data de tempo dos meios de comunicação quando ocorre um evento nos meios de comunicação. A sequência de eventos que chegam ao portão de sinalização pode não refletir a sequência de eventos que chegam no tempo dos media.


### <a name="parameters-based-on-the-physical-time-that-events-arrive-at-the-signal-gate"></a>Parâmetros, com base no tempo físico que os eventos chegam ao portão de sinalização

* **Mínimo de Ativação Tempo (a duração mais curta possível de uma gravação)** : O número mínimo de segundos que o processador do portão de sinal permanece aberto após o seu desencadeamento para receber novos eventos, a menos que seja interrompido pelo Tempo máximo de Ativação.
* **MáximaActivaçãoTime (duração mais longa possível de uma gravação)** : O número máximo de segundos do evento inicial de que o processador do portão de sinal permanece aberto após ter sido acionado para receber novos eventos, independentemente dos eventos recebidos.
* **activaçãoSignalOffset** : O número de segundos entre a ativação do processador do portão de sinal e o início da gravação de vídeo. Normalmente, este valor é negativo porque inicia a gravação antes do evento de desencadeamento.
* **activaçãoEvaluationWindow** : A partir do evento inicial de desencadeamento, o número de segundos em que um evento ocorrido antes do evento inicial, em tempo de mídia, deve chegar ao processador do portão de sinal antes de ser ignorado e considerado uma chegada tardia.

> [!NOTE]
> Uma *chegada tardia* é qualquer evento que chegue após a janela de avaliação de ativação ter passado, mas que chega antes do evento inicial em tempo de media.

### <a name="limits-of-parameters"></a>Limites dos parâmetros

* **activaçãoEvaluationWindow:** 0 segundos a 10 segundos
* **activationSignalOffset** : -1 minuto a 1 minuto
* **Mínimo DeActivação Tempo:** 1 segundo a 1 hora
* **máxima de ativação Tempo** de 1 segundo a 1 hora


No caso de utilização, definiria os parâmetros da seguinte forma:

* **activaçãoEvaluationWindow** : *K* segundos
* **activationSignalOffset** : *-X* segundos
* **ativação mínimaWindow** : *Y* segundos
* **máximaActivaçãoWindow** : *Z* segundos


Aqui está um exemplo de como a secção de nó **do processador signal Gate** iria olhar numa topologia de gráficos de mídia para os seguintes valores de parâmetro:
* **activaçãoEvaluationWindow:** 1 segundo
* **activaçãoSignalOffset** : -5 segundos
* **Mínimo de Ativação Tempo:** 20 segundos
* **máxima de ativação Tempo:** 40 segundos

> [!IMPORTANT]
> Espera-se [formato de duração ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations
) para cada valor de parâmetro. Por exemplo, PT1S = 1 segundo.


```
"processors":              
[
          {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "iotMessageSource"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "-PT5S",
            "minimumActivationTime": "PT20S",
            "maximumActivationTime": "PT40S"
          }
]
```


Agora considere como esta configuração do processador do portão de sinal se comportará em diferentes cenários de gravação.

### <a name="recording-scenarios"></a>Cenários de gravação

**Um evento de uma fonte *(ativação normal)***

Um processador do portão de sinal que recebe um evento resulta numa gravação que inicia 5 segundos (sinal de ativação = 5 segundos) antes de o evento chegar ao portão. O resto da gravação é de 20 segundos (tempo mínimo de ativação = 20 segundos) porque nenhum outro evento chega antes do fim do tempo mínimo de ativação para voltar a registar o portão.

Diagrama de exemplo:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="Diagrama mostrando a ativação normal de um evento de uma fonte.":::

* Duração do registo = -offset + mínimoActivaçãoTime = [E1+offset, E1+minimumActivationTime]


**Dois eventos de uma fonte *(ativação retroduzida)***

Um processador do portão de sinal que recebe dois eventos resulta numa gravação que começa 5 segundos (offset de sinal de ativação = 5 segundos) antes de o evento chegar ao portão. Além disso, o evento 2 chega 5 segundos após o evento 1. Como o evento 2 chega antes do fim do tempo mínimo de ativação do evento 1 (20 segundos), o portão é retrodido. O resto da gravação é de 20 segundos (tempo mínimo de ativação = 20 segundos) porque nenhum outro evento chega antes do fim do tempo mínimo de ativação do evento 2 para voltar a registar o portão.

Diagrama de exemplo:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="Diagrama mostrando a ativação re-tentada de dois eventos de uma fonte.":::

* Duração da gravação = -offset + (chegada do evento 2 - chegada do evento 1) + mínimo DeActivaçãoTime


***N* eventos de uma fonte *(ativação máxima)***

Um processador do portão de sinal que recebe eventos *N* resulta numa gravação que começa 5 segundos (offset de sinal de ativação = 5 segundos) antes do primeiro evento chegar ao portão. À medida que cada evento chega antes do fim do tempo mínimo de ativação de 20 segundos do evento anterior, o portão é continuamente retirado. Permanece aberto até ao tempo máximo de ativação de 40 segundos após o primeiro evento. Então o portão fecha e não aceita mais novos eventos.

Diagrama de exemplo:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="Diagrama mostrando a ativação máxima de eventos N de uma fonte.":::
 
* Duração da gravação = -offset + maximumActivationTime

> [!IMPORTANT]
> Os diagramas anteriores assumem que cada evento chega ao mesmo instante no tempo físico e no tempo mediático. Ou seja, assumem que não há chegadas tardias.

## <a name="next-steps"></a>Passos seguintes

Experimente o [tutorial de gravação de vídeo baseado em eventos.](event-based-video-recording-tutorial.md) Comece por editar o [topology.js.](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json) Modifique os parâmetros para o nó signalgateProcessor e, em seguida, siga o resto do tutorial. Reveja as gravações de vídeo para analisar o efeito dos parâmetros.



