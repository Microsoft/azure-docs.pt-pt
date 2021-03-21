---
title: Scripts SQL no Estúdio Synapse
description: Introdução aos scripts Sinaapse Studio SQL em Azure synapse Analytics.
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 4ed02901aa0d6948e9c6443e5bbcf4ebfbc872f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98118698"
---
# <a name="synapse-studio-sql-scripts-in-azure-synapse-analytics"></a>Scripts Sinaapse Studio SQL em Azure Synapse Analytics 

O Synapse Studio fornece uma interface web de script SQL para você para autor de consultas SQL. 

## <a name="begin-authoring-in-sql-script"></a>Comece a escrever no script SQL 

Existem várias formas de iniciar a experiência de autoria no script SQL. Pode criar um novo script SQL através de um dos seguintes métodos.

1. A partir do menu Desenvolver, selecione o ícone **"+"** e escolha **o script SQL**.

2. A partir do menu **Ações,** escolha **o novo script SQL.**

3. Escolha **importar** do menu **Ações** em Desenvolvimento de Scripts SQL. Selecione um script SQL existente a partir do seu armazenamento local.
![novo guião sql 3 ações](media/author-sql-script/new-sql-script-3-actions.png)

## <a name="create-your-sql-script"></a>Crie o seu script SQL

1. Escolha um nome para o seu script SQL selecionando o botão **Propriedade** e substituindo o nome predefinido atribuído ao script SQL. 
![novo guião sql renomeado](media/author-sql-script/new-sql-script-rename.png)

2. Escolha a piscina SQL específica ou a piscina SQL sem servidor do menu **Connect para** drop-down. Ou, se necessário, escolha a base de dados da base de **dados Use**. 
![nova piscina de escolha sql](media/author-sql-script/new-sql-choose-pool.png)

3. Comece a escrever o seu script SQL utilizando a funcionalidade intellisense.

## <a name="run-your-sql-script"></a>Execute o seu script SQL

Selecione o botão **Executar** para executar o seu script SQL. Os resultados são apresentados por padrão numa tabela.

![nova tabela de resultados de script sql](media/author-sql-script/new-sql-script-results-table.png)

## <a name="export-your-results"></a>Exporte os seus resultados

Pode exportar os resultados para o seu armazenamento local em diferentes formatos (incluindo CSV, Excel, JSON, XML) selecionando "Resultados de exportação" e escolhendo a extensão.

Também pode visualizar os resultados do script SQL num gráfico selecionando o botão **Gráfico.** Selecione a coluna "Tipo gráfico" e **categoria**. Pode exportar o gráfico para uma imagem selecionando **Guardar como imagem**. 

![novo gráfico de resultados de script sql](media/author-sql-script/new-sql-script-results-chart.png)

## <a name="explore-data-from-a-parquet-file"></a>Explore dados a partir de um ficheiro Parquet

Pode explorar ficheiros Parquet numa conta de armazenamento utilizando o script SQL para visualizar o conteúdo do ficheiro.

![novo script sqlod parquet](media/author-sql-script/new-script-sqlod-parquet.png)

## <a name="sql-tables-external-tables-views"></a>Tabelas SQL, tabelas externas, vistas

Ao selecionar o menu **Ações** nos dados, pode selecionar várias ações, tais como:

- Novo script SQL
- Selecione TOP 1000 linhas
- CREATE
- DROP e CRIAR 
 
Explore o gesto disponível clicando nos nós das bases de dados SQL.
 
![nova base de dados de scripts](media/author-sql-script/new-script-database.png)

## <a name="create-folders-and-move-sql-scripts-into-a-folder"></a>Crie pastas e mova scripts SQL numa pasta

A partir do menu Ações em Desenvolvimento de Scripts SQL Escolha "Nova pasta" do menu "Ações" no Develop SQL scripts. E digite o nome da nova pasta na janela pop-up. 

> [!div class="mx-imgBorder"] 
> ![Screenshot que mostra um exemplo de um script SQL com 'Nova pasta' selecionada.](./media/author-sql-script/new-sql-script-create-folder.png)

Para mover um script SQL para uma pasta, pode selecionar o script sql e escolher "Move To" no menu Ações. Em seguida, encontre a pasta de destino na nova janela e mova o script sql para a pasta selecionada. Também pode arrastar rapidamente o script sql e deixá-lo cair numa pasta.  

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/new-sql-script-move-folder.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a autoria de um script SQL, consulte [a Azure Synapse Analytics](../index.yml).