---
title: Diagnosticar e solucionar problemas de visualização de Azure Time Series Insights | Microsoft Docs
description: Entenda como diagnosticar e solucionar problemas com a versão prévia do Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: cdcbe62fdba4f111233451680f95abc757e80ee3
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883325"
---
# <a name="diagnose-and-troubleshoot"></a>Diagnosticar e resolver problemas

Este artigo resume vários problemas comuns que você pode encontrar ao trabalhar com seu ambiente de Azure Time Series Insights visualização. O artigo também descreve as possíveis causas e soluções para cada problema.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>Persisti Não consigo encontrar meu ambiente no Gerenciador de visualização

Esse problema pode ocorrer se você não tiver permissões para acessar o ambiente de Time Series Insights. Os usuários precisam de uma função de acesso de nível de leitor para exibir seu ambiente de Time Series Insights. Para verificar os níveis de acesso atuais e conceder acesso adicional, visite a seção políticas de acesso a dados no recurso de Time Series Insights no [portal do Azure](https://portal.azure.com/).

  [![Ambiente](media/v2-update-diagnose-and-troubleshoot/environment.png)](media/v2-update-diagnose-and-troubleshoot/environment.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>Problema: nenhum dado é visto no Gerenciador de visualização

Há vários motivos comuns pelos quais você pode não ver seus dados no [Azure Time Series insights Gerenciador de visualização](https://insights.timeseries.azure.com/preview).

- A origem do evento pode não estar recebendo dados.

    Verifique se a origem do evento, que é um hub de eventos ou um hub IoT, está recebendo dados de suas marcas ou instâncias. Para verificar, vá para a página Visão geral do recurso na portal do Azure.

    [![Painel-informações](media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png)](media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png#lightbox)

- Os dados de origem do evento não estão no formato JSON.

    Time Series Insights dá suporte apenas a dados JSON. Para exemplos de JSON, veja [formas JSON suportadas](./how-to-shape-query-json.md).

- A chave de origem do evento não tem uma permissão necessária.

  * Para um hub IoT, você precisa fornecer a chave que tem a permissão de **conexão de serviço** .

    [![Configuração (Configuration)](media/v2-update-diagnose-and-troubleshoot/configuration.png)](media/v2-update-diagnose-and-troubleshoot/configuration.png#lightbox)

  * Conforme mostrado na imagem anterior, ambas as políticas **iothubowner** e **serviço** funcionam porque têm permissão de **conexão de serviço** .
  * Para um hub de eventos, você precisa fornecer a chave que tem a permissão **escutar** .
  
    [![Permissões](media/v2-update-diagnose-and-troubleshoot/permissions.png)](media/v2-update-diagnose-and-troubleshoot/permissions.png#lightbox)

  * Conforme mostrado na imagem anterior, as políticas de **leitura** e de **Gerenciamento** funcionam porque têm permissão de **escuta** .

- Seu grupo de consumidores fornecido não é exclusivo para Time Series Insights.

    Durante o registro de um hub IoT ou Hub de eventos, você especifica o grupo de consumidores que é usado para ler os dados. Não compartilhe esse grupo de consumidores. Se o grupo de consumidores for compartilhado, o Hub de eventos subjacente desconectará automaticamente um dos leitores aleatoriamente. Forneça um grupo de consumidores exclusivo para Time Series Insights ler.

- A propriedade de ID da série temporal especificada no momento do provisionamento está incorreta, ausente ou nula.

    Esse problema pode ocorrer se a propriedade ID da série temporal estiver configurada incorretamente no momento do provisionamento do ambiente. Para obter mais informações, consulte [práticas recomendadas para escolher uma ID de série temporal](./time-series-insights-update-how-to-id.md). Neste momento, você não pode atualizar um ambiente de Time Series Insights existente para usar uma ID de série temporal diferente.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problema: alguns dados são mostrados, mas alguns estão ausentes

Você pode estar enviando dados sem a ID da série temporal.

- Esse problema pode ocorrer quando você envia eventos sem o campo ID da série temporal na carga. Para obter mais informações, consulte [formas de JSON com suporte](./how-to-shape-query-json.md).

- Esse problema pode ocorrer porque seu ambiente está sendo limitado.

    > [!NOTE]
    > Neste momento, Time Series Insights dá suporte a uma taxa máxima de ingestão de 6 Mbps.

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problema: o nome da propriedade de carimbo de data/hora da origem do evento não funciona

Certifique-se de que o nome e o valor está em conformidade com as seguintes regras:

* O nome da propriedade Timestamp diferencia maiúsculas de minúsculas.
* O valor da propriedade Timestamp que vem da origem do evento, como uma cadeia de caracteres JSON, `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`tem o formato. Um exemplo de uma cadeia de caracteres é `“2008-04-12T12:53Z”`.

A maneira mais fácil de garantir que o nome da propriedade de carimbo de data/hora seja capturado e funcionando corretamente é usar o Gerenciador de visualização Time Series Insights. Dentro do Time Series Insights Gerenciador de visualização, use o gráfico para selecionar um período de tempo depois de fornecer o nome da propriedade de carimbo de data/hora. Clique com o botão direito do mouse na seleção e selecione a opção **explorar eventos** . O primeiro cabeçalho de coluna é o nome da propriedade de carimbo de data/hora. Ele deve ter `($ts)` ao lado da palavra `Timestamp`, em vez de:

* `(abc)`, que indica que Time Series Insights lê os valores de dados como cadeias de caracteres.
* Ícone de calendário, que indica que Time Series Insights lê o valor de dados como DateTime.
* `#`, que indica que Time Series Insights lê os valores de dados como um inteiro.

Se a propriedade timestamp não for especificada explicitamente, o Hub IoT do evento ou o tempo de enfileiramento do hub de eventos será usado como carimbo de data/hora padrão.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Persisti Não consigo exibir ou editar meu modelo de série temporal

- Você pode estar acessando um ambiente Time Series Insights S1 ou S2.

   Os modelos de série temporal têm suporte apenas em ambientes PAYG. Para obter mais informações sobre como acessar o seu ambiente de S1/S2 partir do Explorador de pré-visualização do Time Series Insights, veja [visualizar dados no explorer](./time-series-insights-update-explorer.md).

   [![Access](media/v2-update-diagnose-and-troubleshoot/access.png)](media/v2-update-diagnose-and-troubleshoot/access.png#lightbox)

- Talvez você não tenha permissões para exibir e editar o modelo.

   Os usuários precisam de acesso no nível de colaborador para editar e exibir seu modelo de série temporal. Para verificar os níveis de acesso atuais e conceder acesso adicional, visite a seção políticas de acesso a dados em seu recurso de Time Series Insights no portal do Azure.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>Problema: todas as minhas instâncias no Gerenciador de visualização não têm um pai

Esse problema pode ocorrer se o seu ambiente não tiver uma hierarquia de modelo de série temporal definida. Para obter mais informações, consulte [trabalhar com modelos de série temporal](./time-series-insights-update-how-to-tsm.md).

  [![Modelos de série temporal](media/v2-update-diagnose-and-troubleshoot/tsm.png)](media/v2-update-diagnose-and-troubleshoot/tsm.png#lightbox)

## <a name="next-steps"></a>Passos Seguintes

- Leia [trabalhar com modelos de série temporal](./time-series-insights-update-how-to-tsm.md).

- Saiba mais sobre [as formas JSON com suporte](./how-to-shape-query-json.md).
