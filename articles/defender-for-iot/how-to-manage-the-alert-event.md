---
title: Gerir eventos de alerta
description: Gerencie os eventos de alerta detetados na sua rede.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 023b33732b80198c4ed187328e8d18314f385f94
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97841865"
---
# <a name="manage-alert-events"></a>Gerir eventos de alerta

Estão disponíveis as seguintes opções para gerir eventos de alerta:

 | Ação | Descrição |
 |--|--|
 | **Learn** | Autorizar o evento detetado. Para mais informações, consulte [Sobre a aprendizagem e desaprender eventos.](#about-learning-and-unlearning-events) |
 | **Confirmação** | Esconda o alerta uma vez para o evento detetado. O alerta será novamente acionado se o evento for detetado novamente. Para mais informações, consulte [Sobre o reconhecimento e não conhecer eventos.](#about-acknowledging-and-unacknowledging-events) |
 | **Mudo** | Ignore continuamente a atividade com dispositivos idênticos e tráfego comparável. Para obter mais informações, consulte [Sobre eventos silenciados e desmudos.](#about-muting-and-unmuting-events) |

## <a name="about-learning-and-unlearning-events"></a>Sobre a aprendizagem e a desaprendimento de eventos

Eventos que indicam desvios da rede aprendida podem refletir alterações de rede válidas. Exemplos podem incluir um novo dispositivo autorizado que aderiu à rede ou uma atualização de firmware autorizada.

Quando seleciona **Aprender,** o sensor considera o tráfego, configurações ou atividade válido. Isto significa que os alertas deixarão de ser desencadeados para o evento. Também significa que o evento não será calculado quando o sensor gera avaliação de risco, vetor de ataque e outros relatórios.

Por exemplo, recebe um alerta que indica a atividade de digitalização de endereços num dispositivo que um scanner de rede não definiu previamente. Se este dispositivo foi adicionado à rede com o propósito de digitalizar, pode instruir o sensor a aprender o dispositivo como um dispositivo de digitalização.

:::image type="content" source="media/how-to-work-with-alerts-sensor/detected.png" alt-text="A janela de digitalização detetada.":::

Eventos aprendidos podem não ser aprendidos. Quando o sensor desaprender os eventos, irá reencontrar alertas relacionados com este evento.

## <a name="about-acknowledging-and-unacknowledging-events"></a>Sobre reconhecer e não reconhecer eventos

Em certas situações, pode não querer que um sensor aprenda um evento detetado, ou a opção pode não estar disponível. Em vez disso, o incidente pode requerer atenuação. Por exemplo:

- **Atenuar uma configuração ou dispositivo de rede:** Recebe um alerta indicando que foi detetado um novo dispositivo na rede. Ao investigar, descobre que o dispositivo é um dispositivo de rede não autorizado. Lida com o incidente desligando o dispositivo da rede.
- **Atualização de uma configuração do sensor**: Recebe um alerta indicando que um servidor iniciou um número excessivo de ligações remotas. Este alerta foi desencadeado porque os limiares de anomalia do sensor foram definidos para desencadear alertas acima de um certo número de sessões num minuto. Lida com o incidente atualizando os limiares.

Depois de realizar a mitigação ou investigação, pode instruir o sensor a ocultar o alerta selecionando **Reconhecimento**. Se o evento for detetado novamente, o alerta será retrossado.

Para esconder o alerta:

  - Selecione **Reconhecimento**.

Para ver o alerta novamente:

  - Aceda ao alerta e selecione **Unacknowledge**.

Alertas não reconhecidos se for necessária uma investigação mais aprofundada.

## <a name="about-muting-and-unmuting-events"></a>Sobre eventos silenciados e desmudos

Em certas circunstâncias, é melhor instruir o seu sensor a ignorar um cenário específico na sua rede. Por exemplo:

  - O motor **Anomaly** aciona um alerta sobre um pico de largura de banda entre dois dispositivos, mas o pico é válido para estes dispositivos.

  - O motor **de Violação de Protocolo** aciona um alerta sobre um desvio de protocolo detetado entre dois dispositivos, mas o desvio é válido entre os dispositivos.

Nestas situações, a aprendizagem não está disponível. Ao aprender não pode ser realizado e você quer suprimir o alerta e remover o dispositivo ao calcular riscos e vetores de ataque, você pode silenciar o evento de alerta em vez disso.

> [!NOTE] 
> Não é possível silenciar eventos em que um dispositivo de internet é definido como a fonte ou destino.

### <a name="what-traffic-is-muted"></a>Que tráfego é silenciado?

Um cenário silenciado inclui os dispositivos de rede e o tráfego detetado para um evento. O título de alerta descreve o tráfego que está a ser silenciado.

O dispositivo ou dispositivos em silenciamento serão apresentados como uma imagem no alerta. Se forem mostrados dois dispositivos, o tráfego entre eles será silenciado.

**Exemplo 1**

Quando um evento é silenciado, é ignorado sempre que o primário (fonte) envia ao secundário (destino) um código de função ilegal, tal como definido pelo fornecedor.

:::image type="content" source="media/how-to-work-with-alerts-sensor/secondary-device-connected.png" alt-text="Dispositivo secundário recebido.":::

**Exemplo 2**

Quando um evento é silenciado, é ignorado sempre que a fonte envia um cabeçalho HTTP com conteúdo ilegal, independentemente do destino.

:::image type="content" source="media/how-to-work-with-alerts-sensor/illegal-http-header-content.png" alt-text="Screenshot de conteúdo ilegal do cabeçalho HTTP.":::

**Depois de um evento é silenciado:**

- O alerta será acessível na vista de alerta **reconhecida** até que não seja desmulado.

- A ação silenciada aparecerá na Linha do Tempo do **Evento**.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/muted-event-notification-screenshot.png" alt-text="Evento detetado e silenciado.":::

- O sensor recalculará os dispositivos ao gerar avaliação de risco, vetor de ataque e outros relatórios. Por exemplo, se silenciar um alerta que detetou tráfego malicioso num dispositivo, esse dispositivo não será calculado no relatório de avaliação de risco.

**Para silenciar e desativar um alerta:**

- Selecione o ícone **Desconte** no alerta.

**Para ver alertas silenciados:**

1. Selecione o formulário de opção **Reconhecida** no ecrã principal **alerta.**

2. Passe por cima de um alerta para ver se está mudo.  

## <a name="see-also"></a>Veja também

[Gerar relatórios](how-to-generate-reports.md)

[Controlar o tráfego que é monitorizado](how-to-control-what-traffic-is-monitored.md)
