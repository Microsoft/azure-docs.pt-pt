---
title: Regras de avaliação da migração do SQL Server para Azure SQL Database
description: Regras de avaliação para identificar problemas com a origem SQL Server que deve ser abordada antes de migrar para a Base de Dados Azure SQL.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: c407b62b5f0308fbb6d9ff7223a3554ca09592db
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027215"
---
# <a name="assessment-rules-for-sql-server-to-azure-sql-database-migration"></a>Regras de avaliação da migração do SQL Server para Azure SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

As ferramentas de migração validam a sua origem SQL Server, executando uma série de regras de avaliação para identificar problemas que devem ser abordados antes de migrar a sua base de dados sql server para Azure SQL Database. 

Este artigo fornece uma lista das regras utilizadas para avaliar a viabilidade de migrar a sua base de dados SQL Server para Azure SQL Database. 


## <a name="bulk-insert"></a>Inserção em massa<a id="BulkInsert"></a>

**Denominação: A ERM INSERT com fonte de dados blob não-Azure não é suportada na Base de Dados Azure SQL.**   
**Categoria**: Emissão   

**Descrição**   
A Base de Dados Azure SQL não pode aceder a partilhas de ficheiros ou pastas do Windows. Consulte a secção "Objetos Impactados" para as utilizações específicas das declarações de INSERÇÃO A GRANEL que não fazem referência a uma bolha Azure. Os objetos com 'BULK INSERT' onde a fonte não é armazenamento de bolhas Azure não funcionarão depois de migrarem para a Base de Dados Azure SQL. 


**Recomendação**   
Em vez disso, será necessário converter declarações de INSERÇÃO A GRANEL que utilizem ficheiros locais ou partilhas de ficheiros para utilizar ficheiros a partir do armazenamento de blob Azure, quando migrar para a Base de Dados Azure SQL. Em alternativa, migrar para o SQL Server na Azure Virtual Machine.

## <a name="compute-clause"></a>Cláusula de computação<a id="ComputeClause"></a>

**Denominação: A cláusula COMPUTE é descontinuada e foi removida.**   
**Categoria**: Aviso   

**Descrição**   
A cláusula COMPUTE gera totais que aparecem como colunas sumárias adicionais no final do conjunto de resultados. No entanto, esta cláusula já não é suportada na Base de Dados Azure SQL. 


**Recomendação**   
Em vez disso, o módulo T-SQL tem de ser reescrito utilizando o operador ROLLUP. O código abaixo demonstra como o COMPUTE pode ser substituído por ROLLUP: 

```sql 
USE AdventureWorks 
GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP; 
```

Mais informações: [Funcionalidade do motor de base de dados descontinuada no SQL Server ](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="change-data-capture-cdc"></a>Alterar a captura de dados (CDC)<a id="CDC"></a>

**Denominação: A captura de dados de alteração (CDC) não é suportada na Base de Dados Azure SQL**   
**Categoria**: Emissão   


**Descrição**   
A Captura de Dados de Alteração (CDC) não é suportada na Base de Dados Azure SQL. Avalie se o Change Tracking pode ser utilizado.  Em alternativa, migrar para Azure SQL Managed Instance ou SQL Server em Azure Virtual Machines. 


**Recomendação**   
A Captura de Dados de Alteração (CDC) não é suportada na Base de Dados Azure SQL. Avalie se o Change Tracking pode ser usado ou considerar migrar para Azure SQL Managed Instance.

Mais informações: [Ativar o rastreio de alterações Azure SQL](https://social.technet.microsoft.com/wiki/contents/articles/2976.azure-sql-how-to-enable-change-tracking.aspx)

## <a name="clr-assemblies"></a>Conjuntos CLR<a id="ClrAssemblies"></a>

**Denominação: Os conjuntos CLR SQL não são suportados na Base de Dados Azure SQL**   
**Categoria**: Emissão   


**Descrição**   
A Azure SQL Database não suporta conjuntos SQL CLR. 


**Recomendação**   
Atualmente, não há forma de o conseguir na Base de Dados Azure SQL. As soluções alternativas recomendadas exigirão alterações do código de aplicação e da base de dados para utilizar apenas conjuntos suportados pela Base de Dados Azure SQL. Alternativamente migrar para Azure SQL Managed Instance ou SQL Server em Azure Virtual Machine

Mais [informações: Diferenças não suportadas de Transact-SQL na Base de Dados SQL](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)

## <a name="cryptographic-provider"></a>Fornecedor criptográfico<a id="CryptographicProvider"></a>

**Denominação: Foi encontrada uma utilização do FORNECEDOR CRIPTOGRÁFICO CREATE ou DO FORNECEDOR CRIPTOGRÁFICO ALTER, que não é suportado na Base de Dados Azure SQL**   
**Categoria**: Emissão   

**Descrição**   
A Azure SQL Database não suporta declarações de FORNECEDORES CRIPTOGRÁFICOs porque não consegue aceder a ficheiros. Consulte a secção Objetos Impactados para obter as utilizações específicas das declarações do FORNECEDOR CRIPTOGRAPHIC. Os objetos com `CREATE CRYPTOGRAPHIC PROVIDER` ou `ALTER CRYPTOGRAPHIC PROVIDER` não funcionam corretamente após migrarem para a Base de Dados Azure SQL.  


**Recomendação**   
Reveja objetos com `CREATE CRYPTOGRAPHIC PROVIDER` ou `ALTER CRYPTOGRAPHIC PROVIDER` . Em quaisquer objetos necessários, remova as utilizações destas funcionalidades. Alternativamente, migrar para o SQL Server na Azure Virtual Machine

## <a name="cross-database-references"></a>Referências de base de dados cruzadas<a id="CrossDataseReferences"></a>

**Denominação: As consultas de base de dados cruzadas não são suportadas na Base de Dados Azure SQL**   
**Categoria**: Emissão   

**Descrição**   
As bases de dados deste servidor utilizam consultas de base de dados cruzadas, que não são suportadas na Base de Dados Azure SQL. 


**Recomendação**   
A Azure SQL Database não suporta consultas de base de dados cruzadas. Recomenda-se as seguintes ações: 
- Migrar a base de dados dependente(s) para a Base de Dados SQL do Azure e utilizar a funcionalidade Elastic Database Query (Atualmente em pré-visualização) para consultar as bases de dados Azure SQL. 
- Mover os conjuntos de dados dependentes de outras bases de dados para a base de dados que está a ser migrada. 
- Migrar para Azure SQL Caso Gerido.
- Migrar para o SQL Server na Azure Virtual Machine. 

Mais informações: [Verifique consulta de base de dados elástica Azure SQL Database (Pré-visualização)](../../database/elastic-query-overview.md)

## <a name="database-compatibility"></a>Compatibilidade da base de dados<a id="DbCompatLevelLowerThan100"></a>

**Denominação: A Azure SQL Database não suporta níveis de compatibilidade abaixo de 100.**   
**Categoria**: Aviso   

**Descrição**   
O nível de compatibilidade da base de dados é uma ferramenta valiosa para ajudar na modernização da base de dados, permitindo que o SqL Server Database Engine seja atualizado, mantendo ao mesmo nível de compatibilidade de aplicações de pré-actualização. A Base de Dados Azure SQL não suporta níveis de compatibilidade abaixo de 100. 


**Recomendação**   
Avalie se a funcionalidade da aplicação está intacta quando o nível de compatibilidade da base de dados for atualizado para 100 em Azure SQL Managed Instance. Alternativamente, migrar para o SQL Server na Azure Virtual Machine

## <a name="database-mail"></a>Correio de base de dados<a id="DatabaseMail"></a>

**Denominação: O Correio da Base de Dados não é suportado na Base de Dados Azure SQL.**   
**Categoria**: Aviso   

**Descrição**   
Este servidor utiliza a funcionalidade Correio da Base de Dados, que não é suportada na Base de Dados Azure SQL.


**Recomendação**   
Considere migrar para Azure SQL Managed Instance que suporta o Correio da Base de Dados.  Em alternativa, considere utilizar as funções Azure e a Sendgrid para realizar a funcionalidade de correio na Base de Dados Azure SQL.

Mais informações: [Enviar e-mail a partir da Base de Dados Azure SQL usando o script de Funções Azure](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)


## <a name="database-principal-alias"></a>Pseudónimo principal da base de dados<a id="DatabasePrincipalAlias"></a>

**Título: SYS. DATABASE_PRINCIPAL_ALIASES é descontinuada e foi removida.**   
**Categoria**: Emissão   

**Descrição**   
O SYS. DATABASE_PRINCIPAL_ALIASES é descontinuada e foi removida na Base de Dados Azure SQL.  


**Recomendação**   
Use papéis em vez de pseudónimos.

Mais informações: [Funcionalidade do motor de base de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="disable_def_cnst_chk-option"></a>Opção DISABLE_DEF_CNST_CHK<a id="DisableDefCNSTCHK"></a>

**Título: A opção SET DISABLE_DEF_CNST_CHK é descontinuada e foi removida.**   
**Categoria**: Emissão   

**Descrição**   
A opção SET DISABLE_DEF_CNST_CHK é descontinuada e foi removida na Base de Dados Azure SQL.  


Mais informações: [Funcionalidade do motor de base de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>DICA FASTFIRSTROW<a id="FastFirstRowHint"></a>

**Denominação: Dica de consulta FASTFIRSTROW é descontinuada e foi removida.**   
**Categoria**: Aviso   

**Descrição**   
A sugestão de consulta FASTFIRSTROW é descontinuada e foi removida na Base de Dados Azure SQL.  


**Recomendação**   
Em vez de consulta FASTFIRSTROW, opção de utilização de sugestões (FAST n).

Mais informações: [Funcionalidade do motor de base de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>FileStream<a id="FileStream"></a>

**Denominação: O filestream não é suportado na Base de Dados Azure SQL**   
**Categoria**: Emissão   

**Descrição**   
A funcionalidade Filestream, que permite armazenar dados não estruturados, como documentos de texto, imagens e vídeos no sistema de ficheiros NTFS, não é suportada na Base de Dados Azure SQL. 


**Recomendação**   
Faça o upload dos ficheiros não estruturados para o armazenamento do Azure Blob e guarde metadados relacionados com estes ficheiros (nome, tipo, localização de URL, chave de armazenamento, etc.) na Base de Dados Azure SQL. Poderá ter de reformular a sua aplicação para permitir que as bolhas de streaming de e para a Base de Dados Azure SQL. Em alternativa, migrar para o SQL Server na Azure Virtual Machine.

Mais informações: [Streaming blobs de e para o blog Azure SQL](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/)


## <a name="linked-server"></a>Servidor ligado<a id="LinkedServer"></a>

**Título: A funcionalidade do servidor ligado não é suportada na Base de Dados Azure SQL**   
**Categoria**: Emissão   

**Descrição**   
Os servidores ligados permitem ao SQL Server Database Engine executar comandos contra fontes de dados OLE DB fora do caso do SQL Server. 


**Recomendação**   
A Azure SQL Database não suporta a funcionalidade do servidor ligado. Recomenda-se a eliminar a necessidade de servidores ligados: 
- Identifique os conjuntos de dados dependentes dos servidores SQL remotos e considere movê-los para a base de dados que está a ser migrada. 
- Migrar a base de dados dependente para Azure e utilizar a funcionalidade Elastic Database Query (pré-visualização) para consultar através de bases de dados na Base de Dados Azure SQL. 

Mais informações: [Verifique consulta elástica Azure SQL Database (Pré-visualização)](../../database/elastic-query-overview.md) 

## <a name="ms-dtc"></a>MS DTC<a id="MSDTCTransactSQL"></a>

**Denominação: START DISTRIBUTED TRANSACTION não é suportado na Base de Dados Azure SQL.**   
**Categoria**: Emissão   

**Descrição**   
A transação distribuída iniciada pela Transact SQL BEGIN DISTRIBUTED TRANSACTION e gerida pelo Microsoft Distributed Transaction Coordinator (MS DTC) não é suportada na Base de Dados Azure SQL.  


**Recomendação**   
Reveja a secção de objetos impactados em Azure Migrate para ver todos os objetos usando BEGIN DISTRUBUTED TRANSACTION. Considere migrar as bases de dados dos participantes para Azure SQL Managed Instance onde as transações distribuídas em várias instâncias são suportadas (atualmente em pré-visualização). Em alternativa, migrar para o SQL Server na Azure Virtual Machine.

Mais informações: [Transações em vários servidores para Azure SQL Managed Instance ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="openrowset-bulk"></a>OPENROWSET (granel)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Denominação: OpenRowSet utilizado em operação a granel com fonte de dados de armazenamento de blob não-Azure não é suportado na Base de Dados Azure SQL.**   
**Categoria**: Emissão   

**Descrição** O OPENROWSET suporta operações a granel através de um fornecedor BULK incorporado que permite que os dados de um ficheiro sejam lidos e devolvidos como um conjunto de linhas. OpenROWSET com fonte de dados de armazenamento de blob não-Azure não é suportado na Base de Dados Azure SQL.


**Recomendação**   
A Azure SQL Database não pode aceder a partilhas de ficheiros e pastas Windows, pelo que os ficheiros devem ser importados do armazenamento de blob Azure. Portanto, apenas o tipo blob DATASOURCE é suportado na função OPENROWSET. Alternativamente, migrar para o SQL Server na Azure Virtual Machine

Mais informações: [Resolução das diferenças Transact-SQL durante a migração para a Base de Dados SQL](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="openrowset-provider"></a>OPENROWSET (fornecedor)<a id="OpenRowsetWithSQLAndNonSQLProvider"></a>

**Denominação: OpenRowSet com fornecedor SQL ou não-SQL não é suportado na Base de Dados Azure SQL.**   
**Categoria**: Emissão   

**Descrição**   
OpenRowSet com fornecedor SQL ou não-SQL é uma alternativa ao acesso a tabelas num servidor ligado e é um método único, ad hoc de ligar e aceder a dados remotos utilizando o OLE DB. O OpenRowSet com o fornecedor SQL ou não-SQL não é suportado na Base de Dados Azure SQL.


**Recomendação**   
A Azure SQL Database suporta o OPENROWSET apenas para importar do armazenamento de blob Azure. Alternativamente, migrar para o SQL Server na Azure Virtual Machine

Mais informações: [Resolução das diferenças Transact-SQL durante a migração para a Base de Dados SQL](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="non-ansi-left-outer-join"></a>Não-ANSI esquerda unção exterior<a id="NonANSILeftOuterJoinSyntax"></a>

**Denominação: O estilo não ANSI de esquerda da junta exterior é descontinuado e foi removido.**   
**Categoria**: Aviso   

**Descrição**   
A junta exterior de estilo não ANSI é descontinuada e foi removida na Base de Dados Azure SQL. 


**Recomendação**   
Use a sintaxe de junção ANSI.

Mais informações: [Funcionalidade do motor de base de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="non-ansi-right-outer-join"></a>Não-ANSI aderir exterior direito<a id="NonANSIRightOuterJoinSyntax"></a>

**Denominação: A junção exterior do estilo não-ANSI é descontinuada e foi removida.**   
**Categoria**: Aviso   

**Descrição**   
A junção exterior do estilo não ANSI é descontinuada e foi removida na Base de Dados Azure SQL. 


**Recomendação**   
Use a sintaxe de junção ANSI.

Mais informações: [Funcionalidade do motor de base de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="next-column"></a>Próxima coluna<a id="NextColumn"></a>

**Título: Tabelas e Colunas nomeadas NEXT conduzirão a um erro na Base de Dados SQL do Azure.**   
**Categoria**: Emissão   

**Descrição**   
Foram detetadas tabelas ou colunas chamadas NEXT. Sequências, introduzidas no Microsoft SQL Server, utilizam a função ANSI standard NEXT VALUE FOR. Se uma tabela ou uma coluna for chamada NEXT e a coluna for nomeada como VALOR, e se a norma ANSI AS for omitida, a declaração resultante pode causar um erro.  


**Recomendação**   
Reescreva as declarações para incluir a palavra-chave AS padrão ANSI ao aliasar uma tabela ou coluna. Por exemplo, quando uma coluna é chamada NEXT e essa coluna é nomeada como VALOR, a consulta `SELECT NEXT VALUE FROM TABLE` causará um erro e deve ser reescrita como SELECT NEXT AS VALUE FROM TABLE. Da mesma forma, quando uma tabela é chamada NEXT e essa tabela é aliasada como VALUE, a consulta `SELECT Col1 FROM NEXT VALUE` causará um erro e deve ser reescrita como `SELECT Col1 FROM NEXT AS VALUE` .

## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Denominação: As chamadas RAISERROR de estilo legado devem ser substituídas por equivalentes modernos.**   
**Categoria**: Aviso   

**Descrição**   
As chamadas RAISERROR como o exemplo abaixo são denominadas como estilo legado porque não incluem as vírgulas e os parênteses. `RAISERROR 50001 'this is a test'`. Este método de chamada RAISERROR é descontinuado e removido na Base de Dados Azure SQL. 


**Recomendação**   
Reescreva a declaração utilizando a sintaxe atual do RAISERROR, ou avalie se a abordagem moderna `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` é viável.

Mais informações: [Funcionalidade do motor de base de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="server-audits"></a>Auditorias de servidores<a id="ServerAudits"></a>

**Título: Utilize funcionalidades de auditoria da Base de Dados Azure SQL para substituir auditorias do servidor**   
**Categoria**: Aviso   

**Descrição**   
As auditorias do servidor não são suportadas na Base de Dados Azure SQL.


**Recomendação**   
Considere as funcionalidades de auditoria da Base de Dados Azure SQL para substituir as Auditorias do Servidor.  O Azure SQL suporta a auditoria e as funcionalidades são mais ricas que o SQL Server. A base de dados Azure SQL pode auditar várias ações e eventos de base de dados, incluindo: Acesso a dados, alterações de Schema (DDL), alterações de dados (DML), Contas, funções e permissões (DCL, exceções de segurança. A Azure SQL Database Auditing aumenta a capacidade de uma organização obter uma visão profunda dos eventos e alterações que ocorrem dentro da sua base de dados, incluindo atualizações e consultas contra os dados. Alternativamente, migrar para Azure SQL Managed Instance ou SQL Server em Azure Virtual Machine.

Mais informações: [Auditoria para Azure SQL Database ](../../database/auditing-overview.md)

## <a name="server-credentials"></a>Credenciais de servidor<a id="ServerCredentials"></a>

**Título: Credencial de âmbito do servidor não é suportada na Base de Dados Azure SQL**   
**Categoria**: Aviso   

**Descrição**   
Uma credencial é um registo que contém as informações de autenticação (credenciais) necessárias para se ligar a um recurso fora do SQL Server. A Azure SQL Database suporta credenciais de base de dados, mas não as criadas no âmbito do SQL Server.   


**Recomendação**   
A Azure SQL Database suporta credenciais de âmbito de base de dados. Converter credenciais de âmbito do servidor para credenciais de âmbito de base de dados. Alternativamente migrar para Azure SQL Managed Instance ou SQL Server em Azure Virtual Machine

Mais informações: [Criação de credenciais de âmbito de base de dados](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)

## <a name="service-broker"></a>Mediador de Serviço<a id="ServiceBroker"></a>

**Título: A funcionalidade de corretor de serviços não é suportada na Base de Dados Azure SQL**   
**Categoria**: Emissão   

**Descrição**   
O SQL Server Service Broker fornece suporte nativo para aplicações de mensagens e filas no Motor de Base de Dados do Servidor SQL. A função de Corretor de Serviços não é suportada na Base de Dados Azure SQL.


**Recomendação**   
A função de Corretor de Serviços não é suportada na Base de Dados Azure SQL. Considere migrar para a Azure SQL Managed Instance que suporta o corretor de serviços no mesmo caso. Em alternativa, migrar para o SQL Server na Azure Virtual Machine. 

## <a name="server-scoped-triggers"></a>Triggers de âmbito do servidor<a id="ServerScopedTriggers"></a>

**Título: O gatilho com âmbito de servidor não é suportado na Base de Dados Azure SQL**   
**Categoria**: Aviso   

**Descrição**   
Um gatilho é um tipo especial de procedimento armazenado que executa em resposta a determinadas ações numa tabela como inserção, eliminação ou atualização de dados. Os gatilhos com âmbito de servidor não são suportados na Base de Dados Azure SQL. A Azure SQL Database não suporta as seguintes opções para gatilhos: PARA LOGON, ENCRIPTAÇÃO, COM APPEND, NÃO PARA REPLICAÇÃO, Opção NOME EXTERNO (não existe suporte a métodos externos), Opção ALL SERVER (DDL Trigger), Trigger em um evento LOGON (Logon Trigger), Azure SQL Database não suporta gatilhos CLR.


**Recomendação**   
Use o gatilho do nível da base de dados. Alternativamente migrar para Azure SQL Managed Instance ou SQL Server em Azure Virtual Machine

Mais informações: [Resolução das diferenças Transact-SQL durante a migração para a Base de Dados SQL](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="sql-agent-jobs"></a>Empregos de agente SQL<a id="AgentJobs"></a>

**Título: Os trabalhos do Agente do Servidor SQL não estão disponíveis na Base de Dados Azure SQL**   
**Categoria**: Aviso   

**Descrição**   
SQL Server Agent é um serviço microsoft Windows que executa tarefas administrativas programadas, que são chamadas de empregos no SQL Server. Os trabalhos do SqL Server Agent não estão disponíveis na Base de Dados Azure SQL. 


**Recomendação** Utilize trabalhos elásticos (pré-visualização), que são a substituição de trabalhos de Agente de Servidor SQL na Base de Dados Azure SQL. Os trabalhos de Base de Dados Elásticas para Azure SQL Database permitem executar de forma fiável scripts T-SQL que abrangem várias bases de dados, enquanto automaticamente reagem e fornecem eventuais garantias de conclusão. Alternativamente, considere migrar para Azure SQL Managed Instance ou SQL Server em Azure Virtual Machines.

Mais informações: [Começar com trabalhos de Base de Dados Elásticas (Pré-visualização) ](../../database/elastic-jobs-overview.md)

## <a name="sql-database-size"></a>Tamanho da base de dados SQL<a id="SQLDBDatabaseSize"></a>

**Denominação: A Azure SQL Database não suporta tamanho de base de dados superior a 100 TB.**   
**Categoria**: Emissão   

**Descrição**   
O tamanho da base de dados é maior do que o tamanho máximo suportado de 100 TB. 


**Recomendação**   
Avalie se os dados podem ser arquivados ou comprimidos ou fragmentos em várias bases de dados. Em alternativa, migrar para o SQL Server na Azure Virtual Machine. 

Mais informações: [vCore limites de recursos](../../database/resource-limits-vcore-single-databases.md) 

## <a name="sql-mail"></a>SQL Mail<a id="SqlMail"></a>

**Denominação: SQL Mail foi descontinuado.**   
**Categoria**: Aviso   

**Descrição**   
O SQL Mail foi descontinuado e removido na Base de Dados Azure SQL.


**Recomendação**   
Considere migrar para Azure SQL Managed Instance ou SQL Server em Azure Virtual Machines e usar o Correio da Base de Dados.

Mais informações: [Funcionalidade do motor de base de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="systemprocedures110"></a>SistemaProcedures110<a id="SystemProcedures110"></a>

**Denominação: Declarações detetadas que referenciam procedimentos armazenados do sistema que não estão disponíveis na Base de Dados Azure SQL.**   
**Categoria**: Aviso   

**Descrição**   
Os sistemas não suportados e os procedimentos armazenados alargados não podem ser utilizados na base de dados Azure SQL - `sp_dboption` , , , , , . . `sp_addserver` `sp_dropalias` `sp_activedirectory_obj` `sp_activedirectory_scp` `sp_activedirectory_start` .


**Recomendação**    
Remova as referências aos procedimentos não suportados do sistema que tenham sido removidos na Base de Dados Azure SQL.

Mais informações: [Funcionalidade do motor de base de dados descontinuada no SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="trace-flags"></a>Traços de bandeiras<a id="TraceFlags"></a>

**Denominação: Azure SQL Database não suporta vestígios de bandeiras**   
**Categoria**: Aviso   

**Descrição**   
As bandeiras de rastreio são usadas para definir temporariamente características específicas do servidor ou para desligar um comportamento específico. As bandeiras de vestígios são frequentemente usadas para diagnosticar problemas de desempenho ou para depurar procedimentos armazenados ou sistemas informáticos complexos. A base de dados Azure SQL não suporta vestígios de bandeiras. 


**Recomendação**   
Reveja a secção de objetos impactados em Azure Migrate para ver todas as bandeiras de vestígios que não são suportadas na Base de Dados Azure SQL e avaliar se podem ser removidas. Alternativamente, migrar para Azure SQL Managed Instance que suporta um número limitado de bandeiras de traços globais ou servidor SQL na Máquina Virtual Azure.

Mais informações: [Resolução das diferenças Transact-SQL durante a migração para a Base de Dados SQL](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="windows-authentication"></a>Autenticação do Windows<a id="WindowsAuthentication"></a>

**Denominação: Os utilizadores de bases de dados mapeados com a autenticação do Windows (segurança integrada) não são suportados na Base de Dados Azure SQL.**   
**Categoria**: Aviso   

**Descrição**   
Azure SQL Database suporta dois tipos de autenticação 
- Autenticação SQL: usa um nome de utilizador e senha 
- Autenticação do Diretório Ativo Azure: utiliza identidades geridas pelo Azure Ative Directory e é suportada por domínios geridos e integrados. 

Os utilizadores de bases de dados mapeados com a autenticação do Windows (segurança integrada) não são suportados na Base de Dados Azure SQL. 



**Recomendação**   
Federate o Diretório Ativo local com o Diretório Ativo Azure. A identidade do Windows pode então ser substituída pelas identidades equivalentes do Azure Ative Directory. Em alternativa, migrar para o SQL Server na Azure Virtual Machine.

Mais informações: [SqL Database security capabilities](../../database/security-overview.md#authentication)

## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Denominação: xp_cmdshell não é suportado na Base de Dados Azure SQL.**   
**Categoria**: Emissão   

**Descrição**   
xp_cmdshell que gera uma concha de comando do Windows e passa numa cadeia para execução não é suportado na Base de Dados Azure SQL. 


**Recomendação**   
Reveja a secção de objetos impactados em Azure Migrate para ver todos os objetos usando xp_cmdshell e avaliar se a referência a xp_cmdshell ou o objeto impactado pode ser removida. Considere também explorar a Azure Automation que oferece um serviço de automação e configuração baseado na nuvem. Em alternativa, migrar para o SQL Server na Azure Virtual Machine. 

## <a name="next-steps"></a>Passos seguintes

Para começar a migrar o seu SQL Server para Azure SQL Database, consulte o guia de migração do [SQL Server para o SQL Database](sql-server-to-sql-database-guide.md).

- Para uma matriz dos serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para o ajudar com vários cenários de base de dados e migração de dados, bem como tarefas especiais, consulte [o Serviço e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre a Base de Dados SQL consulte:
   - [Visão geral da Base de Dados Azure SQL](../../database/sql-database-paas-overview.md)
   - [Custo total da calculadora de propriedade](https://azure.microsoft.com/pricing/tco/calculator/) 

- Para saber mais sobre o ciclo de enquadramento e adoção para migrações em nuvem, ver
   -  [Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [As melhores práticas para custos e cargas de trabalho de tamanho migram para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Para avaliar a camada de acesso à aplicação, consulte o [Kit de Ferramentas de Migração do Acesso a Dados (Pré-visualização)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para mais detalhes sobre como realizar testes da Camada A/B do Acesso de Dados consulte [o Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-overview).
