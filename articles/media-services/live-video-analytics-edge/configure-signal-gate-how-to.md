---
title: Configure um portão de sinal para gravação de vídeo baseada em eventos - Azure
description: Este artigo fornece orientações sobre como configurar um portão de sinal num gráfico de mídia.
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: 4204a43915f9c79cae7dfe82b37e741fee89fb4a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380242"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>Configurar um portão de sinal para gravação de vídeo baseada em eventos

Dentro de um gráfico de mídia, um [nó do processador do portão](media-graph-concept.md#signal-gate-processor) de sinal permite-lhe encaminhar os meios de um nó para outro, quando o portão é acionado por um evento. Quando acionado, o portão abre-se e permite que os meios de comunicação fluam durante uma duração especificada. Na ausência de eventos para acionar o portão, o portão fechar-se-á e os meios de comunicação param de fluir. O processador signal gate é aplicável para a gravação de vídeo baseada em eventos.
Neste artigo, irá conhecer os detalhes sobre como configurar um processador signal gate.

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida
-   [Grafo do suporte de dados](media-graph-concept.md)
-   [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)


## <a name="problem"></a>Problema
O utilizador pode querer começar a gravar um determinado tempo antes ou depois de o portão ter sido acionado por um evento. O utilizador conhece a latência aceitável dentro do seu sistema, pelo que o utilizador pretende especificar a latência do processador signal gate. O utilizador quer especificar o mais curto e longo que a duração da sua gravação pode ser, independentemente do número de novos eventos recebidos.
 
### <a name="use-case-scenario"></a>Cenário de caso de utilização
Suponha que queira gravar o vídeo sempre que a porta da frente do seu prédio abrir. Deseja que o **X** segundos antes da porta seja aberta incluído na gravação. Se a gravação não **Y** for aberta de novo. Se a gravação for **Z** repetidamente aberta, a gravação dura, se a porta for aberta repetidamente. Sabe que o sensor da porta tem uma latência de **K** segundos e quer diminuir a probabilidade de os eventos serem ignorados ("chegadas tardias"), por isso, pretende permitir pelo menos **K** segundos para que os eventos cheguem.


## <a name="solution"></a>Solução

**_Modificação dos parâmetros do processador do portão de_* sinal _

Um processador do portão de sinal é configurado por 4 parâmetros:
- _ *janela de avaliação de ativação**
- **compensação do sinal de ativação**
- **janela de ativação mínima**
- **janela de ativação máxima**. 

Quando o processador do portão de sinal for acionado, permanecerá aberto durante o tempo mínimo de ativação. O evento de ativação começa no horário para o evento mais antigo, mais o offset do sinal de ativação. Se o processador do portão de sinal for acionado novamente, enquanto estiver aberto, o temporizador reinicia e o portão permanecerá aberto durante pelo menos o tempo mínimo de ativação. O processador do portão de sinal nunca ficará aberto mais tempo do que o tempo máximo de ativação. Um evento **(Evento 1)** que ocorre antes de outro evento **(Evento 2)** , baseado em timetamps mediáticos, está sujeito a ser ignorado, se o sistema ficar e o **Event 1** chegar ao processador do portão de sinal após o **Evento 2**. Se o **Evento 1** não chegar entre a chegada do **Evento 2** e a janela de avaliação de **ativação,** o **Evento 1** será ignorado e não passará pelo processador do portão de sinalização. As identificações de correlação estão definidas para cada evento. Estes IDs são definidos a partir do evento inicial e são sequenciais para cada evento seguinte.

> [!IMPORTANT]
> O tempo mediático baseia-se no tempo de tempo mediático de quando ocorreu um evento nos meios de comunicação. A sequência de eventos que chegam ao portão de sinalização pode não refletir a sequência de eventos que chegam no tempo dos media.


### <a name="parameters-based-on-when-events-arrive-in-physical-time-to-the-signal-gate"></a>Parâmetros: (Baseado em quando os eventos chegam em tempo físico ao portão de sinalização)

* **Ativação mínima Tempo de ativação (a duração mais curta possível de uma gravação)** = o número mínimo de segundos que o processador do portão de sinal permanecerá aberto após ser acionado para receber novos eventos, a menos que interrompido pela **máxima Hora de Ativação**
* **máximaActivaçãoTime (duração mais longa possível de uma gravação)** = o número máximo de segundos do evento inicial de que o processador do portão de sinal permanecerá aberto após ser acionado para receber novos eventos, independentemente dos eventos recebidos
* **activaçãoSignalOffset** = o número de segundos entre a ativação do processador do portão de sinal e quando a gravação de vídeo começa, tipicamente este valor é negativo, de modo a iniciar a gravação antes do evento de desencadeamento
* **activaçãoEvaluationWindow** = o número de segundos a partir do evento inicial de desencadeamento, no qual um evento ocorrido antes do evento inicial, em tempo de mídia, deve chegar ao processador do portão de sinal antes de ser ignorado e considerado uma "chegada tardia"

> [!NOTE]
> Uma chegada tardia é qualquer evento que chegue uma vez que a janela de avaliação de ativação tenha passado, mas este evento chegou antes do evento inicial na hora dos media.

### <a name="limits-of-parameters"></a>Limites dos parâmetros

* **activaçãoEvaluationWindow: 0 segundos a 10 segundos**

* **activaçãoSignalOffset: -1 minuto a 1 minuto**

* **ativação mínima Tempo de ativação: 1 segundo a 1 hora**

* **máxima de activaçãoTime: 1 segundo a 1 hora**


Com base no caso de utilização, os parâmetros seriam definidos da seguinte forma:

* **activaçãoEvaluationWindow = K sec**
* **activaçãoSignalOffset = - X seg**
* **ativação mínimaSaow = Y seg**
* **máximaActivaçãoWindow = Z seg**


Aqui está um exemplo do que a secção do nó do processador signal Gate gostaria numa topologia de gráficos de mídia para os seguintes valores de parâmetro:
* **activaçãoEvaluationWindow = 1 segundo**
* **activaçãoSignalOffset = -5 segundos**
* **Tempo deActivação mínima = 20 segundos**
* **máxima de activaçãoTime = 40 segundos**

> [!IMPORTANT]
> Espera-se [formato de duração ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations
) para cada valor de parâmetro. 
**Ex: PT1S = 1 segundo**


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


Vamos considerar como esta configuração do processador do portão de sinal se comportará em diferentes cenários de gravação.


**1 Evento a partir de 1 Fonte ( *Ativação Normal)***

Um processador do portão de sinal que recebe um evento resultaria numa gravação que inicia a "compensação do sinal de ativação" (5) segundos antes do evento chegar ao portão. O restante da gravação é "tempo mínimo de ativação" (20) segundos, uma vez que nenhum outro evento chega antes do tempo mínimo de ativação completar para voltar a registar o portão.

Diagrama de exemplo:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="Ativação normal":::

* Duração do Gravação = -offset + mínimoActivaçãoTime = [E1+offset, E1+minimumActivationTime]


**2 Eventos de 1 Fonte ( *Ativação Retriggered)***

Um processador do portão de sinal que recebe dois eventos resultaria numa gravação que inicia a "compensação do sinal de ativação" (cinco) segundos antes do primeiro evento chegar ao portão. O segundo evento chega cinco segundos depois do primeiro evento, que é antes do "tempo mínimo de ativação" (20) segundos do primeiro evento, pelo que o portão é re-tentado para permanecer aberto. O restante da gravação é "tempo mínimo de ativação" (20) segundos de duração, uma vez que nenhum outro evento chega antes do tempo mínimo de ativação do 2º evento completar para voltar a registar o portão.

Diagrama de exemplo:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="Ativação retriggerada":::

* Duração do Gravação = -offset + (chegada do 2º evento - chegada do 1º evento) + mínimoActivaçãoTime


**N Eventos a partir de 1 Fonte ( *Ativação Máxima)***

Um processador do portão de sinal que recebe eventos N resultaria numa gravação que inicia a "compensação do sinal de ativação" (5) segundos antes do primeiro evento chegar ao portão. À medida que cada evento chega antes da conclusão do "tempo mínimo de ativação" (20) segundos do evento anterior, o portão seria continuamente retrodido e permaneceria aberto até ao "tempo máximo de ativação" (40) segundos após o primeiro evento, no qual o portão fecharia e deixaria de aceitar novos eventos.

Diagrama de exemplo:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="Ativação máxima":::
 
* Duração do Gravação = -offset + maximumActivationTime

> [!IMPORTANT]
> Os diagramas assumem que cada evento chega no mesmo caso em tempo físico e mediático. (Sem chegadas tardias)

## <a name="next-steps"></a>Passos seguintes

### <a name="try-it-out"></a>Experimente-o para fora

[Tutorial de gravação de vídeo baseado em eventos](event-based-video-recording-tutorial.md)

Utilizando o tutorial de gravação de vídeo baseado em eventos, edite o [topology.jsem](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json), modifique os parâmetros para o nó signalgateProcessor e, em seguida, siga o restante do tutorial. Reveja as gravações de vídeo para analisar o efeito dos parâmetros.



