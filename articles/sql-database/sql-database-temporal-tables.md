---
title: Introdução com tabelas temporais no banco de dados SQL do Azure | Microsoft Docs
description: Saiba como começar a usar tabelas temporais no banco de dados SQL do Azure.
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
ms.openlocfilehash: 39c19661a71a8b466aa6ff25be9e895189dfbfb3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566358"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Introdução com tabelas temporais no banco de dados SQL do Azure

As tabelas temporais são um novo recurso de programação do banco de dados SQL do Azure que permite que você acompanhe e analise o histórico completo de alterações em seus recursos, sem a necessidade de codificação personalizada. As tabelas temporais mantêm os dados bem relacionados ao contexto de tempo para que os fatos armazenados possam ser interpretados como válidos somente dentro do período específico. Essa propriedade de tabelas temporais permite uma análise eficiente baseada em tempo e a obtenção de informações de evolução de dados.

## <a name="temporal-scenario"></a>Cenário temporal

Este artigo ilustra as etapas para utilizar tabelas temporais em um cenário de aplicativo. Suponha que você deseja acompanhar a atividade do usuário em um novo site que está sendo desenvolvido do zero ou em um site existente que você deseja estender com a análise de atividade do usuário. Neste exemplo simplificado, presumimos que o número de páginas da Web visitadas durante um período de tempo é um indicador que precisa ser capturado e monitorado no banco de dados do site hospedado no banco de dados SQL do Azure. O objetivo da análise histórica da atividade do usuário é obter entradas para reprojetar o site e fornecer uma experiência melhor para os visitantes.

O modelo de banco de dados para esse cenário é muito simples-a métrica de atividade de usuário é representada com um único campo de inteiro, **PageVisited**, e é capturado junto com informações básicas sobre o perfil do usuário. Além disso, para análise baseada em tempo, você manteria uma série de linhas para cada usuário, em que cada linha representa o número de páginas que um usuário específico visitou em um período de tempo específico.

![Esquema](./media/sql-database-temporal-tables/AzureTemporal1.png)

Felizmente, você não precisa colocar nenhum esforço em seu aplicativo para manter essas informações de atividade. Com as tabelas temporais, esse processo é automatizado, proporcionando a você total flexibilidade durante o design do site e mais tempo para se concentrar na análise de dados em si. A única coisa que você precisa fazer é garantir que a tabela **WebSiteInfo** esteja configurada como [versão temporal do sistema](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). As etapas exatas para utilizar tabelas temporais neste cenário são descritas abaixo.

## <a name="step-1-configure-tables-as-temporal"></a>Passo 1: Configurar tabelas como temporais
Dependendo se você estiver iniciando um novo desenvolvimento ou atualizando o aplicativo existente, você criará tabelas temporais ou modificará as existentes adicionando atributos temporais. Em geral, seu cenário pode ser uma combinação dessas duas opções. Execute essas ações usando [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) ou qualquer outra ferramenta de desenvolvimento Transact-SQL.

> [!IMPORTANT]
> É recomendado utilizar sempre a versão mais recente do Management Studio, para permanecer sincronizado com as atualizações do Microsoft Azure e da Base de Dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Criar nova tabela
Use o item de menu de contexto "nova tabela com versão do sistema" no Pesquisador de objetos do SSMS para abrir o editor de consultas com um script de modelo de tabela temporal e, em seguida, use "especificar valores para parâmetros de modelo" (Ctrl + Shift + M) para preencher o modelo:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

Em SSDT, escolha o modelo "tabela temporal (versão do sistema)" ao adicionar novos itens ao projeto de banco de dados. Isso abrirá o designer de tabela e permitirá que você especifique facilmente o layout da tabela:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Você também pode criar uma tabela temporal especificando as instruções Transact-SQL diretamente, conforme mostrado no exemplo abaixo. Observe que os elementos obrigatórios de cada tabela temporal são a definição de período e a cláusula SYSTEM_VERSIONING com uma referência a outra tabela de usuário que armazenará versões de linha históricas:

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

Quando você cria uma tabela temporal com versão do sistema, a tabela de histórico que acompanha a configuração padrão é criada automaticamente. A tabela de histórico padrão contém um índice de árvore B clusterizado nas colunas de período (fim, início) com a compactação de página habilitada. Essa configuração é ideal para a maioria dos cenários em que as tabelas temporais são usadas, especialmente para a [auditoria de dados](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

Neste caso em particular, visamos executar análise de tendência baseada em tempo sobre um histórico de dados mais longo e com conjuntos de dados maiores, de modo que a opção de armazenamento para a tabela de histórico é um índice columnstore clusterizado. Um columnstore clusterizado fornece compactação e desempenho muito bons para consultas analíticas. As tabelas temporais oferecem a você a flexibilidade de configurar os índices nas tabelas atuais e temporais completamente de forma independente. 

> [!NOTE]
> Os índices Columnstore estão disponíveis na camada Premium e na camada Standard, S3 e acima.
>

O script a seguir mostra como o índice padrão na tabela de histórico pode ser alterado para o columnstore clusterizado:

```
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

As tabelas temporais são representadas no Pesquisador de objetos com o ícone específico para facilitar a identificação, enquanto sua tabela de histórico é exibida como um nó filho.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Alterar tabela existente para temporal
Vamos abordar o cenário alternativo no qual a tabela WebsiteUserInfo já existe, mas não foi projetada para manter um histórico das alterações. Nesse caso, você pode simplesmente estender a tabela existente para tornar-se temporal, conforme mostrado no exemplo a seguir:

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

## <a name="step-2-run-your-workload-regularly"></a>Passo 2: Execute sua carga de trabalho regularmente
A principal vantagem das tabelas temporais é que você não precisa alterar ou ajustar seu site de qualquer forma para executar o controle de alterações. Depois de criadas, as tabelas temporais persistem de forma transparente às versões de linha a cada vez que você realiza modificações em seus dados. 

Para aproveitar o controle de alterações automático para esse cenário específico, vamos apenas atualizar a coluna **PagesVisited** sempre que um usuário terminar sua sessão no site:

```
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
```

É importante observar que a consulta de atualização não precisa saber o tempo exato em que a operação real ocorreu nem como os dados históricos serão preservados para análise futura. Ambos os aspectos são manipulados automaticamente pelo banco de dados SQL do Azure. O diagrama a seguir ilustra como os dados de histórico estão sendo gerados em cada atualização.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Passo 3: Executar análise de dados históricos
Agora, quando o controle de versão do sistema temporal está habilitado, a análise de dados históricos é apenas uma consulta longe de você. Neste artigo, forneceremos alguns exemplos que abordam cenários comuns de análise – para aprender todos os detalhes, explore várias opções introduzidas com a cláusula [for SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) .

Para ver os 10 principais usuários ordenados pelo número de páginas da Web visitadas a partir de uma hora atrás, execute esta consulta:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

Você pode facilmente modificar essa consulta para analisar as visitas do site a partir de um dia atrás, um mês atrás ou em qualquer ponto no passado.

Para executar a análise estatística básica do dia anterior, use o exemplo a seguir:

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

Para pesquisar atividades de um usuário específico, em um período de tempo, use a cláusula Within IN:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

A visualização gráfica é especialmente conveniente para consultas temporais, pois você pode mostrar tendências e padrões de uso de maneira intuitiva com muito facilidade:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Desenvolvendo o esquema de tabela
Normalmente, você precisará alterar o esquema de tabela temporal enquanto estiver fazendo o desenvolvimento de aplicativos. Para isso, basta executar instruções ALTER TABLE regulares e o banco de dados SQL do Azure irá propagar adequadamente as alterações na tabela de histórico. O script a seguir mostra como você pode adicionar um atributo adicional para acompanhamento:

```
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

Da mesma forma, você pode alterar a definição de coluna enquanto sua carga de trabalho está ativa:

```
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Por fim, você pode remover uma coluna que não precisa mais.

```
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
```

Como alternativa, use o [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) mais recente para alterar o esquema de tabela temporal enquanto você estiver conectado ao banco de dados (modo online) ou como parte do projeto de banco de dados (modo offline).

## <a name="controlling-retention-of-historical-data"></a>Controlando a retenção de dados históricos
Com as tabelas temporais com controle da versão do sistema, a tabela de histórico pode aumentar o tamanho do banco de dados mais do que as tabelas normais. Uma tabela de histórico grande e cada vez maior pode se tornar um problema devido aos custos de armazenamento puro, bem como à imposição de um imposto sobre o desempenho na consulta temporal. Portanto, o desenvolvimento de uma política de retenção de dados para gerenciar dados na tabela de histórico é um aspecto importante do planejamento e gerenciamento do ciclo de vida de cada tabela temporal. Com o banco de dados SQL do Azure, você tem as seguintes abordagens para o gerenciamento de dado histórico na tabela temporal:

* [Particionamento de tabela](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Script de limpeza personalizado](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre tabelas temporais, consulte check out [Tables temporal](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables).
- Visite o Channel 9 para ouvir uma [história real de sucesso de implementação temporal do cliente](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) e assistir a uma [demonstração temporal ao vivo](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

