---
title: 'Como: pesquisar o Catálogo de Dados'
description: Este artigo dá uma visão geral de como pesquisar um catálogo de dados.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 178604335968c3664bde51c144759c1c040c359d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564918"
---
# <a name="search-the-azure-purview-data-catalog"></a>Pesse o Catálogo de Dados da Azure Purview

A descoberta de dados é o primeiro passo para uma análise de dados ou uma carga de trabalho de governação de dados para os consumidores de dados. A descoberta de dados pode ser morosa porque pode não saber onde encontrar os dados que pretende. Mesmo depois de encontrar os dados, pode ter dúvidas sobre se pode ou não confiar nos dados e assumir uma dependência dos mesmos.

O objetivo da pesquisa em Azure Purview é acelerar o processo de descoberta de dados para encontrar rapidamente os dados que importam. Este artigo descreve como pesquisar o catálogo de dados do Azure Purview para encontrar rapidamente os dados que procura.

## <a name="search-the-catalog-for-assets"></a>Procure no catálogo por ativos

Em Azure Purview, a barra de pesquisa está localizada no topo do estúdio Purview UX.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Screenshot mostrando a localização da barra de pesquisa Azure Purview" border="true":::

Ao clicar na barra de pesquisa, pode ver o seu histórico de pesquisa recente e ativos recentemente acedidos. Selecione "Ver tudo" para ver todos os ativos recentemente vistos.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="Screenshot mostrando a barra de pesquisa antes de quaisquer palavras-chave terem sido inseridas" border="true":::

Introduza palavras-chave que ajudem a identificar o seu ativo, como o seu nome, tipo de dados, classificações e termos glossários. Ao introduzir palavras-chave relacionadas com o ativo pretendido, o Azure Purview apresenta sugestões sobre o que procurar e potenciais partidas de ativos. Para completar a sua pesquisa, clique em "Ver resultados de pesquisa" ou prima "Entrar".

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="Screenshot mostrando a barra de pesquisa como um utilizador entra em palavras-chave" border="true":::

A página de resultados da pesquisa mostra uma lista de ativos que correspondem às palavras-chave fornecidas por ordem de relevância. Existem vários fatores que podem afetar a pontuação de relevância de um ativo. Pode filtrar mais a lista selecionando lojas de dados específicas, classificações, contactos, etiquetas e termos glossários que se aplicam ao ativo que procura.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Screenshot mostrando os resultados de uma pesquisa" border="true":::

 Clique no seu ativo desejado para ver a página de detalhes do ativo onde pode ver propriedades, incluindo schema, linhagem e proprietários de ativos.

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Screenshot mostrando a página de detalhes do ativo" border="true":::

## <a name="search-query-syntax"></a>Sintaxe de consulta de pesquisa

Todas as consultas de pesquisa consistem em palavras-chave e operadores. Uma palavra-chave é algo que faria parte das propriedades de um ativo. As palavras-chave potenciais podem ser uma classificação, termo glossário, descrição do ativo ou um nome de ativo. Uma palavra-chave pode ser apenas uma parte da propriedade que você está procurando para combinar. Use palavras-chave e os operadores listados abaixo para garantir que o Azure Purview devolve os ativos que procura. 

Abaixo estão os operadores que podem ser usados para compor uma consulta de pesquisa. Os operadores podem ser combinados quantas vezes for necessário numa única consulta.

| Operador | Definição | Exemplo |
| -------- | ---------- | ------- |
| OR | Especifica que um ativo deve ter pelo menos uma das duas palavras-chave. Deve estar em todos os bonés. Um espaço branco também é um operador de OR.  | A consulta `hive OR database` devolve ativos que contenham 'colmeia' ou 'base de dados' ou ambos. |
| AND | Especifica que um ativo deve ter ambas as palavras-chave. Deve estar em todas as tampas | A consulta `hive AND database` devolve ativos que contêm "colmeia" e "base de dados". |
| NOT | Especifica que um ativo não pode conter a palavra-chave para o direito da cláusula NÃO | A consulta `hive NOT database` devolve ativos que contêm 'colmeia', mas não 'base de dados'. |
| () | Agrupa um conjunto de palavras-chave e operadores em conjunto. Ao combinar vários operadores, os parênteses especificam a ordem de operações. | A consulta `hive AND (database OR warehouse)` devolve ativos que contêm 'colmeia' e 'base de dados' ou 'armazém', ou ambos. |
| "" | Especifica o conteúdo exato numa frase que a consulta deve corresponder. | A consulta `"hive database"` devolve ativos que contêm a expressão "base de dados de colmeia" nas suas propriedades |
| * | Um wildcard que combina com um a muitos personagens. Não pode ser o primeiro personagem numa palavra-chave. | A consulta `dat*` devolve ativos que têm propriedades que começam com 'dat' como 'dados' ou 'base de dados'. |
| ? | Um wildcard que combina com um único personagem. Não pode ser o primeiro personagem numa palavra-chave | A consulta `dat?` devolve ativos que têm propriedades que começam com 'dat' e são quatro letras como 'data' ou 'dados'. |

> [!Note]
> Especifique sempre os operadores Boolean **(E**, **OU**, **NÃO)** em todas as tampas. Caso contrário, o caso não importa, nem espaços extras.

## <a name="next-steps"></a>Passos seguintes

- [Como criar, importar e exportar termos glossários](how-to-create-import-export-glossary.md)
- [Como gerir modelos de prazo para glossário de negócios](how-to-manage-term-templates.md)
