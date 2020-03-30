---
title: Visualizar dados do Azure Data Explorer usando grafana
description: Neste artigo, aprende-se a configurar o Azure Data Explorer como fonte de dados para grafana e, em seguida, visualizar dados de um cluster de amostras.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: a1c52007ea86ca0812c4a73a92ce81db6ddadc7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037975"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Visualizar dados do Azure Data Explorer em Grafana

Grafana é uma plataforma de análise que lhe permite consultar e visualizar dados, em seguida, criar e partilhar dashboards com base nas suas visualizações. Grafana fornece um *plugin*Azure Data Explorer, que lhe permite ligar e visualizar dados do Azure Data Explorer. Neste artigo, aprende-se a configurar o Azure Data Explorer como fonte de dados para grafana e, em seguida, visualizar dados de um cluster de amostras.

Utilize o seguinte vídeo, para aprender a usar o plugin Azure Data Explorer da Grafana, configurar o Azure Data Explorer como fonte de dados para grafana e, em seguida, visualizar dados. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Em alternativa, pode [configurar a fonte de dados](#configure-the-data-source) e [visualizar dados](#visualize-data) conforme detalhado no artigo abaixo.

## <a name="prerequisites"></a>Pré-requisitos

Precisa do seguinte para completar este artigo:

* [Grafana versão 5.3.0 ou mais tarde](https://docs.grafana.org/installation/) para o seu sistema operativo

* O [plugin Azure Data Explorer](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) para Grafana

* Um cluster que inclui os dados da amostra StormEvents. Para mais informações, consulte [Quickstart: Crie um cluster e base de dados do Azure Data Explorer e](create-cluster-database-portal.md) [os dados da amostra ingest no Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Especificar propriedades e testar a ligação

Com o principal de serviço atribuído à função *de espectadores,* agora especifica propriedades no seu caso de Grafana, e testa a ligação ao Azure Data Explorer.

1. Em Grafana, no menu esquerdo, selecione o ícone da engrenagem e depois **fontes**de dados .

    ![Origens de dados](media/grafana/data-sources.png)

1. Selecione **Adicionar fonte de dados**.

1. Na página **Fontes** de Dados / Nova página, introduza um nome para a fonte de dados e, em seguida, selecione o tipo **Azure Datasource**do Explorador de Dados .

    ![Nome e tipo de ligação](media/grafana/connection-name-type.png)

1. Introduza o nome do seu cluster no formulário https://{ClusterName}. {Região}.kusto.windows.net. Introduza os outros valores do portal Azure ou CLI. Consulte a tabela abaixo da seguinte imagem para um mapeamento.

    ![Propriedades de ligação](media/grafana/connection-properties.png)

    | Grafana UI | Portal do Azure | CLI do Azure |
    | --- | --- | --- |
    | ID da Subscrição | ID DA SUBSCRIÇÃO | SubscriptionId |
    | Id inquilino | ID do diretório | inquilino |
    | Id do cliente | ID da aplicação | appId |
    | Segredo do cliente | Palavra-passe | palavra-passe |
    | | | |

1. Selecione **Guardar & Teste**.

    Se o teste for bem sucedido, vá para a próxima secção. Se encontrar algum problema, verifique os valores especificados em Grafana e reveja os passos anteriores.

## <a name="visualize-data"></a>Visualizar os dados

Agora que terminou de configurar o Azure Data Explorer como fonte de dados para grafana, é hora de visualizar dados. Vamos dar um exemplo básico aqui, mas há muito mais que pode fazer. Recomendamos que se analise [as consultas de Escrita para o Azure Data Explorer,](write-queries.md) por exemplo, outras consultas para correr contra o conjunto de dados da amostra.

1. Em Grafana, no menu esquerdo, selecione o ícone plus em seguida **Dashboard**.

    ![Criar painel de instrumentos](media/grafana/create-dashboard.png)

1. Sob o separador **Adicionar,** selecione **Graph**.

    ![Adicionar gráfico](media/grafana/add-graph.png)

1. No painel de gráficos, selecione **Título do Painel** **e,** em seguida, editar .

    ![Painel de edição](media/grafana/edit-panel.png)

1. Na parte inferior do painel, selecione **Data Source** e, em seguida, selecione a fonte de dados que configura.

    ![Selecionar origem de dados](media/grafana/select-data-source.png)

1. No painel de consulta, copie na seguinte consulta e, em seguida, selecione **Executar**. A consulta baldea a contagem de eventos por dia para o conjunto de dados da amostra.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Executar consulta](media/grafana/run-query.png)

1. O gráfico não mostra resultados porque é examinado por padrão aos dados das últimas seis horas. No menu superior, selecione **Last 6 horas**.

    ![Últimas seis horas](media/grafana/last-six-hours.png)

1. Especifique uma gama personalizada que cubra 2007, o ano incluído no nosso conjunto de dados de amostrastormEvents. Selecione **Aplicar**.

    ![Gama de datas personalizada](media/grafana/custom-date-range.png)

    Agora o gráfico mostra os dados de 2007, baldeados de dia.

    ![Gráfico acabado](media/grafana/finished-graph.png)

1. No menu superior, selecione o ícone de guardar: ![Salvar o ícone](media/grafana/save-icon.png).

## <a name="create-alerts"></a>Criar Alertas

1. No Home Dashboard, selecione**canais** de **notificação de** > alerta para criar um novo canal de notificação

    ![criar canal de notificação](media/grafana/create-notification-channel.png)

1. Crie um novo **canal de Notificação,** em **seguida, guardar**.

    ![Criar novo canal de notificação](media/grafana/new-notification-channel-adx.png)

1. No **Dashboard**, **selecione Editar** a partir da queda.

    ![selecionar editar no painel de instrumentos](media/grafana/edit-panel-4-alert.png)

1. Selecione o ícone do sino de alerta para abrir o painel **alerta.** Selecione **Criar Alerta**. Complete as seguintes propriedades no painel **alerta.**

    ![propriedades de alerta](media/grafana/alert-properties.png)

1. Selecione o ícone **do painel de dados Save** para guardar as suas alterações.

## <a name="next-steps"></a>Passos seguintes

* [Escrever consultas do Azure Data Explorer](write-queries.md)
