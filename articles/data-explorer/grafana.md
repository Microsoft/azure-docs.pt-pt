---
title: Visualizar dados do Azure Data Explorer usando o Grafana
description: Neste "como", você aprenderá a configurar o Azure Data Explorer como uma fonte de dados para Grafana e, em seguida, Visualizar dados de um cluster de exemplo.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 6/30/2019
ms.openlocfilehash: f1eb9fb0d81d1e9cdf3dd8628a6d7ad1f0ccce92
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581861"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Visualizar dados do Azure Data Explorer no Grafana

O Grafana é uma plataforma de análise que permite consultar e Visualizar dados e, em seguida, criar e compartilhar dashboards com base em suas visualizações. O Grafana fornece um *plug-in*do data Explorer do Azure, que permite que você se conecte e visualize dados do data Explorer do Azure. Neste artigo, você aprende a configurar o Azure Data Explorer como uma fonte de dados para Grafana e, em seguida, Visualizar dados de um cluster de exemplo.

Usando o vídeo a seguir, você pode aprender a usar o plug-in do Azure Data Explorer do Grafana, configurar o Data Explorer do Azure como uma fonte de dados para Grafana e, em seguida, Visualizar dados. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Como alternativa, você pode [Configurar a fonte de dados](#configure-the-data-source) e [Visualizar os dados](#visualize-data) conforme detalhado no artigo abaixo.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do seguinte para concluir este procedimento:

* [Grafana versão 5.3.0 ou posterior](https://docs.grafana.org/installation/) para seu sistema operacional

* O [plug-in do data Explorer do Azure](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) para Grafana

* Um cluster que inclui os dados de exemplo StormEvents. Para obter mais informações, consulte [início rápido: criar um cluster de data Explorer do Azure e um banco](create-cluster-database-portal.md) de [dados e ingerir exemplos de dado no Azure data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Especificar propriedades e testar a conexão

Com a entidade de serviço atribuída à função de *visualizadores* , agora você especifica as propriedades em sua instância do Grafana e testa a conexão com o Azure data Explorer.

1. No Grafana, no menu à esquerda, selecione o ícone de engrenagem e, em seguida, **fontes de dados**.

    ![Origens de dados](media/grafana/data-sources.png)

1. Selecione **Adicionar fonte de dados**.

1. Na página **fontes de dados/nova** , insira um nome para a fonte de dados e selecione o tipo **Azure data Explorer DataSource**.

    ![Nome e tipo da conexão](media/grafana/connection-name-type.png)

1. Insira o nome do cluster no formato https://{ClusterName}. {Region}. Kusto. Windows. net. Insira os outros valores do portal do Azure ou da CLI. Consulte a tabela abaixo da imagem a seguir para obter um mapeamento.

    ![Propriedades de ligação](media/grafana/connection-properties.png)

    | Interface do usuário do amGrafana | Portal do Azure | CLI do Azure |
    | --- | --- | --- |
    | ID da assinatura | ID DA ASSINATURA | SubscriptionId |
    | ID do locatário | ID do diretório | vários |
    | ID do cliente | ID da aplicação | appId |
    | Segredo do cliente | Palavra-passe | palavra-passe |
    | | | |

1. Selecione **salvar & teste**.

    Se o teste for bem-sucedido, vá para a próxima seção. Se você encontrar problemas, verifique os valores especificados em Grafana e examine as etapas anteriores.

## <a name="visualize-data"></a>Visualizar os dados

Agora que você concluiu a configuração do Azure Data Explorer como uma fonte de dados para Grafana, é hora de visualizar os dados. Mostraremos um exemplo básico aqui, mas há muito mais que você pode fazer. Recomendamos examinar as [consultas de gravação do Azure data Explorer](write-queries.md) para obter exemplos de outras consultas a serem executadas no conjunto de dados de exemplo.

1. No Grafana, no menu à esquerda, selecione o ícone de adição e, em seguida, **painel**.

    ![Criar painel](media/grafana/create-dashboard.png)

1. Na guia **Adicionar** , selecione **grafo**.

    ![Adicionar grafo](media/grafana/add-graph.png)

1. No painel gráfico, selecione **título do painel** e **Editar**.

    ![Painel de edição](media/grafana/edit-panel.png)

1. Na parte inferior do painel, selecione **fonte de dados** e selecione a fonte de dados que você configurou.

    ![Selecionar origem de dados](media/grafana/select-data-source.png)

1. No painel de consulta, copie na consulta a seguir e selecione **executar**. A consulta classifica a contagem de eventos por dia para o conjunto de dados de exemplo.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Executar consulta](media/grafana/run-query.png)

1. O grafo não mostra nenhum resultado porque ele tem o escopo definido por padrão para dados das últimas seis horas. No menu superior, selecione **últimas 6 horas**.

    ![Últimas seis horas](media/grafana/last-six-hours.png)

1. Especifique um intervalo personalizado que cubra 2007, o ano incluído em nosso conjunto de dados de exemplo StormEvents. Selecione **Aplicar**.

    ![Intervalo de datas personalizado](media/grafana/custom-date-range.png)

    Agora, o grafo mostra os dados de 2007, bucketdos por dia.

    ![Grafo concluído](media/grafana/finished-graph.png)

1. No menu superior, selecione o ícone salvar: ![Ícone salvar](media/grafana/save-icon.png).

## <a name="next-steps"></a>Passos seguintes

* [Escrever consultas do Azure Data Explorer](write-queries.md)

* [Tutorial: Visualizar dados do Azure Data Explorer no Power BI](visualize-power-bi.md)
