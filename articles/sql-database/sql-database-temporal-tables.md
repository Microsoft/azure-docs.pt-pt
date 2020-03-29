---
title: Começando com tabelas temporais
description: Saiba como começar a usar tabelas temporais na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
ms.date: 06/26/2019
ms.openlocfilehash: 98fd2658f3fbcb0e7e29114d29f8dc6ed39eedf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820712"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Começar com tabelas temporais na base de dados Azure SQL

As Tabelas Temporais são uma nova funcionalidade de programabilidade da Base de Dados Azure SQL que permite acompanhar e analisar todo o histórico de mudanças nos seus dados, sem a necessidade de codificação personalizada. As Tabelas Temporais mantêm os dados intimamente relacionados com o contexto temporal para que os factos armazenados possam ser interpretados como válidos apenas dentro do período específico. Esta propriedade de Tabelas Temporais permite uma análise eficiente baseada no tempo e obter insights da evolução de dados.

## <a name="temporal-scenario"></a>Cenário temporal

Este artigo ilustra os passos para utilizar tabelas temporais num cenário de aplicação. Suponha que pretenda acompanhar a atividade do utilizador num novo website que está a ser desenvolvido de raiz ou num site existente que pretende alargar com a análise da atividade do utilizador. Neste exemplo simplificado, assumimos que o número de páginas web visitadas durante um período de tempo é um indicador que precisa de ser capturado e monitorizado na base de dados do site que está alojada na Base de Dados Azure SQL. O objetivo da análise histórica da atividade do utilizador é obter inputs para redesenhar o website e proporcionar uma melhor experiência para os visitantes.

O modelo de base de dados para este cenário é muito simples - a métrica da atividade do utilizador está representada com um único campo inteiro, **PageVisited,** e é capturado juntamente com informações básicas sobre o perfil do utilizador. Além disso, para análises baseadas no tempo, manteria uma série de linhas para cada utilizador, onde cada linha representa o número de páginas que um determinado utilizador visitou num determinado período de tempo.

![Esquema](./media/sql-database-temporal-tables/AzureTemporal1.png)

Felizmente, não precisa de se esforçar na sua app para manter esta informação de atividade. Com tabelas temporais, este processo é automatizado - dando-lhe total flexibilidade durante o design do site e mais tempo para se concentrar na própria análise de dados. A única coisa que tem de fazer é garantir que a tabela **WebSiteInfo** esteja configurada como [versão temporal do sistema.](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0) Os passos exatos para utilizar tabelas temporais neste cenário são descritos abaixo.

## <a name="step-1-configure-tables-as-temporal"></a>Passo 1: Configurar tabelas como temporais
Dependendo se está a iniciar um novo desenvolvimento ou a atualizar a aplicação existente, irá criar tabelas temporais ou modificar as existentes adicionando atributos temporais. Em geral, o seu cenário pode ser uma mistura destas duas opções. Execute estas ações utilizando o [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [sQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) ou qualquer outra ferramenta de desenvolvimento Transact-SQL.

> [!IMPORTANT]
> É recomendado utilizar sempre a versão mais recente do Management Studio, para permanecer sincronizado com as atualizações do Microsoft Azure e da Base de Dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Criar nova tabela
Utilize o item do menu de contexto "Nova Tabela Versão do Sistema" no SSMS Object Explorer para abrir o editor de consulta com um script de modelo de tabela temporal e, em seguida, usar "Especificar Valores para Parâmetros de Modelo" (Ctrl+Shift+M) para preencher o modelo:

![SMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

No SSDT, escolha o modelo "Tabela Temporal (Versão do Sistema)" ao adicionar novos itens ao projeto da base de dados. Isso abrirá o designer de mesa e permitir-lhe-á especificar facilmente o layout da tabela:

![Estadtnewtable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Também pode criar uma tabela temporal especificando diretamente as declarações Transact-SQL, como mostra o exemplo abaixo. Note que os elementos obrigatórios de cada tabela temporal são a definição PERIOD e a cláusula SYSTEM_VERSIONING com referência a outra tabela de utilizadores que armazenará versões históricas de linha:

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

Quando cria tabela temporal versionizada pelo sistema, a tabela de histórias que acompanha com a configuração predefinida é automaticamente criada. A tabela de história padrão contém um índice de árvore B agrupado nas colunas do período (fim, início) com compressão de página ativada. Esta configuração é ideal para a maioria dos cenários em que as tabelas temporais são utilizadas, especialmente para [a auditoria de dados.](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0) 

Neste caso em particular, pretendemos realizar uma análise de tendências baseada no tempo ao longo de um histórico de dados mais longo e com conjuntos de dados maiores, pelo que a escolha de armazenamento para a tabela de história é um índice de colunas agrupada. Uma loja de colunas agrupada proporciona uma compressão e desempenho muito bons para consultas analíticas. As Tabelas Temporais dão-lhe a flexibilidade para configurar índices nas tabelas atuais e temporais de forma completamente independente. 

> [!NOTE]
> Os índices de colunas estão disponíveis no nível Premium e no nível Standard, S3 e superior.
>

O seguinte script mostra como o índice padrão na tabela de história pode ser alterado para a loja de colunas agrupada:

```
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

As Tabelas Temporais estão representadas no Object Explorer com o ícone específico para uma identificação mais fácil, enquanto a sua tabela de história é exibida como um nó infantil.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Alterar a tabela existente para temporal
Vamos cobrir o cenário alternativo em que a tabela WebsiteUserInfo já existe, mas não foi concebida para manter um histórico de mudanças. Neste caso, pode simplesmente estender a tabela existente para se tornar temporal, como mostra o seguinte exemplo:

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

## <a name="step-2-run-your-workload-regularly"></a>Passo 2: Executar a sua carga de trabalho regularmente
A principal vantagem das Tabelas Temporais é que não precisa de alterar ou ajustar o seu website de forma alguma para realizar o rastreio de alterações. Uma vez criadas, as Tabelas Temporais persistem transparentemente versões de linha anteriores sempre que executa modificações nos seus dados. 

De forma a aproveitar o rastreio automático de alterações para este cenário em particular, vamos apenas atualizar a coluna **PagesVisited** sempre que um utilizador termina a sua sessão no site:

```
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
```

É importante notar que a consulta de atualização não precisa de saber a hora exata em que ocorreu a operação real nem como os dados históricos serão preservados para análise futura. Ambos os aspetos são tratados automaticamente pela Base de Dados Azure SQL. O diagrama que se segue ilustra como os dados do histórico estão a ser gerados em todas as atualizações.

![Arquitetura Temporal](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Passo 3: Realizar análise de dados históricos
Agora, quando a versão temporal do sistema está ativada, a análise de dados históricos está apenas a uma consulta de distância de si. Neste artigo, forneceremos alguns exemplos que abordam cenários de análise comum - para aprender todos os detalhes, explorar várias opções introduzidas com a cláusula [FOR SYSTEM_TIME.](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3)

Para ver os 10 utilizadores do top 10 encomendados pelo número de páginas web visitadas a partir de uma hora atrás, execute esta consulta:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

Você pode facilmente modificar esta consulta para analisar as visitas do site a partir de um dia atrás, há um mês ou em qualquer ponto do passado que você deseja.

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

Para procurar atividades de um utilizador específico, num prazo de tempo, utilize a cláusula IN CONTIDA:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

A visualização gráfica é especialmente conveniente para consultas temporais, pois pode mostrar tendências e padrões de utilização de forma intuitiva muito facilmente:

![Temporal](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Esquema de mesa em evolução
Normalmente, você precisará mudar o esquema da tabela temporal enquanto você está fazendo o desenvolvimento de aplicações. Para isso, basta executar declarações regulares alter table e a Base de Dados Azure SQL irá propagar adequadamente alterações à tabela de história. O seguinte script mostra como pode adicionar atributo adicional para rastrear:

```
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

Da mesma forma, pode alterar a definição de coluna enquanto a sua carga de trabalho está ativa:

```
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Finalmente, pode remover uma coluna de que já não precisa.

```
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
```

Em alternativa, utilize o mais recente [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) para alterar o esquema da tabela temporal enquanto estiver ligado à base de dados (modo online) ou como parte do projeto da base de dados (modo offline).

## <a name="controlling-retention-of-historical-data"></a>Controlo da conservação de dados históricos
Com tabelas temporais versionizadas pelo sistema, a tabela de história pode aumentar o tamanho da base de dados mais do que as tabelas normais. Uma grande e crescente tabela de histórias pode tornar-se um problema, tanto devido aos custos puros de armazenamento, como à imposição de um imposto de desempenho sobre a consulta temporal. Por conseguinte, o desenvolvimento de uma política de retenção de dados para a gestão de dados na tabela de histórias é um aspeto importante do planeamento e gestão do ciclo de vida de cada tabela temporal. Com a Base de Dados Azure SQL, tem as seguintes abordagens para gerir dados históricos na tabela temporal:

* [Criação de partições de tabela](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Script de limpeza personalizada](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre tabelas temporais, consulte [as Tabelas Temporais.](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables)
- Visite o Canal 9 para ouvir uma verdadeira história de sucesso de [implementação temporal](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) do cliente e assistir a uma [demonstração temporal ao vivo.](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016)

