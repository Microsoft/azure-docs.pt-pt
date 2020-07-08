---
title: Scripts SQL no Azure Synapse Studio (pré-visualização)
description: Introdução Azure Synapse Studio (pré-visualização) scripts SQL
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: ee384d6095ccbf25225a435fe8afe4281c5d62df
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921391"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Usando o script SQL no Azure Synapse Studio (pré-visualização)

O Azure Synapse Studio (pré-visualização) fornece uma interface web de script SQL para você para autor de consultas SQL. Pode ligar-se à piscina SQL (pré-visualização) ou ao SQL a pedido (pré-visualização). 

## <a name="begin-authoring-in-sql-script"></a>Comece a escrever no script SQL 

Existem várias formas de iniciar a experiência de autoria no script SQL. Pode criar um novo script SQL através de um dos seguintes métodos.

1. A partir do menu Desenvolver, selecione o ícone **"+"** e escolha **o script SQL**.

    ![newsqlscript](media/author-sql-script/newsqlscript.png)

2. A partir do menu **Ações,** escolha **o novo script SQL.**
    
    ![newsqlscript2actions](media/author-sql-script/newsqlscript2actions.png)

Em alternativa, pode: 

3. Escolha **importar-se** do menu **Ações** em Desenvolvimento de Scripts SQL e selecione um script SQL existente a partir do seu armazenamento local.
 
    ![newsqlscript3actions](media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Crie o seu script SQL

1. Escolha um nome para o seu script SQL selecionando o botão **Propriedade** e substituindo o nome predefinido atribuído ao script SQL.
  
    ![newsqlscriptrename](media/author-sql-script/newsqlscriptrename.png)

2. Escolha a piscina SQL específica ou o SQL a pedido do menu **Connect para** drop-down. Ou, se necessário, escolha a base de dados da base de **dados Use**.
 
    ![newsqlchoosepool](media/author-sql-script/newsqlchoosepool.png)

3. Comece a escrever o seu script SQL utilizando a funcionalidade intellisense.

    ![newsqlintellisense](media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Execute o seu script SQL

Selecione o botão **Executar** para executar o seu script SQL. Os resultados são apresentados por padrão numa tabela.

![newsqlscriptresultstable](media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Exporte os seus resultados

Pode exportar os resultados para o seu armazenamento local em diferentes formatos (incluindo CSV, Excel, JSON, XML) selecionando "Resultados de exportação" e escolhendo a extensão.

Também pode visualizar os resultados do script SQL num gráfico selecionando o botão **Gráfico.** Selecione a coluna "Tipo gráfico" e **categoria**. Pode exportar o gráfico para uma imagem selecionando **Guardar como imagem**. 

![newsqlscriptresultschart](media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Explore dados a partir de um ficheiro Parquet

Pode explorar ficheiros Parquet numa conta de armazenamento utilizando o script SQL para visualizar o conteúdo do ficheiro.

![newscriptsqlodparquet](media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>Tabelas SQL, tabelas externas, vistas

Ao selecionar o menu **Ações** nos dados, pode selecionar várias ações, tais como:

- Novo script SQL
- Selecione TOP 1000 linhas
- CRIAR
- DROP e CRIAR 
 
Explore o gesto disponível clicando à direita nos nós da piscina SQL e SQL a pedido.
 
![base de dados de novosscript](media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a autoria de um script SQL, consulte [a Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).
