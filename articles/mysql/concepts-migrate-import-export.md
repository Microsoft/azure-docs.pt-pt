---
title: Importar e exportar-banco de dados do Azure para MySQL
description: Este artigo explica maneiras comuns de importar e exportar bancos de dados no banco de dados do Azure para MySQL, usando ferramentas como o MySQL Workbench.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 8cf0b88ddc24bfc6bc293dd62416417f1eec3a06
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770956"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrar seu banco de dados MySQL usando importação e exportação
Este artigo explica duas abordagens comuns para importar e exportar dados para um servidor do Azure para MySQL usando o MySQL Workbench. 

## <a name="before-you-begin"></a>Antes de começar
Para percorrer este guia de instruções, você precisa de:
- Um banco de dados do Azure para servidor MySQL, seguindo [criar um banco de dados do Azure para o servidor MySQL usando portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md).
- O MySQL Workbench [foi baixado](https://dev.mysql.com/downloads/workbench/)ou outra ferramenta MySQL para importar e exportar.

## <a name="use-common-tools"></a>Usar ferramentas comuns
Use ferramentas comuns, como MySQL Workbench, Toad ou Navicat para se conectar e importar ou exportar dados remotamente para o Azure Database para MySQL. 

Use essas ferramentas no computador cliente com uma conexão com a Internet para se conectar ao banco de dados do Azure para MySQL. Use uma conexão criptografada por SSL para práticas recomendadas de segurança, conforme descrito em [configurar conectividade SSL no banco de dados do Azure para MySQL](concepts-ssl-connection-security.md).

Você não precisa mover os arquivos de importação e exportação para qualquer local de nuvem especial ao migrar para o banco de dados do Azure para MySQL. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Criar um banco de dados no servidor de banco de dados do Azure para MySQL
Crie um banco de dados vazio no banco de dados do Azure para o servidor MySQL no qual você deseja migrá-los. Use uma ferramenta como MySQL Workbench, Toad ou Navicat para criar o banco de dados. O banco de dados pode ter o mesmo nome que o banco de dado que contém o despejado ou você pode criar um banco de dados com um nome diferente.

Para se conectar, localize as informações de conexão na **visão geral** do banco de dados do Azure para MySQL.

![Localizar as informações de conexão no portal do Azure](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

Adicione as informações de conexão ao MySQL Workbench.

![Cadeia de conexão do MySQL Workbench](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Determinar quando usar as técnicas de importação e exportação em vez de um despejo e uma restauração
Use as ferramentas do MySQL para importar e exportar bancos de dados para o Azure MySQL, nos cenários a seguir. Em outros cenários, você pode se beneficiar do uso da abordagem de [despejo e restauração](concepts-migrate-dump-restore.md) em vez disso. 

- Quando você precisa escolher seletivamente algumas tabelas para importar de um banco de dados MySQL existente para o banco de dados MySQL do Azure, é melhor usar a técnica de importação e exportação.  Ao fazer isso, você pode omitir as tabelas desnecessárias da migração para economizar tempo e recursos. Por exemplo, use a opção `--include-tables` ou `--exclude-tables` com [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) e a opção `--tables` com [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Quando você estiver movendo os objetos de banco de dados diferentes de tabelas, crie explicitamente esses objetos. Inclua restrições (chave primária, chave estrangeira, índices), exibições, funções, procedimentos, gatilhos e outros objetos de banco de dados que você deseja migrar.
- Quando você estiver migrando dados de fontes de dados externas que não sejam do MySQL, Crie arquivos simples e importe-os usando o [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Certifique-se de que todas as tabelas no banco de dados usem o mecanismo de armazenamento InnoDB quando você estiver carregando dados no Azure Database para MySQL. O banco de dados do Azure para MySQL dá suporte apenas ao mecanismo de armazenamento InnoDB, portanto, ele não dá suporte a mecanismos de armazenamento alternativos. Se suas tabelas exigirem mecanismos de armazenamento alternativos, certifique-se de convertê-las para usar o formato de mecanismo InnoDB antes da migração para o banco de dados do Azure para MySQL. 

Por exemplo, se você tiver um WordPress ou um aplicativo Web que usa o mecanismo MyISAM, primeiro converta as tabelas migrando os dados para tabelas InnoDB. Em seguida, restaure para o banco de dados do Azure para MySQL. Use a cláusula `ENGINE=INNODB` para definir o mecanismo para criar uma tabela e, em seguida, transferir os dados para a tabela compatível antes da migração. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Recomendações de desempenho para importação e exportação
-   Crie índices clusterizados e chaves primárias antes de carregar dados. Carregar dados na ordem de chave primária. 
-   Atrasar a criação de índices secundários até que os dados sejam carregados. Crie todos os índices secundários após o carregamento. 
-   Desabilite as restrições de chave estrangeira antes de carregar. A desabilitação de verificações de chave estrangeira fornece ganhos de desempenho significativos. Habilite as restrições e verifique os dados após a carga para garantir a integridade referencial.
-   Carregar dados em paralelo. Evite muito paralelismo que poderia fazer com que você atinja um limite de recursos e monitore os recursos usando as métricas disponíveis no portal do Azure. 
-   Use tabelas particionadas quando apropriado.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importar e exportar usando o MySQL Workbench
Há duas maneiras de exportar e importar dados no MySQL Workbench. Cada um atende a uma finalidade diferente. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Assistentes de importação e exportação de dados de tabela no menu de contexto do pesquisador de objetos
![Assistentes do MySQL Workbench no menu de contexto do pesquisador de objetos](./media/concepts-migrate-import-export/p1.png)

Os assistentes para dados de tabela dão suporte a operações de importação e exportação usando arquivos CSV e JSON. Eles incluem várias opções de configuração, como separadores, seleção de coluna e seleção de codificação. Você pode executar cada assistente em servidores MySQL locais ou conectados remotamente. A ação de importação inclui o mapeamento de tabela, coluna e tipo. 

Você pode acessar esses assistentes no menu de contexto do pesquisador de objetos clicando com o botão direito do mouse em uma tabela. Em seguida, escolha **Assistente de exportação de dados de tabela** ou assistente de importação de dados de **tabela**. 

#### <a name="table-data-export-wizard"></a>Assistente de exportação de dados de tabela
O exemplo a seguir exporta a tabela para um arquivo CSV: 
1. Clique com o botão direito do mouse na tabela do banco de dados a ser exportada. 
2. Selecione **Assistente de exportação de dados de tabela**. Selecione as colunas a serem exportadas, o deslocamento da linha (se houver) e a contagem (se houver). 
3. Na página **selecionar dados para exportação** , clique em **Avançar**. Selecione o caminho do arquivo, CSV ou tipo de arquivo JSON. Selecione também o separador de linha, o método de cadeias de caracteres de circunscrição e separador de campo. 
4. Na página **selecionar local do arquivo de saída** , clique em **Avançar**. 
5. Na página **exportar dados** , clique em **Avançar**.

#### <a name="table-data-import-wizard"></a>Assistente de importação de dados de tabela
O exemplo a seguir importa a tabela de um arquivo CSV:
1. Clique com o botão direito do mouse na tabela do banco de dados a ser importado. 
2. Navegue até o arquivo CSV a ser importado e selecione-o e clique em **Avançar**. 
3. Selecione a tabela de destino (nova ou existente) e marque ou desmarque a caixa de seleção **Truncar tabela antes de importar** . Clique em **Seguinte**.
4. Selecione codificação e as colunas a serem importadas e clique em **Avançar**. 
5. Na página **importar dados** , clique em **Avançar**. O assistente importa os dados de acordo.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Assistentes de exportação e importação de dados SQL no painel navegador
Use um assistente para exportar ou importar o SQL gerado do MySQL Workbench ou gerado a partir do comando mysqldump. Acesse esses assistentes no painel **navegador** ou selecionando **servidor** no menu principal. Em seguida, selecione **exportação de dados** ou **importação de dados**. 

#### <a name="data-export"></a>Exportação de dados
![Exportação de dados do MySQL Workbench usando o painel navegador](./media/concepts-migrate-import-export/p2.png)

Você pode usar a guia **exportação de dados** para exportar os dados do MySQL. 
1. Selecione cada esquema que você deseja exportar, opcionalmente, escolha objetos/tabelas de esquema específicos de cada esquema e gere a exportação. As opções de configuração incluem exportar para uma pasta de projeto ou arquivo SQL independente, despejar rotinas e eventos armazenados ou ignorar dados de tabela. 
 
   Como alternativa, use **exportar um conjunto de resultados** para exportar um conjunto de resultados específico no editor SQL para outro formato, como CSV, JSON, HTML e XML. 
3. Selecione os objetos de banco de dados a serem exportados e configure as opções relacionadas.
4. Clique em **Atualizar** para carregar os objetos atuais.
5. Opcionalmente, abra a guia **Opções avançadas** para refinar a operação de exportação. Por exemplo, adicionar bloqueios de tabela, usar instruções Replace em vez de INSERT e identificadores de aspas com caracteres de acento grave.
6. Clique em **Iniciar exportação** para iniciar o processo de exportação.


#### <a name="data-import"></a>Importação de dados
![Importação de dados do MySQL Workbench usando o Management Navigator](./media/concepts-migrate-import-export/p3.png)

Você pode usar a guia **importação de dados** para importar ou restaurar dados exportados da operação de exportação de dados ou do comando mysqldump. 
1. Escolha a pasta do projeto ou o arquivo SQL independente, escolha o esquema para importar ou escolha **novo** para definir um novo esquema. 
2. Clique em **Iniciar importação** para iniciar o processo de importação.

## <a name="next-steps"></a>Passos seguintes
- Como outra abordagem de migração, leia [migrar seu banco de dados MySQL usando despejo e restauração no banco de dados do Azure para MySQL](concepts-migrate-dump-restore.md).
- Para obter mais informações sobre como migrar bancos de dados do para o Azure para MySQL, consulte o [Guia de migração de banco de dados](https://aka.ms/datamigration). 
