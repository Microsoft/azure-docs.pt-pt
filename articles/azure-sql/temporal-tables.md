---
title: Começar com mesas temporais
description: Saiba como começar a usar tabelas temporais na Base de Dados Azure SQL e na Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 06/26/2019
ms.openlocfilehash: ea037d12417c8fad9d80b77df69285ed2c8df31b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91618663"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database-and-azure-sql-managed-instance"></a>Começando com tabelas temporais na Base de Dados Azure SQL e Azure SQL Gestão De Instância
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

As tabelas temporais são uma funcionalidade de programabilidade da Azure SQL Database e da Azure SQL Managed Instance que lhe permite acompanhar e analisar todo o histórico de alterações nos seus dados, sem necessidade de codificação personalizada. As tabelas temporais mantêm os dados intimamente relacionados com o contexto temporal para que os factos armazenados possam ser interpretados como válidos apenas dentro do período específico. Esta propriedade de tabelas temporais permite uma análise eficiente baseada no tempo e obter insights da evolução dos dados.

## <a name="temporal-scenario"></a>Cenário temporal

Este artigo ilustra os passos para utilizar as tabelas temporais num cenário de candidatura. Suponha que pretende acompanhar a atividade do utilizador num novo website que está a ser desenvolvido a partir do zero ou num website existente que pretende alargar com a análise da atividade do utilizador. Neste exemplo simplificado, assumimos que o número de páginas web visitadas durante um período de tempo é um indicador que precisa de ser capturado e monitorizado na base de dados do site que está hospedado na Base de Dados Azure SQL ou na Azure SQL Managed Instance. O objetivo da análise histórica da atividade do utilizador é obter entradas para redesenhar o site e proporcionar uma melhor experiência para os visitantes.

O modelo de base de dados para este cenário é muito simples - a métrica da atividade do utilizador é representada com um único campo inteiro, **PageVisited**, e é capturado juntamente com informações básicas sobre o perfil do utilizador. Além disso, para análises baseadas no tempo, manteria uma série de linhas para cada utilizador, onde cada linha representa o número de páginas que um determinado utilizador visitou num determinado período de tempo.

![Esquema](./media/temporal-tables/AzureTemporal1.png)

Felizmente, não precisa de fazer qualquer esforço na sua app para manter esta informação de atividade. Com tabelas temporais, este processo é automatizado - dando-lhe total flexibilidade durante o design do site e mais tempo para se concentrar na própria análise de dados. A única coisa que tem de fazer é garantir que a tabela **WebSiteInfo** está configurada como [versão temporal do sistema.](/sql/relational-databases/tables/temporal-tables#what-is-a-system-versioned-temporal-table) Os passos exatos para utilizar as tabelas temporais neste cenário são descritos abaixo.

## <a name="step-1-configure-tables-as-temporal"></a>Passo 1: Configurar as tabelas como temporal

Dependendo se está a iniciar um novo desenvolvimento ou a atualizar a aplicação existente, irá criar tabelas temporais ou modificar as existentes adicionando atributos temporais. No caso geral, o seu cenário pode ser uma mistura destas duas opções. Execute estas ações utilizando [o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS), [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) (SSDT), [Azure Data Studio,](/sql/azure-data-studio/download-azure-data-studio)ou qualquer outra ferramenta de desenvolvimento Transact-SQL.

> [!IMPORTANT]
> Recomenda-se que utilize sempre a versão mais recente do Management Studio para se manter sincronizado com atualizações para a Base de Dados Azure SQL e para a Azure SQL Gerenciada. [Atualize o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

### <a name="create-new-table"></a>Criar nova tabela

Utilize o item do menu de contexto "Nova tabela de System-Versioned" no SSMS Object Explorer para abrir o editor de consulta com um script de modelo de tabela temporal e, em seguida, usar "Especificar valores para parâmetros de modelo" (Ctrl+Shift+M) para preencher o modelo:

![SSMSNewTable](./media/temporal-tables/AzureTemporal2.png)

No SSDT, escolha o modelo "Tabela Temporal (Versão Do Sistema)" ao adicionar novos itens ao projeto da base de dados. Isso abrirá o designer de mesa e permitir-lhe-á especificar facilmente o layout da tabela:

![SSDTNewTable](./media/temporal-tables/AzureTemporal3.png)

Também pode criar uma tabela temporal especificando diretamente as declarações Transact-SQL, como mostra o exemplo abaixo. Note que os elementos obrigatórios de cada tabela temporal são a definição PERIOD e a cláusula SYSTEM_VERSIONING com uma referência a outra tabela de utilizadores que armazenará versões históricas de linha:

```sql
CREATE TABLE WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
```

Quando cria uma tabela temporal com versão do sistema, a tabela de histórico que acompanha a configuração padrão é automaticamente criada. A tabela de histórico padrão contém um índice de árvore B agrupado nas colunas de período (fim, início) com compressão de página ativada. Esta configuração é ideal para a maioria dos cenários em que são utilizadas tabelas temporais, especialmente para [a auditoria de dados.](/sql/relational-databases/tables/temporal-table-usage-scenarios#enabling-system-versioning-on-a-new-table-for-data-audit)

Neste caso em particular, pretendemos realizar uma análise de tendência baseada no tempo ao longo de um histórico de dados mais longo e com conjuntos de dados maiores, pelo que a escolha de armazenamento para a tabela de história é um índice de loja de colunas agrupado. Uma loja de colunas agrupada proporciona uma compressão e desempenho muito bons para consultas analíticas. As tabelas temporais dão-lhe a flexibilidade para configurar índices nas tabelas atuais e temporais de forma completamente independente.

> [!NOTE]
> Os índices de loja de colunas estão disponíveis nos níveis Business Critical, General Purpose e Premium e no nível Standard, S3 e acima.

O seguinte script mostra como o índice predefinido na tabela de história pode ser alterado para a loja de colunas agrupadas:

```sql
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

As tabelas temporais são representadas no Object Explorer com o ícone específico para uma identificação mais fácil, enquanto a sua tabela de história é exibida como um nó infantil.

![AlterTable](./media/temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Alterar a tabela existente para o temporal

Vamos cobrir o cenário alternativo em que a tabela WebsiteUserInfo já existe, mas não foi concebida para manter um histórico de mudanças. Neste caso, pode simplesmente estender a tabela existente para se tornar temporal, como mostra o seguinte exemplo:

```sql
ALTER TABLE WebsiteUserInfo
ADD
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo);

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

## <a name="step-2-run-your-workload-regularly"></a>Passo 2: Executar a sua carga de trabalho regularmente

A principal vantagem das tabelas temporais é que não precisa de alterar ou ajustar o seu website de forma alguma para realizar o rastreio de alterações. Uma vez criados, as tabelas temporais persistem de forma transparente as versões de linha anteriores sempre que realiza modificações nos seus dados.

Para aproveitar o rastreio automático de mudanças para este cenário específico, vamos apenas atualizar a coluna **PagesVisited** sempre que um utilizador termina a sua sessão no site:

```sql
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5
WHERE [UserID] = 1;
```

É importante notar que a consulta de atualização não precisa de saber a hora exata quando ocorreu a operação real, nem como os dados históricos serão preservados para análise futura. Ambos os aspetos são tratados automaticamente pela Base de Dados Azure SQL e pela Azure SQL Managed Instance. O diagrama seguinte ilustra como os dados de história estão a ser gerados em cada atualização.

![TemporalArquitecture](./media/temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Passo 3: Realizar análise de dados históricos

Agora, quando a versão temporal do sistema está ativada, a análise histórica de dados está apenas a uma consulta de distância de si. Neste artigo, forneceremos alguns exemplos que abordam cenários de análise comuns - para conhecer todos os detalhes, explorar várias opções introduzidas com a cláusula [de SYSTEM_TIME PARA.](/sql/relational-databases/tables/temporal-tables#how-do-i-query-temporal-data)

Para ver os 10 utilizadores do top 10 encomendados pelo número de páginas web visitadas a partir de uma hora atrás, executar esta consulta:

```sql
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

Você pode facilmente modificar esta consulta para analisar as visitas do site a partir de um dia atrás, há um mês ou em qualquer ponto do passado que você deseja.

Para efetuar análises estatísticas básicas para o dia anterior, utilize o seguinte exemplo:

```sql
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
```

Para procurar atividades de um utilizador específico, dentro de um período de tempo, utilize a cláusula IN CONTAINED:

```sql
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

A visualização gráfica é especialmente conveniente para consultas temporais, pois pode mostrar tendências e padrões de utilização de uma forma intuitiva muito facilmente:

![Gráfico temporal](./media/temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Esquema de mesa em evolução

Normalmente, terá de alterar o esquema de tabela temporal enquanto está a fazer o desenvolvimento de aplicações. Para isso, basta executar declarações regulares de ALTER TABLE e Azure SQL Database ou Azure SQL Managed Instance propaga adequadamente as alterações na tabela de história. O seguinte script mostra como pode adicionar atributo adicional para rastreio:

```sql
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

Da mesma forma, pode alterar a definição de coluna enquanto a sua carga de trabalho estiver ativa:

```sql
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Finalmente, pode remover uma coluna de que já não precisa.

```sql
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo
    DROP COLUMN TemporaryColumn;
```

Em alternativa, utilize [o SSDT](/sql/ssdt/download-sql-server-data-tools-ssdt) mais recente para alterar o esquema da tabela temporal enquanto estiver ligado à base de dados (modo online) ou como parte do projeto de base de dados (modo offline).

## <a name="controlling-retention-of-historical-data"></a>Controlo da conservação de dados históricos

Com tabelas temporais versadas pelo sistema, a tabela de histórico pode aumentar o tamanho da base de dados mais do que as tabelas normais. Uma grande e cada vez maior tabela de história pode tornar-se um problema, tanto devido aos custos puros de armazenamento, como à imposição de um imposto sobre o desempenho na consulta temporal. Assim, o desenvolvimento de uma política de retenção de dados para a gestão de dados na tabela de história é um aspeto importante do planeamento e gestão do ciclo de vida de cada tabela temporal. Com a Azure SQL Database e a Azure SQL Managed Instance, tem as seguintes abordagens para gerir dados históricos na tabela temporal:

- [Criação de partições de tabela](/sql/relational-databases/tables/manage-retention-of-historical-data-in-system-versioned-temporal-tables#using-table-partitioning-approach)
- [Script de limpeza personalizado](/sql/relational-databases/tables/manage-retention-of-historical-data-in-system-versioned-temporal-tables#using-custom-cleanup-script-approach)

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre tabelas temporais, consulte as [Tabelas Temporais.](/sql/relational-databases/tables/temporal-tables)
- Visite o Canal 9 para ouvir uma [história de sucesso de implementação temporal](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) do cliente e assista a uma [demonstração temporal ao vivo.](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016)
