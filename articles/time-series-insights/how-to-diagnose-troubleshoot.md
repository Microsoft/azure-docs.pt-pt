---
title: Diagnosticar e resolver problemas num ambiente da Gen2 - Azure Time Series Insights Microsoft Docs
description: Aprenda a diagnosticar e resolver problemas num ambiente Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: eca2009ee0470dec9c9ce60d8754f8f7a71619d3
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740543"
---
# <a name="diagnose-and-troubleshoot-an-azure-time-series-insights-gen2-environment"></a>Diagnosticar e resolver problemas num ambiente Azure Time Series Insights Gen2

Este artigo resume vários problemas comuns que poderá encontrar quando trabalhar com o seu ambiente Azure Time Series Insights Gen2. O artigo também descreve potenciais causas e soluções para cada problema.

## <a name="problem-i-cant-find-my-environment-in-the-gen2-explorer"></a>Problema: não consigo encontrar o meu ambiente no Explorador da Gen2

Este problema pode ocorrer se não tiver permissões para aceder ao ambiente de Insights de Séries Temporais. Os utilizadores precisam de uma função de acesso ao nível do leitor para visualizar o seu ambiente de Insights de Séries Tempo. Para verificar os níveis de acesso atuais e conceder acesso adicional, aceda à secção Políticas de Acesso a **Dados** sobre o recurso Time Series Insights no [portal Azure](https://portal.azure.com/).

  [![Verifique as políticas de acesso a dados.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-gen2-explorer"></a>Problema: Nenhum dado é visto no Explorador gen2

Existem várias razões comuns para que os seus dados não apareçam no [Azure Time Series Insights Gen2 Explorer](https://insights.timeseries.azure.com/preview).

- A fonte do seu evento pode não estar a receber dados.

    Verifique se a sua fonte de eventos, que é um centro de eventos ou um hub IoT, está a receber dados das suas etiquetas ou instâncias. Para verificar, aceda à página geral do seu recurso no portal Azure.

    [![Reveja a visão geral das métricas do dashboard.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- Os dados da fonte do seu evento não estão no formato JSON.

    A Time Series Insights suporta apenas dados JSON. Para as amostras JSON, leia [as formas JSON suportadas.](./concepts-json-flattening-escaping-rules.md)

- A chave de origem do evento está a perder uma permissão necessária.

  - Para um hub IoT, você precisa fornecer a chave que tem permissão **de ligação de serviço.**

    [![Verifique as permissões do hub IoT.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    - Tanto as políticas **como** o **serviço** funcionam porque têm permissão **de ligação de serviço.**

  - Para um centro de eventos, você precisa fornecer a chave que tem **Permissão de Escuta.**
  
    [![Reveja as permissões do centro do evento.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    - Ambas as políticas **de Leitura** e **Gestão** funcionam porque têm permissão **de escuta.**

- O seu grupo de consumidores fornecido não é exclusivo da Time Series Insights.

    Durante o registo de um hub ioT ou centro de eventos, você especifica o grupo de consumidores que é usado para ler os dados. Este grupo de consumidores deve ser único por ambiente. Se o grupo de consumidores for partilhado, o centro de eventos subjacente desliga automaticamente um dos leitores aleatoriamente. Forneça um grupo de consumidores único para a Time Series Insights ler.

- A sua propriedade de ID séries de tempo especificada no momento do provisionamento está incorreta, em falta ou nula.

    Este problema pode ocorrer se a propriedade de ID da Série De Tempo estiver configurada incorretamente no momento de provisão do ambiente. Para mais informações, leia [as melhores práticas para escolher um ID da Série De Tempo.](./how-to-select-tsid.md) Neste momento, não é possível atualizar um ambiente de Insights de Séries de Tempo existente para utilizar um ID de Série sonora diferente.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problema: Alguns dados mostram, mas alguns faltam

Pode estar a enviar dados sem o ID da Série DeTemporal.

- Este problema pode ocorrer quando envia eventos sem o campo de ID da Série Detemporal na carga útil. Para mais informações, leia [as formas JSON suportadas.](./concepts-json-flattening-escaping-rules.md)
- Este problema pode ocorrer porque o seu ambiente está a ser estrangulado.

    > [!NOTE]
    > Neste momento, a Time Series Insights suporta uma taxa máxima de ingestão de 6 Mbps.

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>Problema: Os dados estavam a aparecer, mas agora a ingestão parou

- A sua chave de origem de eventos pode ter sido regenerada e o seu ambiente Gen2 precisa da nova chave de origem do evento.

Este problema ocorre quando a chave fornecida ao criar a sua fonte de evento já não é válida. Verias a telemetria no teu hub, mas nenhuma Ingress Recebeu Mensagens em Insights de Séries Tempotárias. Se não tiver a certeza se a chave foi ou não regenerada, pode pesquisar o seu registo de atividades do Event Hubs para "Criar ou atualizar regras de autorização do espaço de nome" ou pesquisar "Criar ou atualizar o Recurso IotHub" para o hub IoT.

Para atualizar o ambiente da Time Series Insights Gen2 com a nova chave abra o seu recurso hub no portal Azure e copie a nova chave. Navegue para o seu recurso TSI e clique em Fontes de Evento.

   [![O screenshot mostra o recurso T S I com o item do menu 'Sources' chamado.](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

Selecione as fontes de evento(s) que têm de onde a ingestão parou, cole na nova tecla e clique em Guardar.

   [![A screenshot mostra T S I recurso com i o T hub chave política introduzido.](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problema: O nome da propriedade da minha fonte de eventos Timestamp não funciona

Certifique-se de que o nome e o valor estão em conformidade com as seguintes regras:

- O nome da propriedade timestamp é sensível a maiíssimos.
- O valor da propriedade Timestamp que vem da fonte do seu evento como uma corda JSON tem o formato `yyyy-MM-ddTHH:mm:ss.FFFFFFFK` . Um exemplo de tal corda `"2008-04-12T12:53Z"` é.

A maneira mais fácil de garantir que o nome da sua propriedade Timestamp é capturado e funcionando corretamente é usar o Time Series Insights Gen2 Explorer. Dentro do Time Series Insights Gen2 Explorer, utilize o gráfico para selecionar um período de tempo depois de fornecer o nome da propriedade Timestamp. Clique com o direito na seleção e selecione a opção **de eventos de exploração.** O primeiro cabeçalho da coluna é o nome da propriedade do Timestamp. Deve ter `($ts)` ao lado da palavra , em vez `Timestamp` de:

- `(abc)`, o que indica que o Time Series Insights lê os valores de dados como cordas.
- O ícone **do calendário,** que indica que o Time Series Insights lê o valor dos dados como data.
- `#`, o que indica que o Time Series Insights lê os valores dos dados como um inteiro.

Se a propriedade Timestamp não for explicitamente especificada, o hub IoT ou o centro de eventos enqueued Time de um evento é usado como o carimbo de tempo padrão.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Problema: não consigo ver os dados da minha loja quente no Explorer

- Pode ter a sua loja quente recentemente, e os dados ainda estão a fluir.
- Pode ter apagado a sua loja quente, caso em que teria perdido dados.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problema: Não consigo ver ou editar o meu modelo de séries de tempo

- Você pode estar a aceder a um ambiente De Insights S1 ou S2 da Série Tempo.

   Os Modelos de Séries de Tempo são suportados apenas em ambientes pay-as-you-go. Para obter mais informações sobre como aceder ao seu ambiente S1 ou S2 a partir do Time Series Insights Gen2 Explorer, leia [os dados de Visualização no Explorer](./concepts-ux-panels.md).

   [![Não há eventos no ambiente.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Pode não ter permissões para visualizar e editar o modelo.

   Os utilizadores precisam de acesso ao nível do contribuinte para editar e ver o seu Modelo série de tempo. Para verificar os níveis de acesso atuais e conceder acesso adicional, aceda à secção Políticas de Acesso a **Dados** no seu recurso Time Series Insights no portal Azure.

## <a name="problem-all-my-instances-in-the-gen2-explorer-lack-a-parent"></a>Problema: Todos os meus casos no Explorador da Gen2 carecem de um pai

Este problema pode ocorrer se o seu ambiente não tiver uma hierarquia do Modelo série de tempo definida. Para mais informações, leia sobre como [trabalhar com modelos de séries temporais.](./time-series-insights-overview.md)

  [![Casos não-parados apresentarão um aviso.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

- Leia sobre como [trabalhar com modelos de séries de tempo.](./time-series-insights-overview.md)

- Conheça as [formas JSON suportadas.](./concepts-json-flattening-escaping-rules.md)

- Rever [o planeamento e os limites](./how-to-plan-your-environment.md) em Azure Time Series Insights Gen2.