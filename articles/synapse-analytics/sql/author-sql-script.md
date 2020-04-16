---
title: Scripts SQL no Estúdio Azure Synapse (pré-visualização)
description: Introdução Azure Synapse Studio (pré-visualização) Scripts SQL
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431075"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Utilização do script SQL no Estúdio Azure Synapse (pré-visualização)

O Azure Synapse Studio (pré-visualização) fornece uma interface web de script SQL para que possa escrever consultas SQL. Pode ligar-se ao pool SQL (pré-visualização) ou ao SQL on-demand (pré-visualização). 

## <a name="begin-authoring-in-sql-script"></a>Comece a autoria no script SQL 

Existem várias formas de iniciar a experiência de autoria no script SQL. Pode criar um novo script SQL através de um dos seguintes métodos.

1. Selecione o ícone "+" e escolha o script SQL.

    > [!div class="mx-imgBorder"] 
    >![newsqlscript](./media/author-sql-script/newsqlscript.png)

2. A partir do menu Ações sob scripts SQL de desenvolvimento Escolha "Novo script SQL" do menu "Actions" sob scripts SQL de desenvolvimento. 

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript2actions.png)

ou 

3. Escolha "Importar" do menu "Ações" em termos de scripts SQL e selecione um script SQL existente a partir do seu armazenamento local.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Crie o seu script SQL

1. Escolha um nome para o seu script SQL selecionando o botão "Propriedade" e substituindo o nome predefinido atribuído ao script SQL.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. Escolha a piscina SQL específica ou o SQL on-demand do menu "Connect to" drop-down. Ou, se necessário, escolha a base de dados de "Use base de dados".

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. Comece a ser autor do seu script SQL utilizando a funcionalidade intellisense.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Execute o seu script SQL

Selecione o botão "Executar" para executar o seu script SQL. Os resultados são apresentados por defeito numa tabela.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Exportar os seus resultados

Pode exportar os resultados para o seu armazenamento local em diferentes formatos (incluindo CSV, Excel, JSON, XML) selecionando "Resultados de Exportação" e escolhendo a extensão.

Também pode visualizar os resultados do script SQL num gráfico selecionando o botão "Chart". Selecione o "Tipo gráfico" e "Coluna de categoria". Pode exportar o gráfico para uma imagem selecionando "Guardar como imagem". 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Explore dados de um ficheiro Parquet.

Pode explorar ficheiros Parquet numa conta de armazenamento utilizando script SQL para pré-visualizar o conteúdo do ficheiro. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>Mesas SQL, tabelas externas, vistas

Ao selecionar o menu "Ações" em dados, pode selecionar várias ações como: "Novo script SQL", "Selecione top 1000 linhas", "CREATE", "DROP and CREATE". Explore o gesto disponível clicando nos nódosos da piscina SQL e da SQL a pedido.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a autoria de um script SQL, consulte [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).