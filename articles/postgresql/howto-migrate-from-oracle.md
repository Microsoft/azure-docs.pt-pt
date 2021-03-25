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
ms.openlocfilehash: b41f894a7e4742b75ea06684a960221d4a5b7641
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024766"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Migrar o Oráculo para Azure Database para PostgreSQL

Este guia ensina-o a migrar o seu esquema Oráculo para a Base de Dados Azure para PostgreSQL. 

Para obter orientações detalhadas e abrangentes sobre migração, consulte os recursos do [Guia de Migração.](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf) 

## <a name="prerequisites"></a>Pré-requisitos

Para migrar o seu esquema Oráculo para a Base de Dados Azure para PostgreSQL, tem de: 

- Verifique se o ambiente de origem está suportado. 
- Faça o download da versão mais recente do [ora2pg.](https://ora2pg.darold.net/) 
- A versão mais recente do [módulo DBD.](https://www.cpan.org/modules/by-module/DBD/) 


## <a name="overview"></a>Descrição Geral

PostgreSQL é uma das bases de dados de código aberto mais avançadas do mundo. Este artigo descreve como usar o utilitário ora2pg gratuito para migrar uma base de dados Oracle para PostgreSQL. Pode utilizar o ora2pg, uma ferramenta gratuita, para migrar uma base de dados Oracle ou MySQL para um esquema compatível com PostgreSQL. O utilitário liga a sua base de dados Oráculo, digitaliza-a automaticamente e extrai a sua estrutura ou dados. Em seguida, o ora2pg gera scripts SQL que você pode carregar na sua base de dados PostgreSQL. O ora2pg pode ser usado para tarefas de engenharia inversa uma base de dados Oracle, realizando uma enorme migração de bases de dados de empresas, ou simplesmente replicando alguns dados da Oracle em uma base de dados PostgreSQL. É fácil de usar e não requer nenhum conhecimento da base de dados da Oracle para além da capacidade de fornecer os parâmetros necessários para se ligar à base de dados do Oráculo.

> [!NOTE]
> Para mais informações sobre a utilização da versão mais recente do ora2pg, consulte a [documentação ora2pg.](https://ora2pg.darold.net/documentation.html)

### <a name="typical-ora2pg-migration-architecture"></a>Arquitetura migratória típica de ora2pg

![Screenshot da arquitetura de migração ora2pg.](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

Após a disponibilização da Base de Dados VM e Azure para postgreSQL, são necessárias duas configurações para permitir a conectividade entre eles: **Permitir serviços Azure** e **impor a conexão SSL,** representada da seguinte forma:

- **Ligação Lâmina de segurança** -> **Permitir o acesso aos Serviços Azure** -> ON

- **Conexão Lâmina de segurança** -> **Definições SSL**  ->  **aplicam ligação SSL** -> DESATIVADA

### <a name="recommendations"></a>Recomendações

- Para melhorar o desempenho das operações de avaliação ou exportação no servidor Oracle, colete estatísticas como seguintes:

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- Dados de exportação utilizando o comando COPY em vez de INSERT.

- Evite exportar tabelas com os seus FKs, constrangimentos e índices – fazê-lo tornará mais lento importar os dados para PostgreSQL.

- Crie vistas materializadas usando a **cláusula de não dados** e refresque-a mais tarde.

- Se possível, implementar índices únicos em vistas materializadas, isto tornará a atualização mais rápida com a `REFRESH MATERIALIZED VIEW CONCURRENTLY` sintaxe.



## <a name="pre-migration"></a>Pré-migração 

Depois de verificar se o seu ambiente de origem é suportado e garantir que abordou quaisquer requisitos, está pronto para iniciar a fase de pré-migração. Esta parte do processo envolve a realização de um inventário das bases de dados que precisa de migrar, avaliando essas bases de dados para potenciais problemas de migração ou bloqueadores, e, em seguida, resolver quaisquer itens que possa ter descoberto. Para migrações heterogéneas como a Oracle para a Base de Dados de Azure para postgreSQL, esta fase também envolve a conversão dos esquemas na ou s base de dados de origem para ser compatível com o ambiente-alvo.

### <a name="discover"></a>Detetar

O objetivo da fase Discover é identificar as fontes de dados existentes e detalhes sobre as funcionalidades que estão a ser usadas para obter uma melhor compreensão e plano para a migração. Este processo envolve a digitalização da rede para identificar todas as instâncias Oráculos da sua organização, juntamente com a versão e funcionalidades em uso.

Os scripts de pré-avaliação da Microsoft Oracle são executados na base de dados da Oracle. Os scripts de pré-avaliação são um conjunto de consultas que atingem os metadados da Oráculo e fornecem o seguinte:

- Inventário de bases de dados, incluindo contagem de objetos por esquema, tipo e estado.

- Uma estimativa aproximada de Dados Brutos em cada esquema (com base em estatísticas).

- Dimensionamento de mesas em cada esquema.

- O número de linhas de código por Pacote, Função, Procedimento, etc.

Descarregue os scripts relacionados a partir do [site ora2pg](http://ora2pg.darold.net/).

### <a name="assess"></a>Avaliação

Depois de concluir o inventário da(s) base de dados oracle(s) para obter uma ideia do tamanho da base de dados e quais são os desafios, o próximo passo é executar a Avaliação.

Estimar o custo de um processo de migração da Oracle para a PostgreSQL não é fácil. Para obter uma boa avaliação deste custo de migração, o ora2pg irá inspecionar todos os objetos de base de dados, todas as funções e procedimentos armazenados para detetar se ainda existem alguns objetos e código PL/SQL que não podem ser automaticamente convertidos por ora2pg.

O ora2pg tem um modo de análise de conteúdo que inspeciona a base de dados da Oracle para gerar um relatório de texto sobre o que a base de dados da Oracle contém e o que não pode ser exportado.

Para ativar o modo **de análise e relatório,** utilize o tipo exportado `SHOW_REPORT` como indicado no seguinte comando:

```
ora2pg -t SHOW_REPORT
```

Depois de analisado o dado de dados, o ora2pg, com a sua capacidade de converter o código SQL e PL/SQL da sintaxe Oracle para PostgreSQL, pode ir mais longe estimando as dificuldades de código e o tempo necessário para realizar uma migração completa da base de dados.

Para estimar o custo de migração em dias-homem, o ora2pg permite-lhe usar uma diretiva de configuração chamada ESTIMATE_COST, que também pode ser ativada na linha de comando:

```
ora2pg -t SHOW_REPORT --estimate_cost
```

A unidade de migração predefinido representa cerca de cinco minutos para um perito em PostgreSQL. Se esta for a sua primeira migração, pode fazê-la mais alto com a diretiva de configuração COST_UNIT_VALUE ou a opção de linha de comando cost_unit_value.

A última linha do relatório mostra o código de migração total estimado em dias-homem, após o número de unidades de migração estimadas para cada objeto.

Esta unidade de migração representa cerca de cinco minutos para um especialista em PostgreSQL. Se esta for a sua primeira migração, pode aumentar o padrão com a diretiva de configuração COST_UNIT_VALUE ou a opção de linha de comando -cost_unit_value. Encontrar abaixo algumas variações da avaliação a) avaliação das tabelas; b) avaliação do esquema das colunas c) avaliação do esquema utilizando a avaliação do esquema por defeito cost_unit (5 min) d) utilizando 10 min como unidade de custos.

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

A saída da avaliação do esquema é ilustrada como abaixo:

**Nível de migração: B-5**

Níveis de migração:

A - Migração que pode ser executada automaticamente

B - Migração com reescrita de código e um custo de dias humanos até 5 dias

C - Migração com reescrita de código e custo de dias humanos superior a 5 dias

Níveis técnicos:

1 = trivial: sem funções armazenadas e sem gatilhos

2 = fácil: sem funções armazenadas mas com gatilhos, sem reescrita manual

3 = simples: funções armazenadas e/ou gatilhos, sem reescrita manual

4 = manual: sem funções armazenadas mas com gatilhos ou vistas com reescrita de código

5 = difícil: funções armazenadas e/ou gatilhos com reescrita de código

A avaliação consiste numa letra (A ou B) para especificar se a migração necessita ou não de reescrita manual, e um número de 1 a 5 para indicar o nível de dificuldade técnica. Tem uma opção adicional -human_days_limit para especificar o número de dias humanos limite onde o nível de migração deve ser definido para C para indicar que precisa de uma enorme quantidade de trabalho e uma gestão completa do projeto com apoio migratório. O padrão é 10 dias humanos. Pode utilizar a diretiva de configuração HUMAN_DAYS_LIMIT para alterar permanentemente este valor por defeito.

Esta funcionalidade foi desenvolvida para ajudar a decidir qual a base de dados que poderia ser migrada primeiro e qual é a equipa que precisa de ser mobilizada.

### <a name="convert"></a>Converter

Com as migrações mínimas em tempo de inatividade, a fonte que está a migrar continua a mudar, derivando do alvo em termos de dados e esquemas, após a migração única ocorrer. Durante a fase **de sincronização de dados,** é necessário garantir que todas as alterações na fonte sejam capturadas e aplicadas ao alvo em tempo real. Depois de verificar se todas as alterações na fonte foram aplicadas ao alvo, pode cortar da fonte para o ambiente alvo.

Neste passo da migração, ocorre a conversão ou tradução do Código Oráculo + DDLS para PostgreSQL. A ferramenta ora2pg exporta automaticamente os objetos Oráculos num formato PostgreSQL. Para esses objetos gerados, alguns não compilam na base de dados PostgreSQL sem alterações manuais.  
O processo de compreensão de quais os elementos que necessitam de intervenção manual consiste em compilar os ficheiros gerados pelo ora2pg contra a base de dados PostgreSQL, verificar o registo e fazer as alterações necessárias até que toda a estrutura do esquema seja compatível com a sintaxe PostgreSQL.


#### <a name="create-migration-template"></a>Criar modelo de migração 

Primeiro, recomenda-se criar o modelo de migração que é fornecido fora da caixa com ora2pg. As duas opções -- project_base e --init_project quando usadas indicam para ora2pg que tem que criar um modelo de projeto com uma árvore de trabalho, um arquivo de configuração e um script para exportar todos os objetos da base de dados do Oráculo. Para mais informações, consulte a [documentação do ora2pg.](https://ora2pg.darold.net/documentation.html)

   Utilize o seguinte comando: 

   ```
   ora2pg --project_base /app/migration/ --init_project test_project
   
   ora2pg --project_base /app/migration/ --init_project test_project
   ```

Exemplo de saída: 
   
   ```
   Creating project test_project. /app/migration/test_project/ schema/ dblinks/ directories/ functions/ grants/ mviews/ packages/ partitions/ procedures/ sequences/ synonyms/    tables/ tablespaces/ triggers/ types/ views/ sources/ functions/ mviews/ packages/ partitions/ procedures/ triggers/ types/ views/ data/ config/ reports/
   
   Generating generic configuration file
   
   Creating script export_schema.sh to automate all exports.
   
   Creating script import_all.sh to automate all imports.
   ```

As fontes/diretório contém o código Oráculo, o esquema/diretório contém o código portuário para PostgreSQL. Os relatórios/diretórios contêm os relatórios html com a avaliação dos custos de migração.


Após a criação da estrutura do projeto, é criado um ficheiro config genérico. Defina a ligação da base de dados Oráculo, bem como os parâmetros config relevantes no config.  Consulte a documentação ora2pg para entender o que pode ser configurado no ficheiro config e como.


#### <a name="export-oracle-objects"></a>Exportar objetos oráculos

Em seguida, exporte os objetos Oráculos como objetos PostgreSQL executando o ficheiro export_schema.sh.

   ```
   cd /app/migration/mig_project
   ./export_schema.sh
   
   Run the following command manually:
   
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

   Para extrair os dados, utilize o seguinte comando:

   ```
   ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
   ```

#### <a name="compile-files"></a>Compilar ficheiros

Por último, compile todos os ficheiros com base de dados Azure para o servidor PostgreSQL. Agora é possível optar por carregar os ficheiros DDL gerados manualmente ou utilizar o segundo script import_all.sh para importar esses ficheiros interativamente.

   ```
   psql -f %namespace%\schema\sequences\sequence.sql -h server1-
   
   server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l
   
   %namespace%\ schema\sequences\create_sequences.log
   
   psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
   ```

   Comando de importação de dados:

   ```
   psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log
   
   psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
   ```

Durante a compilação de ficheiros, verifique os registos e corrija as sintaxes necessárias que o ora2pg não conseguiu converter fora da caixa.

Consulte o livro branco [Oracle para Azure Database for PostgreSQL Migration Sarounds](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) para obter apoio no trabalho em torno de questões.

## <a name="migrate"></a>Migrate 

Depois de ter os pré-requisitos necessários e ter concluído as tarefas associadas à fase **de pré-migração,** está pronto para executar o esquema e a migração de dados.

### <a name="migrate-schema-and-data"></a>Migrar esquemas e dados

Após as correções estarem em vigor, uma construção estável da base de dados está pronta para ser implantada.

Neste ponto, tudo o que é necessário é executar os comandos de importação *de PSQL,* apontando para os ficheiros que contêm o código modificado de modo a compilar os objetos de base de dados contra a base de dados PostgreSQL e importar os dados.

Neste passo, pode ser implementado um pouco de paralelismo na importação dos dados.

### <a name="data-sync-and-cutover"></a>Sincronização de dados e Cutover

Com as migrações online (mínimo de tempo de inatividade), a fonte que está a migrar continua a mudar, derivando do alvo em termos de dados e esquemas, após a migração única ocorrer. Durante a fase **de sincronização de dados,** é necessário garantir que todas as alterações na fonte sejam capturadas e aplicadas ao alvo em tempo real. Depois de verificar se todas as alterações na fonte foram aplicadas ao alvo, pode cortar da fonte para o ambiente alvo.

A partir de março de 2019, se quiser realizar uma migração online, considere usar a Attunity Replicate para as Migrações da Microsoft ou Striim.

Para a migração *delta/incremental* utilizando o ora2pg, a técnica consiste em aplicar para cada mesa uma consulta que aplica um filtro (corte) por data ou hora, etc., e depois disso finalizar a migração aplicando uma segunda consulta que migrará o resto dos dados (restos).

Na tabela de dados de origem, migrar todos os dados históricos primeiro. Um exemplo disso é:

```
select * from table1 where filter_data < 01/01/2019
```

Pode consultar as alterações efetuadas desde a migração inicial, executando um comando semelhante ao seguinte:

```
select * from table1 where filter_data >= 01/01/2019
```

Neste caso, recomenda-se que a validação seja melhorada verificando a paridade de dados de ambos os lados, fonte e alvo.

## <a name="post-migration"></a>Pós-migração 

Depois de ter concluído com sucesso a fase **de Migração,** precisa de passar por uma série de tarefas pós-migração para garantir que tudo está a funcionar da forma mais suave e eficiente possível.

### <a name="remediate-applications"></a>Aplicações corretivas

Após a migração dos dados para o ambiente alvo, todas as aplicações que anteriormente consumiam a fonte precisam de começar a consumir o alvo. A realização deste facto exigirá, em alguns casos, alterações nas aplicações.

### <a name="perform-tests"></a>Realizar testes

Após a migração dos dados para o alvo, efetue testes com base de dados para verificar se as aplicações têm um bom desempenho em função do alvo após a migração.

Para garantir que a origem e o alvo estão devidamente migrados, execute os scripts de validação de dados manuais contra as bases de dados-alvo da Oráculo e pós-SQL.

Idealmente, se as bases de dados de origem e alvo tiverem uma trajetória de networking, o ora2pg deve ser usado para validação de dados. A utilização da ação TEST permite verificar se todos os objetos da base de dados Oracle foram criados no âmbito do PostgreSQL. Executar o comando como mostrado:

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>Otimização

A fase pós-migração é crucial para conciliar quaisquer problemas de precisão de dados e verificar a completude, bem como abordar problemas de desempenho com a carga de trabalho.

## <a name="migration-assets"></a>Ativos de migração 

Para obter assistência adicional para completar este cenário de migração, consulte os seguintes recursos, que foram desenvolvidos em apoio de um envolvimento de projetos de migração no mundo real.

| **Ligação de títulos** | **Descrição**    |
| -------------- | ------------------ |
| [Oráculo para Azure PostgreSQL Livro de Receitas migratórias](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | Este documento destina-se a fornecer aos arquitetos, consultores, DBAs e funções relacionadas com um guia para migrar rapidamente cargas de trabalho da Oracle para a Azure Database para postgreSQL usando a ferramenta ora2pg. |
| [Oracle para Azure PostgreSQL Migrações Soluções Alternativas](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | Este documento destina-se a fornecer aos arquitetos, consultores, DBAs e funções relacionadas com um guia para a fixação/trabalho rápido em torno de problemas enquanto migra cargas de trabalho da Oracle para a Azure Database para PostgreSQL. |
| [Passos para instalar o ora2pg no Windows ou Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | Este documento destina-se a ser utilizado como Guia de Instalação Rápida para permitir a migração de dados de esquemas & da Oracle para a Azure Database para PostgreSQL utilizando a ferramenta ora2pg no Windows ou Linux. Podem ser encontrados detalhes completos sobre a ferramenta em http://ora2pg.darold.net/documentation.html . |

Estes recursos foram desenvolvidos como parte do Programa Ninja Data SQL, que é patrocinado pela equipa de engenharia do Azure Data Group. A carta principal do programa Data SQL Ninja é desbloquear e acelerar a modernização complexa e competir oportunidades de migração da plataforma de dados para a plataforma de dados Azure Data da Microsoft. Se acha que a sua organização estaria interessada em participar no programa Data SQL Ninja, contacte a sua equipa de conta e peça-lhes que apresentem uma nomeação.


### <a name="contact-support"></a>Contactar o suporte

Se precisar de assistência com as suas migrações para além da ferramenta ora2pg, contacte o [ @Ask Azure DB para](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) obter informações sobre outras opções de migração.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma matriz dos serviços/ferramentas da Microsoft e de terceiros disponíveis para o ajudar com vários cenários de base de dados e migração de dados (e tarefas especiais), consulte o serviço [de artigos e ferramentas para a migração de dados.](https://docs.microsoft.com/azure/dms/dms-tools-matrix)

Para saber mais, veja: 
- [Documentação da Base de Dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/)
- [documentação ora2pg](https://ora2pg.darold.net/documentation.html)
- [Site postgreSQL](https://www.postgresql.org/)
- [Suporte de transações autónomas em PostgreSQL](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 

Para conteúdos de vídeo: 
- [Panorâmica da viagem de migração e dos instrumentos/serviços recomendados para a avaliação e migração.](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)