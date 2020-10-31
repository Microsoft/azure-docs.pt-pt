---
title: Importação e exportação - Base de Dados Azure para o MySQL
description: Este artigo explica formas comuns de importar e exportar bases de dados na Base de Dados Azure para o MySQL, utilizando ferramentas como a MySQL Workbench.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: 1b4959cbf082a589c90034f48d597907c9b7e6cc
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128934"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrar a sua base de dados MySQL utilizando a importação e exportação
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]
Este artigo explica duas abordagens comuns para importar e exportar dados para uma Base de Dados Azure para o servidor MySQL utilizando a bancada mySQL Workbench.

Também pode consultar o [Guia de Migração da Base de Dados](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) para obter informações detalhadas e utilizar casos sobre bases de dados migratórias para a Base de Dados Azure para o MySQL. Este guia fornece orientações que conduzirão ao planeamento e execução bem-sucedidos de uma migração MySQL para Azure.

## <a name="before-you-begin"></a>Antes de começar
Para passar por este guia, precisa:
- Uma base de dados Azure para o servidor MySQL, seguindo [a Criação de uma Base de Dados Azure para servidor MySQL utilizando o portal Azure](quickstart-create-mysql-server-database-using-azure-portal.md).
- [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) ou outra ferramenta mySQL de terceiros para fazer a importação/exportação.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Criar uma base de dados na Base de Dados Azure para o servidor MySQL
Crie uma base de dados vazia na Base de Dados Azure para o servidor MySQL utilizando a bancada mySQL Workbench, Toad ou Navicat para criar a base de dados. A base de dados pode ter o mesmo nome que a base de dados que contém os dados despejados, ou pode criar uma base de dados com um nome diferente.

Para se conectar, localize as informações de ligação na **visão geral** da sua Base de Dados Azure para o MySQL.

:::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Encontre as informações de ligação no portal Azure":::

Adicione as informações de ligação à bancada MySQL Workbench.

:::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="Encontre as informações de ligação no portal Azure":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Determinar quando utilizar técnicas de importação e exportação

> [!TIP]
> Para cenários em que pretende despejar e restaurar toda a base de dados, deve usar a abordagem [de despejo e restauro.](concepts-migrate-dump-restore.md)

Utilize ferramentas MySQL para importar e exportar bases de dados para a base de dados Azure MySQL nos seguintes cenários.

- Quando precisa escolher seletivamente algumas tabelas para importar de uma base de dados MySQL existente para a Base de Dados Azure MySQL, o melhor é usar a técnica de importação e exportação.  Ao fazê-lo, pode omitir quaisquer mesas não precisas da migração para poupar tempo e recursos. Por exemplo, utilize o `--include-tables` ou `--exclude-tables` comutador com [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) e o `--tables` interruptor com [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Quando estiver a mover os objetos da base de dados que não sejam tabelas, crie explicitamente esses objetos. Inclua constrangimentos (chave primária, chave estrangeira, índices), vistas, funções, procedimentos, gatilhos e quaisquer outros objetos de base de dados que queira migrar.
- Quando estiver a migrar dados de fontes de dados externas que não uma base de dados MySQL, crie ficheiros planos e importe-os utilizando [o mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

> [!Important]
> Tanto o servidor único como o servidor flexível suportam **apenas o motor de armazenamento InnoDB** . Certifique-se de que todas as tabelas da base de dados utilizam o motor de armazenamento InnoDB quando estiver a carregar dados na Base de Dados Azure para o MySQL.
> Se a sua base de dados de origem utilizar outro motor de armazenamento, por favor converta-se no motor InnoDB antes de migrar a base de dados. Por exemplo, se tiver um WordPress ou uma aplicação web que utilize o motor MyISAM, primeiro converta as tabelas migrando os dados para tabelas InnoDB. Use a cláusula `ENGINE=INNODB` para definir o motor para criar uma tabela e, em seguida, transfira os dados para a tabela compatível antes da migração.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Recomendações de desempenho para importação e exportação
-   Crie índices agrupados e chaves primárias antes de carregar dados. Carregue os dados na ordem principal das chaves.
-   Adiar a criação de índices secundários até depois de os dados estarem carregados. Crie todos os índices secundários após o carregamento.
-   Desative os constrangimentos de chaves estrangeiras antes de carregar. A desativação de verificações de chaves estrangeiras proporciona ganhos significativos de desempenho. Ative os constrangimentos e verifique os dados após a carga para garantir a integridade referencial.
-   Carregue os dados em paralelo. Evite demasiado paralelismo que o faria atingir um limite de recursos e monitorizar os recursos utilizando as métricas disponíveis no portal Azure.
-   Utilize mesas divisórias quando apropriado.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importação e exportação utilizando a bancada mySQL Workbench
Existem duas formas de exportar e importar dados na bancada mySQL Workbench. Cada um serve um propósito diferente.

> [!NOTE]
> Se estiver a adicionar uma ligação ao servidor MySQL Single ou Flexible (Preview) na bancada mySQL Workbench, certifique-se de que:
> - Para o mySQL Single server, o nome de utilizador deve estar neste formato username@servername , '
> - Para o servidor flexível MySQL, pode utilizar o 'username' Se utilizar username@servername ' para ligar, a ligação falhará.

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Os assistentes de exportação e importação de dados de tabela do menu de contexto do navegador de objeto
:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Encontre as informações de ligação no portal Azure":::

Os assistentes para as operações de importação e exportação de dados de tabelas utilizando ficheiros CSV e JSON. Incluem várias opções de configuração, tais como separadores, seleção de colunas e seleção de codificação. Pode executar cada assistente contra servidores MySQL locais ou ligados remotamente. A ação de importação inclui tabela, coluna e mapeamento de tipo.

Pode aceder a estes assistentes a partir do menu de contexto do navegador de objeto clicando à direita numa tabela. Em seguida, escolha o **Assistente de Exportação de Dados de Tabela** ou o Assistente de Importação de **Dados de Tabela** .

#### <a name="table-data-export-wizard"></a>Assistente de exportação de dados de tabela
O exemplo a seguir exporta a tabela para um ficheiro CSV:
1. Clique com o botão direito na tabela da base de dados a exportar.
2. Selecione **o assistente de exportação de dados de tabela** . Selecione as colunas a exportar, remar offset (se houver) e conte (se houver).
3. Na **página Select para exportação,** clique em **Seguinte** . Selecione o caminho do ficheiro, O tipo de ficheiro CSV ou JSON. Selecione também o separador de linha, o método de encadeamento de cordas e o separador de campo.
4. Na página de localização do **ficheiro de saída Select,** clique em **Seguinte** .
5. Na página **de dados de Exportação,** clique em **Seguinte** .

#### <a name="table-data-import-wizard"></a>Assistente de importação de dados de tabela
O exemplo a seguir importa o quadro a partir de um ficheiro CSV:
1. Clique com o direito na tabela da base de dados a ser importada.
2. Navegue e selecione o ficheiro CSV a ser importado e, em seguida, clique em **Seguinte** .
3. Selecione a tabela de destino (nova ou existente) e selecione ou limpe a **tabela Truncate antes de importar** caixa de verificação. Clique em **Seguinte** .
4. Selecione codificação e as colunas a importar e, em seguida, clique em **Seguinte** .
5. Na página **de dados de Importação,** clique em **Seguinte** . O assistente importa os dados em conformidade.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>SqL data export and import wizards from the Navigator pane
Utilize um assistente para exportar ou importar SQL gerado a partir do MySQL Workbench ou gerado a partir do comando mysqldump. Aceda a estes assistentes a partir do painel **do Navegador** ou selecionando o **Servidor** a partir do menu principal. Em seguida, selecione **Data Export** ou **Data Import** .

#### <a name="data-export"></a>Exportação de Dados
:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Encontre as informações de ligação no portal Azure":::

Pode utilizar o **separador Data Export** para exportar os seus dados MySQL.
1. Selecione cada esquema que pretende exportar, escolha opcionalmente objetos/tabelas de esquemas específicos de cada esquema e gere a exportação. As opções de configuração incluem exportação para uma pasta de projeto ou ficheiro SQL independente, rotinas e eventos armazenados de despejo ou saltar dados de tabela.

   Em alternativa, utilize **exporte um conjunto de resultados** para exportar um conjunto de resultados específico no editor SQL para outro formato, como CSV, JSON, HTML e XML.
3. Selecione os objetos de base de dados para exportar e configuure as opções relacionadas.
4. Clique **em Refresh** para carregar os objetos atuais.
5. Opcionalmente, abra o separador **Opções Avançadas** para refinar a operação de exportação. Por exemplo, adicione bloqueios de mesa, use substituir em vez de inserir declarações e citar identificadores com caracteres retroserentes.
6. Clique **em Iniciar Exportação** para iniciar o processo de exportação.


#### <a name="data-import"></a>Importação de Dados
:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Encontre as informações de ligação no portal Azure":::

Pode utilizar o separador **Data Import** para importar ou restaurar os dados exportados da operação de exportação de dados ou do comando mysqldump.
1. Escolha a pasta do projeto ou o ficheiro SQL independente, escolha o esquema para importar ou escolha **Novo** para definir um novo esquema.
2. Clique **em Iniciar Importação** para iniciar o processo de importação.

## <a name="next-steps"></a>Passos seguintes
- Como outra abordagem de migração, leia [Migrar a sua base de dados MySQL usando o despejo e restaurar na Base de Dados Azure para o MySQL](concepts-migrate-dump-restore.md).
- Para obter mais informações sobre bases de dados migratórias para a Base de Dados Azure para o MySQL, consulte o [Guia de Migração da Base de Dados.](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)