---
title: Relatório de classificação sobre os seus dados em Azure Purview usando a Purview Insights
description: Este guia de como ver e utilizar a classificação do Purview nos seus dados.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/24/2020
ms.openlocfilehash: a0eed27636a3f65d68489d1f2249db784ba1d722
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98196542"
---
# <a name="classification-insights-about-your-data-from-azure-purview"></a>Insights de classificação sobre os seus dados a partir do Azure Purview

Este guia de como aceder, visualizar e filtrar relatórios de insight de classificação purview para os seus dados.

As fontes de dados suportadas incluem: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, Azure Cosmos DB (SQL API), Azure Synapse Analytics (anteriormente SQL DW), Azure SQL Database, Azure SQL Managed Instance, SQL Server

Neste guia de como guiar, aprenderá a:

> [!div class="checklist"]
> - Lance a sua conta Desemis da Azure
> - Ver insights de classificação nos seus dados
> - Aprofundar para mais detalhes de classificação nos seus dados

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar com as introspeções de Competência, certifique-se de que completou os seguintes passos:

- Configurar os seus recursos Azure e povoar as contas relevantes com dados de teste

- Configurar e completar uma verificação dos dados de teste em cada fonte de dados

- Inscreva-se em Purview com uma função de [Data Reader ou Data Curator](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles).

Para obter mais informações, consulte [Gerir fontes de dados em Azure Purview (Preview)](manage-data-sources.md).

## <a name="use-purview-classification-insights"></a>Use insights de classificação de visão de competência

Em Azure Purview, as classificações são semelhantes às tags sujeitas, e são usadas para marcar e identificar dados de um tipo específico que é encontrado dentro da sua propriedade de dados durante a digitalização.

A Purview utiliza os mesmos tipos de informação sensíveis que o Microsoft 365, permitindo-lhe esticar as suas políticas de segurança existentes e proteger toda a sua propriedade de dados.

> [!NOTE]
> Depois de digitalizar os seus tipos de origem, dê à **Classification** Insights algumas horas para refletir os novos ativos.

**Para visualizar os insights de classificação:**

1. Vá ao ecrã de instância **do Azure Purview** [no portal Azure](https://aka.ms/purviewportal) e selecione a sua conta 'Purview'.

1. Na página **'Vista Geral',** na secção **Iniciar,** selecione o azulejo da **conta Launch Purview.**

1. Em Purview, selecione o item do menu **Insights** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: à esquerda para aceder à sua área de **Insights.**

1. Na  :::image type="icon" source="media/insights/ico-insights.png" border="false"::: área insights, selecione **Classificação** para exibir o relatório **de insights de classificação de** purga.

   :::image type="content" source="./media/insights/select-classification-labeling-small.png" alt-text="Relatório de insights de classificação" lightbox="media/insights/select-classification-labeling.png":::

   A página principal **de insights de classificação** apresenta as seguintes áreas:

   |Área  |Descrição  |
   |---------|---------|
   |**Visão geral das fontes com classificações**     |Exibe azulejos que fornecem: <br>- O número de subscrições encontradas nos seus dados <br>- O número de classificações únicas encontradas nos seus dados <br>- O número de fontes classificadas encontradas <br>- O número de ficheiros classificados encontrados <br>- O número de tabelas classificadas encontradas         |
   |**Principais fontes com dados classificados (últimos 30 dias)**     |Mostra a tendência, nos últimos 30 dias, do número de fontes encontradas com dados confidenciais.            |
   |**Categorias de classificação superior por fontes**     |Mostra o número de fontes encontradas por categoria de classificação, como **o Financeiro** ou **o Governo.**      |
   |**Classificações superiores para ficheiros**     |Mostra as classificações de topo aplicadas aos ficheiros dos seus dados, tais como números de cartão de crédito ou números de identificação nacional.         |
   |**Classificações de topo para tabelas**     | Mostra as classificações de topo aplicadas às tabelas dos seus dados, tais como informações de identificação pessoal. |   
   |  **Atividade de classificação** <br>(ficheiros e tabelas) |  Apresenta gráficos separados para ficheiros e tabelas, cada um mostrando o número de ficheiros ou tabelas classificados ao longo do prazo selecionado. <br>**Padrão:** 30 dias<br>Selecione o filtro **Tempo** acima dos gráficos para selecionar um período de tempo diferente para visualizar.    |
   |    |    |

## <a name="classification-insights-drilldown"></a>Perfuração de insights de classificação

Em qualquer um dos seguintes gráficos **de insights de classificação,** selecione o link **Ver mais** para perfurar para obter mais detalhes:

- **Categorias de classificação superior por fontes**
- **Classificações superiores para ficheiros**
- **Classificações de topo para tabelas**
- **Dados da atividade de classificação > Classificação**

Por exemplo:

:::image type="content" source="media/insights/view-classifications-small.png" alt-text="Ver todas as classificações" lightbox="media/insights/view-classifications.png":::

Faça qualquer um dos seguintes para saber mais:

|Opção  |Descrição  |
|---------|---------|
|**Filtrar os dados**     |  Utilize os filtros acima da grelha para filtrar os dados apresentados, incluindo o nome de classificação, o nome de subscrição ou o tipo de origem. <br><br>Se não tiver a certeza do nome exato da classificação, pode introduzir parte ou todo o nome na caixa de **palavras-chave** do Filtro.       |
|**Ordenar a grelha** |Selecione um cabeçalho de coluna para separar a grelha por esta coluna. | 
|**Editar colunas**     |  Para exibir mais ou menos colunas na sua grelha, **selecione Editar Colunas** e, em :::image type="icon" source="media/insights/ico-columns.png" border="false"::: seguida, selecione as colunas que pretende visualizar ou alterar a ordem.   |
|**Aprofundar ainda mais**     | Para aprofundar uma classificação específica, selecione um nome na coluna **de classificação** para ver a Classificação por relatório **de origem.** <br><br>Este relatório apresenta dados para a classificação selecionada, incluindo o nome de origem, o tipo de origem, o ID de subscrição e o número de ficheiros e tabelas classificados.      |
|**Procurar ativos**     |  Para navegar pelos ativos encontrados com uma classificação ou fonte específica, selecione uma classificação ou fonte, dependendo do relatório que está a visualizar, e, em seguida, **selecione Procurar ativos** acima dos :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: filtros. <br><br>Os resultados da pesquisa mostram todos os ativos classificados encontrados para o filtro selecionado.  Para mais informações, consulte [o Catálogo de Dados Azure Purview.](how-to-search-catalog.md)       |
| | |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os relatórios de insight da Azure Purview
> [!div class="nextstepaction"]
> [Insights glossários](glossary-insights.md)

> [!div class="nextstepaction"]
> [Digitalizar insights](scan-insights.md)

> [!div class="nextstepaction"]
> [Insights de rotulagem de sensibilidade](./sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Insights de extensão de ficheiros](file-extension-insights.md)