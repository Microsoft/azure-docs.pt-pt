---
title: Diagnosticar e resolver problemas relacionados com a pré-visualização do Azure Time Series Insights | Documentos da Microsoft
description: Saiba como diagnosticar e resolver problemas com a pré-visualização do Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: d18f8f4808d5ca9dd7eca1561b1ee08c2073f5fa
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237547"
---
# <a name="diagnose-and-troubleshoot"></a>Diagnosticar e resolver problemas

Este artigo resume vários problemas comuns que poderá encontrar quando trabalha com o seu ambiente de pré-visualização do Azure Time Series Insights. O artigo também descreve possíveis causas e soluções para cada problema.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>Problema: Não consigo encontrar meu ambiente no Gerenciador de pré-visualização

Esse problema pode ocorrer se não tiver permissões para aceder ao ambiente do Time Series Insights. Os utilizadores têm uma função de acesso de leitor de nível para ver o seu ambiente do Time Series Insights. Para verificar os atuais níveis de acesso e conceder acesso adicional, visite a secção de políticas de acesso de dados do recurso de Time Series Insights na [portal do Azure](https://portal.azure.com/).

  [![Environment](media/v2-update-diagnose-and-troubleshoot/environment.png)](media/v2-update-diagnose-and-troubleshoot/environment.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>Problema: não existem dados são vistos no Gerenciador de pré-visualização

Há vários motivos comuns, por que não poderá ver os dados no [Explorador de pré-visualização do Azure Time Series Insights](https://insights.timeseries.azure.com/preview).

- A origem do evento poderá não estar a receber dados.

    Certifique-se de que a origem do evento, que é um hub de eventos ou de um hub IoT, está a receber dados da sua etiquetas ou instâncias. Para verificar, vá para a página de descrição geral do seu recurso no portal do Azure.

    [![informações de dashboard](media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png)](media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png#lightbox)

- Os dados de origem do evento não estão no formato JSON.

    O Time Series Insights suporta apenas os dados JSON. Para exemplos de JSON, veja [formas JSON suportadas](./how-to-shape-query-json.md).

- A chave de origem do evento em falta uma permissão necessária.

  * Para um hub IoT, tem de fornecer a chave que tenha **ligação de serviço** permissão.

    [![Configuração (Configuration)](media/v2-update-diagnose-and-troubleshoot/configuration.png)](media/v2-update-diagnose-and-troubleshoot/configuration.png#lightbox)

  * Conforme mostrado na imagem anterior, ambas as políticas **iothubowner** e **service** funcionam porque têm **serviço ligar** permissão.
  * Para um hub de eventos, tem de fornecer a chave que tenha **escutar** permissão.
  
    [![Permissões](media/v2-update-diagnose-and-troubleshoot/permissions.png)](media/v2-update-diagnose-and-troubleshoot/permissions.png#lightbox)

  * Conforme mostrado na imagem anterior, ambos os **ler** e **gerir** políticas funcionam porque têm **escutar** permissão.

- O grupo de consumidores fornecido não é exclusivo para o Time Series Insights.

    Durante o registo de um IoT hub ou hub de eventos, especifique o grupo de consumidores que é utilizado para ler os dados. Não partilhe nesse grupo de consumidores. Se o grupo de consumidores for partilhado, o hub de eventos subjacente automaticamente desliga um dos leitores aleatoriamente. Forneça um grupo de consumidores exclusivo para ler a partir do Time Series Insights.

- Sua propriedade de ID de série de tempo especificada no momento do provisionamento está incorreto, em falta ou é nulo.

    Esse problema pode ocorrer se a propriedade de ID de série de tempo não está configurada corretamente ao tempo de aprovisionamento do ambiente. Para obter mais informações, consulte [melhores práticas para a escolha de um ID de série de tempo](./time-series-insights-update-how-to-id.md). Neste momento, não é possível atualizar um ambiente de Time Series Insights existente para utilizar um ID de série de tempo diferente

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problema: alguns dados mostra, mas alguns está em falta

Pode estar a enviar dados sem a série de tempo de ID.

- Esse problema pode ocorrer quando os eventos são enviados sem o campo de ID de série de tempo no payload. Para obter mais informações, consulte [formas JSON suportadas](./how-to-shape-query-json.md).

- Esse problema pode ocorrer porque o seu ambiente está a ser limitado.

    > [!NOTE]
    > Neste momento, o Time Series Insights suporta uma taxa máxima de ingestão de 6 Mbps.

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problema: o nome da propriedade Timestamp minha origem de evento não funciona

Certifique-se de que o nome e o valor está em conformidade com as seguintes regras:

* O nome da propriedade Timestamp diferencia maiúsculas de minúsculas.
* O valor da propriedade Timestamp que vem da origem de evento, como uma cadeia de caracteres do JSON, tem o formato `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Um exemplo de uma cadeia de caracteres é `“2008-04-12T12:53Z”`.

A maneira mais fácil para garantir que seu nome de propriedade Timestamp é capturado e a funcionar corretamente é utilizar o Explorador do Time Series Insights pré-visualização. No Explorador do Time Series Insights pré-visualização, utilize o gráfico para selecionar um período de tempo depois que forneceu o nome da propriedade Timestamp. A seleção com o botão direito e selecione o **explorar eventos** opção. O cabeçalho de coluna primeiro é o nome da propriedade Timestamp. Deve ter `($ts)` junto a palavra `Timestamp`, em vez:

* `(abc)`, que indica que o Time Series Insights lê os valores de dados como cadeias de caracteres.
* Ícone de calendário, que indica que o Time Series Insights lê o valor de dados como datetime.
* `#`, que indica que o Time Series Insights lê os valores de dados como um número inteiro.

Se a propriedade Timestamp explicitamente não for especificada, o IoT hub ou hub de eventos colocados em fila de tempo de um evento é utilizado como o carimbo de hora padrão.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problema: Não consigo ver ou editar o meu modelo de série de tempo

- Que pode ser aceder a um ambiente de S2 ou Time Series Insights S1.

   Modelos de série de tempo são suportados apenas em ambientes de PAYG. Para obter mais informações sobre como acessar o seu ambiente de S1/S2 partir do Explorador de pré-visualização do Time Series Insights, veja [visualizar dados no explorer](./time-series-insights-update-explorer.md).

   [![Access](media/v2-update-diagnose-and-troubleshoot/access.png)](media/v2-update-diagnose-and-troubleshoot/access.png#lightbox)

- Poderá não ter permissões para ver e editar o modelo.

   Os utilizadores têm acesso no nível de Contribuidor para editar e ver o seu modelo de série de tempo. Para verificar os atuais níveis de acesso e conceder acesso adicional, visite a secção de políticas de acesso de dados no seu recurso do Time Series Insights no portal do Azure.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>Problema: um elemento principal não têm todas as minhas instâncias no Gerenciador de pré-visualização

Esse problema pode ocorrer se o seu ambiente não tiver uma hierarquia de modelo de série de tempo definida. Para obter mais informações, consulte [trabalhar com modelos de série de tempo](./time-series-insights-update-how-to-tsm.md).

  [![Modelos de série de tempo](media/v2-update-diagnose-and-troubleshoot/tsm.png)](media/v2-update-diagnose-and-troubleshoot/tsm.png#lightbox)

## <a name="next-steps"></a>Passos Seguintes

- Leia [trabalhar com modelos de série de tempo](./time-series-insights-update-how-to-tsm.md).

- Saiba mais sobre [suportado formas JSON](./how-to-shape-query-json.md).
