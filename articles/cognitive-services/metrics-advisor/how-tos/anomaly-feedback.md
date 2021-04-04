---
title: Fornecer feedback de anomalia ao serviço Métrica Advisor
titleSuffix: Azure Cognitive Services
description: Saiba como enviar feedback sobre anomalias encontradas pelo seu exemplo de Metrics Advisor e afinar os resultados.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbullwin
ms.openlocfilehash: 96a6dfebdd0047d5a6f4ff3e295ab96dd8c7226a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96184974"
---
# <a name="provide-anomaly-feedback"></a>Fornecer feedback de anomalia

O feedback do utilizador é um dos métodos mais importantes para descobrir defeitos dentro do sistema de deteção de anomalias. Aqui fornecemos uma forma de os utilizadores marcarem resultados de deteção incorretos diretamente numa série de tempo, e aplicarem o feedback imediatamente. Desta forma, um utilizador pode ensinar o sistema de deteção de anomalias a fazer a deteção de anomalias para uma série de tempo específica através de interações ativas. 

> [!NOTE]
> Atualmente, o feedback só afetará os resultados de deteção de anomalias por **deteção inteligente,** mas não **o limiar de hard threshold** e **change** threshold .

## <a name="how-to-give-time-series-feedback"></a>Como dar feedback de séries de tempo

Pode fornecer feedback a partir da página de detalhes métricos em qualquer série. Basta selecionar qualquer ponto, e verá o diálogo de feedback abaixo. Mostra-lhe as dimensões da série que escolheu. Pode resmarcar valores de dimensão ou até remover alguns deles para obter um lote de dados da série de tempo. Depois de escolher séries de tempo, selecione o botão **Adicionar** para adicionar o feedback, existem quatro tipos de feedback que pode dar. Para anexar vários itens de feedback, selecione o botão **Guardar** uma vez que completa as suas anotações.

:::image type="content" source="../media/feedback/click-on-any-point.png" alt-text="Gráfico com dados da série de tempo com uma linha azul e pontos vermelhos em vários pontos. Caixa vermelha em torno de um ponto com o texto: Selecione qualquer ponto":::

:::image type="content" source="../media/feedback/select-or-remove.png" alt-text="Adicione a caixa de diálogo de feedback com duas dimensões e a opção de selecionar ou remover dimensões e adicionar feedback.":::

### <a name="mark-the-anomaly-point-type"></a>Marque o tipo de ponto de anomalia

Como mostrado na imagem abaixo, o diálogo de feedback preencherá automaticamente o tempo de marcação do seu ponto escolhido, embora possa editar este valor. Em seguida, selecione se pretende identificar este item como um `Anomaly` `NotAnomaly` , ou `AutoDetect` .

:::image type="content" source="../media/feedback/anomaly-value.png" alt-text="Deixe cair menu com escolhas de Anomalia, NotAnomaly e AutoDetect":::

A seleção aplicará o seu feedback ao processamento futuro de deteção de anomalias da mesma série. Os pontos processados não serão recalculados. Isto significa que se marcares uma Anomalia como NãoAnomaly, iremos suprimir anomalias semelhantes no futuro, e se marcares um `NotAnomaly` ponto como `Anomaly` , tendemos a detetar pontos semelhantes `Anomaly` aos do futuro. Se `AutoDetect` for escolhido, qualquer feedback anterior sobre o mesmo ponto será ignorado no futuro.

## <a name="provide-feedback-for-multiple-continuous-points"></a>Fornecer feedback para vários pontos contínuos 

Se desejar dar feedback de anomalia para vários pontos contínuos ao mesmo tempo, selecione o grupo de pontos que pretende anotar. Verá o intervalo de tempo escolhido automaticamente preenchido quando fornecer feedback de anomalia.

:::image type="content" source="../media/feedback/continuous-anomaly-feedback.png" alt-text="Menu de feedback de anomalia com anomalia selecionada e um intervalo de tempo específico":::

Para ver se um ponto individual é afetado pelo seu feedback de anomalia, ao navegar numa série de tempo, selecione um único ponto. Se o seu resultado de deteção de anomalias tiver sido alterado por feedback, a ponta da ferramenta mostrará **afetada pelo feedback: verdadeiro**. Se mostrar **afetado pelo feedback: falso,** isto significa que foi realizado um cálculo de feedback de anomalia para este ponto, mas o resultado da deteção de anomalias não deve ser alterado.

:::image type="content" source="../media/feedback/affected-by-feedback.png" alt-text="Visualização da ponta da ferramenta com as palavras: Afetada pelo feedback: verdadeiro, realçado a vermelho":::

Há algumas situações em que não sugerimos dar feedback:

- A anomalia é causada por um feriado. Sugere-se que use um evento predefinido para resolver este tipo de alarme falso, pois será mais preciso.
- A anomalia é causada por uma alteração conhecida da fonte de dados. Por exemplo, uma mudança de sistema a montante aconteceu na altura. Nesta situação, espera-se que dê um alerta de anomalia, uma vez que o nosso sistema não sabia o que causou a mudança de valor e quando mudanças de valor semelhantes voltarão a acontecer. Assim, não sugerimos anotar este tipo de questão como `NotAnomaly` .

## <a name="change-points"></a>Mudar de pontos

Por vezes, a mudança de tendência dos dados afetará os resultados da deteção de anomalias. Quando for tomada uma decisão sobre se um ponto é ou não uma anomalia, a última janela de dados da história será tomada em consideração. Quando a sua série de tempo tiver uma mudança de tendência, pode marcar o ponto de mudança exato, isto ajudará o nosso detetor de anomalias em análise futura.

Como mostra a figura abaixo, pode selecionar `ChangePoint` para o tipo de feedback e selecionar , ou da lista de `ChangePoint` `NotChangePoint` `AutoDetect` recuos.

:::image type="content" source="../media/feedback/changepoint.png" alt-text="Alterar menu de pontos com opções de dropdown contendo opções para ChangePoint, NotChangePoint e AutoDetect":::

> [!NOTE]
> Se os seus dados continuarem a mudar, só terá de marcar um ponto como `ChangePoint` um , por isso, se marcar um `timerange` , preencheremos automaticamente o tempo e o tempo do último ponto. Neste caso, a sua anotação só afetará os resultados de deteção de anomalias após 12 pontos.

## <a name="seasonality"></a>Sazonalidade

Para os dados sazonais, quando realizamos a deteção de anomalias, um passo é estimar o período (sazonalidade) das séries tempor, e aplicá-lo à fase de deteção de anomalias. Às vezes, é difícil identificar um período preciso, e o período também pode mudar. Um período mal definido pode ter efeitos colaterais nos resultados de deteção de anomalias. Pode encontrar o período atual a partir de uma ponta de ferramenta, o seu nome é `Min Period` .

:::image type="content" source="../media/feedback/min-period.png" alt-text="Sobreposição da ponta da ferramenta com as palavras Period e o número sete realçado a vermelho.":::

Pode fornecer feedback durante o período para corrigir este tipo de erro de deteção de anomalias. Como o número mostra, pode definir um valor de período. A unidade `interval` significa uma granularidade. Aqui intervalos zero significa que os dados não são sazonais. Também pode selecionar `AutoDetect` se pretende cancelar o feedback anterior e deixar o período de deteção do gasoduto automaticamente. 
 
> [!NOTE]
> Ao definir o período de tempo não precisa de atribuir um relógio de tempo ou de tempo, o período afetará futuras deteções de anomalias em séries temporais a partir do momento em que der feedback.


:::image type="content" source="../media/feedback/period-feedback.png" alt-text="Menu com período de autodetect definido para 28 e intervalo definido para zero indicando não sazonal.":::

## <a name="provide-comment-feedback"></a>Fornecer feedback de comentários

Também pode adicionar comentários para anotar e fornecer contexto aos seus dados. Para adicionar comentários, selecione um intervalo de tempo e adicione o texto para o seu comentário.

:::image type="content" source="../media/feedback/feedback-comment.png" alt-text="Menu com a capacidade de definir um intervalo de tempo e uma caixa para adicionar um comentário baseado em texto":::

## <a name="time-series-batch-feedback"></a>Feedback do lote de séries de tempo

Como descrito anteriormente, o feedback modal permite-lhe reselecção ou remoção de valores de dimensão, para obter um lote de séries de tempo definidas por um filtro de dimensão. Também pode abrir este modal clicando no botão "+" para feedback a partir do painel esquerdo e selecionar dimensões e valores de dimensão.

:::image type="content" source="../media/feedback/feedback-time-series.png" alt-text="Menu com um sinal azul plus realçado em vermelho ao lado da palavra Feedback":::

:::image type="content" source="../media/feedback/feedback-dimension.png" alt-text="Adicione menu de feedback com duas dimensões indicadas por Dim1 e Dim2":::

## <a name="how-to-view-feedback-history"></a>Como ver a história do feedback

Há duas maneiras de ver a história do feedback. Pode selecionar o botão de histórico de comentários a partir do painel esquerdo e verá uma lista de comentários modal. Ele lista todo o feedback que já deu antes, seja para filtros de série única ou de dimensão.

:::image type="content" source="../media/feedback/feedback-history-options.png" alt-text="Menu da lista de comentários'":::

Outra forma de ver a história do feedback é de uma série. Verá vários botões no canto superior direito de cada série. Selecione o botão de feedback do show e a linha mudará de pontos de anomalia para mostrar entradas de feedback. A bandeira verde representa um ponto de mudança, e os pontos azuis são outros pontos de feedback. Também pode selecioná-los e obterá uma lista de comentários modal que lista os detalhes do feedback dado para este ponto.

:::image type="content" source="../media/feedback/feedback-history-graph.png" alt-text="Gráfico de história de feedback":::

:::image type="content" source="../media/feedback/feedback-list.png" alt-text="Menu de lista de comentários com duas dimensões":::

> [!NOTE]
> Qualquer pessoa que tenha acesso à métrica está autorizada a dar feedback, para que possa ver feedback dado por outros proprietários de feedfeed. Se editar o mesmo ponto que outra pessoa, o seu feedback substituirá a entrada de feedback anterior.       

## <a name="next-steps"></a>Passos seguintes
- [Diagnosticar um incidente.](diagnose-incident.md)
- [Configurar métricas e otimizar a configuração da deteção](configure-metrics.md)
- [Configurar alertas e obter notificações através de um alerta](../how-tos/alerts.md)