---
title: Gerir várias bases de dados com piscinas elásticas
description: Gerir e escalar várias bases de dados na Base de Dados Azure SQL - centenas e milhares - utilizando piscinas elásticas. Um preço para os recursos que pode distribuir sempre que necessário.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, sstein
ms.date: 12/9/2020
ms.openlocfilehash: c478edf95ae345d64da630400fbf63ac613b73a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100653640"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-databases-in-azure-sql-database"></a>Piscinas elásticas ajudam a gerir e escalar várias bases de dados na Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

As piscinas elásticas Azure SQL Database são uma solução simples e rentável para gerir e escalar várias bases de dados que têm exigências de utilização variadas e imprevisíveis. As bases de dados de uma piscina elástica estão num único servidor e partilham um número de recursos a um preço definido. Com os conjuntos elásticos na Base de Dados SQL do Azure, os programadores de SaaS podem otimizar o desempenho do preço num grupo de bases de dados dentro de um orçamento prescrito e, ao mesmo tempo, oferecer elasticidade de desempenho para cada base de dados.

## <a name="what-are-sql-elastic-pools"></a>O que são piscinas elásticas SQL

Os programadores de SaaS criam as aplicações sobre camadas de dados de grande escala, que consistem em várias bases de dados. Um padrão de aplicação comum é aprovisionar uma base de dados individual para cada cliente. Mas diferentes clientes muitas vezes têm padrões de uso variados e imprevisíveis, e é difícil prever os requisitos de recursos de cada utilizador de base de dados individual. Tradicionalmente, tinha duas opções:

- Recursos de sobre-oferta baseados no uso máximo e sobre remuneração, ou
- Sub-provisão para economizar custos, em detrimento do desempenho e da satisfação do cliente durante picos.

As piscinas elásticas resolvem este problema garantindo que as bases de dados obtenham os recursos de desempenho de que necessitam quando precisam. Fornecem um mecanismo de alocação de recursos simples dentro de um orçamento previsível. Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](saas-tenancy-app-design-patterns.md).
>
> [!IMPORTANT]
> Não há carga por base de dados para piscinas elásticas. Você é cobrado por cada hora que uma piscina existe no mais alto eDTU ou vCores, independentemente do uso ou se a piscina estava ativa por menos de uma hora.

Piscinas elásticas permitem ao desenvolvedor adquirir recursos para um pool partilhado por várias bases de dados para acomodar períodos imprevisíveis de utilização por bases de dados individuais. Pode configurar recursos para o pool com base no modelo de compra baseado na [DTU](service-tiers-dtu.md) ou no [modelo de compra baseado em vCore.](service-tiers-vcore.md) A exigência de recursos para um pool é determinada pela utilização agregada das suas bases de dados. A quantidade de recursos disponíveis para a piscina é controlada pelo orçamento do promotor. O desenvolvedor simplesmente adiciona bases de dados ao pool, configura opcionalmente os recursos mínimos e máximos para as bases de dados (ou DTUs mínimos e máximos ou vCores mínimos ou máximos dependendo da sua escolha do modelo de reabastecimento), e, em seguida, define os recursos do pool com base no seu orçamento. Os programadores podem utilizar os conjuntos para fazer crescer de forma contínua o serviço, de uma “lean startup” para uma empresa madura e em constante crescimento.

Dentro do conjunto, é dada às bases de dados individuais a flexibilidade para se dimensionarem automaticamente dentro de parâmetros definidos. Sob carga pesada, uma base de dados pode consumir mais recursos para satisfazer a procura. As bases de dados sob cargas leves consomem menos, e as bases de dados sem carga não consomem recursos. O aprovisionamento de recursos para o conjunto completo e não para bases de dados individuais simplifica as tarefas de gestão. Além disso, tens um orçamento previsível para a piscina. Os recursos adicionais podem ser adicionados a uma piscina existente com tempo mínimo de inatividade. Do mesmo modo, se já não forem necessários recursos adicionais, podem ser removidos de uma piscina existente a qualquer momento. E pode adicionar ou remover bases de dados da piscina. Se uma base de dados estiver a subutilizar recursos de forma previsível, remova-a.

> [!NOTE]
> Ao mover bases de dados para dentro ou para fora de uma piscina elástica, não há tempo de paragem, exceto por um breve período de tempo (na ordem dos segundos) no final da operação quando as ligações de base de dados são largadas.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Quando deve considerar uma piscina elástica SQL Database

Os conjuntos são bastante adequados para um grande número de bases de dados com padrões de utilização específicos. Para uma determinada base de dados, este padrão caracteriza-se por uma utilização média baixa com picos de utilização relativamente raros. Inversamente, várias bases de dados com utilização persistente de médio-alto não devem ser colocadas na mesma piscina elástica.

Quantas mais bases de dados adicionar a um conjunto, maior será a poupança. Dependendo do seu padrão de utilização da aplicação, é possível ver poupanças com apenas duas bases de dados S3.

As secções seguintes ajudam-no a avaliar se a sua coleção de bases de dados específica pode beneficiar de estar num conjunto. Os exemplos utilizam conjuntos Standard, mas os mesmos princípios também se aplicam aos conjuntos Básicos e Premium.

### <a name="assessing-database-utilization-patterns"></a>Avaliar os padrões de utilização da base de dados

A figura seguinte mostra um exemplo de uma base de dados que está inativa muito tempo, mas que também, periodicamente, apresenta picos de atividade. Este é um padrão de utilização adequado para um conjunto:

   ![uma base de dados individual adequada para um conjunto](./media/elastic-pool-overview/one-database.png)

O gráfico ilustra o uso do DTU durante um período de 1 hora das 12:00 às 1:00 onde cada ponto de dados tem 1 minuto de granularidade. Às 12:10 DB1 atinge os 90 DTUs, mas a sua média global é inferior a cinco DTUs. Um tamanho de cálculo S3 é necessário para executar esta carga de trabalho numa única base de dados, mas isso deixa a maioria dos recursos não uusados durante períodos de baixa atividade.

Um conjunto permite que estas DTUs não utilizadas sejam partilhadas entre várias bases de dados, pelo que reduz as DTUs necessárias e os custos gerais.

Com base no exemplo anterior, imagine que existem bases de dados adicionais com padrões de utilização semelhantes a DB1. Nas duas figuras seguintes abaixo, a utilização de quatro bases de dados e 20 bases de dados são em camadas no mesmo gráfico para ilustrar a natureza não sobreposta da sua utilização ao longo do tempo utilizando o modelo de compra baseado em DTU:

   ![quatro bases de dados com um padrão de utilização adequado para um conjunto](./media/elastic-pool-overview/four-databases.png)

   ![vinte bases de dados com um padrão de utilização adequado para um conjunto](./media/elastic-pool-overview/twenty-databases.png)

A utilização agregada de DTUs nas 20 bases de dados é ilustrada pela linha preta na figura anterior. Mostra que a utilização agregada de DTUs nunca excede as cem e indica que as 20 bases de dados podem partilhar 100 eDTUs ao longo deste período de tempo. Isto resulta numa redução de 20x em DTUs e numa redução de preço de 13x em comparação com a colocação de cada uma das bases de dados em tamanhos de computação S3 para bases de dados individuais.

Este exemplo é ideal pelos motivos seguintes:

- Existem grandes diferenças entre a utilização de pico e utilização média por base de dados.
- A utilização de pico de cada base de dados ocorre em diferentes alturas no tempo.
- Os eDTUs são partilhados entre muitas bases de dados.

No modelo de compra da DTU, o preço de uma piscina é uma função da piscina eDTUs. Embora o preço unitário de eDTU de um conjunto seja 1,5 vezes superior ao preço unitário de DTU para bases de dados individuais, **as eDTUs de conjuntos podem ser partilhadas por muitas bases de dados e são necessárias menos eDTUs totais**. Estas distinções nos preços e na partilha das eDTUs são as bases do potencial de poupança nos custos que os conjuntos podem proporcionar.

No modelo de compra vCore, o preço unitário vCore para piscinas elásticas é o mesmo que o preço unitário vCore para bases de dados individuais.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Como posso escolher o tamanho correto da piscina

O melhor tamanho para uma piscina depende dos recursos agregados necessários para todas as bases de dados na piscina. Trata-se de determinar o seguinte:

- Recursos máximos de computação utilizados por todas as bases de dados da piscina.  Os recursos computacional são indexados por eDTUs ou vCores dependendo da sua escolha de modelo de compra.
- Bytes de armazenamento máximos utilizados por todas as bases de dados do conjunto.

Para os níveis de serviço e limites de recursos em cada modelo de compra, consulte o [modelo de compra baseado em DTU](service-tiers-dtu.md) ou o modelo de compra baseado em [vCore](service-tiers-vcore.md).

Os seguintes passos podem ajudá-lo a estimar se uma piscina é mais rentável do que bases de dados individuais:

1. Estimar os eDTUs ou vCores necessários para a piscina da seguinte forma:
   - Para o modelo de compra baseado em DTU:
     - MAX (<*número total de DTU* utilização média de &times; *DTU por DB*>, <Número de utilização de *DBs* &times; *peak DTU simultaneamente* por DB>)
   - Para o modelo de compra baseado em vCore:
     - MAX (<*Número total de DBs* &times; *Utilização média vCore por DB*>, <número de *DBs peak* &times; *vCore utilização em simultâneo por DBs Peak vCore utilização por DB*>)
2. Estimar o espaço de armazenamento total necessário para o pool adicionando o tamanho de dados necessário para todas as bases de dados na piscina. Para o modelo de compra DTU, determine então o tamanho da piscina eDTU que fornece esta quantidade de armazenamento.
3. Para o modelo de compra baseado em DTU, pegue o maior das estimativas do eDTU do Passo 1 e Passo 2. Para o modelo de compra baseado em vCore, pegue a estimativa vCore do Passo 1.
4. Consulte a [página de preços da Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/) e encontre o menor tamanho da piscina que é maior do que a estimativa do Passo 3.
5. Compare o preço da piscina do Passo 4 com o preço de utilizar os tamanhos de cálculo adequados para bases de dados individuais.

> [!IMPORTANT]
> Se o número de bases de dados de uma piscina se aproximar do máximo suportado, certifique-se de considerar a [gestão de Recursos em piscinas elásticas densas](elastic-pool-resource-management.md).

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Utilização de outras funcionalidades da Base de Dados SQL com piscinas elásticas

### <a name="elastic-jobs-and-elastic-pools"></a>Trabalhos elásticos e piscinas elásticas

Com um conjunto, as tarefas de gestão são simplificadas através da execução de scripts em **[tarefas elásticas](elastic-jobs-overview.md)**. Uma tarefa elástica elimina a maior parte da monotonia associada a grandes números de bases de dados.

Para obter mais informações sobre outras ferramentas de base de dados para trabalhar com várias bases de dados, veja [Aumentar horizontalmente com a Base de Dados SQL do Azure](elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opções de continuidade de negócios para bases de dados em piscina elástica

As bases de dados de conjunto suportam geralmente as mesmas [funcionalidades de continuidade de negócio](business-continuity-high-availability-disaster-recover-hadr-overview.md) que estão disponíveis para as bases de dados individuais.

- **Restauro para um ponto anterior no tempo**

  A restauração pontual utiliza cópias de dados automáticas para recuperar uma base de dados numa piscina a um ponto específico no tempo. Veja [Restauro para um ponto anterior no tempo](recovery-using-backups.md#point-in-time-restore)

- **Geo-restauro**

  O geo-restauro fornece a opção de recuperação padrão quando uma base de dados não está disponível devido a um incidente na região onde a base de dados está hospedada. Consulte [restaurar uma base de dados Azure SQL ou falhar para um secundário](disaster-recovery-guidance.md)

- **Georreplicação ativa**

  Para aplicações que tenham requisitos de recuperação mais agressivos do que o geo-restauro podem oferecer, configurar [a geo-replicação Ativa](active-geo-replication-overview.md) ou um [grupo de falha automática](auto-failover-group-overview.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Criação de uma nova piscina elástica SQL Database utilizando o portal Azure

Há duas formas de criar uma piscina elástica no portal Azure.

1. Vá ao [portal Azure](https://portal.azure.com) para criar uma piscina elástica. Procure e selecione **Azure SQL**.
2. **Selecione +Adicione** para abrir a página **de opção de implementação Select SQL.** Pode ver informações adicionais sobre piscinas elásticas selecionando detalhes do **Show** no azulejo **de Bases de Dados.**
3. No azulejo **das bases de dados,** selecione **a piscina elástica** no dropdown do tipo **de recurso** e, em seguida, selecione **Criar**:

   ![Criar um conjunto elástico](./media/elastic-pool-overview/create-elastic-pool.png)

4. Ou pode criar uma piscina elástica navegando para um servidor existente e clicando **+ Piscina Nova** para criar uma piscina diretamente nesse servidor.

> [!NOTE]
> Pode criar várias piscinas num servidor, mas não é possível adicionar bases de dados de diferentes servidores na mesma piscina.

O nível de serviço da piscina determina as funcionalidades disponíveis para os elásticos na piscina e a quantidade máxima de recursos disponíveis para cada base de dados. Para mais informações, consulte os limites de recursos para piscinas elásticas no [modelo DTU.](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes) Para limites de recursos baseados em vCore para piscinas elásticas, consulte [os limites de recursos baseados em vCore - piscinas elásticas](resource-limits-vcore-elastic-pools.md).

Para configurar os recursos e preços da piscina, clique na **piscina Configure.** Em seguida, selecione um nível de serviço, adicione bases de dados à piscina e configuure os limites de recursos para o pool e suas bases de dados.

Quando tiver concluído a configuração da piscina, pode clicar em 'Aplicar', nomear a piscina e clicar em 'OK' para criar a piscina.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Monitorize uma piscina elástica e suas bases de dados

No portal Azure, pode monitorizar a utilização de uma piscina elástica e as bases de dados dentro dessa piscina. Também pode fazer um conjunto de alterações na sua piscina elástica e submeter todas as alterações ao mesmo tempo. Estas alterações incluem adicionar ou remover bases de dados, alterar as definições de piscina elástica ou alterar as definições da base de dados.

Pode utilizar as ferramentas de [monitorização](./performance-guidance.md) e alerta de desempenho [incorporadas,](./alerts-insights-configure-portal.md)combinadas com classificações de desempenho.  Além disso, a BASE de Dados SQL pode [emitir métricas e registos de recursos](./metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal) para uma monitorização mais fácil.

## <a name="customer-case-studies"></a>Casos práticos de clientes

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  A SnelStart usou piscinas elásticas com base de dados Azure SQL para expandir rapidamente os seus serviços de negócio a uma taxa de 1.000 novas bases de dados Azure SQL por mês.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  A Umbraco utiliza piscinas elásticas com Base de Dados Azure SQL para rapidamente prestação e escala de serviços para milhares de inquilinos na nuvem.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)

   A Daxko/CSI utiliza piscinas elásticas com Base de Dados Azure SQL para acelerar o seu ciclo de desenvolvimento e melhorar os seus serviços e desempenho ao cliente.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre preços, consulte [os preços da piscina elástica.](https://azure.microsoft.com/pricing/details/sql-database/elastic)
- Para escalar piscinas elásticas, veja [piscinas elásticas de escalonamento](elastic-pool-scale.md) e [escale uma piscina elástica - código de amostra](scripts/monitor-and-scale-pool-powershell.md)
- Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](saas-tenancy-app-design-patterns.md).
- Para um tutorial SaaS utilizando piscinas elásticas, consulte [Introdução à aplicação Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md).
- Para conhecer a gestão de recursos em piscinas elásticas com muitas bases de dados, consulte [a gestão de Recursos em piscinas elásticas densas.](elastic-pool-resource-management.md)
