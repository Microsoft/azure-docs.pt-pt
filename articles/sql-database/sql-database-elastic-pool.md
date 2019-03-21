---
title: Gerir várias bases de dados SQL com conjuntos elástico-Azure | Documentos da Microsoft
description: Gerir e dimensionar várias bases de dados SQL - centenas e milhares - utilizam conjuntos elásticos. Um preço para os recursos que pode distribuir onde necessário.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, carlrab
manager: craigg
ms.date: 02/28/2019
ms.openlocfilehash: 96160745fa8702fc6f931904098c28d8968de2c1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58010232"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>O ajudam a gerir e dimensionar várias bases de dados SQL do Azure de conjuntos elásticos

Os conjuntos elásticos da base de dados SQL são uma solução simples e rentável para gerir e dimensionar várias bases de dados com necessidades de utilização extremamente variáveis e imprevisíveis. As bases de dados num conjunto elástico são num único servidor de base de dados do Azure SQL e partilham um número definido de recursos a um preço do conjunto. Com os conjuntos elásticos na Base de Dados SQL do Azure, os programadores de SaaS podem otimizar o desempenho do preço num grupo de bases de dados dentro de um orçamento prescrito e, ao mesmo tempo, oferecer elasticidade de desempenho para cada base de dados.

## <a name="what-are-sql-elastic-pools"></a>Quais são os conjuntos elásticos SQL

Os programadores de SaaS criam as aplicações sobre camadas de dados de grande escala, que consistem em várias bases de dados. Um padrão de aplicação comum é aprovisionar uma base de dados individual para cada cliente. Contudo, muitas vezes, os padrões de cada cliente são distintos e imprevisíveis, sendo difícil prever os requisitos de recursos de cada utilizador de bases de dados individual. Tradicionalmente, tem duas opções:

- Aprovisionar excessivamente recursos com base na utilização máxima e ao longo de pagamento, ou
- Aprovisionar insuficientemente para economizar custos, às custas de satisfação de desempenho e o cliente durante os picos.

Conjuntos elásticos resolvem este problema ao garantir que as bases de dados recebem os recursos de desempenho que necessitam, quando precisarem. Fornecem um mecanismo de alocação de recursos simples dentro de um orçamento previsível. Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

> [!IMPORTANT]
> Não existe nenhum custo por base de dados para conjuntos elásticos. É faturado por cada hora, que existe um conjunto no eDTU mais alto ou vCores, independentemente da utilização ou se o conjunto esteve ativo durante menos de uma hora.

Conjuntos elásticos permitem aos programadores comprar recursos para um conjunto partilhado por várias bases de dados para acomodar períodos de utilização imprevisíveis bases de dados individuais. Pode configurar recursos para o conjunto com qualquer uma base no [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) ou o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md). O pedido de recurso para um conjunto é determinado pela utilização agregada de seus bancos de dados. A quantidade de recursos disponíveis para os conjuntos é controlada pelo orçamento do programador. O desenvolvedor simplesmente adiciona bases de dados ao conjunto, define os recursos mínimos e máximos para as bases de dados (o mínimo e máximo DTUs ou mínimo ou máximo vCores dependendo de sua escolha de obtenção de recursos modelo) e, em seguida, define os recursos do conjunto com base em suas orçamento. Os programadores podem utilizar os conjuntos para fazer crescer de forma contínua o serviço, de uma “lean startup” para uma empresa madura e em constante crescimento.

Dentro do conjunto, é dada às bases de dados individuais a flexibilidade para se dimensionarem automaticamente dentro de parâmetros definidos. Com muita carga, uma base de dados pode consumir mais recursos para satisfazer a procura. Bases de dados sob cargas mais leves consomem menos e bases de dados em qualquer carga não consomem nenhum recurso. O aprovisionamento de recursos para o conjunto completo e não para bases de dados individuais simplifica as tarefas de gestão. Além disso, tem um orçamento previsível para o conjunto. Recursos adicionais podem ser adicionados a um conjunto existente sem períodos de indisponibilidade da base de dados, exceto pelo fato das bases de dados poderão ter de ser movidas para fornecer os recursos de computação adicionais para a nova reserva de eDTU. Da mesma forma, se já não são necessários recursos extras eles podem ser removidos de um conjunto existente em qualquer ponto no tempo. Além disso, pode adicionar ou subtrair bases de dados ao conjunto. Se uma base de dados estiver a subutilizar recursos de forma previsível, remova-a.

> [!NOTE]
> Ao mover as bases de dados para dentro ou fora de um conjunto elástico, não existe nenhum tempo de inatividade, exceto para um breve período de tempo (na ordem de segundos) no final da operação quando ligações de base de dados são ignoradas.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Quando deve pensar num conjunto elástico da base de dados SQL

Os conjuntos são bastante adequados para um grande número de bases de dados com padrões de utilização específicos. Para uma determinada base de dados, este padrão caracteriza-se por uma utilização média baixa com picos de utilização relativamente raros.

Quantas mais bases de dados adicionar a um conjunto, maior será a poupança. Dependendo do padrão de utilização de aplicações, é possível ver poupanças com um mínimo de duas bases de dados S3.

As secções seguintes ajudam-no a avaliar se a sua coleção de bases de dados específica pode beneficiar de estar num conjunto. Os exemplos utilizam conjuntos Standard, mas os mesmos princípios também se aplicam aos conjuntos Básicos e Premium.

### <a name="assessing-database-utilization-patterns"></a>Avaliar os padrões de utilização da base de dados

A figura seguinte mostra um exemplo de uma base de dados que está inativa muito tempo, mas que também, periodicamente, apresenta picos de atividade. Este é um padrão de utilização adequado para um conjunto:

   ![uma base de dados individual adequada para um conjunto](./media/sql-database-elastic-pool/one-database.png)

Para o período de cinco minutos ilustrado, DB1 tem um pico até 90 DTUs, mas a utilização média global é inferior a cinco DTUs. Um tamanho de computação de S3 é necessário para executar esta carga de trabalho num único banco de dados, mas tal deixa a maior parte dos recursos inutilizados durante os períodos de pouca atividade.

Um conjunto permite que estas DTUs não utilizadas sejam partilhadas entre várias bases de dados, pelo que reduz as DTUs necessárias e os custos gerais.

Com base no exemplo anterior, imagine que existem bases de dados adicionais com padrões de utilização semelhantes a DB1. Nas próximas duas figuras abaixo, a utilização de quatro bases de dados e 20 bases de dados é incluída no mesmo gráfico para ilustrar a natureza não sobreposta da respetiva utilização ao longo do tempo usando o modelo de compra baseado em DTU:

   ![quatro bases de dados com um padrão de utilização adequado para um conjunto](./media/sql-database-elastic-pool/four-databases.png)

   ![vinte bases de dados com um padrão de utilização adequado para um conjunto](./media/sql-database-elastic-pool/twenty-databases.png)

A utilização agregada de DTUs nas 20 bases de dados é ilustrada pela linha preta na figura anterior. Mostra que a utilização agregada de DTUs nunca excede as cem e indica que as 20 bases de dados podem partilhar 100 eDTUs ao longo deste período de tempo. Isso resulta numa redução de 20 x na DTUs e tamanhos de bases de dados individuais de computação de uma redução de 13 vezes preço em comparação com colocar cada base de dados no S3.

Este exemplo é ideal pelos motivos seguintes:

- Existem grandes diferenças entre a utilização de pico e utilização média por base de dados.
- A utilização de pico de cada base de dados ocorre em diferentes alturas no tempo.
- Os eDTUs são partilhados entre muitas bases de dados.

O preço de um conjunto é uma função das eDTUs do conjunto. Embora o preço unitário de eDTU de um conjunto seja 1,5 vezes superior ao preço unitário de DTU para bases de dados individuais, **as eDTUs de conjuntos podem ser partilhadas por muitas bases de dados e são necessárias menos eDTUs totais**. Estas distinções nos preços e na partilha das eDTUs são as bases do potencial de poupança nos custos que os conjuntos podem proporcionar.

As regras básicas seguintes relacionadas com a contagem de base de dados e a utilização de base de dados ajudam a garantir que os conjuntos oferecem custos reduzidos em comparação comparado a utilização de tamanhos de computação para bases de dados individuais.

### <a name="minimum-number-of-databases"></a>Número mínimo de bases de dados

Se a quantidade de agregado de recursos para bases de dados individuais for superior a 1,5 vezes os recursos necessários para o agrupamento, um conjunto elástico é mais económico.

***Exemplo de modelo de compra baseado em DTU***<br>
De um conjunto de 100 Edtus seja mais rentável do que utilizar tamanhos de computação para bases de dados individuais, são necessárias, pelo menos, duas bases de dados S3 ou, pelo menos, 15 bases de dados S0.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Número máximo de bases de dados com picos simultâneos

Ao partilhar recursos, nem todas as bases de dados num conjunto em simultâneo podem utilizar recursos até ao limite disponível para bases de dados individuais. As bases de dados menos horas de pico simultâneo, menores dos recursos do agrupamento podem ser definidos mais econômica do e. Em geral, não mais do que 2/3 (ou 67) das bases de dados no conjunto devem ter o pico para o respetivo limite de recursos.

***Exemplo de modelo de compra baseado em DTU***

Para reduzir os custos de três bases de dados S3 num conjunto de 200 eDTU, duas bases de dados, no máximo, podem ter o pico de utilização ao mesmo tempo. Caso contrário, se mais de duas destas quatro bases de dados S3 tiverem o pico em simultâneo, o conjunto terá de ser dimensionado para mais de 200 eDTUs. Se o conjunto for redimensionado para mais de 200 eDTUs, mais bases de dados S3 seriam necessário ser adicionado ao agrupamento para manter os custos inferior de tamanhos de bases de dados individuais de computação.

Tenha em conta que este exemplo não considera a utilização de outras bases de dados do conjunto. Se todas as bases de dados tiverem alguma utilização num determinado momento, menos de 2/3 (67%) das bases de dados podem ter o pico em simultâneo.

### <a name="resource-utilization-per-database"></a>Utilização de recursos por base de dados

Uma grande diferença entre o pico e a utilização média das bases de dados indica períodos longos de pouca utilização e curtos períodos de elevada utilização. Este padrão de utilização é ideal para partilhar recursos entre bases de dados. A inclusão de bases de dados num conjunto deve ser considerada quando o pico de utilização for cerca de 1,5 vezes superior à utilização média.

**Exemplo de modelo de compra baseado em DTU**: Uma base de dados S3 que tenha como pico 100 DTUs e utilize, em média, 67 DTUs ou menos, é uma boa candidata para partilhar eDTUs num conjunto. Em alternativa, uma base de dados S1 que tenha como pico 20 DTUs e utilize, em média, 13 DTUs ou menos, é uma boa candidata para um conjunto.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Como devo escolher o tamanho do conjunto correto

O melhor tamanho para um conjunto depende dos recursos de agregação necessários para todas as bases de dados no conjunto. Isso envolve determinar o seguinte:

- Recursos máximos utilizados por todas as bases de dados no conjunto (limite máximo de DTUs ou vCores máxima, dependendo de sua escolha de obtenção de recursos modelo).
- Bytes de armazenamento máximos utilizados por todas as bases de dados do conjunto.

Para os escalões de serviço disponíveis para cada modelo de recursos, consulte a [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) ou o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

Nos casos em que não pode utilizar as ferramentas, as instruções passo a passo seguintes podem ajudá-lo a prever se um conjunto tem uma relação de custo-eficácia melhor do que as bases de dados individuais.

1. Estime as eDTUs ou vCores necessárias para o conjunto da seguinte forma:

   Para o modelo de compra baseado em DTU: Máx. (<*Número Total de DBs* X *utilização média de DTUs por DB*>,<br>  
   <*Número de DBs com pico em simultâneo* X *utilização de pico de DTUs por DB*)

   Para o modelo de compra baseado em vCore: Máx. (<*número Total de DBs* X *média de utilização de vCore por DB*>,<br>  
   <*Número de com picos simultâneos DBs* X *pico de utilização de vCore por DB*)

2. Calcule o espaço de armazenamento necessário para o conjunto ao adicionar o número de bytes de que todas as bases de dados do conjunto precisam. Em seguida, determine o tamanho do conjunto de eDTUs que disponibiliza esta quantidade de armazenamento.
3. Para o modelo de compra baseado em DTU, têm o maior entre as estimativas de eDTU do passo 1 e 2. Para o modelo de compra baseado em vCore, têm a estimativa de vCore do passo 1.
4. Consulte a [base de dados SQL página de preços](https://azure.microsoft.com/pricing/details/sql-database/) e localizar o conjunto de menor tamanho que for maior do que a estimativa do passo 3.
5. Compare o preço do conjunto do passo 5 para o preço de utilizar os tamanhos de computação adequados para bases de dados individuais.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Utilizar outras funcionalidades de base de dados SQL com conjuntos elásticos

### <a name="elastic-jobs-and-elastic-pools"></a>Tarefas elásticas e conjuntos elásticos

Com um conjunto, as tarefas de gestão são simplificadas através da execução de scripts em **[tarefas elásticas](sql-database-elastic-jobs-overview.md)**. Uma tarefa elástica elimina a maior parte da monotonia associada a grandes números de bases de dados. Para começar, veja [Introdução às Tarefas elásticas](sql-database-elastic-jobs-getting-started.md).

Para obter mais informações sobre outras ferramentas de base de dados para trabalhar com várias bases de dados, veja [Aumentar horizontalmente com a Base de Dados SQL do Azure](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opções de continuidade de negócio para bases de dados num conjunto elástico

As bases de dados agrupadas suportam geralmente as mesmas [funcionalidades de continuidade de negócio](sql-database-business-continuity.md) que estão disponíveis para as bases de dados individuais.

- **Restauro de ponto no tempo**

  Restauro de ponto no tempo utiliza cópias de segurança da base de dados automática para recuperar uma base de dados num conjunto para um ponto específico no tempo. Veja [Restauro para um ponto anterior no tempo](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Restauro geográfico**

  O restauro geográfico fornece a opção de recuperação predefinida quando uma base de dados está indisponível devido a um incidente na região onde está hospedado o banco de dados. Veja [Restaurar uma Base de Dados SQL do Azure ou fazer a ativação pós-falha para uma secundária](sql-database-disaster-recovery.md)

- **Georreplicação ativa**

  Para aplicações que têm requisitos de recuperação mais agressivos do que o restauro geográfico pode oferecer, configure [georreplicação ativa](sql-database-active-geo-replication.md) ou uma [grupo de ativação pós-falha automática](sql-database-auto-failover-group.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Criar um novo conjunto elástico de base de dados SQL com o portal do Azure

Existem duas formas de criar um conjunto elástico no portal do Azure.

1. Pode criar um conjunto elástico, procurando **agrupamento elástico de SQL** no **Marketplace** ou clicando em **+ adicionar** sobre os conjuntos elásticos SQL Non painel. É possível especificar um servidor novo ou existente através deste conjunto de aprovisionamento de fluxo de trabalho.
2. Ou pode criar um conjunto elástico, navegando para um SQL server existente e clique em **criar conjunto** para criar um conjunto diretamente para esse servidor. A única diferença aqui é que ignore o passo em que especifica o servidor durante o conjunto de aprovisionamento de fluxo de trabalho.

> [!NOTE]
> Pode criar múltiplos conjuntos num servidor, mas não é possível adicionar bases de dados a partir de diferentes servidores ao mesmo conjunto.

Escalão de serviço do conjunto determina as funcionalidades disponíveis para os elásticos no conjunto e a quantidade máxima de recursos disponíveis para cada base de dados. Para obter detalhes, consulte os limites de recursos para conjuntos elásticos na [modelo DTU](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). Para limites de recursos baseados em vCore para conjuntos elásticos, veja [limites de recursos baseados em vCore - conjuntos elásticos](sql-database-vcore-resource-limits-elastic-pools.md).

Para configurar os recursos e preços do conjunto, clique em **configurar conjunto**. Em seguida, selecione um escalão de serviço, adicionar bases de dados para o conjunto e configurar os limites de recursos para o agrupamento e respetivas bases de dados.

Quando concluir a configuração do conjunto, pode clicar em "Aplicar", nome do conjunto e clique em 'OK' para criar o conjunto.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Monitorizar um conjunto elástico e respetivas bases de dados

No portal do Azure, pode monitorizar a utilização de conjuntos elásticos e bases de dados dentro desse conjunto. Também pode fazer um conjunto de alterações ao seu conjunto elástico e submeter todas as alterações ao mesmo tempo. Estas alterações incluem adicionar ou remover bases de dados, alterar as definições do conjunto elástico ou alterar as definições de base de dados.

Para iniciar a monitorização do conjunto elástico, encontrar e abrir um conjunto elástico no portal. Verá um ecrã que lhe dá uma visão geral do Estado de seu conjunto elástico em primeiro lugar. Isto inclui:

- Gráficos de monitorização que mostra a utilização de recursos do conjunto elástico
- Alertas recentes e recomendações, se estiver disponível, para o conjunto elástico

O gráfico seguinte mostra um conjunto elástico de exemplo:

![Vista de conjunto](./media/sql-database-elastic-pool-manage-portal/basic.png)

Se pretender obter mais informações sobre o conjunto, pode clicar em qualquer uma das informações disponíveis nesta visão geral. Clicar no **utilização de recursos** gráfico leva-o para a vista de monitorização do Azure, onde pode personalizar a janela de métricas e tempo mostrada no gráfico. Clicar em quaisquer notificações disponíveis leva-o para um painel que mostra todos os detalhes desse alerta ou uma recomendação.

Se gostaria de monitorizar as bases de dados dentro de seu conjunto, pode clicar em **base de dados de utilização de recursos** no **monitorização** secção do menu de recursos no lado esquerdo.

![Página de utilização de recursos do banco de dados](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Para personalizar a exibição de gráfico

Pode editar o gráfico e a página de métrica para apresentar a outras métricas, como a percentagem de CPU, percentagem de es de dados e percentagem utilizada da e/s de registo.

Sobre o **editar gráfico** formulário, pode selecionar uma hora fixa de intervalo ou clique em **personalizado** para selecionar qualquer período de 24 horas nas últimas duas semanas e, em seguida, selecione os recursos para monitorizar.

### <a name="to-select-databases-to-monitor"></a>Para selecionar as bases de dados para monitorizar

Por predefinição, o gráfico na **utilização de recursos de bases de dados** painel mostrará as bases de dados principais 5 DTUS ou da CPU (consoante o escalão de serviço). Pode mudar as bases de dados neste gráfico, selecionando e desmarcá-lo de bases de dados na lista a seguir o gráfico por meio das caixas de verificação à esquerda.

Também pode selecionar métricas mais para exibição lado a lado nesta tabela de base de dados para obter uma visão mais completa do seu desempenho de bases de dados.

Para obter mais informações, consulte [criar alertas de base de dados SQL no portal do Azure](sql-database-insights-alerts-portal.md).

## <a name="customer-case-studies"></a>Casos práticos de clientes

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  A SnelStart utilizado conjuntos elásticos com o Azure SQL Database para expandir rapidamente os seus serviços empresariais a uma taxa de 1000 novas de dados SQL Azure por mês.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco utiliza conjuntos elásticos com o Azure SQL Database para os serviços rapidamente aprovisionar e Dimensionar para milhares de inquilinos na nuvem.

- [Daxko/CSI](https://customers.microsoft.com/story/csi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services)

  Daxko/CSI utiliza conjuntos elásticos com o SQL Database do Azure para acelerar o seu ciclo de desenvolvimento e para melhorar o serviço ao cliente e o desempenho.

## <a name="next-steps"></a>Passos Seguintes

- Para dimensionar conjuntos elásticos, veja [Dimensionar conjuntos elásticos](sql-database-elastic-pool.md) e [dimensionar um conjunto elástico - código de exemplo](scripts/sql-database-monitor-and-scale-pool-powershell.md)
- Para ver um vídeo, consulte [curso de vídeo do Microsoft Virtual Academy sobre capacidades elásticas da base de dados do Azure SQL](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
- Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).
- Para obter um tutorial de SaaS que utilizam conjuntos elásticos, veja [introdução à aplicação Wingtip SaaS](sql-database-wtp-overview.md).
