---
title: Importação e exportação - Base de Dados Azure para MySQL
description: Este artigo explica formas comuns de importar e exportar bases de dados na Base de Dados Azure para o MySQL, utilizando ferramentas como a Bancada de Trabalho MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: 83b0a69e063e9427c726216ef873f5a1c97f9582
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78163731"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrar a sua base de dados MySQL utilizando a importação e a exportação
Este artigo explica duas abordagens comuns para importar e exportar dados para uma Base de Dados Azure para servidor MySQL utilizando a Bancada de Trabalho MySQL. 

## <a name="before-you-begin"></a>Antes de começar
Para passar por este guia de como guiar, você precisa:
- Uma base de dados Azure para servidor MySQL, seguindo [criar uma base de dados Azure para servidor MySQL utilizando](quickstart-create-mysql-server-database-using-azure-portal.md)o portal Azure .
- MySQL Workbench [MySQL Workbench Download](https://dev.mysql.com/downloads/workbench/) ou outra ferramenta MySQL de terceiros para fazer a importação/exportação.

## <a name="use-common-tools"></a>Use ferramentas comuns
Utilize utilitários e ferramentas comuns, como a Bancada de Trabalho MySQL ou a mysqldump, para ligar e importar ou exportar dados remotamente para a Base de Dados Azure para o MySQL. 

Utilize estas ferramentas na sua máquina cliente com uma ligação à Internet para ligar à Base de Dados Azure para o MySQL. Utilize uma ligação encriptada por SSL para as melhores práticas de segurança, conforme descrito na [conectividade Configure SSL na Base de Dados Azure para MySQL](concepts-ssl-connection-security.md).

Não precisa de mover os seus ficheiros de importação e exportação para qualquer local especial na nuvem ao migrar para a Base de Dados Azure para o MySQL. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Criar uma base de dados na Base de Dados Azure para servidor MySQL
Crie uma base de dados vazia na Base de Dados Azure para o servidor MySQL onde pretende migrar os dados. Utilize uma ferramenta como a bancada mySQL, sapo ou Navicat para criar a base de dados. A base de dados pode ter o mesmo nome que a base de dados que contém os dados despejados, ou pode criar uma base de dados com um nome diferente.

Para se ligar, localize as informações de ligação na **visão geral** da sua Base de Dados Azure para MySQL.

![Encontre as informações de ligação no portal Azure](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

Adicione as informações de ligação à bancada mySQL.

![Cadeia de ligação à bancada de trabalho MySQL](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Determinar quando utilizar técnicas de importação e exportação em vez de um despejo e restaurar
Utilize ferramentas MySQL para importar e exportar bases de dados para a Base de Dados Azure MySQL nos seguintes cenários. Noutros cenários, poderá beneficiar-se da utilização da [abordagem de despejo e restauro.](concepts-migrate-dump-restore.md) 

- Quando você precisa escolher seletivamente algumas tabelas para importar de uma base de dados MySQL existente para A Base de Dados Azure MySQL, o melhor é usar a técnica de importação e exportação.  Ao fazê-lo, pode omitir quaisquer mesas desnecessárias da migração para poupar tempo e recursos. Por exemplo, `--include-tables` utilize `--exclude-tables` o interruptor ou como `--tables` com a bomba de [misqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) e o interruptor com [misqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Quando estiver a mover os objetos da base de dados para além das tabelas, crie explicitamente esses objetos. Inclua constrangimentos (chave primária, chave estrangeira, índices), vistas, funções, procedimentos, gatilhos e quaisquer outros objetos de base de dados que queira migrar.
- Quando estiver a migrar dados de fontes de dados externas que não uma base de dados MySQL, crie ficheiros planos e importe-os utilizando [a mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Certifique-se de que todas as tabelas da base de dados utilizam o motor de armazenamento InnoDB quando estiver a carregar dados para a Base de Dados Azure para o MySQL. A Base de Dados Azure para MySQL suporta apenas o motor de armazenamento InnoDB, pelo que não suporta motores de armazenamento alternativos. Se as suas tabelas necessitarem de motores de armazenamento alternativos, certifique-se de que os converte para utilizar o formato do motor InnoDB antes da migração para a Base de Dados Azure para o MySQL. 

Por exemplo, se tiver uma aplicação WordPress ou web que utilize o motor MyISAM, converta primeiro as tabelas migrando os dados para tabelas InnoDB. Em seguida, restaurar para azure Base de dados para MySQL. Utilize a `ENGINE=INNODB` cláusula para definir o motor para a criação de uma tabela e, em seguida, transfira os dados para a tabela compatível antes da migração. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Recomendações de desempenho para importação e exportação
-   Crie índices agrupados e chaves primárias antes de carregar dados. Carregue os dados na ordem principal. 
-   Adiar a criação de índices secundários até depois de os dados estarem carregados. Crie todos os índices secundários após o carregamento. 
-   Desative os constrangimentos das chaves estrangeiras antes de carregar. A desativação de controlos de chaves estrangeiras proporciona ganhos significativos de desempenho. Ative os constrangimentos e verifique os dados após a carga para garantir a integridade referencial.
-   Carregue os dados em paralelo. Evite demasiado paralelismo que o faça atingir um limite de recursos e monitorize os recursos utilizando as métricas disponíveis no portal Azure. 
-   Utilize mesas divididas quando apropriado.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importação e exportação utilizando a bancada MySQL
Existem duas formas de exportar e importar dados na bancada mySQL. Cada um tem um propósito diferente. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Dados de tabela supremia e importadores do menu de contexto do navegador de objetos
![Assistentes da bancada de trabalho MySQL no menu de contexto do navegador de objetos](./media/concepts-migrate-import-export/p1.png)

Os assistentes para dados de tabela suportam operações de importação e exportação utilizando ficheiros CSV e JSON. Incluem várias opções de configuração, tais como separadores, seleção de colunas e seleção de codificação. Pode executar cada assistente contra servidores MySQL locais ou ligados remotamente. A ação de importação inclui o mapeamento de tabelas, colunas e tipos. 

Pode aceder a estes assistentes a partir do menu de contexto do navegador de objetos clicando numa tabela. Em seguida, escolha o Assistente de **Exportação** de Dados de Tabela ou **o Assistente de Importação de Dados**de Tabela . 

#### <a name="table-data-export-wizard"></a>Assistente de exportação de dados de tabela
O exemplo seguinte exporta a tabela para um ficheiro CSV: 
1. Clique na tabela da base de dados a exportar. 
2. Selecione **Assistente de Exportação**de Dados de Tabela . Selecione as colunas a exportar, remar offset (se houver) e contar (se houver). 
3. Nos **dados Select para página de exportação,** clique em **Seguinte**. Selecione o caminho de ficheiro, CSV ou tipo de ficheiro JSON. Selecione também o separador de linha, o método de fecho de cordas e o separador de campo. 
4. Na página de localização do **ficheiro de saída Select,** clique em **Seguinte**. 
5. Na página de dados da **Exportação,** clique em **Seguinte**.

#### <a name="table-data-import-wizard"></a>Assistente de importação de dados de tabela
O exemplo que se segue importa a tabela de um ficheiro CSV:
1. Clique na tabela da base de dados para ser importada. 
2. Navegue e selecione o ficheiro CSV a importar e, em seguida, clique em **Seguinte**. 
3. Selecione a tabela de destino (nova ou existente) e selecione ou limpe a tabela Truncate antes da caixa de **verificação de importação.** Clique em **Seguinte**.
4. Selecione codificação e as colunas a importar e, em seguida, clique em **Seguinte**. 
5. Na página de **dados da Importação,** clique em **Seguinte**. O assistente importa os dados em conformidade.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>SQL data exportando e importando feiticeiros do painel Navigator
Utilize um assistente para exportar ou importar SQL gerado a partir da Bancada de Trabalho MySQL ou gerado a partir do comando mysqldump. Aceda a estes assistentes a partir do painel **do Navegador** ou selecionando o **Servidor** a partir do menu principal. Em seguida, selecione **Dados Exportação** ou Importação de **Dados**. 

#### <a name="data-export"></a>Exportação de Dados
![Exportação de dados da bancada de trabalho MySQL utilizando o painel Navigator](./media/concepts-migrate-import-export/p2.png)

Pode utilizar o separador **Exportação** de Dados para exportar os seus dados MySQL. 
1. Selecione cada esquema que pretende exportar, escolha opcionalmente objetos/tabelas de esquemas específicos de cada esquema e gere a exportação. As opções de configuração incluem exportação para uma pasta de projeto ou ficheiro SQL autossuficiente, rotinas e eventos armazenados de despejo, ou dados de tabela de salto. 
 
   Em alternativa, utilize **a Exportação de um Conjunto** de Resultados para exportar um resultado específico definido no editor SQL para outro formato, tais como CSV, JSON, HTML e XML. 
3. Selecione os objetos de base de dados para exportar e configure as opções relacionadas.
4. Clique em **Refresh** para carregar os objetos atuais.
5. Opcionalmente, abra o separador **Opções Avançadas** para aperfeiçoar a operação de exportação. Por exemplo, adicione fechaduras de mesa, substitua em vez de inserir declarações e cite identificadores com caracteres de backtick.
6. Clique **em Iniciar exportação** para iniciar o processo de exportação.


#### <a name="data-import"></a>Importação de Dados
![Importação de dados da bancada de trabalho MySQL usando o Navegador de Gestão](./media/concepts-migrate-import-export/p3.png)

Pode utilizar o separador **Data Import** para importar ou restaurar os dados exportados da operação de exportação de dados ou do comando mysqldump. 
1. Escolha a pasta do projeto ou o ficheiro SQL autossuficiente, escolha o esquema para importar ou escolha **o Novo** para definir um novo esquema. 
2. Clique **em Iniciar importar** para iniciar o processo de importação.

## <a name="next-steps"></a>Passos seguintes
- Como outra abordagem de migração, leia Migrate a sua base de [dados MySQL usando lixeira e restauro na Base de Dados Azure para mySQL](concepts-migrate-dump-restore.md).
- Para mais informações sobre bases de dados migratórias para a Base de Dados Azure para MySQL, consulte o Guia de [Migração](https://aka.ms/datamigration)da Base de Dados . 
