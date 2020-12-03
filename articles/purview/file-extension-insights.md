---
title: Relatório de extensão de ficheiros sobre os seus dados utilizando o Purview Insights
description: Este guia de como ver e utilizar a extensão do ficheiro Purview Insights reportando os seus dados.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/24/2020
ms.openlocfilehash: 63bb235dcebc67b753e01dd8b193ef1a2b66787d
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555170"
---
# <a name="file-extension-insights-about-your-data-from-azure-purview"></a>Insights de extensão de ficheiros sobre os seus dados a partir do Azure Purview 

Este guia de como aceder, visualizar e filtrar insights sobre as extensões de ficheiros, ou tipos de ficheiros, encontrados nos seus dados.

As fontes de dados suportadas incluem: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2

Neste guia de como guiar, aprenderá a:
> [!div class="checklist"]
> * Lance a sua conta Desemis da Azure. 
> - Ver insights de extensão de ficheiros nos seus dados
> - Faça um exercício para obter mais detalhes da extensão do ficheiro nos seus dados

## <a name="prerequisites"></a>Pré-requisitos 

Antes de começar com as introspeções de Competência, certifique-se de que completou os seguintes passos:

- Configurar os seus recursos Azure e povoar as contas relevantes com dados de teste

- Configurar e completar uma verificação dos dados de teste em cada fonte de dados

Para obter mais informações, consulte [Gerir fontes de dados em Azure Purview (Preview)](manage-data-sources.md).

## <a name="use-purview-file-extension-insights"></a>Use insights de extensão de ficheiros de competência

Ao digitalizar os seus ativos, o Azure Purview é capaz de detetar os tipos de ficheiros encontrados na sua propriedade de dados e fornecer-lhe mais detalhes sobre cada tipo de ficheiro. Os detalhes incluem quantos ficheiros de cada tipo tem, onde esses ficheiros estão e se são digitalizáveis para dados sensíveis.

**Para visualizar insights de extensão de ficheiros:**

1. Vá ao ecrã de instância **do Azure Purview** [no portal Azure](https://aka.ms/purviewportal) e selecione a sua conta 'Purview'.

1. Na página **'Vista Geral',** na secção **Iniciar,** selecione o azulejo da **conta Launch Purview.**

1. Em Purview, selecione o item do menu **Insights** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: à esquerda para aceder à sua área de **Insights.**
    
1. No **Insights**, selecione o **separador extensões de ficheiro.**

    O relatório apresenta um resumo de quantas extensões de ficheiros únicas são encontradas, bem como um gráfico de extensões superiores de 10 encontradas, ao longo do prazo selecionado (padrão: 30 dias).

    :::image type="content" source="media/file-extension-insights/file-extension-overview-small.png" alt-text="Relatório de extensão de ficheiros - visão geral" lightbox="media/file-extension-insights/file-extension-overview.png":::

    Faça qualquer um dos seguintes para saber mais:

    - Selecione o seletor **de tempo** na parte superior do relatório para alterar o tempo para o qual foram encontradas extensões de ficheiro.
    
    - Selecione **Ver mais** abaixo do gráfico para ver uma lista completa das extensões de ficheiro encontradas. Para obter mais informações, consulte [a extensão do ficheiro para obter a broca](#file-extension-insights-drilldown). 

### <a name="file-extension-insights-drilldown"></a>Perfuração de insights de extensão de ficheiros

Depois de ter visto as informações de alto nível sobre os tipos de ficheiros encontrados na sua propriedade de dados, forneça mais detalhes sobre onde estão localizados e se podem ser digitalizados para obter dados sensíveis.

Por exemplo:

:::image type="content" source="media/file-extension-insights/file-extension-drilldown-small.png" alt-text="Relatório de extensão de ficheiros - perfuração" lightbox="media/file-extension-insights/file-extension-drilldown.png":::

A grelha mostra detalhes de cada extensão de ficheiro encontrada, incluindo:

- **Contagem de ficheiros**. O número de ficheiros com a extensão especificada.
- **Digitalização de conteúdos**. Se a extensão do ficheiro é ou não suportada para procurar dados sensíveis.
- **Assinaturas**. O número de assinaturas em que a extensão especificada foi encontrada.
- **Fontes**. O número de fontes encontradas com a extensão de ficheiro especificada.



Utilize os filtros acima da grelha para filtrar os dados apresentados:

|Opção  |Descrição  |
|---------|---------|
|**Filtrar por palavra-chave**     |    Introduza texto na caixa **de palavras-chave do Filtro para**  visualizar filtrar os seus tipos de ficheiros pelo nome. Por exemplo, para ver apenas PDFs, insira `PDF` .     |
|**Time**        | Selecione para filtrar por um período de tempo específico para quando os seus dados foram criados. <br>**Padrão:** 30 dias  |
|**Extensão de nome de ficheiro**     |Selecione para filtrar a grelha por um ou mais tipos de ficheiros.        |
|**Origens**    |Selecione para filtrar a grelha pelas fontes de dados específicas. |
|**Digitalização de conteúdos**     |Selecione para escolher **Suportado** ou **Não Suportado**, para mostrar apenas tipos de ficheiros que podem ser analisados para dados sensíveis, ou dados que não possam ser digitalizados, tais como **.cert** ou **.jpg** ficheiros. |
| | |

Acima dos filtros, **selecione Editar colunas** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: para exibir mais ou menos colunas na sua grelha ou para alterar a ordem. 

Para separar a grelha, selecione um cabeçalho de coluna para classificar por esta coluna.
## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os relatórios de insight da Azure Purview
> [!div class="nextstepaction"]
> [Insights glossários](glossary-insights.md)

> [!div class="nextstepaction"]
> [Digitalizar insights](scan-insights.md)

> [!div class="nextstepaction"]
> [Insights de classificação](./classification-insights.md)

> [!div class="nextstepaction"]
> [Insights do rótulo de sensibilidade](sensitivity-insights.md)
