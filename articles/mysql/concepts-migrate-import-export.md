---
title: Importação e exportação - Base de Dados Azure para o MySQL
description: Este artigo explica formas comuns de importar e exportar bases de dados na Base de Dados Azure para o MySQL, utilizando ferramentas como a MySQL Workbench.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: 049a0ad45ea82210d8fac28db0fb3d067841bba4
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625148"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrar a base de dados MySQL através de importação e exportação

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Este artigo explica duas abordagens comuns para importar e exportar dados para uma Base de Dados Azure para o servidor MySQL utilizando a bancada mySQL Workbench.

Para obter orientações de migração detalhadas e abrangentes, consulte os [recursos do guia de migração.](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) 

Para outros cenários de migração, consulte o [Guia de Migração da Base de Dados.](https://datamigration.microsoft.com/) 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a migrar a sua base de dados MySQL, tem de:
- Criar uma [base de dados Azure para o servidor MySQL utilizando o portal Azure](quickstart-create-mysql-server-database-using-azure-portal.md).
- Descarregue e instale [mySQL Workbench](https://dev.mysql.com/downloads/workbench/) ou outra ferramenta MySQL de terceiros para importação e exportação.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Criar uma base de dados na Base de Dados Azure para o servidor MySQL
Crie uma base de dados vazia na Base de Dados Azure para o servidor MySQL utilizando a bancada mySQL Workbench, Toad ou Navicat. A base de dados pode ter o mesmo nome que a base de dados que contém os dados despejados, ou pode criar uma base de dados com um nome diferente.

Para se conectar, faça o seguinte:

1. No portal Azure, procure as informações de ligação no painel **de visão geral** da sua base de dados Azure para o MySQL.

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Screenshot da base de dados Azure para informações de ligação ao servidor MySQL no portal Azure.":::

1. Adicione as informações de ligação à bancada MySQL Workbench.

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="Cadeia de conexão MySQL Workbench":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Determinar quando utilizar técnicas de importação e exportação

> [!TIP]
> Para cenários em que pretende despejar e restaurar toda a base de dados, use a abordagem [de despejo e restauro.](concepts-migrate-dump-restore.md)

Nos seguintes cenários, utilize ferramentas MySQL para importar e exportar bases de dados na sua base de dados MySQL. Para outras ferramentas, aceda à secção "Métodos de Migração" (página 22) do guia de [migração MySQL para Azure Database](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1.pdf). 

- Quando precisa escolher seletivamente algumas tabelas para importar de uma base de dados MySQL existente para a sua base de dados Azure MySQL, o melhor é usar a técnica de importação e exportação.  Ao fazê-lo, pode omitir quaisquer mesas não precisas da migração para poupar tempo e recursos. Por exemplo, utilize o `--include-tables` ou `--exclude-tables` comutador com [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) e o `--tables` interruptor com [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Quando estiver a mover objetos de base de dados que não sejam tabelas, crie explicitamente esses objetos. Inclua constrangimentos (chave primária, chave estrangeira e índices), pontos de vista, funções, procedimentos, gatilhos e quaisquer outros objetos de base de dados que queira migrar.
- Quando estiver a migrar dados de fontes de dados externas que não uma base de dados MySQL, crie ficheiros planos e importe-os utilizando [o mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

> [!Important]
> Tanto o Servidor Único como o Servidor Flexível suportam *apenas o motor de armazenamento InnoDB*. Certifique-se de que todas as tabelas da base de dados utilizam o motor de armazenamento InnoDB quando estiver a carregar dados na sua base de dados Azure para o MySQL.
>
> Se a sua base de dados de origem utilizar outro motor de armazenamento, converta-se no motor InnoDB antes de migrar a base de dados. Por exemplo, se tiver um WordPress ou uma aplicação web que utilize o motor MyISAM, primeiro converta as tabelas migrando os dados para tabelas InnoDB. Use a cláusula `ENGINE=INNODB` para definir o motor para criar uma tabela e, em seguida, transfira os dados para a tabela compatível antes da migração.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Recomendações de desempenho para importação e exportação

Para um melhor desempenho de importação e exportação de dados, recomendamos que faça o seguinte:
-   Crie índices agrupados e chaves primárias antes de carregar os dados. Carregue os dados na ordem principal da chave.
-   Atrase a criação de índices secundários até ao momento em que os dados são carregados.
-   Desative as restrições de chaves estrangeiras antes de carregar os dados. A desativação de verificações de chaves estrangeiras proporciona ganhos significativos de desempenho. Ative os constrangimentos e verifique os dados após a carga para garantir a integridade referencial.
-   Carregue os dados em paralelo. Evite demasiado paralelismo que o faria atingir um limite de recursos e monitorizar os recursos utilizando as métricas disponíveis no portal Azure.
-   Utilize mesas divisórias quando apropriado.

## <a name="import-and-export-data-by-using-mysql-workbench"></a>Dados de importação e exportação utilizando a bancada mySQL Workbench
Existem duas formas de exportar e importar dados no MySQL Workbench: a partir do menu de contexto do navegador de objetos ou do painel do Navegador. Cada método serve um propósito diferente.

> [!NOTE]
> Se adicionar uma ligação ao MySQL Single Server ou ao Servidor Flexível (Pré-visualização) na bancada mySQL Workbench, faça o seguinte:
> - Para o MySQL Single Server, certifique-se de que o nome de utilizador está no formato *\<username@servername>* .
> - Para o MySQL Flexible Server, utilize *\<username>* apenas. Se utilizar *\<username@servername>* para ligar, a ligação falhará.

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>Executar os assistentes de exportação e importação de dados da tabela a partir do menu de contexto do navegador de objeto

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Screenshot dos comandos de exportação e importação de assistentes mySQL no menu de contexto do navegador de objeto.":::

Os assistentes de dados da tabela suportam operações de importação e exportação utilizando ficheiros CSV e JSON. Os assistentes incluem várias opções de configuração, tais como separadores, seleção de colunas e seleção de codificação. Pode executar cada assistente contra servidores MySQL locais ou ligados remotamente. A ação de importação inclui tabela, coluna e mapeamento de tipo.

Para aceder a estes assistentes a partir do menu de contexto do navegador de objeto, clique com o botão direito de uma tabela e, em seguida, selecione **O Assistente de Exportação de Dados de Tabela** ou Assistente de Importação de **Dados de Tabela**.

#### <a name="the-table-data-export-wizard"></a>O assistente de exportação de dados de tabela

Para exportar uma tabela para um ficheiro CSV:

1. Clique com o botão direito na tabela da base de dados a exportar.
1. Selecione **o assistente de exportação de dados de tabela**. Selecione as colunas a exportar, remar offset (se houver) e conte (se houver).
1. Nos **dados do Select para o painel de exportação,** selecione **Seguinte**. Selecione o caminho do ficheiro, O tipo de ficheiro CSV ou JSON. Selecione também o separador de linha, o método de encadeamento de cordas e o separador de campo.
1. No painel de localização do **ficheiro de saída Select,** selecione **Seguinte**.
1. No painel de dados de **exportação,** selecione **Seguinte**.

#### <a name="the-table-data-import-wizard"></a>O assistente de importação de dados de tabela

Para importar uma tabela a partir de um ficheiro CSV:

1. Clique com o direito na tabela da base de dados a ser importada.
1. Procure e selecione o ficheiro CSV para ser importado e, em seguida, selecione **Next**.
1. Selecione a tabela de destino (nova ou existente), selecione ou limpe a **tabela Truncate antes de importar** caixa de verificação e, em seguida, selecione **Next**.
1. Selecione a codificação e as colunas a importar e, em seguida, selecione **Seguinte**.
1. No painel **de dados de importação,** selecione **Seguinte**. O assistente importa os dados.

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Executar os assistentes de exportação e importação de dados SQL a partir do painel do Navegador
Utilize um assistente para exportar ou importar dados SQL gerados a partir do MySQL Workbench ou do comando mysqldump. Pode aceder aos assistentes a partir do painel do **Navegador** ou pode selecionar o **Servidor** a partir do menu principal.

#### <a name="export-data"></a>Exportar dados

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Screenshot de usar o painel do Navegador para exibir o painel de exportação de dados na bancada MySQL Workbench.":::

Pode utilizar o painel **de exportação de dados** para exportar os seus dados MySQL.

1. Na bancada MySQL Workbench, no painel **do Navegador,** selecione **Data Export**.

1. No painel **data export,** selecione cada esquema que pretende exportar.
 
   Para cada esquema, pode selecionar objetos ou tabelas de esquemas específicos para exportar. As opções de configuração incluem exportação para uma pasta de projeto ou um ficheiro SQL independente, rotinas e eventos armazenados de despejo ou saltar dados de tabela.

   Em alternativa, utilize **exporte um conjunto de resultados** para exportar um conjunto de resultados específico no editor SQL para outro formato, como CSV, JSON, HTML e XML.

1. Selecione os objetos de base de dados para exportar e configuure as opções relacionadas.
1. Selecione **Refresh** para carregar os objetos atuais.
1. Opcionalmente, selecione **Advanced Options** no canto superior direito para refinar a operação de exportação. Por exemplo, adicione bloqueios de mesa, use substituir em vez de inserir declarações e citar identificadores com caracteres retroserentes.
1. Selecione **Start Export** para iniciar o processo de exportação.


#### <a name="import-data"></a>Importar dados

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Screenshot de usar o painel do Navegador para exibir o painel de importação de dados na bancada MySQL Workbench.":::

Pode utilizar o painel **de importação de dados** para importar ou restaurar dados exportados da operação de exportação de dados ou do comando mysqldump.

1. Na bancada MySQL Workbench, no painel **do Navegador,** selecione **Data Export/Restore**.
1. Selecione a pasta do projeto ou o ficheiro SQL autossuficiente, selecione o esquema para importar ou selecione o botão **Novo** para definir um novo esquema.
1. Selecione **Start Import** para iniciar o processo de importação.

## <a name="next-steps"></a>Passos seguintes
- Para outra abordagem de migração, consulte [a base de dados MySQL para uma base de dados Azure para o MySQL utilizando o despejo e o restauro.](concepts-migrate-dump-restore.md)
- Para obter mais informações sobre bases de dados migratórias para uma base de dados Azure para o MySQL, consulte o [Guia de Migração da Base de Dados.](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)
