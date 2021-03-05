---
title: Utilizar o Purview Studio
description: Este artigo conceptual descreve como usar o Azure Purview Studio.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: ca25bbb72ff853f819f3e8ce4e0092ddb762b156
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183816"
---
# <a name="use-purview-studio"></a>Utilizar o Purview Studio

Este artigo apresenta uma visão geral de algumas das principais características do Azure Purview.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Ative Purview já foi criada no portal Azure e o utilizador tem permissões para aceder ao Purview Studio.

## <a name="launch-purview-account"></a>Lançar conta Depview

* Para lançar a sua conta Purview, vá às contas Doeview no portal Azure, selecione a conta que pretende lançar e lance a conta.

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Screenshot da seleção para lançar o catálogo de conta Azure Purview.":::

* Outra forma de lançar a conta Purview é ir para `https://web.purview.azure.com` , selecione **Azure Ative Directory** e um nome de conta para lançar a conta.

## <a name="home-page"></a>Página de boas-vindas

**A casa** é a página de partida para o cliente Azure Purview.

 :::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="Screenshot da página inicial.":::

A lista que se segue resume as principais características da **página inicial.** Cada número da lista corresponde a um número realçado na imagem anterior.

1. Nome amigável do catálogo. Pode definir o nome do catálogo nas informações da Conta **do Centro de Gestão.**  >  

2. A análise do catálogo mostra o número de:
    - Utilizadores, grupos e aplicações
    - Origens de dados
    - Elementos
    - Termos do glossário

3. A caixa de pesquisa permite-lhe procurar por ativos de dados em todo o catálogo de dados.

4. Os botões de acesso rápido dão acesso a funções frequentemente utilizadas da aplicação. Os botões que são apresentados dependem da função atribuída à sua conta de utilizador.

    - Para *o curador de dados,* os botões são Knowledge **Center,** **Browse Assets,** **Manage Glossary** e **View Insights.**
    - Para *o leitor de dados,* os botões em destaque são Knowledge **Center,** **Browse Assets**, View **Glossary** e **View Insights**.
    - Para o curador de dados *do administrador* de  +  dados, os botões em destaque são o **Knowledge Center,** **Register Data Sources,** **Browse Assets** e Manage **Glossary**.
    - Para o leitor de dados *do administrador de*  +  *dados,* os botões em destaque são **o Knowledge Center,** o Register Data **Sources**, **o Browse Assets** e o View **Glossary**.

5. A barra de navegação à esquerda ajuda-o a localizar as páginas principais da aplicação. Os botões que são apresentados dependem da função atribuída à sua conta de utilizador.

    - Para *o curador de dados,* os botões são **Home,** **Glossário,** **Insights,** e **Management center.**
    - Para *o leitor de dados,* os botões são **Home,** **Glossário,** **Insights,** e **Management center**.
    - Para o curador/leitor de dados *do administrador* de  +  *dados,* os botões são **Home**, **Sources**, **Glossary,** **Insights** e **Management Center**.
  
6. O separador **recentemente acedido** mostra uma lista de ativos de dados recentemente acedidos. Para obter informações sobre o acesso a ativos, consulte [procurar no Catálogo de Dados](how-to-search-catalog.md) e procurar por tipo de [ativo.](how-to-browse-catalog.md#browse-experience)  O meu separador **de itens** é uma lista de ativos de dados detidos pelo utilizador com sessão iniciada.
7. **Links Úteis** contém links para o estado da região, documentação, preços, visão geral e estado de visão geral
8. A barra de navegação superior contém informações sobre notas/atualizações de lançamento, alterar conta de competência, notificações, ajuda e secções de feedback.

## <a name="knowledge-center"></a>Centro de Conhecimento

O Knowledge center é onde você pode encontrar todos os vídeos e tutoriais relacionados com a Purview.

## <a name="guided-tours"></a>Visitas guiadas

Cada UX no Azure Purview Studio terá visitas guiadas para dar uma visão geral da página. Para iniciar a visita guiada, selecione **ajuda** na barra superior e selecione **visitas guiadas.**

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="Screenshot da visita guiada.":::

> [!Important]
   > A função de Administrador de Fonte de Dados por si só não tem acesso ao Purview Studio.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicione um diretor de segurança](tutorial-scan-data.md)
