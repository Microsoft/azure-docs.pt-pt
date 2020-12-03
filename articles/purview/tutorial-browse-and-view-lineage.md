---
title: 'Tutorial: Navegue em ativos em Azure Purview e veja a sua linhagem'
description: Este tutorial descreve como procurar por ativos no catálogo e ver a linhagem de dados.
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 8f548261f180209ff3a6f664f03163e6e7a5c523
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555873"
---
# <a name="tutorial-browse-assets-in-azure-purview-preview-and-view-their-lineage"></a>Tutorial: Navegue em ativos em Azure Purview (pré-visualização) e veja a sua linhagem

> [!IMPORTANT]
> A Azure Purview está atualmente em PREVIEW. Os [Termos Complementares de Utilização para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam às funcionalidades do Azure que estão em versão beta, pré-visualização ou ainda não lançadas para a disponibilidade geral.

Este tutorial mostra-lhe como navegar em ativos em Azure Purview e ver os seus detalhes importantes, como a linhagem.

Este tutorial é *parte 3 de uma série tutorial* de cinco partes na qual você aprende os fundamentos de como gerir a governação de dados através de uma propriedade de dados usando Azure Purview. Este tutorial baseia-se no trabalho que concluiu na [Parte 2: Navegue na página inicial do Azure Purview (pré-visualização) e procure um ativo](tutorial-asset-search.md).

Neste tutorial, vai:

> [!div class="checklist"]
>
> * Procure por ativos no catálogo.
> * Veja a linhagem de bens.

## <a name="prerequisites"></a>Pré-requisitos

* [Tutorial Completo: Navegue na página inicial do Azure Purview (pré-visualização) e procure um ativo](tutorial-asset-search.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="browse-for-assets-in-the-catalog"></a>Procure por ativos no catálogo

No tutorial anterior, aprendeu como a pesquisa pode ajudá-lo a descobrir ativos no catálogo Azure Purview. Outra forma de descobrir os ativos no catálogo é utilizando a experiência de navegação do catálogo.

Esta secção mostra-lhe como navegar no seu catálogo Azure Purview.

1. Navegue para o seu recurso Azure Purview no portal Azure e selecione **Open Purview Studio**. É automaticamente levado para a página inicial do seu Estúdio de Purview.

1. Na página **Inicial,** **selecione Browse assets**.

   Aparece a página **de ativos Browse,** que apresenta um resumo de todos os tipos de ativos do seu catálogo.

1. Para explorar os vários ativos do tipo Azure Data Lake Gen2 disponíveis no seu catálogo, selecione o azulejo **Azure Data Lake Gen2.**

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/browse-by-asset-type.png" alt-text="Screenshot mostrando a página de Browse por tipo de ativo com Azure Data Lake Gen2 selecionado.":::

1. Se existirem vários ativos, pode selecionar o cabeçalho da coluna **Name** para classificar os ativos alfabeticamente. Neste tutorial, há apenas um ativo Azure Data Lake Storage Gen2.

1. Selecione o nome do ativo.

1. Selecione o conjunto de recursos **Contoso_GrossProfit_{N}.ssv.** Se este ativo não existir no seu catálogo, escolha outro.

   :::image type="content" source="media/tutorial-browse-and-view-lineage/view-adls-assets.png" alt-text="Lista de recursos da Azure Data Lake Storage Gen2":::

## <a name="view-the-lineage-of-assets"></a>Ver a linhagem de ativos

Na página de detalhes do ativo, explore a origem dos dados.

1. Selecione o **separador linhagem** do conjunto de recursos **Contoso_GrossProfit_{N}.ssv.**

   O ativo selecionado é apresentado. As informações de linhagem que vê mostram que este conjunto de recursos foi copiado da sua conta de armazenamento Azure Blob para a Azure Data Lake Storage Gen2.

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/view-lineage.png" alt-text="Screenshot mostrando a vista de Linhagem para o ativo.":::

1. Selecione o ativo blob nesta página e selecione o Switch para o link **de ativos.**

   Observe que a janela mudou para o conjunto de recursos Azure Blob, que foi a fonte do Azure Data Lake Storage Gen2 original.

1. Selecione o **separador Visão** Geral para investigar o ativo e confirme os seus dados.

Para obter informações sobre como criar uma atividade de fluxo de dados da Azure Data Factory entre um conjunto de recursos de Azure Blob e Azure Data Lake Storage Gen2 e observar a linhagem, consulte a [atividade de fluxo de dados da Azure Data Factory](../data-factory/concepts-data-flow-overview.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
>
> * Procure por ativos no catálogo.
> * Veja a linhagem de bens.

Avance para o próximo tutorial para aprender sobre conjuntos de recursos, detalhes de ativos, esquemas e classificações.

> [!div class="nextstepaction"]
> [Conjuntos de recursos, detalhes de ativos, esquemas e classificações](tutorial-schemas-and-classifications.md)
