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
ms.date: 07/28/2020
ms.openlocfilehash: 3b76af2c6c949f2591cee880a1991c6f240806a2
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107900"
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

O preço de um conjunto é uma função das eDTUs do conjunto. Embora o preço unitário de eDTU de um conjunto seja 1,5 vezes superior ao preço unitário de DTU para bases de dados individuais, **as eDTUs de conjuntos podem ser partilhadas por muitas bases de dados e são necessárias menos eDTUs totais**. Estas distinções nos preços e na partilha das eDTUs são as bases do potencial de poupança nos custos que os conjuntos podem proporcionar.

As seguintes regras do polegar relacionadas com a contagem de bases de dados e a utilização da base de dados ajudam a garantir que um pool oferece um custo reduzido em comparação com a utilização de tamanhos de cálculo para bases de dados individuais.

### <a name="minimum-number-of-databases"></a>Número mínimo de bases de dados

Se a quantidade agregada de recursos para bases de dados individuais for superior a 1,5x os recursos necessários para a piscina, então uma piscina elástica é mais rentável.

***Exemplo do modelo de compra baseado em DTU*** São necessárias pelo menos duas bases de dados S3 ou pelo menos 15 S0 para que um pool de 100 eDTU seja mais rentável do que utilizar tamanhos de cálculo para bases de dados individuais.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Número máximo de bases de dados com picos simultâneos

Ao partilhar recursos, nem todas as bases de dados de um pool podem simultaneamente utilizar recursos até ao limite disponível para bases de dados únicas. Quanto menos bases de dados atingirem simultaneamente o pico, menor é a definição dos recursos da piscina e mais rentável se torna a piscina. Em geral, não mais de 2/3 (ou 67%) das bases de dados na piscina devem simultaneamente atingir o limite de recursos.

***Exemplo do modelo de compra baseado em DTU*** Para reduzir os custos de três bases de dados S3 num pool de 200 eDTU, no máximo duas destas bases de dados podem simultaneamente atingir o pico na sua utilização. Caso contrário, se mais de duas destas quatro bases de dados S3 tiverem o pico em simultâneo, o conjunto terá de ser dimensionado para mais de 200 eDTUs. Se a piscina for redimensionada para mais de 200 eDTUs, mais bases de dados S3 teriam de ser adicionadas à piscina para manter os custos inferiores aos tamanhos de cálculo para bases de dados individuais.

Note que este exemplo não considera a utilização de outras bases de dados na piscina. Se todas as bases de dados tiverem alguma utilização num determinado momento, menos de 2/3 (67%) das bases de dados podem ter o pico em simultâneo.

### <a name="resource-utilization-per-database"></a>Utilização de recursos por base de dados

Uma grande diferença entre o pico e a utilização média das bases de dados indica períodos longos de pouca utilização e curtos períodos de elevada utilização. Este padrão de utilização é ideal para partilhar recursos entre bases de dados. A inclusão de bases de dados num conjunto deve ser considerada quando o pico de utilização for cerca de 1,5 vezes superior à utilização média.

***Exemplo do modelo de compra baseado em DTU*** Uma base de dados S3 que atinge 100 DTUs e, em média, utiliza 67 DTUs ou menos é um bom candidato para a partilha de eDTUs numa piscina. Em alternativa, uma base de dados S1 que tenha como pico 20 DTUs e utilize, em média, 13 DTUs ou menos, é uma boa candidata para um conjunto.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Como posso escolher o tamanho correto da piscina

O melhor tamanho para uma piscina depende dos recursos agregados necessários para todas as bases de dados na piscina. Trata-se de determinar o seguinte:

- Recursos máximos utilizados por todas as bases de dados da piscina (ou DTUs máximos ou vCores máximos dependendo da sua escolha do modelo de compra).
- Bytes de armazenamento máximos utilizados por todas as bases de dados do conjunto.

Para os níveis de serviço disponíveis e limites para cada modelo de recurso, consulte o [modelo de compra baseado em DTU](service-tiers-dtu.md) ou o modelo de compra baseado em [vCore.](service-tiers-vcore.md)

Os seguintes passos podem ajudá-lo a estimar se uma piscina é mais rentável do que bases de dados individuais:

1. Estimar os eDTUs ou vCores necessários para a piscina da seguinte forma:

Para o modelo de compra baseado em DTU:

MAX (<Número total de DTU média *de DBs* X *por DB*>, <Número de utilização *DBs* X *Peak DTU simultaneamente* por DB>)

Para o modelo de compra baseado em vCore:

MAX (<Número total de utilização média vCore *de DBs* X *por> DB,* <Número de utilização *simultânea de DBs* X *Peak vCore por DB*>)

2. Calcule o espaço de armazenamento necessário para o conjunto ao adicionar o número de bytes de que todas as bases de dados do conjunto precisam. Em seguida, determine o tamanho do conjunto de eDTUs que disponibiliza esta quantidade de armazenamento.
3. Para o modelo de compra baseado em DTU, pegue o maior das estimativas do eDTU do Passo 1 e Passo 2. Para o modelo de compra baseado em vCore, pegue a estimativa vCore do Passo 1.
4. Consulte a [página de preços da Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/) e encontre o menor tamanho da piscina que é maior do que a estimativa do Passo 3.
5. Compare o preço da piscina do Passo 5 com o preço de utilizar os tamanhos de cálculo adequados para bases de dados individuais.

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

- **Georrestauro**

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

Para começar a monitorizar a sua piscina elástica, encontre e abra uma piscina elástica no portal. Você verá primeiro um ecrã que lhe dá uma visão geral do estado da sua piscina elástica. O que está incluído:

- Gráficos de monitorização mostrando o uso de recursos da piscina elástica
- Alertas e recomendações recentes, se disponíveis, para a piscina elástica

O gráfico a seguir mostra uma piscina elástica exemplo:

![Vista para a piscina](./media/elastic-pool-overview/basic.png)

Se quiser mais informações sobre o pool, pode clicar em qualquer informação disponível nesta visão geral. Clicar no gráfico **de utilização de recursos** irá levá-lo à vista de Monitorização Azure, onde pode personalizar as métricas e a janela de tempo mostrada na tabela. Clicar em quaisquer notificações disponíveis irá levá-lo a uma lâmina que mostre todos os detalhes desse alerta ou recomendação.

Se quiser monitorizar as bases de dados dentro da sua piscina, pode clicar na **utilização de recursos da Base de Dados** na secção de **Monitorização** do menu de recursos à esquerda.

![Página de utilização de recursos de base de dados](./media/elastic-pool-overview/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Para personalizar o ecrã do gráfico

Pode editar o gráfico e a página métrica para apresentar outras métricas, como a percentagem de CPU, percentagem de IO de dados e percentagem de IO utilizada.

No formulário **Edit Gráfico,** pode selecionar um intervalo de tempo fixo ou clicar **no costume** para selecionar qualquer janela de 24 horas nas últimas duas semanas e, em seguida, selecionar os recursos para monitorizar.

### <a name="to-select-databases-to-monitor"></a>Para selecionar bases de dados para monitorizar

Por predefinição, o gráfico na lâmina **de utilização de recursos de base** de dados mostrará as 5 principais bases de dados por DTU ou CPU (dependendo do seu nível de serviço). Pode alternar as bases de dados deste gráfico selecionando e desacolando bases de dados da lista abaixo da tabela através das caixas de verificação à esquerda.

Também pode selecionar mais métricas para visualizar lado a lado nesta tabela de bases de dados para obter uma visão mais completa do desempenho das suas bases de dados.

Para obter mais informações, consulte [criar alertas de base de dados SQL no portal Azure.](alerts-insights-configure-portal.md)

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
