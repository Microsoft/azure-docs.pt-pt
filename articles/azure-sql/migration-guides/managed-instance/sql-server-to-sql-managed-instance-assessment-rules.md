---
title: Regras de avaliação do SQL Server para a migração de instâncias geridas sql
description: Regras de avaliação para identificar problemas com a origem SQL Server que deve ser abordada antes de migrar para Azure SQL Managed Instance.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: 760a6496ff297ae6328810589f780b430d55b18a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054725"
---
# <a name="assessment-rules-for-sql-server-to-sql-managed-instance-migration"></a>Regras de avaliação do SQL Server para a migração de instâncias geridas sql
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

As ferramentas de migração validam a sua origem SQL Server, executando uma série de regras de avaliação para identificar problemas que devem ser abordados antes de migrar a sua base de dados do SQL Server para Azure SQL Managed Instance. 

Este artigo fornece uma lista das regras utilizadas para avaliar a viabilidade de migrar a sua base de dados sql Server para Azure SQL Managed Instance. 

## <a name="analysiscommand-job"></a>Trabalho de AnáliseCommand<a id="AnalysisCommandJob"></a>

**Denominação: Análise O passo de trabalho da Empresa não é suportado em Azure SQL Managed Instance.**   
**Categoria**: Aviso   


**Descrição**   
É um passo de trabalho que dirige um comando dos Serviços de Análise. Análise O passo de trabalho da Empresa não é suportado em Azure SQL Managed Instance.

**Recomendação**     
Reveja a secção de objetos impactados em Azure Migrate para ver todos os trabalhos usando o passo de trabalho do Comando de Serviço de Análise e avaliar se o passo de trabalho ou o objeto impactado podem ser removidos. Em alternativa, migrar para o SQL Server na Azure Virtual Machine.

Mais informações: [Diferenças de agente de servidor SQL em Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="analysisquery-job"></a>Trabalho de AnáliseQuery<a id="AnalysisQueryJob"></a>

**Denominação: AnáliseSA passo de trabalho não é suportado em Azure SQL Managed Instance.**   
**Categoria**: Aviso   

**Descrição**   
É um passo de trabalho que executa uma consulta de Serviços de Análise. AnáliseA passo de trabalho Não é suportado em Azure SQL Managed Instance.


**Recomendação**   
Reveja a secção de objetos impactados em Azure Migrate para ver todos os trabalhos usando o passo de trabalho do Serviço de Análise e avaliar se o passo de trabalho ou o objeto impactado podem ser removidos. Em alternativa, migrar para o SQL Server na Azure Virtual Machine.

Mais informações: [Diferenças de agente de servidor SQL em Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="assembly-from-file"></a>Montagem a partir de arquivo<a id="AssemblyFromFile"></a>

**Denominação: 'CREATE ASSEMBLY' e 'ALTER ASSEMBLY' com um parâmetro de ficheiro não são suportados em Azure SQL Managed Instance.**   
**Categoria**: Aviso   

**Descrição**   
A Azure SQL Managed Instance não pode aceder a partilhas de ficheiros ou pastas Windows. Consulte a secção "Objetos Impactados" para as utilizações específicas das declarações de INSERÇÃO A GRANEL que não fazem referência a uma bolha Azure. Objetos com 'BULK INSERT' onde a fonte não é armazenamento de bolhas Azure não funcionarão depois de migrarem para Azure SQL Managed Instance.


**Recomendação**   
Em vez disso, terá de converter declarações de INSERÇÃO A GRANEL que utilizem ficheiros locais ou partilhas de ficheiros para utilizar ficheiros a partir do armazenamento de blob Azure, ao migrar para a Azure SQL Managed Instance. Em alternativa, migrar para o SQL Server na Azure Virtual Machine. 

Mais informações: [Diferenças CLR em Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#clr)

## <a name="bulk-insert"></a>Inserção em massa<a id="BulkInsert"></a>

**Denominação: A ERM INSERT com fonte de dados blob não-Azure não é suportada em Azure SQL Managed Instance.**   
**Categoria**: Emissão   

**Descrição**   
A Azure SQL Managed Instance não pode aceder a partilhas de ficheiros ou pastas Windows. Consulte a secção "Objetos Impactados" para as utilizações específicas das declarações de INSERÇÃO A GRANEL que não fazem referência a uma bolha Azure. Objetos com 'BULK INSERT' onde a fonte não é armazenamento de bolhas Azure não funcionarão depois de migrarem para Azure SQL Managed Instance.


**Recomendação**   
Em vez disso, terá de converter declarações de INSERÇÃO A GRANEL que utilizem ficheiros locais ou partilhas de ficheiros para utilizar ficheiros a partir do armazenamento de blob Azure, ao migrar para a Azure SQL Managed Instance.

Mais informações: [Inserção a granel e diferenças OPENROWSET em Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="clr-security"></a>Segurança CLR<a id="ClrStrictSecurity"></a>

**Denominação: Os conjuntos CLR marcados como SAFE ou EXTERNAL_ACCESS são considerados INSEGUROS**   
**Categoria**: Emissão   

**Descrição**   
O modo de segurança rígida CLR é aplicado em Azure SQL Managed Instance. Este modo é ativado por predefinição e introduz alterações de rutura para bases de dados que contenham conjuntos CLR definidos pelo utilizador marcados como SEGUROS ou EXTERNAL_ACCESS.


**Recomendação**   
O CLR utiliza a Code Access Security (CAS) no Quadro .NET, que já não é suportado como uma fronteira de segurança. Começando com o motor de base de dados SQL Server 2017 (14.x), é introduzida uma `sp_configure` opção chamada clr strict security para aumentar a segurança dos conjuntos CLR. A segurança rígida clr é ativada por padrão, e trata os conjuntos SAFE e EXTERNAL_ACCESS CLR como se estivessem marcados INSEGUROS. Quando a segurança rígida clr é desativada, um conjunto CLR criado com PERMISSION_SET = SAFE pode ser capaz de aceder a recursos externos do sistema, chamar código não gerido e adquirir privilégios sysadmin. Depois de permitir uma segurança rigorosa, quaisquer conjuntos que não estejam assinados deixarão de ser carregados. Além disso, se uma base de dados tiver conjuntos seguros ou EXTERNAL_ACCESS, as declarações RESTORE ou ATTACH DATABASE podem ser concluídas, mas os conjuntos podem não conseguir carregar. Para carregar os conjuntos, deve alterar ou largar e recriar cada conjunto de modo a que seja assinado com um certificado ou chave assimétrica que tenha um login correspondente com a permissão DE MONTAGEM INSEGURA no servidor.

Mais informações: [ClR segurança rigorosa](/sql/database-engine/configure-windows/clr-strict-security)

## <a name="compute-clause"></a>Cláusula de computação<a id="ComputeClause"></a>

**Denominação: A cláusula COMPUTE é descontinuada e foi removida.**   
**Categoria**: Aviso   

**Descrição**   
A cláusula COMPUTE gera totais que aparecem como colunas sumárias adicionais no final do conjunto de resultados. No entanto, esta cláusula já não é suportada em Azure SQL Managed Instance.



**Recomendação**   
Em vez disso, o módulo T-SQL tem de ser reescrito utilizando o operador ROLLUP. O código abaixo demonstra como o COMPUTE pode ser substituído por ROLLUP: 

```sql
USE AdventureWorks GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) 
BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP;
```

Mais informações: [Funcionalidade do motor de base de dados descontinuada no SqL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="cryptographic-provider"></a>Fornecedor criptográfico<a id="CryptographicProvider"></a>

**Denominação: Foi encontrada uma utilização do FORNECEDOR CRIPTOGRÁFICO CREATE ou DO FORNECEDOR CRIPTOGRÁFICO ALTER, que não é suportado em Azure SQL Managed Instance.**   
**Categoria**: Emissão   

**Descrição**   
A Azure SQL Managed Instance não suporta declarações de FORNECEDORES CRIPTOGRÁFICOs porque não consegue aceder a ficheiros. Consulte a secção Objetos Impactados para obter as utilizações específicas das declarações do FORNECEDOR CRIPTOGRAPHIC. Os objetos com 'CREATE CRYPTOGRAPHIC PROVIDER' ou 'ALTER CRYPTOGRAPHIC PROVIDER' não funcionarão corretamente após migrarem para a Azure SQL Managed Instance.


**Recomendação**   
Reveja os objetos com "CREATE CRYPTOGRAPHIC PROVIDER" ou "ALTER CRYPTOGRAPHIC PROVIDER". Em quaisquer objetos necessários, remova as utilizações destas funcionalidades. Em alternativa, migrar para o SQL Server na Azure Virtual Machine. 

Mais informações: [Diferenças de fornecedores criptográficos em Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#cryptographic-providers)

## <a name="database-compatibility"></a>Compatibilidade da base de dados<a id="DbCompatLevelLowerThan100"></a>

**Denominação: Nível de compatibilidade da base de dados abaixo de 100 não é suportado**   
**Categoria**: Aviso   

**Descrição**   
O Nível de Compatibilidade da Base de Dados é uma ferramenta valiosa para ajudar na modernização da base de dados, permitindo que o SqL Server Database Engine seja atualizado, mantendo ao mesmo nível de compatibilidade de aplicações de ligação, mantendo o mesmo nível de compatibilidade pré-actualização da base de dados. Azure SQL Managed Instance não suporta níveis de compatibilidade abaixo de 100. Quando a base de dados com nível de compatibilidade inferior a 100 é restaurada em Azure SQL Managed Instance, o nível de compatibilidade é atualizado para 100. 


**Recomendação...** Avalie se a funcionalidade da aplicação está intacta quando o nível de compatibilidade da base de dados for atualizado para 100 em Azure SQL Managed Instance. Em alternativa, migrar para o SQL Server na Azure Virtual Machine.

Mais informações: [Níveis de compatibilidade suportados em Instância Gerida Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#compatibility-levels)

## <a name="database-principal-alias"></a>Pseudónimo principal da base de dados<a id="DatabasePrincipalAlias"></a>

**Título: SYS. DATABASE_PRINCIPAL_ALIASES é descontinuada e foi removida.**   
**Categoria**: Emissão   

**Descrição**   
O SYS. DATABASE_PRINCIPAL_ALIASES é descontinuada e foi removida em Azure SQL Managed Instance.


**Recomendação**   
Use papéis em vez de pseudónimos.

Mais informações: [Funcionalidade do motor de base de dados descontinuada no SqL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="disable_def_cnst_chk-option"></a>Opção DISABLE_DEF_CNST_CHK<a id="DisableDefCNSTCHK"></a>

**Título: A opção SET DISABLE_DEF_CNST_CHK é descontinuada e foi removida.**   
**Categoria**: Emissão   

**Descrição**   
A opção SET DISABLE_DEF_CNST_CHK é descontinuada e foi removida em Azure SQL Managed Instance.


Mais informações: [Funcionalidade do motor de base de dados descontinuada no SqL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>DICA FASTFIRSTROW<a id="FastFirstRowHint"></a>

**Denominação: Dica de consulta FASTFIRSTROW é descontinuada e foi removida.**   
**Categoria**: Aviso   

**Descrição**   
A sugestão de consulta FASTFIRSTROW é descontinuada e foi removida em Azure SQL Managed Instance.


**Recomendação**   
Em vez de consulta FASTFIRSTROW, opção de utilização de sugestões (FAST n).

Mais informações: [Funcionalidade do motor de base de dados descontinuada no SqL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>FileStream<a id="FileStream"></a>

**Denominação: Filestream e Filetable não são suportados em Azure SQL Managed Instance.**   
**Categoria**: Emissão   

**Descrição**   
A funcionalidade Filestream, que permite armazenar dados não estruturados, tais como documentos de texto, imagens e vídeos no sistema de ficheiros NTFS, não é suportada em Azure SQL Managed Instance. **Esta base de dados não pode ser migrada, uma vez que a cópia de segurança que contém grupos de ficheiros Filestream não pode ser restaurada em Azure SQL Managed Instance.**


**Recomendação**   
Faça o upload dos ficheiros não estruturados para o armazenamento do Azure Blob e guarde metadados relacionados com estes ficheiros (nome, tipo, localização de URL, chave de armazenamento, etc.) em Azure SQL Managed Instance. Poderá ter de reformular a sua aplicação para permitir que as bolhas de streaming de e para a Azure SQL Managed Instance. Em alternativa, migrar para o SQL Server na Azure Virtual Machine.

Mais informações: [Streaming Blobs De e A partir do blog SQL Azure](https://azure.microsoft.com/en-in/blog/streaming-blobs-to-and-from-sql-azure/)

## <a name="heterogeneous-ms-dtc"></a>MS DTC heterogéneo<a id="MIHeterogeneousMSDTCTransactSQL"></a>

**Título: BEGIN DISTRIBUTED TRANSACTION with non-SQL Server remote server is not supported in Azure SQL Managed Instance.**   
**Categoria**: Emissão   

**Descrição**   
A transação distribuída iniciada pelo Transact SQL BEGIN DISTRIBUTED TRANSACTION e gerida pelo Microsoft Distributed Transaction Coordinator (MS DTC) não é suportada em Azure SQL Managed Instance se o servidor remoto não for o SQL Server. 


**Recomendação**   
Reveja a secção de objetos impactados em Azure Migrate para ver todos os objetos usando BEGIN DISTRUBUTED TRANSACTION. Considere migrar as bases de dados dos participantes para Azure SQL Managed Instance onde as transações distribuídas em várias instâncias são suportadas (atualmente em pré-visualização). Em alternativa, migrar para o SQL Server na Azure Virtual Machine.

Mais informações: [Transações em vários servidores para Azure SQL Managed Instance ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)

## <a name="homogenous-ms-dtc"></a>MS DTC Homogéneo<a id="MIHomogeneousMSDTCTransactSQL"></a>

**Título: BEGIN DISTRIBUTED TRANSACTION é suportado em vários servidores para Azure SQL Managed Instance.**   
**Categoria**: Emissão   

**Descrição**   
A transação distribuída iniciada por Transact SQL BEGIN DISTRIBUTED TRANSACTION e gerida pelo Microsoft Distributed Transaction Coordinator (MS DTC) é suportada em vários servidores para Azure SQL Managed Instance.


**Recomendação**   
Reveja a secção de objetos impactados em Azure Migrate para ver todos os objetos usando BEGIN DISTRUBUTED TRANSACTION. Considere migrar as bases de dados dos participantes para Azure SQL Managed Instance onde as transações distribuídas em várias instâncias são suportadas (atualmente em pré-visualização). Em alternativa, migrar para o SQL Server na Azure Virtual Machine. 

Mais informações: [Transações em vários servidores para Azure SQL Managed Instance](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="linked-server-non-sql-provider"></a>Servidor ligado (fornecedor não-SQL)<a id="LinkedServerWithNonSQLProvider"></a>

**Título: O servidor ligado ao fornecedor de servidores não-SQL não é suportado em Azure SQL Managed Instance.**   
**Categoria**: Emissão   

**Descrição**   
Os servidores ligados permitem ao SQL Server Database Engine executar comandos contra fontes de dados OLE DB fora do caso do SQL Server. O servidor ligado com o fornecedor de servidores não-SQL não é suportado em Azure SQL Managed Instance. 


**Recomendação**   
A azure SQL Managed Instance não suporta a funcionalidade do servidor ligado se o fornecedor de servidor remoto não for O Servidor SQL como o Oracle, Sybase, etc. 

Recomenda-se a eliminar a necessidade de servidores ligados: 
- Identifique a(s) base de dados dependente(s) a partir de servidores não SQL remotos e considere movê-los para a base de dados que está a ser migrada. 
- Migrar a base de dados dependente(s) para alvos suportados como SQL Managed Instance, SQL Database, Azure Synapse SQL e SQL Server. 
- Considere criar um servidor ligado entre a Azure SQL Managed Instance e o SQL Server na Azure Virtual Machine (SQL VM).  Em seguida, a partir do SQL VM criar servidor ligado à Oracle, Sybase etc. Esta abordagem envolve dois lúpulo, mas pode ser usada como solução temporária.  
- Em alternativa, migrar para o SQL Server na Azure Virtual Machine.

Mais informações: [Diferenças de servidores ligados em Azure SQL Gestão De Instância](../../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)

## <a name="merge-job"></a>Trabalho de fusão<a id="MergeJob"></a>

**Denominação: O passo de trabalho de fusão não é suportado em Azure SQL Managed Instance.**   
**Categoria**: Aviso   

**Descrição**   
É um passo de trabalho que ativa o agente de fusão de replicação. O Agente de Fusão de Replicação é um utilitário executável que aplica o instantâneo inicial mantido nas tabelas de bases de dados aos Assinantes. Também funde alterações incrementais de dados que ocorreram no Editor após a criação do snapshot inicial, e concilia conflitos de acordo com as regras que configura ou usando um resolver personalizado que cria. O passo de trabalho de fusão não é suportado em Azure SQL Managed Instance.


**Recomendação**   
Reveja a secção de objetos impactados em Azure Migrate para ver todos os empregos usando o passo de trabalho da Merge e avaliar se o passo de trabalho ou o objeto impactado podem ser removidos. Alternativamente, migrar para o SQL Server na Azure Virtual Machine

Mais informações: [Diferenças de agente de servidor SQL em Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="mi-database-size"></a>Tamanho da base de dados do MI<a id="MIDatabaseSize<"></a>

**Denominação: Azure SQL Managed Instance não suporta o tamanho da base de dados superior a 8 TB.**   
**Categoria**: Emissão   

**Descrição**   
O tamanho da base de dados é maior do que o máximo de caso reservado. **Esta base de dados não pode ser selecionada para migração, uma vez que o tamanho excedeu o limite permitido.**


**Recomendação**   
Avalie se os dados podem ser arquivados comprimidos ou fragmentos em várias bases de dados. Em alternativa, migrar para o SQL Server na Azure Virtual Machine.

Mais informações: [Características de geração de hardware do Azure SQL Gestão De Instância ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)



## <a name="mi-instance-size"></a>Tamanho da instância do MI<a id="MIInstanceSize<"></a>

**Denominação: O tamanho máximo de armazenamento de instância em Azure SQL Managed Instance não pode ser superior a 8 TB.**   
**Categoria**: Aviso   

**Descrição**   
O tamanho de todas as bases de dados é maior do que o máximo de caso reservado.  


**Recomendação**   
Considere migrar as bases de dados para diferentes instâncias geridas Azure SQL ou para O SQL Server na Azure Virtual Machine se todas as bases de dados tão bem existirem no mesmo caso. 

Mais informações: [Características de geração de hardware do Azure SQL Gestão De Instância ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)


## <a name="multiple-log-files"></a>Vários ficheiros de registo<a id="MultipleLogFiles<"></a>

**Denominação: Azure SQL Managed Instance não suporta vários ficheiros de registo.**   
**Categoria**: Emissão   

**Descrição**   
O SQL Server permite que uma base de dados faça login em vários ficheiros. Esta base de dados tem vários ficheiros de registo que não são suportados em Azure SQL Managed Instance. **Esta base de dados não pode ser migrada, uma vez que a cópia de segurança não pode ser restaurada em Azure SQL Managed Instance. 
**

**Recomendação**   
Azure SQL Managed Instance suporta apenas um único registo por base de dados. Tem de eliminar todos, menos um dos ficheiros de registo, antes de migrar esta base de dados para Azure: 

```sql
ALTER DATABASE [database_name] REMOVE FILE [log_file_name]
```

Mais [informações: Opções de base de dados não suportadas em Azure SQL Gestão De Instância  ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#database-options)



## <a name="next-column"></a>Próxima coluna<a id="NextColumn"></a>

**Título: Tabelas e Colunas nomeadas NEXT conduzirão a um erro em Azure SQL Managed Instance.**   
**Categoria**: Emissão   

**Descrição**   
Foram detetadas tabelas ou colunas chamadas NEXT. Sequências, introduzidas no Microsoft SQL Server, utilizam a função ANSI standard NEXT VALUE FOR. Se uma tabela ou uma coluna for chamada NEXT e a coluna for nomeada como VALOR, e se a norma ANSI AS for omitida, a declaração resultante pode causar um erro.


**Recomendação**   
Reescreva as declarações para incluir a palavra-chave AS padrão ANSI ao aliasar uma tabela ou coluna. Por exemplo, quando uma coluna é nomeada NEXT e essa coluna é nomeada como VALOR, a consulta SELECT NEXT VALUE FROM TABLE causará um erro e deve ser reescrita como SELECT NEXT AS VALUE FROM TABLE. Da mesma forma, quando uma tabela é chamada NEXT e esta tabela é aliasada como VALOR, a consulta SELECT Col1 FROM NEXT VALUE causará um erro e deve ser reescrita como SELECT Col1 FROM NEXT AS VALUE.



## <a name="non-ansi-style-left-outer-join"></a>Estilo não-ANSI deixou a união exterior<a id="NonANSILeftOuterJoinSyntax"></a>

**Denominação: O estilo não ANSI de esquerda da junta exterior é descontinuado e foi removido.**   
**Categoria**: Aviso   

**Descrição**   
A junta exterior de estilo não ANSI é descontinuada e foi removida em Azure SQL Managed Instance. 


**Recomendação**   
Use a sintaxe de junção ANSI.

Mais informações: [Funcionalidade do motor de base de dados descontinuada no SqL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="non-ansi-style-right-outer-join"></a>Não-ANSI estilo direito exterior<a id="NonANSIRightOuterJoinSyntax"></a>

**Denominação: A junção exterior do estilo não-ANSI é descontinuada e foi removida.**   
**Categoria**: Aviso   

**Descrição**   
A junção exterior do estilo não ANSI é descontinuada e foi removida em Azure SQL Managed Instance. 



Mais informações: [Funcionalidade do motor de base de dados descontinuada no SqL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

**Recomendação**   
Use a sintaxe de junção ANSI.

## <a name="databases-exceed-100"></a>Bases de dados superiores a 100 <a id="NumDbExceeds100"></a>

**Denominação: Azure SQL Managed Instance suporta um máximo de 100 bases de dados por exemplo.**   
**Categoria**: Aviso   

**Descrição**   
O número máximo de bases de dados suportadas em Azure SQL Managed Instance é de 100, a menos que o limite de tamanho de armazenamento de instância tenha sido atingido.



**Recomendação**   
Considere migrar as bases de dados para diferentes instâncias geridas Azure SQL ou para O SQL Server na Azure Virtual Machine se todas as bases de dados tão bem existirem no mesmo caso. 

Mais informações: [Limites de recursos geridos a Azure SQL ](../../managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="openrowset-non-blob-data-source"></a>OPENROWSET (fonte de dados não blob)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Denominação: OpenRowSet utilizado em operação a granel com fonte de dados de armazenamento de blob não-Azure não é suportado em Azure SQL Managed Instance.**   
**Categoria**: Emissão   

**Descrição**   
O OPENROWSET suporta operações a granel através de um fornecedor BULK incorporado que permite que os dados de um ficheiro sejam lidos e devolvidos como um conjunto de linhas. OpenROWSET com fonte de dados de armazenamento de blob não-Azure não é suportado em Azure SQL Managed Instance. 



**Recomendação**   
A função OPENROWSET só pode ser utilizada para executar consultas em casos de SQL Server (geridas, no local ou em Máquinas Virtuais). Apenas os valores SQLNCLI, SQLNCLI11 e SQLOLEDB são suportados como fornecedor. Portanto, a ação de recomendação é que identifiquem a(s) base de dados dependente(s) de servidores remotos não-SQL e considerar movê-los para a base de dados que está a ser migrada. Alternativamente, migrar para o SQL Server na Azure Virtual Machine

Mais informações: [Inserção a granel e diferenças OPENROWSET em Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)

## <a name="openrowset-non-sql-provider"></a>OPENROWSET (fornecedor não SQL)<a id="OpenRowsetWithNonSQLProvider"></a>

**Denominação: OpenRowSet com fornecedor não-SQL não é suportado em Azure SQL Managed Instance.**   
**Categoria**: Emissão   

**Descrição**   
Este método é uma alternativa ao acesso a tabelas num servidor ligado e é um método único, ad hoc, de ligação e acesso a dados remotos utilizando o OLE DB. O OpenRowSet com fornecedor não-SQL não é suportado em Azure SQL Managed Instance. 



**Recomendação**   
A função OPENROWSET só pode ser utilizada para executar consultas em casos de SQL Server (geridas, no local ou em Máquinas Virtuais). Apenas os valores SQLNCLI, SQLNCLI11 e SQLOLEDB são suportados como fornecedor. Portanto, a ação de recomendação é que identifiquem a(s) base de dados dependente(s) de servidores remotos não-SQL e considerar movê-los para a base de dados que está a ser migrada.

Mais informações: [Inserção a granel e diferenças OPENROWSET em Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="powershell-job"></a>Trabalho PowerShell<a id="PowerShellJob"></a>

**Denominação: O passo de trabalho da PowerShell não é suportado em Azure SQL Managed Instance.**   
**Categoria**: Aviso   

**Descrição**   
É um passo de trabalho que executa um guião PowerShell. O passo de trabalho powerShell não é suportado em Azure SQL Managed Instance. 



**Recomendação**   
Reveja a secção de objetos impactados em Azure Migrate para ver todos os trabalhos usando o passo de trabalho da PowerShell e avaliar se o passo de trabalho ou o objeto impactado podem ser removidos.  Avalie se a Azure Automation pode ser utilizada. Alternativamente, migrar para o SQL Server na Azure Virtual Machine

Mais informações: [Diferenças de agente de servidor SQL em Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="queue-reader-job"></a>Trabalho de leitor de fila<a id="QueueReaderJob"></a>

**Denominação: O passo de trabalho do leitor de fila não é suportado em Azure SQL Managed Instance.**   
**Categoria**: Aviso   

**Descrição**   
É um passo de trabalho que ativa a replicação do Agente de Leitores de Fila. O Agente de Leitores de Fila de Replicação é um executável que lê mensagens armazenadas numa fila do Microsoft SQL Server ou numa Fila de Mensagens do Microsoft e, em seguida, aplica essas mensagens ao Editor. O Agente de Leitores de Fila é utilizado com publicações instantâneas e transacionais que permitem a atualização em fila. O passo de trabalho do Leitor de Fila não é suportado em Azure SQL Managed Instance.


**Recomendação**   
Reveja a secção de objetos impactados em Azure Migrate para ver todos os trabalhos usando o passo de trabalho do Queue Reader e avaliar se o passo de trabalho ou o objeto impactado podem ser removidos. Em alternativa, migrar para o SQL Server na Azure Virtual Machine.

Mais informações: [Diferenças de agente de servidor SQL em Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Denominação: As chamadas RAISERROR de estilo legado devem ser substituídas por equivalentes modernos.**   
**Categoria**: Aviso   

**Descrição**   
As chamadas RAISERROR como o exemplo abaixo são denominadas como estilo legado porque não incluem as vírgulas e os parênteses. RAISERROR 50001 "isto é um teste". Este método de chamada RAISERROR é descontinuado e removido em Azure SQL Managed Instance.



**Recomendação**   
Reescreva a declaração utilizando a sintaxe atual do RAISERROR, ou avalie se a abordagem moderna `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` é viável.

Mais informações: [Funcionalidade do motor de base de dados descontinuada no SqL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="service-broker"></a>Corretor de serviços<a id="ServiceBrokerWithNonLocalAddress"></a>

**Título: A função de corretor de serviços é parcialmente suportada em Azure SQL Managed Instance.**   
**Categoria**: Emissão   

**Descrição**   
O SQL Server Service Broker fornece suporte nativo para aplicações de mensagens e filas no Motor de Base de Dados do Servidor SQL. Esta base de dados tem o corretor de serviços de cross-instance habilitado que não é suportado em Azure SQL Managed Instance. 


**Recomendação**   
A Azure SQL Managed Instance não suporta o corretor de serviços de cross-instance, ou seja, onde o endereço não é local. Tem de desativar o Corretor de Serviços utilizando o seguinte comando antes de migrar esta base de dados para Azure: `ALTER DATABASE [database_name] SET DISABLE_BROKER` Além disso, também poderá ser necessário remover ou parar o ponto de terminação do Corretor de Serviços, de modo a evitar que as mensagens cheguem no caso SQL. Uma vez que a base de dados tenha sido migrada para Azure, você pode olhar para a funcionalidade Azure Service Bus para implementar um sistema de mensagens genérico baseado na nuvem em vez de Corretor de Serviços. Em alternativa, migrar para o SQL Server na Azure Virtual Machine. 

Mais informações: [Diferenças de corretor de serviços em Azure SQL Gestd Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#service-broker)

## <a name="sql-mail"></a>SQL Mail<a id="SqlMail"></a>

**Denominação: SQL Mail foi descontinuado.**   
**Categoria**: Aviso   


**Descrição**   
O SQL Mail foi descontinuado e removido em Azure SQL Managed Instance.



**Recomendação**   
Use o Correio da Base de Dados.

Mais informações: [Funcionalidade do motor de base de dados descontinuada no SqL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="systemprocedures110"></a>SistemaProcedures110<a id="SystemProcedures110"></a>


**Denominação: Declarações detetadas que referenciam procedimentos armazenados do sistema que não estão disponíveis em Azure SQL Managed Instance.**   
**Categoria**: Aviso   

**Descrição**   
O sistema não suportado e os procedimentos armazenados alargados não podem ser utilizados em Azure SQL Managed Instance - `sp_dboption` , , , , , , e `sp_addserver` `sp_dropalias` `sp_activedirectory_obj` `sp_activedirectory_scp` `sp_activedirectory_start` . 




**Recomendação**   
Remova as referências a procedimentos de sistema não suportados que tenham sido removidos em Azure SQL Managed Instance.

Mais informações: [Funcionalidade do motor de base de dados descontinuada no SqL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="transact-sql-job"></a>Trabalho transact-SQL<a id="TransactSqlJob"></a>

**Título: O passo de trabalho da TSQL inclui comandos não apoiados em Azure SQL Managed Instance**   
**Categoria**: Aviso   


**Descrição**   
É um passo de trabalho que executa scripts TSQL na hora programada. O passo de trabalho da TSQL inclui comandos não suportados que não são suportados em Azure SQL Managed Instance.



**Recomendação**   
Reveja a secção de objetos impactados em Azure Migrate para ver todos os trabalhos que incluam comandos não suportados em Azure SQL Managed Instance e avaliar se o passo de trabalho ou o objeto impactado podem ser removidos. Em alternativa, migrar para o SQL Server na Azure Virtual Machine.

Mais informações: [Diferenças de agente de servidor SQL em Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="trace-flags"></a>Traços de bandeiras<a id="TraceFlags"></a>

**Denominação: Foram encontradas bandeiras de rastreio não suportadas em Azure SQL Managed Instance**   
**Categoria**: Aviso   


**Descrição**   
Azure SQL Managed Instance suporta apenas um número limitado de bandeiras de traços globais. As bandeiras de rastreio da sessão não são apoiadas.



**Recomendação**   
Reveja a secção de objetos impactados em Azure Migrate para ver todas as bandeiras de vestígios que não são suportadas em Azure SQL Managed Instance e avaliar se podem ser removidas. Em alternativa, migrar para o SQL Server na Azure Virtual Machine.

Mais informações: [Trace bandeiras](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql#trace-flags)


## <a name="windows-authentication"></a>Autenticação do Windows<a id="WindowsAuthentication"></a>

**Denominação: Os utilizadores de bases de dados mapeados com a autenticação do Windows (segurança integrada) não são suportados em Instância Gerida Azure SQL**   
**Categoria**: Aviso   


**Descrição**   
Azure SQL Managed Instance suporta dois tipos de autenticação: 
- Autenticação SQL, que usa um nome de utilizador e senha
- Autenticação do Azure Active Directory, que utiliza identidades geridas pelo Azure Active Directory e é suportada para domínios geridos e integrados. 

Os utilizadores de bases de dados mapeados com a autenticação do Windows (segurança integrada) não são suportados em Azure SQL Managed Instance. 


**Recomendação**   
Federate o Diretório Ativo local com o Diretório Ativo Azure. A identidade do Windows pode então ser substituída pelas identidades equivalentes do Azure Ative Directory. Em alternativa, migrar para o SQL Server na Azure Virtual Machine.

Mais informações: [SqL Managed Instance security capabilites](../../database/security-overview.md#authentication)


## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Denominação: xp_cmdshell não é suportado em Azure SQL Managed Instance.**   
**Categoria**: Emissão   


**Descrição**   
Xp_cmdshell que desova uma concha de comando do Windows e passa numa cadeia para execução não é suportado em Azure SQL Managed Instance. 



**Recomendação**   
Reveja a secção de objetos impactados em Azure Migrate para ver todos os objetos usando xp_cmdshell e avaliar se a referência a xp_cmdshell ou o objeto impactado pode ser removida. Considere explorar a Azure Automation que oferece serviço de automação e configuração baseado na nuvem. Em alternativa, migrar para o SQL Server na Azure Virtual Machine.

Mais informações: [Diferenças de procedimento armazenadas em Azure SQL Gestão De Instância](../../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)

## <a name="next-steps"></a>Passos seguintes

Para começar a migrar o seu SqL Server para Azure SQL Managed Instance, consulte o [guia de migração SQL Server para SQL Managed Instance](sql-server-to-managed-instance-guide.md).

- Para uma matriz dos serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para o ajudar com vários cenários de base de dados e migração de dados, bem como tarefas especiais, consulte [o Serviço e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre a Azure SQL Managed Instance consulte:
   - [Níveis de serviço em Azure SQL Gestão De Instância](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Diferenças entre o SQL Server e o Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Custo total da calculadora de propriedade](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre o ciclo de enquadramento e adoção para migrações em nuvem, ver
   -  [Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [As melhores práticas para custos e cargas de trabalho de tamanho migram para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para avaliar a camada de acesso à aplicação, consulte o [Kit de Ferramentas de Migração do Acesso a Dados (Pré-visualização)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para mais detalhes sobre como realizar testes da Camada A/B do Acesso de Dados consulte [o Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-overview).