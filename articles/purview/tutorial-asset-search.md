---
title: 'Tutorial: Navegue na página inicial do Azure Purview e procure um ativo'
description: Este tutorial descreve como usar funcionalidades na página inicial do Azure Purview e pesquisar no catálogo.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 02/22/2020
ms.openlocfilehash: 5285d5b2c7cbe6845fb6043fa1096f94254602e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677842"
---
# <a name="tutorial-navigate-the-azure-purview-preview-home-page-and-search-for-an-asset"></a>Tutorial: Navegue na página inicial do Azure Purview (pré-visualização) e procure um ativo

> [!IMPORTANT]
> A Azure Purview está atualmente em PREVIEW. Os [Termos Complementares de Utilização para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam às funcionalidades do Azure que estão em versão beta, pré-visualização ou ainda não lançadas para a disponibilidade geral.

Neste tutorial, você se familiariza com Azure Purview navegando as características da página inicial e procurando um ativo no catálogo.

Este artigo é *parte 2 de uma série tutorial* de cinco partes na qual você aprende os fundamentos de como gerir a governação de dados através de uma propriedade de dados usando Azure Purview. Este tutorial baseia-se no trabalho que concluiu na [Parte 1: Digitalizar dados com Azure Purview](tutorial-scan-data.md)

Neste tutorial, vai:

> [!div class="checklist"]
>
> * Navegue na página inicial do Azure Purview.
> * Procure um ativo.
> * Adicione um proprietário de ativos.

## <a name="prerequisites"></a>Pré-requisitos

* [Tutorial Completo: Digitalizar dados com Azure Purview](tutorial-scan-data.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="navigate-the-azure-purview-home-page"></a>Navegue na página inicial do Azure Purview

Os passos seguintes acompanham-no através da página inicial do Azure Purview.

1. Navegue para o seu recurso Azure Purview no portal Azure e selecione **Open Purview Studio**. É automaticamente levado para a página inicial do seu Estúdio de Purview.

   O topo da página inicial exibe o nome do seu catálogo e um conjunto de análises de catálogos. Incluem-se o número de fontes, ativos de dados e termos glossários. No resumo, pode ver que existem mais de 200 ativos no total e 113 termos glossários. Este número atualiza à medida que a sua organização digitaliza e adiciona mais termos ao Azure Purview.

   :::image type="content" source="./media/tutorial-asset-search/purview-home-page.png" alt-text="Screenshot mostrando a página inicial do Azure Purview.":::

1. Selecione **procurar ativos** para ver todos os seus ativos.

## <a name="search-for-an-asset"></a>Procurar um ativo

Antes de começar, aqui está uma rápida atualização sobre alguma terminologia fundamental:

* **Ativo**: Um único objeto no catálogo que é conciso e contém a definição de qualquer entidade na propriedade de dados. Exemplos de entidades incluem uma tabela SQL, um relatório Power BI e a sua atividade na fábrica de dados.
  
* **Schema**: Também conhecido como coluna ou atributo, um esquema representa a estrutura de um ativo ou de um conjunto de recursos.

* **Conjunto de recursos**: Um único objeto no catálogo que representa muitos objetos físicos no armazenamento. Estes objetos geralmente partilham um esquema comum, e na maioria dos casos, uma convenção de nomeação ou estrutura de pasta. Por exemplo, o formato de data é *yyyy/mm/dd*. Para obter mais informações, consulte [os conjuntos de recursos de Compreensão.](concept-resource-sets.md)

* **Tipo de ativo**: Agrupamento de ativos e conjuntos de recursos sob um nome lógico, que normalmente mapeia para o nome da plataforma de dados.

Siga estes passos para procurar um ativo e marque-se como proprietário:

1. Na caixa de **catálogos Search** na sua página inicial, insira o nome do grupo de recursos que contém o seu espólio de dados (o grupo de recursos que criou no tutorial anterior). Aparecerá uma lista de sugestões.

1. Selecione **Ver os resultados da pesquisa**. Como todos os seus ativos começam com o nome do seu grupo de recursos, todos eles aparecem nos resultados da pesquisa. Fora deste tutorial, procuraria nomes de ativos específicos, não grupos de recursos.

    :::image type="content" source="./media/tutorial-asset-search/search-suggestions.png" alt-text="Screenshot mostrando a lista de sugestões de pesquisa de catálogo.":::

1. Pode utilizar os filtros na navegação esquerda para filtrar por tipo de ativo, classificação, contacto, etiqueta e termo glossário.

1. Para procurar um conjunto de recursos, comece a escrever o nome do conjunto. Aparecerá uma lista de sugestões de pesquisa com as palavras-chave corretas. Também pode procurar nomes absolutos colocando-os em aspas.

   :::image type="content" source="media/tutorial-asset-search/keyword-search.png" alt-text="Pesquisa de ativos de palavra-chave Azure Purview":::

## <a name="edit-an-asset"></a>Editar um ativo

1. Selecione um dos ativos dos resultados da sua pesquisa. Em seguida, **selecione Editar**

1. No **separador Visão** Geral, pode adicionar uma descrição.

    :::image type="content" source="./media/tutorial-asset-search/overview-tab.png" alt-text="Screenshot mostrando o campo Descrição no separador Visão Geral.":::

1. Selecione o **separador Contactos.** Ao lado **dos Proprietários**, em Select user **ou grupo,** comece a digitar o seu endereço de e-mail corporativo.

    :::image type="content" source="./media/tutorial-asset-search/contacts-tab.png" alt-text="Screenshot mostrando campos povoados.":::

    Uma sugestão de nome é exibida automaticamente.

1. Ao lado de **Experts**, em **Select user or group**, insira um nome (por exemplo, o nome do seu gestor) e, em seguida, selecione **Save**.

    A descrição, nome do proprietário e campos de nomes especializados estão agora povoados.

## <a name="next-steps"></a>Passos seguintes

Antes de passar ao próximo tutorial desta série, aproveite mais algum tempo para explorar a página inicial do Azure Purview por conta própria. Neste tutorial, ficou a saber como:

> [!div class="checklist"]
>
> * Navegue na página inicial do Azure Purview.
> * Procure um ativo.
> * Adicione um proprietário de ativos.

Avance para o próximo tutorial para aprender a procurar ativos em Azure Purview e descubra a linhagem de ativos.

> [!div class="nextstepaction"]
> [Navegue em ativos e veja a sua linhagem](tutorial-browse-and-view-lineage.md)
