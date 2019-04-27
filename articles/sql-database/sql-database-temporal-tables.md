---
title: Introdução às tabelas temporais na base de dados SQL do Azure | Documentos da Microsoft
description: Saiba como começar com o uso tabelas temporais na base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 49491c5283ba16c5379c1115fae597bd7fd6ea19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614084"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Introdução às tabelas temporais na base de dados SQL do Azure

Tabelas temporais são um novo recurso de programação de base de dados do SQL do Azure que permite que controle e analise o histórico completo das alterações nos seus dados, sem a necessidade de codificação personalizada. Tabelas temporais manter os dados intimamente ligados ao contexto de tempo para que podem ser interpretados fatos armazenados como válido apenas dentro do período específico. Esta propriedade de tabelas temporais permite eficiente análise baseados no tempo e obtenção de informações de evolução de dados.

## <a name="temporal-scenario"></a>Cenário temporal

Este artigo ilustra as etapas para utilizar tabelas temporais num cenário de aplicação. Suponha que deseja controlar a atividade do usuário num novo Web site que está a ser desenvolvido a partir do zero ou num site existente que pretende expandir com a análise de atividade do utilizador. Neste exemplo simplificado, partimos do princípio de que o número de páginas da web visitadas durante um período de tempo é um indicador de que precisa ser capturados e monitorizados na base de dados do site que está alojado no Azure SQL Database. O objetivo da análise histórica da atividade do utilizador é obter entradas de reestruturar o Web site e fornecer a melhor experiência para os visitantes.

O modelo de base de dados para este cenário é muito simples - métrica de atividade do usuário é representada com um campo de número inteiro único **PageVisited**e é capturado, juntamente com informações básicas sobre o perfil de utilizador. Além disso, para análise baseados no tempo, também manteria uma série de linhas para cada utilizador, onde cada linha representa o número de páginas visitado de um usuário específico dentro de um período de tempo específico.

![Esquema](./media/sql-database-temporal-tables/AzureTemporal1.png)

Felizmente, não é necessário colocar qualquer esforço na sua aplicação para manter essas informações de atividade. Tabelas temporais, este processo é automatizado - fornecendo a total flexibilidade durante o design do site e mais tempo para se concentrar a análise de dados em si. A única coisa que precisa fazer é garantir que **WebSiteInfo** tabela está configurada como [temporais com versão do sistema](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). Os passos exatos para utilizar tabelas temporais neste cenário são descritos abaixo.

## <a name="step-1-configure-tables-as-temporal"></a>Passo 1: Configurar tabelas como temporal
Dependendo se estiver a começar o desenvolvimento de novo ou atualizar a aplicação existente, irá criar as tabelas temporais ou modificar as existentes ao adicionar atributos temporais. Em geral caso, seu cenário pode ser uma combinação destas duas opções. Execute estes ação utilizando [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) ou qualquer outra ferramenta de desenvolvimento de Transact-SQL.

> [!IMPORTANT]
> É recomendado utilizar sempre a versão mais recente do Management Studio, para permanecer sincronizado com as atualizações do Microsoft Azure e da Base de Dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Criar nova tabela
Utilize o item de menu de contexto "Nova tabela com versão do sistema" no SSMS Object Explorer para abrir o editor de consultas com um script de modelo de tabela temporal e, em seguida, utilize "Especificar valores para parâmetros de modelo" (Ctrl + Shift + M) para preencher o modelo:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

No SSDT, escolha o modelo de "(com versão do sistema) de tabela Temporal" quando a adição de novos itens para o projeto de banco de dados. Que irá abrir o designer de tabela e permitem-lhe especificar facilmente o esquema de tabela:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Também pode criar a tabela temporal, especificando as instruções Transact-SQL diretamente, conforme mostrado no exemplo abaixo. Tenha em atenção que os elementos obrigatórios de cada tabela temporal são a definição do período e a cláusula SYSTEM_VERSIONING com uma referência a outra tabela de utilizador que irá armazenar as versões de linha históricos:

```
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

Quando cria a tabela temporal com versão do sistema, a tabela de histórico que acompanha este artigo com a configuração padrão é criada automaticamente. A tabela de histórico de padrão contém um índice de árvore B em cluster em colunas de período (end, início) com a compactação page ativada. Esta configuração é ideal para a maioria dos cenários em que são utilizadas as tabelas temporais, especialmente para [auditoria de dados](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

Neste caso em particular, visamos de executar a análise de tendências baseados no tempo ao longo de um histórico de dados mais tempo e com conjuntos de dados maiores, para que a opção de armazenamento para a tabela de histórico é um índice columnstore em cluster. Um columnstore em cluster fornece compactação muito bom e desempenho em consultas de análise. Tabelas temporais dão-lhe a flexibilidade para configurar os índices em tabelas temporais e atuais completamente independente. 

> [!NOTE]
> Os índices Columnstore estão disponíveis no escalão Premium e no escalão Standard, S3 e acima.
>

O script seguinte mostra como índice predefinido na tabela do histórico pode ser alterado para o columnstore em cluster:

```
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

Tabelas temporais são representadas no pesquisador de objetos com o ícone específico para identificação mais fácil, embora sua tabela de histórico é apresentada como um nó subordinado.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Alterar a tabela existente para temporal
Vamos abordar o cenário alternativo no qual a tabela de WebsiteUserInfo já existe, mas não foi criada para manter um histórico das alterações. Neste caso, simplesmente pode expandir a tabela existente para se tornar temporal, conforme mostrado no exemplo a seguir:

```
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

## <a name="step-2-run-your-workload-regularly"></a>Passo 2: Executar regularmente a sua carga de trabalho
A principal vantagem de tabelas temporais é que não é necessário alterar ou ajustar o seu Web site de qualquer forma, para efetuar o registo de alterações. Depois de criado, as tabelas temporais transparente manter versões anteriores de linha sempre que efetuar modificações nos seus dados. 

Para poder tirar partido de alterações automático de controlo para este cenário específico, vamos apenas atualizar coluna **PagesVisited** sempre que quando o usuário termina her/his sessão no Web site:

```
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
```

É importante notar que a consulta de atualização não precisa saber o tempo exato em que ocorreu a operação atual, nem como dados históricos serão mantidos para análise futura. Ambos os aspectos são manipulados automaticamente pela base de dados do SQL do Azure. O diagrama seguinte ilustra como os dados de histórico está a ser gerados em cada atualização.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Passo 3: Executar uma análise de dados históricos
Agora quando a versão de sistema temporal está ativada, a análise de dados históricos é apenas uma consulta distante de. Neste artigo, forneceremos alguns exemplos que abordam cenários comuns de análise - para obter todos os detalhes, explore várias opções, introduzidas com o [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) cláusula.

Para ver os 10 principais utilizadores ordenados pelo número de páginas da web visitadas no momento da elaboração há uma hora, execute esta consulta:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

Pode facilmente modificar esta consulta para analisar as visitas a partir de um dia atrás, há um mês ou a qualquer momento no passado desejar.

Para efetuar análises estatísticas básicas para o dia anterior, utilize o seguinte exemplo:

```
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
```

Procurar atividades de um utilizador específico, num período de tempo, utilize a cláusula contidos em:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

Visualização gráfica é especialmente conveniente para consultas temporais, o que pode mostrar tendências e padrões de utilização num intuitiva maneira muito facilmente:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Esquema de tabela em evolução
Normalmente, terá de alterar o esquema de tabela temporal enquanto estão a fazer o desenvolvimento de aplicações. Para isso, basta executar regular ALTER TABLE instruções e a base de dados do Azure SQL adequadamente propagará as alterações para a tabela de histórico. O script seguinte mostra como é possível adicionar o atributo adicional para o controlo de:

```
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

Da mesma forma, pode alterar a definição de coluna enquanto a carga de trabalho está ativa:

```
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Por fim, pode remover uma coluna que não é mais necessário.

```
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
```

Em alternativa, utilize a versão mais recente [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) para alterar o esquema de tabela temporal enquanto estiver ligado à base de dados (modo online) ou como parte do projeto de banco de dados (modo offline).

## <a name="controlling-retention-of-historical-data"></a>Controlar a retenção de dados históricos
Tabelas temporais com versão do sistema, a tabela de histórico pode aumentar o tamanho da base de dados mais do que as tabelas regulares. Uma tabela de histórico de grande e crescente pode se tornar um problema ambos devido a custos de armazenamento pura, bem como impor um desempenho de impostos sobre como consultar temporal. Por conseguinte, o desenvolvimento de uma política de retenção de dados para o gerenciamento de dados da tabela de histórico é um aspecto importante de planejar e gerenciar o ciclo de vida de cada tabela temporal. Base de dados SQL do Azure, tem as seguintes abordagens para a gestão de dados do histórico na tabela temporal:

* [A criação de partições de tabela](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Script de limpeza personalizado](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>Passos Seguintes
Para obter informações detalhadas em tabelas temporais, confira [documentação do MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
Visite o Channel 9 para ouvir uma [história de sucesso de implementação temporal de clientes reais](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) e ver um [live demonstração temporal](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

