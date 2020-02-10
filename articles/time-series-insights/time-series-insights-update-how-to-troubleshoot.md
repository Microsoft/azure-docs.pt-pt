---
title: Diagnosticar e resolver problemas um ambiente de pré-visualização - Azure Time Series Insights  Microsoft Docs
description: Aprenda a diagnosticar e resolver problemas com um ambiente de pré-visualização da Série De Tempo Azure Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: a306707f0ed47fba8fd854d820554bc1bd80e8bc
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110296"
---
# <a name="diagnose-and-troubleshoot-a-preview-environment"></a>Diagnosticar e resolver problemas um ambiente de pré-visualização

Este artigo resume vários problemas comuns que poderá encontrar quando trabalha com o seu ambiente de pré-visualização da Série De Tempo Azure Insights. O artigo também descreve potenciais causas e soluções para cada problema.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>Problema: Não consigo encontrar o meu ambiente no explorador de pré-visualização

Este problema pode ocorrer se não tiver permissão para aceder ao ambiente Time Series Insights. Os utilizadores precisam de uma função de acesso ao nível dos leitores para visualizar o seu ambiente time series Insights. Para verificar os atuais níveis de acesso e conceder acesso adicional, aceda à secção políticas de acesso a **dados** no recurso Time Series Insights no [portal Azure](https://portal.azure.com/).

  [![Verificar as políticas de acesso a dados.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>Problema: Não são vistos dados no explorador de pré-visualização

Existem várias razões comuns pelas quais os seus dados podem não aparecer no explorador de [pré-visualização](https://insights.timeseries.azure.com/preview)de Insights da Série De Tempo Azure .

- A fonte do seu evento pode não estar a receber dados.

    Verifique se a sua fonte de evento, que é um hub de eventos ou um hub IoT, está a receber dados das suas tags ou instâncias. Para verificar, consulte a página geral do seu recurso no portal Azure.

    [![Rever a visão geral das métricas do painel de instrumentos.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- Os dados de origem do evento não estão no formato JSON.

    Time Series Insights suporta apenas dados JSON. Para amostras JSON, leia [as formas JSON suportadas](./how-to-shape-query-json.md).

- A chave de origem do evento está a faltar uma permissão necessária.

  * Para um hub IoT, você precisa fornecer a chave que tem permissão de ligação de **serviço.**

    [![Verifique as permissões do hub ioT.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    * Tanto as políticas **iothubowner** como o trabalho de **serviço** porque têm permissão de ligação de **serviço.**

  * Para um centro de eventos, você precisa fornecer a chave que tem permissão **ouvir.**
  
    [![Rever permissões do centro de eventos.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    * Tanto as políticas **de Leitura** como **de Gestão** funcionam porque têm permissão **de Escuta.**

- O seu grupo de consumidores fornecido não é exclusivo da Time Series Insights.

    Durante o registo de um hub ioT ou centro de eventos, especifica o grupo de consumidores que é usado para ler os dados. Este grupo de consumidores deve ser único em ambiente. Se o grupo de consumidores for partilhado, o centro de eventos subjacente desliga automaticamente um dos leitores aleatoriamente. Forneça um grupo de consumidores único para que a Time Series Insights leia.

- A sua propriedade id da Série Time especificada no momento do fornecimento é incorreta, em falta ou nuca.

    Este problema pode ocorrer se a propriedade id da Série Tempo estiver configurada incorretamente no momento do fornecimento do ambiente. Para mais informações, leia [as melhores práticas para escolher um ID](./time-series-insights-update-how-to-id.md)da Série De Tempo . Neste momento, não é possível atualizar um ambiente de Insights da Série Time existente para utilizar um ID diferente da Série De Tempo.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problema: Alguns dados mostram, mas alguns estão desaparecidos

Pode estar a enviar dados sem o ID da Série Do Tempo.

- Este problema pode ocorrer quando envia eventos sem o campo de ID da Série Time na carga útil. Para mais informações, leia as [formas JSON suportadas](./how-to-shape-query-json.md).
- Este problema pode ocorrer porque o seu ambiente está a ser estrangulado.

    > [!NOTE]
    > Neste momento, a Time Series Insights suporta uma taxa máxima de ingestão de 6 Mbps.

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problema: O nome da propriedade timestamp da minha fonte de evento não funciona

Certifique-se de que o nome e o valor está em conformidade com as seguintes regras:

* O nome da propriedade timestamp é sensível ao caso.
* O valor da propriedade timestamp que vem da sua fonte de evento como uma cadeia JSON tem o formato `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Um exemplo de tal corda é `“2008-04-12T12:53Z”`.

A maneira mais fácil de garantir que o seu nome de propriedade timestamp é capturado e funcionando corretamente é usar o explorador de pré-visualização time series Insights. Dentro do explorador de pré-visualização time series Insights, utilize o gráfico para selecionar um período de tempo após fornecer o nome de propriedade timestamp. Clique na seleção e selecione a opção **de explorar eventos.** O cabeçalho da primeira coluna é o nome da propriedade do Timestamp. Deveria ter `($ts)` ao lado da palavra `Timestamp`, em vez de:

* `(abc)`, o que indica que a Time Series Insights lê os valores dos dados como cordas.
* O ícone do **calendário,** que indica que a Time Series Insights lê o valor dos dados como data.
* `#`, o que indica que a Time Series Insights lê os valores dos dados como um inteiro.

Se a propriedade timestamp não for explicitamente especificada, o centro ioT ou centro de eventos enqueued De um evento é usado como carimbo de tempo padrão.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Problema: Não consigo ver dados da minha loja quente no explorador

- Pode ter aprovisionado a sua loja quente recentemente, e os dados ainda estão a fluir.
- Pode ter apagado a sua loja quente, caso em que teria perdido dados.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problema: Não consigo ver ou editar o meu Modelo de Série Sem Tempo

- Podes estar a aceder a um ambiente Time Series Insights S1 ou S2.

   Os Modelos time series são suportados apenas em ambientes pay-as-you-go. Para obter mais informações sobre como aceder ao seu ambiente S1 ou S2 do explorador de pré-visualização da Série Time Insights, leia [os dados visualizar no explorador](./time-series-insights-update-explorer.md).

   [![nenhum evento no ambiente.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Pode não ter permissões para visualizar e editar o modelo.

   Os utilizadores precisam de acesso ao nível dos contribuintes para editar e visualizar o seu Modelo de Série de Tempo. Para verificar os níveis de acesso atuais e conceder acesso adicional, aceda à secção políticas de acesso a **dados** no seu recurso Time Series Insights no portal Azure.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>Problema: Todos os meus casos no explorador de pré-visualização não têm um pai

Este problema pode ocorrer se o seu ambiente não tiver uma hierarquia do Modelo da Série Tempo definida. Para mais informações, leia [Trabalhar com modelos da Série de Tempo.](./time-series-insights-update-how-to-tsm.md)

  [![casos não parentais apresentarão um aviso.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

- Ler [Trabalho com Modelos de Séries de Tempo](./time-series-insights-update-how-to-tsm.md).

- Saiba mais sobre [as formas JSON suportadas.](./how-to-shape-query-json.md)

- Reveja o [planeamento e os limites](./time-series-insights-update-plan.md) na Pré-visualização de Insights da Série De Tempo Azure.
