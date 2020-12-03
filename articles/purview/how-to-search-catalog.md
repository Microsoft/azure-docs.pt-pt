---
title: 'Como: pesquisar o Catálogo de Dados'
description: Este artigo dá uma visão geral de como pesquisar um catálogo de dados.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/15/2020
ms.openlocfilehash: 87ca3c49d6a96f083cc50f01f5b9a4a739d688a1
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554027"
---
# <a name="search-the-azure-purview-data-catalog"></a>Pesse o Catálogo de Dados da Azure Purview

Este artigo descreve como utilizar as várias funcionalidades de pesquisa no Catálogo de Dados do Azure Purview.

## <a name="search-the-catalog-for-assets"></a>Procure no catálogo por ativos

Os passos para realizar uma pesquisa de ativos são:

1. [Abra o diálogo de pesquisa de ativos](#open-the-asset-search-dialog) selecionando catálogo de **pesquisa**.
1. [Introduza termos de pesquisa](#enter-search-terms) para encontrar ativos com características que correspondam aos termos.
1. [Desatrei os filtros rápidos](#set-quick-filters) para reduzir a procura.
1. [Inicie a pesquisa](#start-the-search) e vá para os resultados da pesquisa.

Não importa se define filtros rápidos antes ou depois de introduzir os termos de pesquisa.

Se não houver termos de pesquisa e filtros, os resultados da pesquisa incluem todos os ativos.

### <a name="open-the-asset-search-dialog"></a>Abra o diálogo de pesquisa de ativos

Abra o diálogo de pesquisa de ativos selecionando **catálogo de pesquisa**.

:::image type="content" source="./media/how-to-search-catalog/search-catalog.png" alt-text="Abaixo do 'catálogo de pesquisa' há um painel esquerdo com filtros de pesquisa, e um painel direito com pesquisas recentes." border="true":::

O diálogo de pesquisa mostra filtros rápidos, histórico de pesquisa e uma lista de ativos recentemente acedidos.

:::image type="content" source="./media/how-to-search-catalog/asset-search-dialog.png" alt-text="A lista de pesquisa está no painel certo, abaixo do 'Catálogo de Pesquisa'." border="true":::

### <a name="enter-search-terms"></a>Insira termos de pesquisa

Introduza um ou mais termos de pesquisa no **catálogo Search.** À medida que escreve, os termos de pesquisa correspondentes de pesquisas recentes estão listados nas **Suas pesquisas recentes,** os termos de pesquisa sugeridos estão listados nas sugestões de Pesquisa , e os ativos de **dados correspondentes** estão listados em **sugestões de Ativos.**

:::image type="content" source="./media/how-to-search-catalog/enter-search-terms.png" alt-text="Screenshot mostrando os resultados de uma pesquisa sendo introduzido na caixa de catálogo de pesquisa":::

Os resultados da pesquisa incluem apenas ativos com uma ou mais características que correspondam aos termos de pesquisa. Estas características incluem nome do ativo, tipo de ativo, classificações e contactos.

#### <a name="types-of-search-criteria"></a>Tipos de critérios de pesquisa

O Azure Purview suporta os seguintes tipos de critérios de pesquisa.

> [!Note]
> Especifique sempre os operadores Boolean **(E**, **OU**, **NÃO)** em todas as tampas. Caso contrário, o caso não importa, nem espaços extras.

- **colmeia**: Encontre documentos que contenham **colmeia.**
- **base de dados da colmeia**: Encontre documentos que contenham exatamente **bases de dados de colmeias.**
- **base de dados :Encontre** documentos que contenham **colmeia** ou **base de dados,** ou ambos.
- **base de dados de colmeias e bases de dados** **, colmeia && base de dados**: Encontre documentos que contenham **colmeia** e base **de dados.**
- **colmeia e (base de dados ou armazém)**: Encontre documentos que contenham **colmeia** e **base de dados** ou **armazém,** ou ambos.
- **base de dados NÃO:** Encontre documentos que contenham **colmeia,** mas não **base de dados**.
- **hiv**: Encontre documentos que contenham uma palavra que comece com **o hiv**. Por exemplo, **hiv,** **colmeia,** **hivbar** (* é um wild card que combina com qualquer número de caracteres).

### <a name="set-quick-filters"></a>Definir filtros rápidos

A lista de resultados da pesquisa baseia-se nos termos de pesquisa que introduz no **catálogo De pesquisa,** e nos valores que seleciona para os filtros rápidos.

Um filtro rápido limita a lista de resultados de pesquisa a ativos que têm um valor selecionado de uma característica. O filtro tem uma lista de desistências e uma caixa de texto. A lista de down-down mostra valores da característica que estão nos resultados de pesquisa *atuais.* Ao lado de cada valor da lista está uma contagem do número de ativos nos resultados de pesquisa atuais que têm esse valor. Se selecionar um valor da lista, os resultados da pesquisa serão restritos a ativos com esse valor. Só pode selecionar um valor.

Os resultados de pesquisa atuais utilizados na formação da lista de abandono são determinados por:

- Os termos de pesquisa que estão inseridos no **catálogo Search.** 
- Os valores selecionados nos filtros rápidos.

Aqui está um exemplo do filtro rápido "Tipo de ativo".

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="O exemplo do filtro rápido do tipo de ativo." border="true":::

Pode introduzir texto na caixa de texto para restringir os valores da lista de recuos aos valores que correspondam ou correspondam parcialmente ao texto. Para, por exemplo, utilizar a caixa de texto, consulte [o filtro rápido de pesquisa: filtro por tipo de ativo,](#search-quick-filter-filter-by-asset-type)e Procure filtro [rápido: filtro por classificação](#search-quick-filter-filter-by-classification).

#### <a name="search-quick-filter-filter-by-asset-type"></a>Procure filtro rápido: filtro por tipo de ativo

Para filtrar por tipo de ativo, utilize o filtro rápido **do tipo Ativo.** A lista de down-down mostra os tipos de ativos encontrados nos resultados de pesquisa atuais, conforme determinado pelos termos de pesquisa e pelos filtros rápidos. Para cada tipo, é indicado o número de ativos desse tipo.

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="Destacam-se os filtros rápidos do tipo de ativo. Mostra tipos de ativos, e uma contagem para cada um." border="true":::

Selecione um tipo de ativo para restringir os resultados de pesquisa a ativos desse tipo. Só pode selecionar um tipo.

Para mostrar apenas tipos de ativos cujos nomes correspondem a uma corda, introduza a cadeia na caixa de texto. Por exemplo, para mostrar apenas tipos de ativos com **sql** em seus nomes, insira **sql**.

:::image type="content" source="./media/how-to-search-catalog/filter-asset-types.png" alt-text="O painel Quick Filters tem sql para 'Tipo de activo'. A lista de ativos que contêm sql mostra três entradas." border="true":::

Selecione um tipo de ativo para restringir os resultados de pesquisa a ativos desse tipo. Pode selecionar apenas um tipo.

#### <a name="search-quick-filter-filter-by-classification"></a>Pesquisar filtro rápido: filtro por classificação

Para filtrar por classificação de ativos, utilize o filtro rápido **de classificação.** A lista de down-down mostra as classificações que foram atribuídas a um ou mais ativos nos resultados de pesquisa atuais, conforme determinado pelos termos de pesquisa e pelos filtros rápidos. Para cada classificação, é indicado o número de ativos atribuídos a essa classificação.

:::image type="content" source="./media/how-to-search-catalog/classification-quick-filter.png" alt-text="Destacam-se os filtros rápidos de classificação." border="true":::

Selecione uma classificação para restringir os resultados de pesquisa aos ativos atribuídos a essa classificação. Só pode selecionar uma classificação.

Para mostrar apenas classificações cujos nomes correspondem a uma corda, introduza a corda na caixa de texto. Por exemplo, para mostrar apenas classificações com **número** nos seus nomes, **introduza o número**.

:::image type="content" source="./media/how-to-search-catalog/filter-classifications.png" alt-text="No painel quick filters, a classificação é 'banco', e as classificações listadas contêm esse valor." border="true":::

Selecione uma classificação para restringir os resultados de pesquisa a ativos que foram atribuídos a essa classificação. Não se pode selecionar mais do que uma classificação.

#### <a name="search-quick-filter-filter-by-contacts"></a>Procure filtro rápido: filtro por contactos

Um *contacto* é uma pessoa que é atribuída a um ativo como proprietário ou perito. Quando vê os dados do ativo, os contactos são apresentados no separador **Contactos.**

Há duas formas de procurar ativos que tenham um contacto particular que lhes seja atribuído.

- Introduza a all ou parte do nome de contacto no **catálogo Search** e faça uma pesquisa. Os resultados da pesquisa incluirão ativos que tenham contactos cujos nomes correspondam aos seus termos de pesquisa.
- Selecione o contacto de interesse no filtro rápido **de contacto** e faça uma pesquisa.

:::image type="content" source="./media/how-to-search-catalog/contact-quick-filter.png" alt-text="O valor de Pessoa no painel de filtros rápidos é 'darren'. Há três sugestões no painel de sugestões." border="true":::

## <a name="search-example"></a>Exemplo de pesquisa

Vamos considerar um exemplo hipotético para ver como os termos de pesquisa e filtros rápidos interagem para determinar os resultados da pesquisa. Em particular, vamos monitorizar a contagem de ativos tipo **Azure Blob Storage**.

- Fazemos a primeira pesquisa sem termos de pesquisa inseridos e sem valores selecionados nos filtros rápidos. A pesquisa encontra todos os bens no catálogo. A lista de resultados de pesquisa e o filtro rápido **tipo Ativo** revelam:

    - A lista de resultados de pesquisa tem 164.230 ativos, que são todos os ativos do catálogo.
    - A lista de drop-down **do tipo Ativo** tem 43 entradas. Estes são todos os tipos de ativos no catálogo. Uma vez que cada ativo é de um e único tipo, a soma das contagens dos 43 tipos de ativos é de 164.230.
    - O tipo de ativo **Azure Blob Storage** é a primeira entrada na lista de down-down do filtro rápido **tipo Ativo.** Os valores são encomendados por contagem, o maior primeiro, por isso **o Azure Blob Storage** é o tipo de ativo mais comum. A sua contagem é de 118.174.

- Entramos agora no **catálogo** **Search** e fazemos outra pesquisa. Os resultados da pesquisa incluem apenas ativos com características que correspondam **ao parquet.** Isto reduz todas as contagens, da seguinte forma:

    - A lista de resultados da pesquisa tem 493 ativos. Apenas 493 dos 164.230 ativos do catálogo têm características que correspondem a "parquet".
    - A lista de drop-down **do tipo Ativo** tem 15 entradas. Cada um dos 493 ativos é de um destes 15 tipos, e a soma das contagens dos 15 tipos é de 493.
    - Existem 456 ativos do tipo **Azure Blob Storage**. Os outros 37 (493 menos 456) ativos são de um dos outros 14 tipos.

- Agora olhamos para a lista de um filtro rápido diferente, **Classificação:**

    - Existem 12 classificações para os 493 ativos na lista de resultados de pesquisa. As contagens para os 493 ativos não se resumem a 493, uma vez que qualquer número de classificações pode ser atribuída a um ativo.
    - A classificação **do Nome da Pessoa** é atribuída a 36 ativos, mais do que qualquer outra classificação.

- Selecionamos a classificação **do Nome da Pessoa.** A lista de resultados de pesquisa cai para 36 ativos, como esperado desde que a contagem para o **Nome da Pessoa** foi de 36. Nenhum destes resultados é do tipo **Azure Blob Storage**.

Podemos concluir que não há nenhum ativo cujo tipo seja **Azure Blob Storage** que **corresponda ao parquet,** e que tenha uma classificação **do Nome da Pessoa.**

## <a name="start-the-search"></a>Inicie a pesquisa

Quando procura, os termos de pesquisa que introduz no **catálogo Search** são correspondidos às características do ativo. Estas características incluem nome, tipo, classificação e contactos. Os ativos com características correspondentes aparecem na lista de resultados de pesquisa, a menos que sejam excluídos por um filtro rápido.

Depois de introduzir os termos de pesquisa e definir os filtros rápidos, inicie a pesquisa de uma destas formas:

- Para pesquisar com base nos termos introduzidos, selecione o ícone de pesquisa ( :::image type="icon" source="./media/how-to-search-catalog/search-icon.png"::: ), prima **Introdução** ou selecione **Ver os resultados da pesquisa**.
- Para pesquisar usando termos de uma pesquisa anterior, selecione-os a partir das **suas pesquisas recentes.**
- Para pesquisar usando os termos sugeridos, selecione-os a partir de **sugestões** de Pesquisa .

Selecione um ativo a partir de **sugestões** de Ativos para ir diretamente para a página de detalhes para o ativo. Nenhuma busca está feita.

A lista de resultados para sugestões e pesquisas de utilizadores pode diferir ligeiramente. Os resultados na lista de sugestões baseiam-se em correspondências difusas, enquanto os resultados de pesquisa iniciados pelo utilizador são baseados em correspondências exatas.

Quando pesquisa, a página **de resultados de Pesquisa** aparece e lista os ativos encontrados pela pesquisa.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Screenshot mostrando os resultados da pesquisa para um valor de pesquisa de contoso.":::

Para ver os detalhes do ativo, selecione um nome de ativo.

Utilize os controlos na parte inferior de uma página de resultados de pesquisa para navegar para outras páginas de resultados de pesquisa.

:::image type="content" source="./media/how-to-search-catalog/page-navigation.png" alt-text="Screenshot mostrando como navegar através das páginas de resultados de pesquisa.":::

### <a name="sort-search-results"></a>Ordenar resultados de pesquisa

Use **ordenar por** para classificar os resultados da pesquisa por **relevância** ou **nome**.

:::image type="content" source="./media/how-to-search-catalog/sort-by.png" alt-text="Screenshot mostrando como classificar os resultados da pesquisa. Para este exemplo, a lista de dropdown do Sort por dropdown está definida como Relevância.":::

### <a name="search-results-dynamic-filters"></a>Filtros dinâmicos de resultados de pesquisa

O painel **de filtros** na página **de resultados da Pesquisa** tem filtros que fornecem filtragem dinâmica dos ativos na lista de resultados de pesquisa. A filtragem é dinâmica na medida em que filtros adicionais podem aparecer com base em seleções de filtros.

Os filtros dinâmicos têm uma caixa de verificação para cada valor na lista de espera. Utilize estas caixas de verificação para filtrar os valores que quiser.

#### <a name="search-results-dynamic-filter-filter-by-asset-type"></a>Filtro dinâmico de resultados de pesquisa: filtro por tipo de ativo

Se selecionar um tipo de ativo na lista de drop-down **do tipo Ativo,** aparecem filtros dinâmicos que lhe dão formas adicionais de reduzir os resultados da sua pesquisa. Os filtros variam consoante o tipo de ativo selecionado. Por exemplo, se selecionar **Azure SQL Database,** aparecem filtros dinâmicos para servidor, base de dados e esquemas. Os valores nestes filtros são dos ativos nos resultados de pesquisa do tipo selecionado.

:::image type="content" source="./media/how-to-search-catalog/asset-type-dynamic-filter.png" alt-text="O item de filtro Azure SQL Database é o único item 'Tipo de activo' selecionado. Um resultado de pesquisa desse tipo de ativo também é destacado." border="true":::

#### <a name="search-results-dynamic-filter-filter-by-classification"></a>Filtro dinâmico dos resultados de pesquisa: filtro por classificação

Cada classificação na lista **de classificação** aplica-se a pelo menos um item na lista de resultados de pesquisa. Selecione uma ou mais classificações para reduzir os seus resultados de pesquisa a ativos das classificações selecionadas.

:::image type="content" source="./media/how-to-search-catalog/classification-dynamic-filter.png" alt-text="Destaca-se o filtro de classificação dos &quot;resultados de pesquisa&quot;." border="true":::

#### <a name="edit-and-delete-search-results-filters"></a>Editar e eliminar filtros de resultados de pesquisa

Para remover um filtro, limpe a caixa de verificação ao lado do nome do filtro.

### <a name="recently-accessed-assets"></a>Ativos recentemente acedidos

A secção **recentemente acedida** da caixa de pesquisa expandida exibe os seus ativos mais recentemente acedidos, se houver.

- Selecione **Ver tudo** na secção **recentemente acedida** para ver a lista completa de ativos recentemente acedidos.

   :::image type="content" source="./media/how-to-search-catalog/get-to-recent-view.png" alt-text="Imagem de ecrã mostrando a secção recentemente acedida da caixa de pesquisa expandida.":::

   Aparece uma lista de ativos recentemente acedidos.

   :::image type="content" source="./media/how-to-search-catalog/recent.png" alt-text="Screenshot mostrando uma lista de ativos recentemente acedidos.":::

- Para filtrar pelo nome, introduza uma cadeia de pesquisa no **Filtro pelo nome**.

- Para remover itens da lista, selecione-os com as suas caixas de verificação e, em seguida, selecione **Remover**.

   :::image type="content" source="./media/how-to-search-catalog/remove-from-recent-view.png" alt-text="Screenshot mostrando como remover itens de uma lista de ativos recentemente acedidos.":::

- Para limpar toda a lista, selecione **Clear**.

   :::image type="content" source="./media/how-to-search-catalog/clear-recent-view-selections.png" alt-text="Screenshot mostrando como limpar uma lista de ativos recentemente acedidos":::

### <a name="search-assets"></a>Pesquisar ativos

Muitas páginas que **não o Home** têm uma caixa de ativos de **busca** no topo. Por exemplo, aqui está uma página de detalhes de ativos, com **os ativos de pesquisa** destacados.

:::image type="content" source="./media/how-to-search-catalog/search-assets.png" alt-text="Screenshot mostrando uma página de detalhes do ativo com ativos de pesquisa destacados":::

Selecione **procurar os ativos** para lançar uma caixa de pesquisa como a que obtém do catálogo **Search** on **Home,** com as mesmas capacidades.

:::image type="content" source="./media/how-to-search-catalog/search-assets-dialog.png" alt-text="Screenshot mostrando uma caixa de ativos de pesquisa expandida.":::

## <a name="next-steps"></a>Passos seguintes

- [Como criar, importar e exportar termos glossários](how-to-create-import-export-glossary.md)
- [Como gerir modelos de prazo para glossário de negócios](how-to-manage-term-templates.md)
