---
title: 'Oracle para Azure Database for PostgreSQL: Guia de migração'
titleSuffix: Azure Database for PostgreSQL
description: Este guia ensina-o a migrar o seu esquema Oráculo para a Base de Dados Azure para PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: 931528ec415cabde8e862db17b9f8f26502f6788
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968939"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Migrar o Oráculo para Azure Database para PostgreSQL

Este guia ensina-o a migrar o seu esquema Oráculo para a Base de Dados Azure para PostgreSQL. 

Para obter orientações de migração detalhadas e abrangentes, consulte os [recursos do guia migratório.](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf) 

## <a name="prerequisites"></a>Pré-requisitos

Para migrar o seu esquema Oráculo para a Base de Dados Azure para PostgreSQL, tem de: 

- Verifique se o ambiente de origem está suportado. 
- Faça o download da versão mais recente do [ora2pg.](https://ora2pg.darold.net/) 
- Tenha a versão mais recente do [módulo DBD.](https://www.cpan.org/modules/by-module/DBD/) 


## <a name="overview"></a>Descrição Geral

PostgreSQL é uma das bases de dados de código aberto mais avançadas do mundo. Este artigo descreve como usar a ferramenta ora2pg gratuita para migrar uma base de dados Oracle para PostgreSQL. Pode utilizar o ora2pg para migrar uma base de dados Oracle ou base de dados MySQL para um esquema compatível com PostgreSQL. 

A ferramenta ora2pg liga a sua base de dados Oráculo, digitaliza-a automaticamente e extrai a sua estrutura ou dados. Em seguida, o ora2pg gera scripts SQL que você pode carregar na sua base de dados PostgreSQL. Pode utilizar o ora2pg para tarefas como engenharia inversa a uma base de dados oracle, migrar uma enorme base de dados empresarial ou simplesmente replicar alguns dados da Oracle numa base de dados PostgreSQL. A ferramenta é fácil de usar e não requer nenhum conhecimento da base de dados da Oracle além da capacidade de fornecer os parâmetros necessários para se ligar à base de dados Oráculo.

> [!NOTE]
> Para mais informações sobre a utilização da versão mais recente do ora2pg, consulte a [documentação ora2pg.](https://ora2pg.darold.net/documentation.html)

### <a name="typical-ora2pg-migration-architecture"></a>Arquitetura migratória típica de ora2pg

![Screenshot da arquitetura de migração ora2pg.](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

Depois de providenciar a Base de Dados VM e Azure para PostgreSQL, precisa de duas configurações para permitir a conectividade entre eles: **Permitir o acesso aos serviços Azure** e impor a **conexão SSL**: 

- **Ligação A** lâmina de segurança > **permitir o acesso aos serviços Azure**  >  **ON**

- **Ligação Lâmina de segurança** > **Definições SSL**  >  **aplicam ligação SSL**  >  **DEsativada**

### <a name="recommendations"></a>Recomendações

- Para melhorar o desempenho das operações de avaliação ou exportação no servidor Oracle, recolher estatísticas:

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- Dados de exportação utilizando o `COPY` comando em vez de `INSERT` .

- Evite exportar tabelas com as suas chaves estrangeiras (FKs), constrangimentos e índices. Estes elementos abrandam o processo de importação de dados para PostgreSQL.

- Crie pontos de vista materializados utilizando a *cláusula de não dados.* Em seguida, refrescar as vistas mais tarde.

- Se possível, utilize índices únicos em vistas materializadas. Estes índices podem acelerar a atualização quando utilizar a sintaxe `REFRESH MATERIALIZED VIEW CONCURRENTLY` .


## <a name="premigration"></a>Pré-estreia 

Depois de verificar se o seu ambiente de origem é suportado e que se dirigiu a quaisquer pré-requisitos, está pronto para iniciar a fase de pré-estreia. Para começar: 

1. Invente as bases de dados que precisa para migrar. 
1. Avalie essas bases de dados para potenciais problemas de migração ou bloqueadores.
1. Resolva quaisquer itens que tenha descoberto. 
 
Para migrações heterogéneas como a Oracle para a Base de Dados de Azure para postgreSQL, esta fase também envolve tornar os esquemas de base de dados de origem compatíveis com o ambiente alvo.

### <a name="discover"></a>Detetar

O objetivo da fase de descoberta é identificar as fontes de dados existentes e detalhes sobre as funcionalidades que estão a ser utilizadas. Esta fase ajuda-o a compreender melhor e a planear a migração. O processo envolve a digitalização da rede para identificar todas as instâncias Oráculos da sua organização, juntamente com a versão e funcionalidades em uso.

Os scripts de pré-avaliação da Microsoft para a Oracle correm contra a base de dados da Oracle. Os scripts de pré-avaliação consultam os metadados do Oráculo. Os scripts fornecem:

- Um inventário de base de dados, incluindo contagens de objetos por esquema, tipo e estado.
- Uma estimativa aproximada dos dados brutos em cada esquema, com base em estatísticas.
- O tamanho das mesas em cada esquema.
- O número de linhas de código por embalagem, função, procedimento, e assim por diante.

Descarregue os scripts relacionados a partir do [site ora2pg](https://ora2pg.darold.net/).

### <a name="assess"></a>Avaliação

Depois de inventariar as bases de dados da Oracle, terá uma ideia do tamanho da base de dados e dos potenciais desafios. O próximo passo é fazer a avaliação.

Estimar o custo de uma migração da Oráculo para a PostgreSQL não é fácil. Para avaliar o custo de migração, o ora2pg verifica todos os objetos, funções e procedimentos armazenados para objetos e código PL/SQL que não pode converter automaticamente.

A ferramenta ora2pg tem um modo de análise de conteúdo que inspeciona a base de dados da Oracle para gerar um relatório de texto. O relatório descreve o que a base de dados do Oráculo contém e o que não pode ser exportado.

Para ativar o modo *de análise e relatório,* utilize o tipo exportado `SHOW_REPORT` como indicado no seguinte comando:

```
ora2pg -t SHOW_REPORT
```

A ferramenta ora2pg pode converter o código SQL e PL/SQL da sintaxe Oracle para PostgreSQL. Assim, após a análise da base de dados, o ora2pg pode estimar as dificuldades de código e o tempo necessário para migrar uma base de dados completa.

Para estimar o custo de migração em dias humanos, o ora2pg permite-lhe usar uma diretiva de configuração chamada `ESTIMATE_COST` . Também pode permitir esta diretiva com um aviso de comando:

```
ora2pg -t SHOW_REPORT --estimate_cost
```

A unidade de migração predefinido representa cerca de cinco minutos para um perito em PostgreSQL. Se esta migração for a sua primeira, pode aumentar a unidade de migração predefinido utilizando a diretiva de configuração `COST_UNIT_VALUE` ou a `--cost_unit_value` opção de linha de comando.

A última linha do relatório mostra o código de migração total estimado em dias humanos. A estimativa segue o número de unidades de migração estimadas para cada objeto.

Esta unidade de migração representa cerca de cinco minutos para um especialista em PostgreSQL. Se esta migração for a sua primeira, pode aumentar o padrão utilizando a diretiva de configuração `COST_UNIT_VALUE` ou a opção de linha de comando `--cost_unit_value` . 

No seguinte exemplo de código, vê algumas variações de avaliação: 
* Avaliação de tabelas
* Avaliação de colunas
* Avaliação de esquema que usa uma unidade de custo padrão de 5 minutos
* Avaliação de esquema que usa uma unidade de custos de 10 minutos

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

Aqui está a saída do nível de migração de avaliação de esquemaS B-5:

* Níveis de migração:

  * A - Migração que pode ser executada automaticamente
    
  * B - Migração com reescrita de código e um custo de dias humanos até 5 dias
    
  * C - Migração com reescrita de código e custo de dias humanos ao longo de 5 dias
    
* Níveis técnicos:

   * 1 = Trivial: Sem funções armazenadas e sem gatilhos

   * 2 = Fácil: Sem funções armazenadas, mas aciona; sem reescrita manual

   * 3 = Simples: Funções armazenadas e/ou gatilhos; sem reescrita manual

   * 4 = Manual: Sem funções armazenadas, mas gatilhos ou vistas com reescrita de código

   * 5 = Difícil: Funções armazenadas e/ou gatilhos com reescrita de código

A avaliação consiste em: 
* Uma letra (A ou B) para especificar se a migração precisa de reescrita manual.

* Um número de 1 a 5 para indicar a dificuldade técnica. 

Outra `-human_days_limit` opção, especifica o limite dos dias-humanos. Aqui, definir o nível de migração para C para indicar que a migração precisa de uma grande quantidade de trabalho, gestão completa de projetos e apoio migratório. O padrão é de 10 dias humanos. Pode utilizar a diretiva de configuração `HUMAN_DAYS_LIMIT` para alterar permanentemente este valor predefinido.

Esta avaliação de esquema foi desenvolvida para ajudar os utilizadores a decidir qual base de dados migrar primeiro e quais as equipas a mobilizar.

### <a name="convert"></a>Converter

Nas migrações mínimas, a sua fonte de migração muda. Deriva do alvo em termos de dados e esquema após a migração única. Durante a fase *de sincronização de dados,* certifique-se de que todas as alterações na fonte são capturadas e aplicadas ao alvo em tempo real. Depois de verificar se todas as alterações são aplicadas ao alvo, pode *cortar* da fonte para o ambiente alvo.

Neste passo da migração, os scripts do código Oráculo e do DDL são convertidos ou traduzidos para PostgreSQL. A ferramenta ora2pg exporta automaticamente os objetos Oráculos num formato PostgreSQL. Alguns dos objetos gerados não podem ser compilados na base de dados PostgreSQL sem alterações manuais.  

Para entender quais os elementos que precisam de intervenção manual, primeiro compilar os ficheiros gerados pelo ora2pg contra a base de dados PostgreSQL. Verifique o registo e, em seguida, faça as alterações necessárias até que a estrutura do esquema seja compatível com a sintaxe PostgreSQL.


#### <a name="create-a-migration-template"></a>Criar um modelo de migração 

Recomendamos a utilização do modelo de migração que o ora2pg fornece. Quando utiliza as opções `--project_base` `--init_project` e, o ora2pg cria um modelo de projeto com uma árvore de trabalho, um ficheiro de configuração e um script para exportar todos os objetos da base de dados do Oráculo. Para mais informações, consulte a [documentação do ora2pg.](https://ora2pg.darold.net/documentation.html)

Utilize o seguinte comando: 

```
ora2pg --project_base /app/migration/ --init_project test_project

ora2pg --project_base /app/migration/ --init_project test_project
```

Aqui está a saída do exemplo: 
   
```
Creating project test_project. /app/migration/test_project/ schema/ dblinks/ directories/ functions/ grants/ mviews/ packages/ partitions/ procedures/ sequences/ synonyms/    tables/ tablespaces/ triggers/ types/ views/ sources/ functions/ mviews/ packages/ partitions/ procedures/ triggers/ types/ views/ data/ config/ reports/

Generating generic configuration file

Creating script export_schema.sh to automate all exports.

Creating script import_all.sh to automate all imports.
```

O `sources/` diretório contém o código Oráculo. O `schema/` diretório contém o código portuário para PostgreSQL. E o `reports/` diretório contém os relatórios html e a avaliação dos custos de migração.


Após a criação da estrutura do projeto, é criado um ficheiro config genérico. Defina a ligação da base de dados Oracle e os parâmetros config relevantes no ficheiro config. Para obter mais informações sobre o ficheiro config, consulte a [documentação do ora2pg.](https://ora2pg.darold.net/documentation.html)


#### <a name="export-oracle-objects"></a>Exportar objetos oráculos

Em seguida, exporte os objetos Oráculos como objetos PostgreSQL executando o ficheiro *export_schema.sh*.

```
cd /app/migration/mig_project
./export_schema.sh
```

Executar manualmente o seguinte comando.

```
SET namespace="/app/migration/mig_project"

ora2pg -t DBLINK -p -o dblink.sql -b %namespace%/schema/dblinks -c
%namespace%/config/ora2pg.conf
ora2pg -t DIRECTORY -p -o directory.sql -b %namespace%/schema/directories -c
%namespace%/config/ora2pg.conf
ora2pg -p -t FUNCTION -o functions2.sql -b %namespace%/schema/functions -c
%namespace%/config/ora2pg.conf ora2pg -t GRANT -o grants.sql -b %namespace%/schema/grants -c %namespace%/config/ora2pg.conf ora2pg -t MVIEW -o mview.sql -b %namespace%/schema/   mviews -c %namespace%/config/ora2pg.conf
ora2pg -p -t PACKAGE -o packages.sql
%namespace%/config/ora2pg.conf -b %namespace%/schema/packages -c
ora2pg -p -t PARTITION -o partitions.sql %namespace%/config/ora2pg.conf -b %namespace%/schema/partitions -c
ora2pg -p -t PROCEDURE -o procs.sql
%namespace%/config/ora2pg.conf -b %namespace%/schema/procedures -c
ora2pg -t SEQUENCE -o sequences.sql
%namespace%/config/ora2pg.conf -b %namespace%/schema/sequences -c
ora2pg -p -t SYNONYM -o synonym.sql -b %namespace%/schema/synonyms -c
%namespace%/config/ora2pg.conf
ora2pg -t TABLE -o table.sql -b %namespace%/schema/tables -c %namespace%/config/ora2pg.conf ora2pg -t TABLESPACE -o tablespaces.sql -b %namespace%/schema/tablespaces -c
%namespace%/config/ora2pg.conf
ora2pg -p -t TRIGGER -o triggers.sql -b %namespace%/schema/triggers -c
%namespace%/config/ora2pg.conf ora2pg -p -t TYPE -o types.sql -b %namespace%/schema/types -c %namespace%/config/ora2pg.conf ora2pg -p -t VIEW -o views.sql -b %namespace%/   schema/views -c %namespace%/config/ora2pg.conf
```

Para extrair os dados, utilize o seguinte comando.

```
ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
```

#### <a name="compile-files"></a>Compilar ficheiros

Por fim, compile todos os ficheiros com a Base de Dados Azure para o servidor PostgreSQL. Pode optar por carregar os ficheiros DDL gerados manualmente ou utilizar o segundo script *import_all.sh* para importar esses ficheiros interativamente.

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-

server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l

%namespace%\ schema\sequences\create_sequences.log

psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
```

Aqui está o comando de importação de dados:

```
psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log

psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
```

Enquanto os ficheiros estão a ser compilados, verifique os registos e corrija qualquer sintaxe que o ora2pg não possa converter por si só.

Para obter mais informações, consulte [a Oracle para a Azure Database para soluções de migração pós-SQL.](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf)

## <a name="migrate"></a>Migrate 

Depois de ter os pré-requisitos necessários e completar os passos de pré-visualização, pode iniciar o esquema e a migração de dados.

### <a name="migrate-schema-and-data"></a>Migrar esquemas e dados

Quando tiver feito as correções necessárias, uma construção estável da base de dados está pronta para ser implantada. Executar os `psql` comandos de importação, apontando para os ficheiros que contêm o código modificado. Esta tarefa compila os objetos de base de dados com a base de dados PostgreSQL e importa os dados.

Neste passo, pode implementar um nível de paralelismo na importação dos dados.

### <a name="sync-data-and-cut-over"></a>Sincronizar dados e cortar

Nas migrações online (mínimo de tempo de inatividade), a fonte de migração continua a mudar. Deriva do alvo em termos de dados e esquema após a migração única. 

Durante a fase *de sincronização de dados,* certifique-se de que todas as alterações na fonte são capturadas e aplicadas ao alvo em tempo real. Depois de verificar se todas as alterações são aplicadas, pode cortar da fonte para o ambiente alvo.

Para fazer uma migração online, contacte AskAzureDBforPostgreSQL@service.microsoft.com para apoio.

Numa migração *delta/incremental* que utiliza ora2pg, para cada tabela, utilize uma consulta que filtra *(cortes)* por data, hora ou outro parâmetro. Em seguida, termine a migração usando uma segunda consulta que migra os dados restantes.

Na tabela de dados de origem, migrar todos os dados históricos primeiro. Eis um exemplo:

```
select * from table1 where filter_data < 01/01/2019
```

Pode consultar as alterações desde a migração inicial, executando um comando como este:

```
select * from table1 where filter_data >= 01/01/2019
```

Neste caso, recomendamos que melhore a validação verificando a paridade de dados de ambos os lados, a fonte e o alvo.

## <a name="postmigration"></a>Pós-imigração 

Após a fase *de migração,* complete as tarefas de pós-migração para garantir que tudo esteja funcionando da forma mais suave e eficiente possível.

### <a name="remediate-applications"></a>Aplicações corretivas

Após a migração dos dados para o ambiente alvo, todas as aplicações que anteriormente consumiam a fonte precisam de começar a consumir o alvo. A configuração às vezes requer alterações nas aplicações.

### <a name="test"></a>Teste

Após a migração dos dados para o alvo, faça testes com base de dados para verificar se as aplicações funcionam bem com o alvo. Certifique-se de que a origem e o alvo estão corretamente migrados executando os scripts de validação de dados manuais contra as bases de dados-alvo da fonte Oracle e postgreSQL.

Idealmente, se as bases de dados de origem e alvo tiverem uma trajetória de networking, o ora2pg deve ser usado para validação de dados. Pode utilizar a `TEST` ação para garantir que todos os objetos da base de dados Oracle foram criados em PostgreSQL. 

Execute este comando:

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>Otimização

A fase de pós-imigração é crucial para conciliar quaisquer problemas de precisão de dados e verificar a completude. Nesta fase, aborda também problemas de desempenho com a carga de trabalho.

## <a name="migration-assets"></a>Ativos de migração 

Para mais informações sobre este cenário de migração, consulte os seguintes recursos. Apoiam o envolvimento de projetos de migração no mundo real.

| Recurso | Descrição    |
| -------------- | ------------------ |
| [Livro de receitas de migração da Oracle para Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | Este documento ajuda arquitetos, consultores, administradores de bases de dados e funções relacionadas a migrar rapidamente cargas de trabalho da Oracle para a Azure Database para PostgreSQL utilizando ora2pg. |
| [Soluções de migração do Oráculo para Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | Este documento ajuda arquitetos, consultores, administradores de bases de dados e funções relacionadas a corrigir ou trabalhar rapidamente em torno de problemas enquanto migra cargas de trabalho da Oracle para a Azure Database para PostgreSQL. |
| [Passos para instalar ora2pg no Windows ou Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | Este documento fornece um guia de instalação rápida para esquemas migratórios e dados da Oracle para Azure Database para PostgreSQL utilizando ora2pg no Windows ou Linux. Para mais informações, consulte a [documentação do ora2pg.](http://ora2pg.darold.net/documentation.html) |

A equipa de Engenharia Data SQL desenvolveu estes recursos. A principal carta desta equipa é desbloquear e acelerar a modernização complexa dos projetos de migração da plataforma de dados para a plataforma de dados Microsoft Azure.

## <a name="more-support"></a>Mais apoio

Para ajuda à migração para além do âmbito da ferramenta ora2pg, contacte [ @Ask a Azure DB para PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com).

## <a name="next-steps"></a>Passos seguintes

Para uma matriz de serviços e ferramentas para base de dados e migração de dados e para tarefas de especialidade, consulte [Serviços e ferramentas para a migração de dados.](https://docs.microsoft.com/azure/dms/dms-tools-matrix)

Documentação: 
- [Documentação da Base de Dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/)
- [documentação ora2pg](https://ora2pg.darold.net/documentation.html)
- [Site postgreSQL](https://www.postgresql.org/)
- [Suporte de transações autónomas em PostgreSQL](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 
