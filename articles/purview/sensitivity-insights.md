---
title: Etiqueta de sensibilidade reportando os seus dados em Azure Purview usando a Purview Insights
description: Este guia de como ver e utilizar a etiqueta de sensibilidade purview reportando nos seus dados.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/24/2020
ms.openlocfilehash: dffecb48a8faa869cb3df450cc220e86195bbc87
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98199381"
---
# <a name="sensitivity-label-insights-about-your-data-in-azure-purview"></a>Insights do rótulo de sensibilidade sobre os seus dados em Azure Purview

Este guia de como aceder, visualizar e filtrar insights de segurança fornecidos por etiquetas de sensibilidade aplicadas aos seus dados.

As fontes de dados suportadas incluem: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, SQL Server, Azure SQL Database, Azure SQL Managed Instance

Neste guia de como guiar, aprenderá a:

> [!div class="checklist"]
> - Lance a sua conta Desemis da Azure.
> - Ver insights de rotulagem de sensibilidade nos seus dados
> - Aprofundar para obter mais detalhes de rotulagem de sensibilidade nos seus dados

> [!NOTE]
> As etiquetas de sensibilidade encontradas nos [ativos do Power BI](register-scan-power-bi-tenant.md) que são digitalizados pela Purview não são atualmente apresentadas no relatório Desídus de Rotulagem de Sensibilidade. 
>
> Para visualizar etiquetas de sensibilidade nos ativos do Power BI, consulte o ativo no [Catálogo de Dados](how-to-search-catalog.md)da Purview .
> 
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar com as introspeções de Competência, certifique-se de que completou os seguintes passos:

- Configurar os seus recursos Azure e povoar as contas relevantes com dados de teste

- [Alargou as etiquetas de sensibilidade da Microsoft 365 a ativos em Azure Purview](create-sensitivity-label.md), e criou ou selecionou as etiquetas que pretende aplicar aos seus dados.

- Configurar e completar uma verificação dos dados de teste em cada fonte de dados

- Inscreva-se em Purview com uma função de [Data Reader ou Data Curator](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles).

Para obter mais informações, consulte [Gerir as fontes de dados em Azure Purview (Preview)](manage-data-sources.md) e [rotular automaticamente os seus dados em Azure Purview](create-sensitivity-label.md).

## <a name="use-purview-sensitivity-labeling-insights"></a>Utilize insights de rotulagem de sensibilidade purview

Em Purview, as classificações são semelhantes às tags de assunto, e são usadas para marcar e identificar dados de um tipo específico que é encontrado dentro da sua propriedade de dados durante a digitalização.

As etiquetas de sensibilidade permitem-lhe indicar o quão sensíveis determinados dados são na sua organização. Por exemplo, um nome específico do projeto pode ser altamente confidencial dentro da sua organização, enquanto que esse mesmo termo não é confidencial para outras organizações. 

As classificações são correspondidas diretamente, como um número de segurança social, que tem uma classificação de Número de **Segurança Social.** 

Em contrapartida, aplicam-se etiquetas de sensibilidade quando uma ou mais classificações e condições são encontradas em conjunto. Neste contexto, [as condições](/microsoft-365/compliance/apply-sensitivity-label-automatically) referem-se a todos os parâmetros que pode definir para dados não estruturados, tais como **a proximidade a outra classificação**, e **a confiança de %**. 

A Purview utiliza as mesmas classificações, também conhecidas como [tipos de informação sensível,](/microsoft-365/compliance/sensitive-information-type-entity-definitions)como o Microsoft 365. Isto permite-lhe estender as suas etiquetas de sensibilidade existentes através dos seus ativos Azure Purview.

> [!NOTE]
> Depois de digitalizar os seus tipos de origem, dê à **Sensibilidade Insights** algumas horas para refletir os novos ativos.

**Para visualizar insights de rotulagem de sensibilidade:**

1. Vá à página inicial do **Azure Purview.**

1. Na página **'Vista Geral',** na secção **Iniciar,** selecione o azulejo da **conta Launch Purview.**

1. Em Purview, selecione o item do menu **Insights** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: à esquerda para aceder à sua área de **Insights.**

1. Na área **de Insights,** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: selecione **etiquetas de sensibilidade** para exibir o relatório de insights de **rotulagem de sensibilidade** do purview.

    > [!NOTE]
    > Se este relatório estiver vazio, pode não ter estendido as suas etiquetas de sensibilidade à Azure Purview. Para obter mais informações, consulte [automaticamente os seus dados no Azure Purview](create-sensitivity-label.md).

   :::image type="content" source="media/insights/sensitivity-labeling-insights-small.png" alt-text="Insights de rotulagem de sensibilidade" lightbox="media/insights/sensitivity-labeling-insights.png":::

   A página principal **de insights de rotulagem de sensibilidade** apresenta as seguintes áreas:

   |Área  |Descrição  |
   |---------|---------|
   |**Visão geral de fontes com rótulos de sensibilidade**     |Exibe azulejos que fornecem: <br>- O número de subscrições encontradas nos seus dados. <br>- O número de rótulos de sensibilidade únicos aplicados nos seus dados <br>- O número de fontes com rótulos de sensibilidade aplicados <br>- O número de ficheiros e tabelas encontrados com etiquetas de sensibilidade aplicadas|
   |**Principais fontes com dados rotulados (últimos 30 dias)**     | Mostra a tendência, nos últimos 30 dias, do número de fontes com rótulos de sensibilidade aplicados.       |
   |**Principais rótulos aplicados em todas as fontes**     |Mostra as principais etiquetas aplicadas em todos os seus recursos de dados Purview. |
   |**Principais rótulos aplicados em ficheiros**     |Mostra as etiquetas de sensibilidade superiores aplicadas aos ficheiros dos seus dados.          |
   |**Principais rótulos aplicados em mesas**     | Mostra as etiquetas de sensibilidade superiores aplicadas às tabelas de bases de dados nos seus dados. |   
   |  **Atividade de rotulagem**  |  Apresenta gráficos separados para ficheiros e tabelas, cada um mostrando o número de ficheiros ou tabelas rotulados ao longo do prazo selecionado. <br>**Padrão:** 30 dias<br>Selecione o filtro **Tempo** acima dos gráficos para selecionar um período de tempo diferente para visualizar.    |
   |    |    |

## <a name="sensitivity-labeling-insights-drilldown"></a>Perfuração de insights de rotulagem de sensibilidade

Em qualquer um dos seguintes gráficos **de etiquetagem de sensibilidade,** selecione o link **Ver mais** para perfurar para obter mais detalhes:

- **Principais rótulos aplicados em todas as fontes**
- **Principais rótulos aplicados em ficheiros**
- **Principais rótulos aplicados em mesas**
- **Atividade de rotulagem > Dados rotulados**

Por exemplo:

:::image type="content" source="media/insights/sensitivity-label-drilldown-small.png" alt-text="Perfuração de etiqueta de sensibilidade" lightbox="media/insights/sensitivity-label-drilldown.png":::

Faça qualquer um dos seguintes para saber mais:

|Opção  |Descrição  |
|---------|---------|
|**Filtrar os dados**     |  Utilize os filtros acima da grelha para filtrar os dados apresentados, incluindo o nome da etiqueta, o nome da assinatura ou o tipo de origem. <br><br>Se não tiver a certeza do nome exato da etiqueta, pode introduzir parte ou todo o nome na caixa de **palavras-chave** do Filtro.       |
|**Ordenar a grelha** |Selecione um cabeçalho de coluna para separar a grelha por esta coluna. | 
|**Editar colunas**     |  Para exibir mais ou menos colunas na sua grelha, **selecione Editar Colunas** e, em :::image type="icon" source="media/insights/ico-columns.png" border="false"::: seguida, selecione as colunas que pretende visualizar ou alterar a ordem.    <br><br>Selecione um cabeçalho de coluna para separar a grelha por esta coluna.   |
|**Aprofundar ainda mais**     | Para aprofundar uma etiqueta específica, selecione um nome na coluna da **etiqueta Sensibilidade** para visualizar o rótulo por relatório **de origem.** <br><br>Este relatório apresenta dados para a etiqueta selecionada, incluindo o nome de origem, o tipo de origem, o ID de subscrição e o número de ficheiros e tabelas classificados.      |
|**Procurar ativos**     |  Para navegar pelos ativos encontrados com uma etiqueta ou fonte específica, selecione uma ou mais etiquetas ou fontes, dependendo do relatório que está a visualizar e, em seguida, **selecione Procurar ativos** acima dos :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: filtros. <br><br>Os resultados da pesquisa mostram todos os ativos rotulados encontrados para o filtro selecionado.  Para mais informações, consulte [o Catálogo de Dados Azure Purview.](how-to-search-catalog.md)       |
| | |

## <a name="sensitivity-label-integration-with-microsoft-365-compliance"></a>Integração de etiquetas de sensibilidade com a conformidade da Microsoft 365

Uma estreita integração com [a Microsoft Information Protection](/microsoft-365/compliance/information-protection) oferecida no Microsoft 365 significa que o Purview permite formas diretas de estender a visibilidade à sua propriedade de dados e classificar e rotular os seus dados.

Para que as suas etiquetas de sensibilidade Microsoft 365 sejam estendidas aos seus ativos em Azure Purview, tem de ativar a Proteção de Informação para O Azure Purview, no centro de conformidade Microsoft 365.

Para obter mais informações, consulte [automaticamente os seus dados no Azure Purview](create-sensitivity-label.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre estes relatórios de insight da Azure Purview:

- [Insights glossários](glossary-insights.md)
- [Digitalizar insights](scan-insights.md)
- [Insights de classificação](./classification-insights.md)
- [Insights de extensão de ficheiros](file-extension-insights.md)
