---
title: Guia de utilizador de linhagem do catálogo de dados (pré-visualização)
description: Este artigo fornece uma visão geral da característica da linhagem do catálogo da Azure Purview.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/29/2020
ms.openlocfilehash: a319dbce2502f35272cf9b70da2022f581d64275
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554827"
---
# <a name="azure-purview-data-catalog-lineage-user-guide"></a>Guia de utilizadores do catálogo de dados Azure Purview

Este artigo fornece uma visão geral das funcionalidades da linhagem de dados no Catálogo de Dados Azure Purview.

## <a name="background"></a>Fundo

Uma das funcionalidades da plataforma do Azure Purview é a capacidade de mostrar a linhagem entre conjuntos de dados criados por processos de dados. Sistemas como Data Factory, Data Share e Power BI captam a linhagem de dados à medida que se move. O relatório de linhagem personalizado também é suportado através de ganchos Atlas e REST API.

## <a name="lineage-collection"></a>Coleção de linhagens 
 Os metadados recolhidos em Azure Purview a partir de sistemas de dados da empresa são costurados para mostrar o fim da linhagem de dados final. Os sistemas de dados que recolhem a linhagem em Purview são amplamente categorizados em três tipos.

### <a name="data-processing-system"></a>Sistema de processamento de dados
A integração de dados e as ferramentas ETL podem empurrar a linhagem para o Azure Purview no tempo de execução. Ferramentas como Data Factory, Data Share, Synapse, Azure Databricks, e assim por diante, pertencem a esta categoria de sistemas de dados. Os sistemas de processamento de dados referem conjuntos de dados como fonte de diferentes bases de dados e soluções de armazenamento para criar conjuntos de dados-alvo. A lista de sistemas de processamento de dados atualmente integrados com o Purview para a linhagem está listada na tabela abaixo.


| Sistema de processamento de dados | Âmbito suportado |
| ---------------------- | ------------|
| Azure Data Factory | [Atividade Copiar](how-to-link-azure-data-factory.md#data-factory-copy-data-support) <br> [Atividade de fluxo de dados](how-to-link-azure-data-factory.md#data-factory-data-flow-support) <br> [Executar atividade de pacote SSIS](how-to-link-azure-data-factory.md#data-factory-execute-ssis-package-support) |
| Azure Data Share | [Partilhar o artigo Fotos](how-to-link-azure-data-share.md) |
 
### <a name="data-storage-systems"></a>Sistemas de armazenamento de dados
As bases de dados & soluções de armazenamento como o SQL Server, Teradata e SAP têm motores de consulta para transformar dados usando linguagem de script. A linhagem de dados dos procedimentos armazenados é recolhida em Purview e costurada com linhagem de outros sistemas.

| Sistema de armazenamento de dados | Âmbito suportado |
| ---------------------- | ------------|
| Teradata | Procedimentos armazenados

### <a name="data-analytics--reporting-systems"></a>Sistemas de reporte de & de análise de dados
Sistemas de dados como Azure ML e Power BI reportam linhagem em Azure Purview. Estes sistemas utilizarão os conjuntos de dados dos sistemas de armazenamento e processarão através do seu modelo de meta para criar experiências bi dashboard, ML e assim por diante.

| Sistema de reporte de & de análise de dados | Âmbito suportado |
| ---------------------- | ------------|
| Power BI | [Conjuntos de dados, fluxos de dados, relatórios & dashboards](register-scan-power-bi-tenant.md)

## <a name="get-started-with-lineage"></a>Começar com linhagem

A linhagem em Purview inclui conjuntos de dados e processos. Os conjuntos de dados também são referidos como nódos enquanto os processos também podem ser chamados de bordas:

* **Conjunto de dados (nó)**: Um conjunto de dados (estruturado ou não estruturado) fornecido como entrada para um processo. Por exemplo, uma tabela SQL, blob Azure e ficheiros (como .csv e .xml), são todos considerados conjuntos de dados. Na secção de linhagem de Purview, os conjuntos de dados são representados por caixas retangulares.

* **Processo (Borda)**: Uma atividade ou transformação realizada num conjunto de dados é chamada de processo. Por exemplo, a atividade de cópia ADF, data share snapshot e assim por diante. Na secção de linhagem de Purview, os processos são representados por caixas de ponta redonda.

Para aceder a informações de linhagem para um ativo em Purview, siga os passos:

1. No portal Azure, aceda à página de [contas do Azure Purview](https://aka.ms/purviewportal).

1. Selecione a sua conta Azure Purview da lista e, em seguida, selecione **a conta de visão geral** **do** Lançamento.

1. Na página **inicial** do Azure Purview, procure um nome de conjunto de dados ou o nome do processo, como a atividade ADF Copy ou Data Flow. E, em seguida, pressione Enter.

1. A partir dos resultados da pesquisa, selecione o ativo e selecione o **separador Linhagem.**

   :::image type="content" source="./media/catalog-lineage-user-guide/select-lineage-from-asset.png" alt-text="Screenshot mostrando como selecionar o separador Lineage." border="true":::

## <a name="asset-level-lineage"></a>Linhagem ao nível dos ativos

O Azure Purview suporta a linhagem do nível de ativos para os conjuntos de dados e processos. Para ver a linhagem de nível de ativo ir para o **separador Lineage** do ativo atual no catálogo. Selecione o nó de ativo do conjunto de dados atual. Por predefinição, a lista de colunas pertencentes aos dados aparece no painel esquerdo.

   :::image type="content" source="./media/catalog-lineage-user-guide/view-columns-from-lineage.png" alt-text="Screenshot mostrando como selecionar Ver colunas na página de linhagem" border="true":::

## <a name="column-level-lineage"></a>Linhagem ao nível da coluna

O Azure Purview suporta a linhagem ao nível da coluna para os conjuntos de dados. Para ver a linhagem ao nível da coluna, vá ao **separador lineage** do ativo atual no catálogo e siga os passos abaixo:

1. Uma vez que esteja no separador de linhagem, no painel esquerdo, selecione a caixa de verificação ao lado de cada coluna que pretende exibir na linhagem de dados.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png" alt-text="Screenshot mostrando como selecionar colunas para visualizar na página de linhagem." lightbox="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png":::

1. Passe sobre uma coluna selecionada no painel esquerdo ou no conjunto de dados da tela de linhagem para ver o mapeamento da coluna. Todas as instâncias da coluna são destacadas.

   :::image type="content" source="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png" alt-text="Screenshot mostrando como pairar sobre um nome de coluna para realçar o fluxo da coluna em um caminho de linhagem de dados." lightbox="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png":::

1. Se o número de colunas for maior do que o que pode ser visualizado no painel esquerdo, utilize a opção de filtro para selecionar uma coluna específica pelo nome. Em alternativa, pode utilizar o rato para percorrer a lista.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-columns-by-name.png" alt-text="Screenshot mostrando como filtrar colunas por nome de coluna na página de linhagem." lightbox="./media/catalog-lineage-user-guide/filter-columns-by-name.png":::

1. Se a tela de linhagem contiver mais nós e arestas, utilize o filtro para selecionar o ativo de dados ou processar nós pelo nome. Em alternativa, pode utilizar o rato para fazer uma panorâmica à volta da janela da linhagem.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-assets-by-name.png" alt-text="Screenshot mostrando os nós do ativo de dados pelo nome na página de linhagem." lightbox="./media/catalog-lineage-user-guide/filter-assets-by-name.png":::

1. Utilize o toggle no painel esquerdo para realçar a lista de conjuntos de dados na tela de linhagem. Se desligar o toggle, qualquer ativo que contenha pelo menos uma das colunas selecionadas é apresentado. Se ligar o toggle, apenas são apresentados conjuntos de dados que contenham todas as colunas.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png" alt-text="Screenshot mostrando como usar o toggle para filtrar a lista de nós na página de linhagem." lightbox="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png":::

1. Selecione **Switch para ativo** em qualquer ativo para ver os metadados correspondentes a partir da vista de linhagem. Fazê-lo é uma forma eficaz de navegar para outro ativo no catálogo a partir da vista da linhagem.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-switch-to-asset.png" alt-text="Screenshot como selecionar Switch para ativo num ativo de dados de linhagem." lightbox="./media/catalog-lineage-user-guide/select-switch-to-asset.png":::

1. A tela de linhagem pode tornar-se complexa para conjuntos de dados populares. Para evitar a confusão, a vista padrão só mostrará cinco níveis de linhagem para o ativo em foco. O resto da linhagem pode ser expandido clicando nas bolhas na tela de linhagem. Os consumidores de dados também podem esconder os ativos na tela que não têm qualquer interesse. Para reduzir ainda mais a desordem, desligue o toggle **More Lineage** no topo da tela de linhagem. Esta ação esconderá todas as bolhas em tela de linhagem.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png" alt-text="Screenshot mostrando como alternar Mais linhagem." lightbox="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png":::

1. Utilize os botões inteligentes na tela de linhagem para obter uma visão ótima da linhagem. O layout automático, zoom para caber, Zoom in/out, ecrã completo e mapa de navegação estão disponíveis para uma experiência de linhagem imersiva no catálogo.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png" alt-text="Screenshot mostrando como selecionar os botões inteligentes de linhagem." lightbox="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png":::

## <a name="next-steps"></a>Passos seguintes

* [Link para Azure Data Factory para linhagem](how-to-link-azure-data-factory.md)
* [Link para Azure Data Share para linhagem](how-to-link-azure-data-share.md)