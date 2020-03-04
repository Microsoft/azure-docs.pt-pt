---
title: Gerir várias bases de dados com piscinas elásticas
description: Gerir e escalar várias bases de dados SQL - centenas e milhares - usando piscinas elásticas. Um preço por recursos que pode distribuir sempre que necessário.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, carlrab
ms.date: 08/06/2019
ms.openlocfilehash: 3c476393153f6bc1d18d5c163bcd69484583eb15
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252693"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Piscinas elásticas ajudam a gerir e escalar várias bases de dados Azure SQL

Os conjuntos elásticos da base de dados SQL são uma solução simples e rentável para gerir e dimensionar várias bases de dados com necessidades de utilização extremamente variáveis e imprevisíveis. As bases de dados de um pool elástico estão num único servidor de base de dados Azure SQL e partilham um número definido de recursos a um preço definido. Com os conjuntos elásticos na Base de Dados SQL do Azure, os programadores de SaaS podem otimizar o desempenho do preço num grupo de bases de dados dentro de um orçamento prescrito e, ao mesmo tempo, oferecer elasticidade de desempenho para cada base de dados.

## <a name="what-are-sql-elastic-pools"></a>O que são piscinas elásticas SQL

Os programadores de SaaS criam as aplicações sobre camadas de dados de grande escala, que consistem em várias bases de dados. Um padrão de aplicação comum é aprovisionar uma base de dados individual para cada cliente. Mas diferentes clientes têm frequentemente padrões de utilização variados e imprevisíveis, e é difícil prever os requisitos de recursos de cada utilizador de base de dados individual. Tradicionalmente, tinha duas opções:

- Recursos sobre-provisionamento com base no uso máximo e sobre os salários, ou
- Sub-oferta para economizar custos, em detrimento do desempenho e satisfação do cliente durante os picos.

Os agrupamentos elásticos resolvem este problema garantindo que as bases de dados obtêm os recursos de desempenho de que necessitam quando precisam. Fornecem um mecanismo de alocação de recursos simples dentro de um orçamento previsível. Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

> [!IMPORTANT]
> Não há nenhuma taxa por base de dados para piscinas elásticas. Você é cobrado por cada hora que uma piscina existe no mais alto eDTU ou vCores, independentemente do uso ou se a piscina estava ativa por menos de uma hora.

Piscinas elásticas permitem ao desenvolvedor adquirir recursos para um pool partilhado por várias bases de dados para acomodar períodos imprevisíveis de utilização por bases de dados individuais. Pode configurar recursos para a piscina com base no [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) ou no modelo de compra baseado em [vCore](sql-database-service-tiers-vcore.md). O requisito de recurso para um pool é determinado pela utilização agregada das suas bases de dados. A quantidade de recursos disponíveis para a piscina é controlada pelo orçamento do promotor. O desenvolvedor simplesmente adiciona bases de dados ao pool, define os recursos mínimos e máximos para as bases de dados (mínimo e máximo dTUs ou vCores mínimos ou máximos dependendo da sua escolha de modelo de reabastecimento), e, em seguida, define os recursos da piscina com base na sua orçamento. Os programadores podem utilizar os conjuntos para fazer crescer de forma contínua o serviço, de uma “lean startup” para uma empresa madura e em constante crescimento.

Dentro do conjunto, é dada às bases de dados individuais a flexibilidade para se dimensionarem automaticamente dentro de parâmetros definidos. Sob carga pesada, uma base de dados pode consumir mais recursos para satisfazer a procura. As bases de dados sob cargas leves consomem menos, e as bases de dados sem carga não consomem recursos. O aprovisionamento de recursos para o conjunto completo e não para bases de dados individuais simplifica as tarefas de gestão. Além disso, tens um orçamento previsível para a piscina. Recursos adicionais podem ser adicionados a um pool existente sem tempo de inatividade de base de dados, exceto que as bases de dados podem precisar de ser transferidas para fornecer os recursos de computação adicionais para a nova reserva eDTU. Do mesmo modo, se já não forem necessários recursos adicionais, podem ser removidos de uma piscina existente a qualquer momento. Além disso, pode adicionar ou subtrair bases de dados ao conjunto. Se uma base de dados estiver a subutilizar recursos de forma previsível, remova-a.

> [!NOTE]
> Ao mover bases de dados para dentro ou para fora de uma piscina elástica, não há tempo de paragem, exceto por um breve período de tempo (por ordem de segundos) no final da operação quando as ligações de base de dados são retiradas.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Quando deve considerar uma piscina elástica SQL Database

Os conjuntos são bastante adequados para um grande número de bases de dados com padrões de utilização específicos. Para uma determinada base de dados, este padrão caracteriza-se por uma utilização média baixa com picos de utilização relativamente raros.

Quantas mais bases de dados adicionar a um conjunto, maior será a poupança. Dependendo do padrão de utilização da aplicação, é possível ver poupanças com apenas duas bases de dados S3.

As secções seguintes ajudam-no a avaliar se a sua coleção de bases de dados específica pode beneficiar de estar num conjunto. Os exemplos utilizam conjuntos Standard, mas os mesmos princípios também se aplicam aos conjuntos Básicos e Premium.

### <a name="assessing-database-utilization-patterns"></a>Avaliar os padrões de utilização da base de dados

A figura seguinte mostra um exemplo de uma base de dados que está inativa muito tempo, mas que também, periodicamente, apresenta picos de atividade. Este é um padrão de utilização adequado para um conjunto:

   ![uma base de dados individual adequada para um conjunto](./media/sql-database-elastic-pool/one-database.png)

Para o período de cinco minutos ilustrado, DB1 tem um pico até 90 DTUs, mas a utilização média global é inferior a cinco DTUs. É necessário um tamanho de computação S3 para executar esta carga de trabalho numa única base de dados, mas isso deixa a maioria dos recursos não utilizados durante períodos de baixa atividade.

Um conjunto permite que estas DTUs não utilizadas sejam partilhadas entre várias bases de dados, pelo que reduz as DTUs necessárias e os custos gerais.

Com base no exemplo anterior, imagine que existem bases de dados adicionais com padrões de utilização semelhantes a DB1. Nos dois próximos dados abaixo, a utilização de quatro bases de dados e 20 bases de dados são em camadas no mesmo gráfico para ilustrar a natureza não sobreposta da sua utilização ao longo do tempo utilizando o modelo de compra baseado em DTU:

   ![quatro bases de dados com um padrão de utilização adequado para um conjunto](./media/sql-database-elastic-pool/four-databases.png)

   ![vinte bases de dados com um padrão de utilização adequado para um conjunto](./media/sql-database-elastic-pool/twenty-databases.png)

A utilização agregada de DTUs nas 20 bases de dados é ilustrada pela linha preta na figura anterior. Mostra que a utilização agregada de DTUs nunca excede as cem e indica que as 20 bases de dados podem partilhar 100 eDTUs ao longo deste período de tempo. Isto resulta numa redução de 20x em DTUs e numa redução de preço de 13x em comparação com a colocação de cada uma das bases de dados em tamanhos de computação S3 para bases de dados únicas.

Este exemplo é ideal pelos motivos seguintes:

- Existem grandes diferenças entre a utilização de pico e utilização média por base de dados.
- A utilização de pico de cada base de dados ocorre em diferentes alturas no tempo.
- Os eDTUs são partilhados entre muitas bases de dados.

O preço de um conjunto é uma função das eDTUs do conjunto. Embora o preço unitário de eDTU de um conjunto seja 1,5 vezes superior ao preço unitário de DTU para bases de dados individuais, **as eDTUs de conjuntos podem ser partilhadas por muitas bases de dados e são necessárias menos eDTUs totais**. Estas distinções nos preços e na partilha das eDTUs são as bases do potencial de poupança nos custos que os conjuntos podem proporcionar.

As seguintes regras do polegar relacionadas com a contagem de bases de dados e a utilização da base de dados ajudam a garantir que um pool fornece custos reduzidos em comparação com a utilização de tamanhos de cálculo para bases de dados únicas.

### <a name="minimum-number-of-databases"></a>Número mínimo de bases de dados

Se a quantidade agregada de recursos para bases de dados únicas for superior a 1,5x os recursos necessários para a piscina, então um conjunto elástico é mais rentável.

***Exemplo de modelo de compra baseado em DTU***<br>
São necessárias pelo menos duas bases de dados S3 ou pelo menos 15 Bases de dados S0 para que um pool de 100 eDTU seja mais rentável do que a utilização de tamanhos de cálculo para bases de dados únicas.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Número máximo de bases de dados com picos simultâneos

Ao partilhar recursos, nem todas as bases de dados de um pool podem simultaneamente utilizar recursos até ao limite disponível para bases de dados individuais. Quanto menos bases de dados atingirem simultaneamente, menor será o conjunto dos recursos de piscina e mais rentável se torna a piscina. Em geral, não mais do que 2/3 (ou 67%) das bases de dados da piscina devem simultaneamente atingir o seu limite de recursos.

***Exemplo de modelo de compra baseado em DTU***

Para reduzir os custos de três bases de dados S3 num conjunto de 200 eDTU, duas bases de dados, no máximo, podem ter o pico de utilização ao mesmo tempo. Caso contrário, se mais de duas destas quatro bases de dados S3 tiverem o pico em simultâneo, o conjunto terá de ser dimensionado para mais de 200 eDTUs. Se a piscina for redimensionada para mais de 200 eDTUs, mais bases de dados S3 teriam de ser adicionadas à piscina para manter os custos mais baixos do que os tamanhos de computação para bases de dados individuais.

Note que este exemplo não considera a utilização de outras bases de dados na piscina. Se todas as bases de dados tiverem alguma utilização num determinado momento, menos de 2/3 (67%) das bases de dados podem ter o pico em simultâneo.

### <a name="resource-utilization-per-database"></a>Utilização de recursos por base de dados

Uma grande diferença entre o pico e a utilização média das bases de dados indica períodos longos de pouca utilização e curtos períodos de elevada utilização. Este padrão de utilização é ideal para partilhar recursos entre bases de dados. A inclusão de bases de dados num conjunto deve ser considerada quando o pico de utilização for cerca de 1,5 vezes superior à utilização média.

**Exemplo do modelo de compra baseado em DTU**: Uma base de dados S3 que atinge 100 DTUs e que, em média, utiliza 67 DTUs ou menos é um bom candidato para partilhar EDTUs numa piscina. Em alternativa, uma base de dados S1 que tenha como pico 20 DTUs e utilize, em média, 13 DTUs ou menos, é uma boa candidata para um conjunto.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Como escolho o tamanho correto da piscina

O melhor tamanho para uma piscina depende dos recursos agregados necessários para todas as bases de dados da piscina. Isto envolve determinar o seguinte:

- Máximos recursos utilizados por todas as bases de dados da piscina (ou DTUs máximos ou vCores máximos dependendo da sua escolha de modelo de resourcing).
- Bytes de armazenamento máximos utilizados por todas as bases de dados do conjunto.

Para obter níveis de serviço disponíveis para cada modelo de recurso, consulte o [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) ou o modelo de compra baseado em [vCore](sql-database-service-tiers-vcore.md).

Nos casos em que não pode utilizar as ferramentas, as instruções passo a passo seguintes podem ajudá-lo a prever se um conjunto tem uma relação de custo-eficácia melhor do que as bases de dados individuais.

1. Calcule as eDTUs ou vCores necessárias para a piscina da seguinte forma:

   Para o modelo de compra baseado em DTU: MAX (<*Número total de DBs* X *média de utilização DTU por DB*>;<br>  
   <*Número de DBs com pico em simultâneo* X *utilização de pico de DTUs por DB*)

   Para o modelo de compra baseado em vCore: MAX (<*Número total de DBs* X *utilização vCore média por DB*>;<br>  
   <*Número de DBs* x pico simultâneo *utilização vCore por DB*)

2. Calcule o espaço de armazenamento necessário para o conjunto ao adicionar o número de bytes de que todas as bases de dados do conjunto precisam. Em seguida, determine o tamanho do conjunto de eDTUs que disponibiliza esta quantidade de armazenamento.
3. Para o modelo de compra baseado em DTU, pegue o maior das estimativas do eDTU a partir do passo 1 e passo 2. Para o modelo de compra baseado em vCore, pegue a estimativa vCore do Passo 1.
4. Consulte a página de preços da Base de [Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/) e encontre o menor tamanho da piscina que é maior do que a estimativa do Passo 3.
5. Compare o preço da piscina do Passo 5 com o preço da utilização dos tamanhos de cálculo apropriados para bases de dados individuais.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Usando outras funcionalidades da Base de Dados SQL com piscinas elásticas

### <a name="elastic-jobs-and-elastic-pools"></a>Trabalhos elásticos e piscinas elásticas

Com um conjunto, as tarefas de gestão são simplificadas através da execução de scripts em **[tarefas elásticas](elastic-jobs-overview.md)** . Uma tarefa elástica elimina a maior parte da monotonia associada a grandes números de bases de dados.

Para obter mais informações sobre outras ferramentas de base de dados para trabalhar com várias bases de dados, veja [Aumentar horizontalmente com a Base de Dados SQL do Azure](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opções de continuidade de negócios para bases de dados em um pool elástico

As bases de dados de conjunto suportam geralmente as mesmas [funcionalidades de continuidade de negócio](sql-database-business-continuity.md) que estão disponíveis para as bases de dados individuais.

- **Restauro pontual**

  A restauração pontual utiliza cópias de dados automáticas para recuperar uma base de dados numa piscina até um determinado ponto no tempo. Veja [Restauro para um ponto anterior no tempo](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Geo-restauro**

  A Geo-restore fornece a opção de recuperação por defeito quando uma base de dados não está disponível devido a um incidente na região onde a base de dados está hospedada. Veja [Restaurar uma Base de Dados SQL do Azure ou fazer a ativação pós-falha para uma secundária](sql-database-disaster-recovery.md)

- **Georreplicação ativa**

  Para aplicações que tenham requisitos de recuperação mais agressivos do que o geo-restauro pode oferecer, configurar [a geo-replicação ativa](sql-database-active-geo-replication.md) ou um [grupo de auto-failover](sql-database-auto-failover-group.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Criação de uma nova piscina elástica SQL Database utilizando o portal Azure

Há duas maneiras de criar uma piscina elástica no portal Azure.

1. Vá ao [portal Azure](https://portal.azure.com) para criar uma piscina elástica. Procure e selecione **Azure SQL**.
2. Selecione **+Adicionar** para abrir a página de opção de **implementação Select SQL.** Pode ver informações adicionais sobre piscinas elásticas selecionando detalhes do **Show** no azulejo **base de dados.**
3. No azulejo **base de dados,** selecione **piscina elástica** no **dropdown** do tipo Recurso e, em seguida, **selecione Criar:**

   ![Criar um conjunto elástico](./media/sql-database-elastic-pool/create-elastic-pool.png)


1. Ou pode criar uma piscina elástica navegando para um servidor Azure SQL existente e clicando **+ Nova piscina** para criar uma piscina diretamente nesse servidor.

> [!NOTE]
> Pode criar várias piscinas num servidor, mas não pode adicionar bases de dados de diferentes servidores para a mesma piscina.

O nível de serviço da piscina determina as funcionalidades disponíveis para os elásticos da piscina e a quantidade máxima de recursos disponíveis para cada base de dados. Para mais detalhes, consulte os limites de recursos para piscinas elásticas no [modelo DTU](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). Para os limites de recursos baseados em vCore para piscinas elásticas, consulte [os limites de recursos baseados em vCore - piscinas elásticas](sql-database-vcore-resource-limits-elastic-pools.md).

Para configurar os recursos e preços da piscina, clique em **Configurar a piscina**. Em seguida, selecione um nível de serviço, adicione bases de dados à piscina e configure os limites de recursos para a piscina e suas bases de dados.

Quando tiver concluído a configuração da piscina, pode clicar em 'Aplicar', nomear a piscina e clicar em 'OK' para criar a piscina.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Monitorize uma piscina elástica e as suas bases de dados

No portal Azure, é possível monitorizar a utilização de uma piscina elástica e as bases de dados dentro dessa piscina. Também pode fazer um conjunto de alterações na sua piscina elástica e submeter todas as alterações ao mesmo tempo. Estas alterações incluem adicionar ou remover bases de dados, alterar as definições elásticas da piscina ou alterar as definições da base de dados.

Para começar a monitorizar a sua piscina elástica, encontre e abra uma piscina elástica no portal. Primeiro verá um ecrã que lhe dê uma visão geral do estado da sua piscina elástica. Isto inclui:

- Gráficos de monitorização que mostram o uso de recursos da piscina elástica
- Alertas e recomendações recentes, se disponíveis, para a piscina elástica

O seguinte gráfico mostra um exemplo de piscina elástica:

![Vista para a piscina](./media/sql-database-elastic-pool-manage-portal/basic.png)

Se quiser mais informações sobre a piscina, pode clicar em qualquer uma das informações disponíveis nesta visão geral. Clicar na tabela de **utilização** do Recurso irá levá-lo à vista de monitorização do Azure, onde pode personalizar as métricas e a janela de tempo mostradas na tabela. Clicar em quaisquer notificações disponíveis irá levá-lo a uma lâmina que mostre todos os detalhes desse alerta ou recomendação.

Se quiser monitorizar as bases de dados dentro da sua piscina, pode clicar na **utilização** de recursos da Base de Dados na secção **de Monitorização** do menu de recursos à esquerda.

![Página de utilização de recursos de base de dados](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Para personalizar o ecrã da tabela

Pode editar o gráfico e a página métrica para exibir outras métricas, tais como percentagem de CPU, percentagem de IO de dados e percentagem de IO de log utilizada.

No formulário **Edit Chart,** pode selecionar um intervalo de tempo fixo ou clicar **em costume** para selecionar qualquer janela de 24 horas nas últimas duas semanas e, em seguida, selecionar os recursos para monitorizar.

### <a name="to-select-databases-to-monitor"></a>Para selecionar bases de dados para monitorizar

Por predefinição, o gráfico na lâmina de **utilização** de recursos de base de dados mostrará as 5 principais bases de dados por DTU ou CPU (dependendo do seu nível de serviço). Pode configurar as bases de dados deste gráfico selecionando e desselecndo bases de dados da lista abaixo da tabela através das caixas de verificação à esquerda.

Pode também selecionar mais métricas para ver lado a lado nesta tabela de bases de dados para obter uma visão mais completa do desempenho das suas bases de dados.

Para mais informações, consulte a criação de [alertas de Base de Dados SQL no portal Azure](sql-database-insights-alerts-portal.md).

## <a name="customer-case-studies"></a>Casos práticos de clientes

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  A SnelStart utilizou piscinas elásticas com base de dados Azure SQL para expandir rapidamente os seus serviços de negócio a uma taxa de 1.000 novas Bases de Dados Azure SQL por mês.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  A Umbraco utiliza piscinas elásticas com base de dados Azure SQL para fornecer e escalar rapidamente serviços para milhares de inquilinos na nuvem.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)    

   A Daxko/CSI utiliza piscinas elásticas com base de dados Azure SQL para acelerar o seu ciclo de desenvolvimento e melhorar os seus serviços e desempenho ao cliente.   

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre preços, consulte [preços de piscina elástica](https://azure.microsoft.com/pricing/details/sql-database/elastic).
- Para escalar piscinas elásticas, consulte [piscinas elásticas e dimensionar](sql-database-elastic-pool-scale.md) [uma piscina elástica - código de amostra](scripts/sql-database-monitor-and-scale-pool-powershell.md)
- Para um vídeo, consulte o curso de vídeo da Microsoft Virtual Academy sobre as capacidades elásticas da Base de [Dados Azure SQL](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
- Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).
- Para um tutorial SaaS utilizando piscinas elásticas, consulte [Introdução à aplicação Wingtip SaaS](sql-database-wtp-overview.md).
- Para aprender sobre a gestão de recursos em piscinas elásticas com muitas bases de dados, consulte a [gestão de recursos em piscinas elásticas densas.](sql-database-elastic-pool-resource-management.md)
