---
title: 'Tutorial: Explore conjuntos de recursos, detalhes, esquemas e classificações em Azure Purview (pré-visualização)'
description: Este tutorial descreve como usar conjuntos de recursos, detalhes de ativos, esquemas e classificações.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: c74324ebeeefeed361c0557c45a280a411effa22
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693329"
---
# <a name="tutorial-explore-resource-sets-details-schemas-and-classifications-in-azure-purview-preview"></a>Tutorial: Explore conjuntos de recursos, detalhes, esquemas e classificações em Azure Purview (pré-visualização)

> [!IMPORTANT]
> A Azure Purview está atualmente em PREVIEW. Os [Termos Complementares de Utilização para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam às funcionalidades do Azure que estão em versão beta, pré-visualização ou ainda não lançadas para a disponibilidade geral.

Neste tutorial, você explora os componentes-chave do seu catálogo: conjuntos de recursos, detalhes de ativos, esquemas e classificações.

Este tutorial é *parte 4 de uma série tutorial* de cinco partes na qual você aprende os fundamentos de como gerir a governação de dados através de uma propriedade de dados usando Azure Purview. Este tutorial baseia-se no trabalho que concluiu na [Parte 3: Navegue em Azure Purview (pré-visualização) e veja a sua linhagem](tutorial-browse-and-view-lineage.md).

Neste tutorial, vai aprender a:

> [!div class="checklist"]
>
> * Ver conjuntos de recursos.
> * Ver detalhes do ativo.
> * Editar o esquema e adicionar classificações.

## <a name="prerequisites"></a>Pré-requisitos

* [Tutorial Completo: Navegue em Azure Purview (pré-visualização) e veja a sua linhagem](tutorial-browse-and-view-lineage.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="view-resource-sets"></a>Ver conjuntos de recursos

Um conjunto de recursos é um único objeto no catálogo que representa muitos objetos físicos no armazenamento. Os objetos geralmente partilham um esquema comum e, na maioria dos casos, uma convenção de nomeação ou estrutura de pasta. Por exemplo, o formato de data é *yy/mm/dd*. Para obter mais informações sobre conjuntos de recursos, consulte [os conjuntos de recursos de Compreensão](concept-resource-sets.md).

1. Navegue para o seu recurso Azure Purview no portal Azure e selecione **Open Purview Studio**. É automaticamente levado para a página inicial do seu Estúdio de Purview.

2. Introduza **contoso_staging_positivecashflow_ n** na caixa **de ativos de Busca** e, em seguida, selecione **Contoso_Staging_PositiveCashFlow_{N}.csv** dos resultados da pesquisa.

## <a name="view-asset-details"></a>Ver detalhes do ativo

1. O **separador 'Visão Geral'** apresenta a **Descrição,** **termos glossários** e **Propriedades,** como o **Nome qualificado**.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/overview-tab.png" alt-text="Screenshot mostrando o separador Visão Geral de uma página de ativo definida por recursos.":::

1. In **Properties**, note os seguintes dois campos:

   * **partitionCount**: Indica o número de ficheiros físicos associados a este conjunto de recursos.
   * **schemaCount**: Indica o número de variações de esquema que foram encontradas dentro deste conjunto de recursos.

   A Azure Purview povoa estas propriedades no prazo de 24 horas após completar a verificação na [parte 1 desta série tutorial.](tutorial-scan-data.md)

1. Selecione o separador **Contactos** para rever os **valores de Peritos** e **Proprietários.**

## <a name="edit-the-schema-and-add-classifications"></a>Editar o esquema e adicionar classificações

1. Selecione o **separador Schema.** Observe os nomes das colunas e as classificações que lhes estão associadas. Note que a varredura povoou as propriedades automaticamente.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/schema-tab.png" alt-text="Screenshot mostrando o separador Schema.":::

1. Para editar o ativo, selecione o botão **Editar** na parte superior esquerda. Em seguida, selecione o **separador Schema.**

1. Adicione uma **Descrição** para uma coluna ou mude o nome da coluna para um nome mais amigável.

1. Adicione uma classificação ao nível do ativo selecionando a classificação do **nível da Coluna** para um nome de coluna que não tenha uma classificação definida.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/edit-schema.png" alt-text="Página de edição de Schema":::

1. Quando terminar as alterações, **selecione Guardar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
>
> * Ver conjuntos de recursos.
> * Ver detalhes do ativo.
> * Editar o esquema e adicionar classificações.

Avance para o próximo tutorial para aprender sobre o glossário e como definir e importar novos termos para ativos.

> [!div class="nextstepaction"]
> [Criar e importar termos glossários](tutorial-import-create-glossary-terms.md)