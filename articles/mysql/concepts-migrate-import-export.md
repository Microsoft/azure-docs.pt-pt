---
title: Importar e exportar na base de dados do Azure para MySQL
description: Este artigo explica as formas comuns de importar e exportar bases de dados na base de dados do Azure para MySQL, utilizando ferramentas como o MySQL Workbench.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/01/2018
ms.openlocfilehash: fa72037c8f54271f5651667765c5d5e2e9c03619
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60838120"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrar a sua base de dados MySQL através de importar e exportar
Este artigo explica as duas abordagens comuns para importar e exportar dados para uma base de dados do Azure para o servidor MySQL com o MySQL Workbench. 

## <a name="before-you-begin"></a>Antes de começar
Para seguir este guia de procedimentos, terá de:
- Uma base de dados do Azure para o servidor MySQL, ao seguir [criar uma base de dados do Azure para o servidor MySQL com o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md).
- MySQL Workbench [transferido](https://dev.mysql.com/downloads/workbench/), ou outra ferramenta de MySQL para importar e exportar.

## <a name="use-common-tools"></a>Utilize ferramentas comuns
Utilize ferramentas comuns, como o MySQL Workbench, Toad ou Navicat remotamente ligar e importar ou exportar dados na base de dados do Azure para MySQL. 

Utilize essas ferramentas no seu computador cliente com uma ligação à Internet para ligar à base de dados do Azure para MySQL. Utilizar uma ligação encriptada por SSL para melhores práticas de segurança, conforme descrito em [configurar a conectividade SSL na base de dados do Azure para MySQL](concepts-ssl-connection-security.md).

Não é necessário mover a importar e exportar ficheiros em qualquer local na cloud especiais ao migrar a base de dados do Azure para MySQL. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Criar uma base de dados na base de dados do Azure para o servidor MySQL
Crie uma base de dados vazia na base de dados do Azure para o servidor MySQL onde pretende migrar os dados. Utilize uma ferramenta como o MySQL Workbench, Toad ou Navicat para criar a base de dados. A base de dados pode ter o mesmo nome que a base de dados que contém os dados capturados, ou pode criar uma base de dados com um nome diferente.

Para entrar em contato, localize as informações de ligação no **descrição geral** da base de dados do Azure para MySQL.

![Localizar as informações de ligação no portal do Azure](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

Adicione as informações de ligação para o MySQL Workbench.

![Cadeia de ligação do MySQL Workbench](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Determinar quando deve utilizar a importação e exportação técnicas em vez de um despejo e restaurar
Utilize ferramentas do MySQL para importar e exportar bases de dados na base de dados do Azure MySQL nos seguintes cenários. Em outros casos, poderá se beneficiar de utilizar o [capturar e restaurar](concepts-migrate-dump-restore.md) abordar em vez disso. 

- Quando precisa escolher seletivamente algumas tabelas para importar uma base de dados existente do MySQL para a base de dados do Azure MySQL, é melhor utilizar a importação e exportação técnica.  Ao fazê-lo, pode omitir quaisquer tabelas desnecessárias da migração para poupar tempo e recursos. Por exemplo, utilizar o `--include-tables` ou `--exclude-tables` mudar com [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) e o `--tables` mudar com [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Quando estiver a mover os objetos de base de dados que não são tabelas, crie explicitamente esses objetos. Inclua restrições (chave primária, chave externa, índices), vistas, funções, procedimentos, acionadores e quaisquer outros objetos de base de dados que pretende migrar.
- Quando estiver a migrar dados de origens de dados externos que não seja uma base de dados do MySQL, criar arquivos simples e importá-los usando [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Certifique-se de que todas as tabelas na base de dados usar o mecanismo de armazenamento InnoDB quando está carregando dados na base de dados do Azure para MySQL. Base de dados do Azure para MySQL suporta apenas o mecanismo de armazenamento InnoDB, para que ele não dá suporte a mecanismos de armazenamento alternativo. Se as suas tabelas necessitam de mecanismos de armazenamento alternativo, certifique-se de que convertê-los para utilizar o formato do motor InnoDB antes da migração para a base de dados do Azure para MySQL. 

Por exemplo, se tiver uma aplicação web ou de WordPress que utiliza o motor de MyISAM, comece por converter as tabelas ao migrar os dados em tabelas de InnoDB. Em seguida, restaure a base de dados do Azure para MySQL. Utilize a cláusula `ENGINE=INNODB` para definir o mecanismo para a criação de uma tabela e, em seguida, transferir dados para a tabela compatível antes da migração. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Recomendações de desempenho para importação e exportação
-   Crie índices em cluster e as chaves primárias antes de carregar dados. Carregar os dados na ordem de chave primária. 
-   Atraso de criação de índices secundários até depois de dados é carregada. Crie todos os índices secundários, após o carregamento. 
-   Desative as restrições de chave estrangeira antes do carregamento. Desabilitar as verificações de chave estrangeiras fornece ganhos significativos de desempenho. Ative as restrições e verifique se os dados após o carregamento para garantir a integridade referencial.
-   Carregar dados em paralelo. Evite muito paralelismo que seria fazer com que atingir um limite de recursos e monitorizar recursos com as métricas disponíveis no portal do Azure. 
-   Utilize as tabelas particionadas, quando apropriado.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importar e exportar com o MySQL Workbench
Existem duas formas de exportar e importar dados em MySQL Workbench. Cada serve a uma finalidade diferente. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Dados da tabela Exportar e importar assistentes a partir do menu de contexto o pesquisador de objetos
![Assistentes do MySQL Workbench no menu de contexto o pesquisador de objetos](./media/concepts-migrate-import-export/p1.png)

Os assistentes para dados da tabela suportam a importação e operações de exportação utilizando ficheiros CSV e JSON. Eles incluem diversas opções de configuração, tais como separadores, seleção de coluna e seleção de codificação. Pode executar cada assistente em servidores de MySQL locais ou remotamente ligados. A ação de importação inclui a tabela, coluna e mapeamento de tipo. 

Pode aceder a estes assistentes no menu de contexto o pesquisador de objetos clicando numa tabela. Em seguida, escolhem **Assistente de exportação de dados de tabela** ou **Assistente de importação de dados de tabela**. 

#### <a name="table-data-export-wizard"></a>Assistente de exportação de dados de tabela
O exemplo seguinte exporta a tabela para um ficheiro CSV: 
1. Com o botão direito a tabela da base de dados para ser exportada. 
2. Selecione **Assistente de exportação de dados de tabela**. Selecione as colunas a ser exportado, linha deslocamento (se aplicável) e count (se houver). 
3. Sobre o **selecionar dados para a exportação** página, clique em **próxima**. Selecione o caminho do ficheiro, o tipo de ficheiro de JSON ou CSV. Também selecione o separador de linha, o método de colocar as cadeias de caracteres e o separador de campo. 
4. Sobre o **localização do ficheiro de saída selecione** página, clique em **próxima**. 
5. Sobre o **exportar dados** página, clique em **próxima**.

#### <a name="table-data-import-wizard"></a>Assistente de importação de dados de tabela
O exemplo seguinte importa a tabela a partir de um ficheiro CSV:
1. Com o botão direito a tabela da base de dados a serem importados. 
2. Procure e selecione o ficheiro CSV para importar e, em seguida, clique em **seguinte**. 
3. Selecione a tabela de destino (nova ou existente) e selecione ou desmarque os **Truncar tabela antes de importar** caixa de verificação. Clique em **Seguinte**.
4. Selecione a codificação e as colunas a ser importados e, em seguida, clique em **seguinte**. 
5. Sobre o **importar dados** página, clique em **próxima**. O assistente importa os dados em conformidade.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Dados do SQL, exportar e importar assistentes a partir do painel do navegador
Utilize um Assistente para exportar ou importar SQL gerado a partir do MySQL Workbench ou gerado do comando mysqldump. Aceder a estes assistentes a partir do **navegador** painel ou ao selecionar **servidor** no menu principal. Em seguida, selecione **exportar dados** ou **importação de dados**. 

#### <a name="data-export"></a>Exportação de Dados
![Dados do MySQL Workbench exportar com o painel do navegador](./media/concepts-migrate-import-export/p2.png)

Pode utilizar o **exportar dados** separador para exportar os dados do MySQL. 
1. Selecione cada esquema de que pretende exportar, escolha os objetos de/tabelas do esquema específico de cada esquema e gerar a exportação. Opções de configuração incluem a exportação para um independente de SQL de ficheiro ou pasta do projeto, despejar eventos e rotinas armazenadas ou ignorar os dados da tabela. 
 
   Em alternativa, utilize **exportar um conjunto de resultados** para exportar um resultado específico definido no editor de SQL para outro formato, como CSV, JSON, HTML e XML. 
3. Selecione os objetos de base de dados para exportar e configure as opções relacionadas.
4. Clique em **atualizar** para carregar os objetos atuais.
5. Opcionalmente, abra a **opções avançadas** separador para refinar a operação de exportação. Por exemplo, adicione bloqueios de tabela, substituição de utilização em vez de instruções insert e identificadores de cotação com carateres de acento grave.
6. Clique em **iniciar exportar** para iniciar o processo de exportação.


#### <a name="data-import"></a>Importação de dados
![Importação de dados do Workbench do MySQL através do navegador de gestão](./media/concepts-migrate-import-export/p3.png)

Pode utilizar o **importação de dados** separador para importar ou restaurar dados exportados da operação de exportação de dados ou do comando mysqldump. 
1. Escolha o independente de SQL de ficheiro ou pasta do projeto, escolha o esquema para importar para ou escolha **New** para definir um novo esquema. 
2. Clique em **começar a importar** para iniciar o processo de importação.

## <a name="next-steps"></a>Passos Seguintes
- Como outra abordagem de migração, leia [migrar a sua base de dados MySQL com a cópia de segurança e restaurar na base de dados do Azure para MySQL](concepts-migrate-dump-restore.md).
- Para obter mais informações sobre como migrar bases de dados para a base de dados do Azure para MySQL, veja a [guia de migração de bases de dados](https://aka.ms/datamigration). 
