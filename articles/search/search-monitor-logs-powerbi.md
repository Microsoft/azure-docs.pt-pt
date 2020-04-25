---
title: Visualizar registos e métricas de pesquisa cognitiva azure com power bi
description: Visualizar registos e métricas de pesquisa cognitiva azure com power bi
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: e01ac332e61f51909ff1617f1716cd719b67c319
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82127873"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Visualizar registos e métricas de pesquisa cognitiva azure com power bi
[A Pesquisa Cognitiva Azure](https://docs.microsoft.com/azure/search/search-what-is-azure-search) permite-lhe armazenar registos de operação e métricas de serviço sobre o seu serviço de pesquisa numa conta de Armazenamento Azure. Esta página fornece instruções para como visualizar essa informação através de uma aplicação de modelo de Power BI. A aplicação fornece informações detalhadas sobre o seu serviço de pesquisa, incluindo informações sobre métricas de Pesquisa, Indexação, Operações e Serviço.

Você pode encontrar a app de modelo de power bi **azure pesquisa cognitiva: analisar registos e métricas** no mercado de [aplicações power BI](https://appsource.microsoft.com/marketplace/apps).

## <a name="how-to-get-started-with-the-app"></a>Como começar com a app

1. Ativar o registo de recursos e métricas para o seu serviço de pesquisa:

    1. Crie ou identifique uma conta de [Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) existente onde possa arquivar os registos
    1. Navegue para o seu serviço de Pesquisa Cognitiva Azure no portal Azure
    1. Na secção de monitorização da coluna esquerda, selecione **definições** de diagnóstico

        ![](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. Selecione **+ Adicione a definição de diagnóstico**
    1. Consulte o Arquivo numa Conta de **Armazenamento,** forneça as informações da sua conta de Armazenamento e verifique **os OperationLogs** e **AllMetrics**

        ![](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. Selecione **Guardar**

1. Depois de ativada a exploração madeireira, utilize o seu serviço de pesquisa para começar a gerar registos e métricas. Demora até uma hora até que os recipientes apareçam no armazenamento blob com estes troncos. Você verá um recipiente de **insights-logs-operationlogs** para registos de tráfego de pesquisa e um recipiente **insights-metrics-pt1m** para métricas.

1. Encontre a App Azure Cognitive Search Power BI no mercado de [Aplicações Power BI](https://appsource.microsoft.com/marketplace/apps) e instale-a num novo espaço de trabalho ou num espaço de trabalho existente. A aplicação chama-se **Pesquisa Cognitiva Azure: Analise Registos e Métricas.**

1. Depois de instalar a aplicação, selecione a aplicação da sua lista de aplicações no Power BI.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. Selecione **Ligar** para ligar os seus dados

    ![](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. Insera o nome da conta de armazenamento que contém os seus registos e métricas. Por padrão, a aplicação analisará os últimos 10 dias de dados, mas este valor pode ser alterado com o parâmetro **Days.**

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. Selecione **A Chave** como método de autenticação e forneça a sua chave de conta de armazenamento. Selecione **Private** como o nível de privacidade. Clique em Iniciar Sessão para iniciar o processo de carregamento.

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. Aguarde que os dados se atualização. Isto pode levar algum tempo dependendo da quantidade de dados que tem. Pode ver se os dados ainda estão a ser atualizados com base no indicador abaixo.

    ![](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. Uma vez concluída a atualização de dados, selecione Relatório de **Pesquisa Cognitiva Azure** para visualizar o relatório.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. Certifique-se de que refresca a página depois de abrir o relatório para que se abra com os seus dados.

    ![](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>Como alterar os parâmetros da aplicação
Se quiser visualizar dados de uma conta de armazenamento diferente ou alterar o número de dias de dados para consulta, siga os passos abaixo para alterar os parâmetros **Dias** e **ArmazenamentoAccount.**

1. Navegue para as suas aplicações Power BI, encontre a sua aplicação Azure Cognitive Search e selecione o botão de **aplicação Edit** para ver o espaço de trabalho.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. Selecione **Definições** a partir das opções dataset.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. Enquanto no separador Datasets, altere os valores do parâmetro e selecione **Aplicar**. Se houver algum problema com a ligação, atualize as credenciais de origem de dados na mesma página.

1. Navegue de volta ao espaço de trabalho e selecione **Refresh agora** a partir das opções dataset.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. Abra o relatório para ver os dados atualizados. Pode também ter de atualizar o relatório para ver os dados mais recentes.

## <a name="troubleshooting"></a>Resolução de problemas
Se descobrir que não consegue ver os seus dados seguir estes passos de resolução de problemas:

1. Abra o relatório e refresque a página para se certificar de que está a ver os dados mais recentes. Há uma opção no relatório para refrescar os dados. Selecione isto para obter os dados mais recentes.

1. Certifique-se de que o nome da conta de armazenamento e a chave de acesso que forneceu estão corretos. O nome da conta de armazenamento deve corresponder à conta configurada com os registos do seu serviço de pesquisa.

1. Confirme que a sua conta de armazenamento contém os recipientes **insights-logs-operationlogs** e **insights-metrics-pt1m** e cada recipiente tem dados. Os registos e métricas estarão dentro de algumas camadas de pastas.

1. Verifique se o conjunto de dados ainda é refrescante. O indicador de estado de atualização é indicado no passo 8 acima. Se ainda for refrescante, aguarde até que a atualização esteja completa para abrir e refrescar o relatório.

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre a Pesquisa Cognitiva Azure](https://docs.microsoft.com/azure/search/)

[O que é o Power BI?](https://docs.microsoft.com/power-bi/fundamentals/power-bi-overview)

[Conceitos básicos para designers no serviço Power BI](https://docs.microsoft.com/power-bi/service-basic-concepts)