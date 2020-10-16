---
title: Visualizar registos e métricas de pesquisa cognitiva do Azure com Power BI
description: Visualizar registos e métricas de pesquisa cognitiva do Azure com Power BI
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 90691fda7b0bf58768c7e9be6a78fb27a7807186
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400371"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Visualizar registos e métricas de pesquisa cognitiva do Azure com Power BI
[A Azure Cognitive Search](./search-what-is-azure-search.md) permite-lhe armazenar registos de operação e métricas de serviço sobre o seu serviço de pesquisa numa conta de Armazenamento Azure. Esta página fornece instruções para como visualizar essa informação através de uma Aplicação de Modelo de Power BI. A aplicação fornece informações detalhadas sobre o seu serviço de pesquisa, incluindo informações sobre métricas de Pesquisa, Indexação, Operações e Serviço.

Pode encontrar a Aplicação De Modelo de Power BI **Azure Cognitive Search: Analisar registos e métricas** no mercado de [aplicações Power BI](https://appsource.microsoft.com/marketplace/apps).

## <a name="how-to-get-started-with-the-app"></a>Como começar com a app

1. Ativar a métrica e o registo de recursos para o seu serviço de pesquisa:

    1. Criar ou identificar uma conta de [Armazenamento Azure](../storage/common/storage-account-create.md) existente onde pode arquivar os registos
    1. Navegue para o seu serviço de Pesquisa Cognitiva Azure no portal Azure
    1. Na secção de Monitorização da coluna esquerda, selecione **definições de diagnóstico**

        :::image type="content" source="media/search-monitor-logs-powerbi/diagnostic-settings.png" alt-text="Screenshot mostrando como selecionar definições de diagnóstico na secção de Monitorização do serviço de Pesquisa Cognitiva Azure." border="false":::

    1. Selecione **+ Adicionar definição de diagnóstico**
    1. Consulte **o Arquivo para uma conta de armazenamento,** forneça as informações da sua conta de armazenamento e verifique **OperaçãoLogs** e **AllMetrics**

        :::image type="content" source="media/search-monitor-logs-powerbi/add-diagnostic-setting.png" alt-text="Screenshot mostrando como selecionar definições de diagnóstico na secção de Monitorização do serviço de Pesquisa Cognitiva Azure.":::
    1. Selecione **Guardar**

1. Depois de ter sido ativado o registo, utilize o seu serviço de pesquisa para começar a gerar registos e métricas. Leva até uma hora antes que os recipientes apareçam no armazenamento blob com estes troncos. Você verá um recipiente **de insights-logs-operationlogs** para pesquisa de registos de tráfego e um recipiente **insights-metrics-pt1m** para métricas.

1. Encontre a App Azure Cognitive Search Power BI no mercado power [BI Apps](https://appsource.microsoft.com/marketplace/apps) e instale-a num novo espaço de trabalho ou num espaço de trabalho existente. A aplicação chama-se **Azure Cognitive Search: Analise Logs and Metrics**.

1. Depois de instalar a aplicação, selecione a aplicação da sua lista de aplicações no Power BI.

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile.png" alt-text="Screenshot mostrando como selecionar definições de diagnóstico na secção de Monitorização do serviço de Pesquisa Cognitiva Azure.":::

1. Selecione **Connect** para ligar os seus dados

    :::image type="content" source="media/search-monitor-logs-powerbi/get-started-with-your-new-app.png" alt-text="Screenshot mostrando como selecionar definições de diagnóstico na secção de Monitorização do serviço de Pesquisa Cognitiva Azure.":::

1. Insira o nome da conta de armazenamento que contém os seus registos e métricas. Por predefinição, a aplicação irá analisar os últimos 10 dias de dados, mas este valor pode ser alterado com o parâmetro **Days.**

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account.png" alt-text="Screenshot mostrando como selecionar definições de diagnóstico na secção de Monitorização do serviço de Pesquisa Cognitiva Azure.":::

1. Selecione **a Chave** como método de autenticação e forneça a chave da sua conta de armazenamento. Selecione **Private** como o nível de privacidade. Clique em Iniciar Sessão para iniciar o processo de carregamento.

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png" alt-text="Screenshot mostrando como selecionar definições de diagnóstico na secção de Monitorização do serviço de Pesquisa Cognitiva Azure.":::

1. Aguarde que os dados se a refresh. Isto pode demorar algum tempo, dependendo da quantidade de dados que tiver. Pode ver se os dados ainda estão a ser atualizados com base no indicador abaixo.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-refreshing.png" alt-text="Screenshot mostrando como selecionar definições de diagnóstico na secção de Monitorização do serviço de Pesquisa Cognitiva Azure.":::

1. Uma vez concluída a atualização dos dados, selecione **O Relatório de Pesquisa Cognitiva do Azure** para visualizar o relatório.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-report.png" alt-text="Screenshot mostrando como selecionar definições de diagnóstico na secção de Monitorização do serviço de Pesquisa Cognitiva Azure.":::![]()

1. Certifique-se de atualizar a página após a abertura do relatório para que abra com os seus dados.

    :::image type="content" source="media/search-monitor-logs-powerbi/powerbi-search.png" alt-text="Screenshot mostrando como selecionar definições de diagnóstico na secção de Monitorização do serviço de Pesquisa Cognitiva Azure.":::

## <a name="how-to-change-the-app-parameters"></a>Como alterar os parâmetros da aplicação
Se pretender visualizar dados a partir de uma conta de armazenamento diferente ou alterar o número de dias de dados para consulta, siga os passos abaixo para alterar os parâmetros **Days** and **StorageAccount.**

1. Navegue para as suas aplicações Power BI, encontre a sua aplicação Azure Cognitive Search e selecione o botão **de aplicação Editar** para ver o espaço de trabalho.

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png" alt-text="Screenshot mostrando como selecionar definições de diagnóstico na secção de Monitorização do serviço de Pesquisa Cognitiva Azure.":::

1. Selecione **Definições** a partir das opções de Conjunto de Dados.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-settings.png" alt-text="Screenshot mostrando como selecionar definições de diagnóstico na secção de Monitorização do serviço de Pesquisa Cognitiva Azure.":::

1. Enquanto está no separador Conjuntos de Dados, altere os valores dos parâmetros e **selecione Aplicar**. Se houver algum problema com a ligação, atualize as credenciais de origem de dados na mesma página.

1. Volte para o espaço de trabalho e **selecione Refresh agora** a partir das opções dataset.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png" alt-text="Screenshot mostrando como selecionar definições de diagnóstico na secção de Monitorização do serviço de Pesquisa Cognitiva Azure.":::

1. Abra o relatório para ver os dados atualizados. Poderá também ser necessário atualizar o relatório para ver os dados mais recentes.

## <a name="troubleshooting"></a>Resolução de problemas
Se descobrir que não pode ver os seus dados seguir estes passos de resolução de problemas:

1. Abra o relatório e refresque a página para se certificar de que está a ver os dados mais recentes. Há uma opção no relatório para atualizar os dados. Selecione isto para obter os dados mais recentes.

1. Certifique-se de que o nome da conta de armazenamento e a chave de acesso fornecida estão corretos. O nome da conta de armazenamento deve corresponder à conta configurada com os registos do seu serviço de pesquisa.

1. Confirme que a sua conta de armazenamento contém os **insights-logs-operaçãologs** e **insights-metrics-pt1m** e cada recipiente tem dados. Os troncos e métricas estarão dentro de algumas camadas de pastas.

1. Verifique se o conjunto de dados ainda é refrescante. O indicador de estado de atualização é indicado no passo 8 acima. Se ainda for refrescante, aguarde até que a atualização esteja completa para abrir e refrescar o relatório.

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre a Azure Cognitive Search](./index.yml)

[O que é o Power BI?](/power-bi/fundamentals/power-bi-overview)

[Conceitos básicos para designers no serviço Power BI](/power-bi/service-basic-concepts)