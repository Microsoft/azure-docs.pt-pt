---
title: Gerenciar vários bancos de dados SQL com pools elásticos – Azure | Microsoft Docs
description: Gerencie e dimensione vários bancos de dados SQL-centenas e milhares-usando pools elásticos. Um preço para os recursos que você pode distribuir quando necessário.
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
ms.openlocfilehash: 305a35d1d40d94d1ce8e463b4af51eb84278bc3f
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510268"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Os pools elásticos ajudam você a gerenciar e dimensionar vários bancos de dados SQL do Azure

Os conjuntos elásticos da base de dados SQL são uma solução simples e rentável para gerir e dimensionar várias bases de dados com necessidades de utilização extremamente variáveis e imprevisíveis. Os bancos de dados em um pool elástico estão em um único servidor de banco de dados SQL do Azure e compartilham um número definido de recursos a um preço definido. Com os conjuntos elásticos na Base de Dados SQL do Azure, os programadores de SaaS podem otimizar o desempenho do preço num grupo de bases de dados dentro de um orçamento prescrito e, ao mesmo tempo, oferecer elasticidade de desempenho para cada base de dados.

## <a name="what-are-sql-elastic-pools"></a>O que são pools elásticos do SQL

Os programadores de SaaS criam as aplicações sobre camadas de dados de grande escala, que consistem em várias bases de dados. Um padrão de aplicação comum é aprovisionar uma base de dados individual para cada cliente. Contudo, muitas vezes, os padrões de cada cliente são distintos e imprevisíveis, sendo difícil prever os requisitos de recursos de cada utilizador de bases de dados individual. Tradicionalmente, você tinha duas opções:

- Provisionamento excessivo de recursos com base no uso de pico e pagamento por excesso, ou
- Subprovisione para economizar custos, às custas do desempenho e da satisfação do cliente durante os picos.

Os pools elásticos resolvem esse problema garantindo que os bancos de dados obtenham os recursos de desempenho de que precisam quando precisam. Fornecem um mecanismo de alocação de recursos simples dentro de um orçamento previsível. Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

> [!IMPORTANT]
> Não há nenhum encargo por banco de dados para pools elásticos. Você é cobrado por cada hora em que um pool existe no maior eDTU ou vCores, independentemente do uso ou se o pool esteve ativo por menos de uma hora.

Os pools elásticos permitem que o desenvolvedor compre recursos para um pool compartilhado por vários bancos de dados para acomodar períodos imprevisíveis de uso por bancos de dados individuais. Você pode configurar recursos para o pool com base no [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) ou no [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md). O requisito de recurso para um pool é determinado pela utilização agregada de seus bancos de dados. A quantidade de recursos disponíveis para o pool é controlada pelo orçamento do desenvolvedor. O desenvolvedor simplesmente adiciona bancos de dados ao pool, define os recursos mínimos e máximos para os bancos de dados (DTUs mínima e máxima ou mínimo ou máximo vCores dependendo da sua escolha de modelo de origem) e, em seguida, define os recursos do pool com base em seus visto. Os programadores podem utilizar os conjuntos para fazer crescer de forma contínua o serviço, de uma “lean startup” para uma empresa madura e em constante crescimento.

Dentro do conjunto, é dada às bases de dados individuais a flexibilidade para se dimensionarem automaticamente dentro de parâmetros definidos. Sob carga pesada, um banco de dados pode consumir mais recursos para atender à demanda. Os bancos de dados sob cargas leves consomem menos e os bancos de dados sem carga não consomem nenhum recurso. O aprovisionamento de recursos para o conjunto completo e não para bases de dados individuais simplifica as tarefas de gestão. Além disso, você tem um orçamento previsível para o pool. Recursos adicionais podem ser adicionados a um pool existente sem tempo de inatividade do banco de dados, exceto que talvez seja necessário movê-los para fornecer os recursos de computação adicionais para a nova reserva de eDTU. Da mesma forma, se os recursos extras não forem mais necessários, eles poderão ser removidos de um pool existente a qualquer momento. Além disso, pode adicionar ou subtrair bases de dados ao conjunto. Se uma base de dados estiver a subutilizar recursos de forma previsível, remova-a.

> [!NOTE]
> Ao mover bancos de dados para dentro ou para fora de um pool elástico, não há nenhum tempo de inatividade, exceto por um breve período (em ordem de segundos) no final da operação quando as conexões de banco de dados são descartadas.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Quando você deve considerar um pool elástico do banco de dados SQL

Os conjuntos são bastante adequados para um grande número de bases de dados com padrões de utilização específicos. Para uma determinada base de dados, este padrão caracteriza-se por uma utilização média baixa com picos de utilização relativamente raros.

Quantas mais bases de dados adicionar a um conjunto, maior será a poupança. Dependendo do padrão de utilização de aplicações, é possível ver poupanças com um mínimo de duas bases de dados S3.

As secções seguintes ajudam-no a avaliar se a sua coleção de bases de dados específica pode beneficiar de estar num conjunto. Os exemplos utilizam conjuntos Standard, mas os mesmos princípios também se aplicam aos conjuntos Básicos e Premium.

### <a name="assessing-database-utilization-patterns"></a>Avaliar os padrões de utilização da base de dados

A figura seguinte mostra um exemplo de uma base de dados que está inativa muito tempo, mas que também, periodicamente, apresenta picos de atividade. Este é um padrão de utilização adequado para um conjunto:

   ![uma base de dados individual adequada para um conjunto](./media/sql-database-elastic-pool/one-database.png)

Para o período de cinco minutos ilustrado, DB1 tem um pico até 90 DTUs, mas a utilização média global é inferior a cinco DTUs. Um tamanho de computação S3 é necessário para executar essa carga de trabalho em um único banco de dados, mas isso deixa a maioria dos recursos não utilizados durante períodos de baixa atividade.

Um conjunto permite que estas DTUs não utilizadas sejam partilhadas entre várias bases de dados, pelo que reduz as DTUs necessárias e os custos gerais.

Com base no exemplo anterior, imagine que existem bases de dados adicionais com padrões de utilização semelhantes a DB1. Nas próximas duas figuras abaixo, a utilização de quatro bancos de dados e 20 bancos de dados são dispostos em camadas no mesmo grafo para ilustrar a natureza não sobreposta de sua utilização ao longo do tempo usando o modelo de compra baseado em DTU:

   ![quatro bases de dados com um padrão de utilização adequado para um conjunto](./media/sql-database-elastic-pool/four-databases.png)

   ![vinte bases de dados com um padrão de utilização adequado para um conjunto](./media/sql-database-elastic-pool/twenty-databases.png)

A utilização agregada de DTUs nas 20 bases de dados é ilustrada pela linha preta na figura anterior. Mostra que a utilização agregada de DTUs nunca excede as cem e indica que as 20 bases de dados podem partilhar 100 eDTUs ao longo deste período de tempo. Isso resulta em uma redução de 20x em DTUs e em uma redução de preço de 13x em comparação à colocação de cada um dos bancos de dados em tamanhos de computação S3 para bancos de dados individuais.

Este exemplo é ideal pelos motivos seguintes:

- Existem grandes diferenças entre a utilização de pico e utilização média por base de dados.
- A utilização de pico de cada base de dados ocorre em diferentes alturas no tempo.
- Os eDTUs são partilhados entre muitas bases de dados.

O preço de um conjunto é uma função das eDTUs do conjunto. Embora o preço unitário de eDTU de um conjunto seja 1,5 vezes superior ao preço unitário de DTU para bases de dados individuais, **as eDTUs de conjuntos podem ser partilhadas por muitas bases de dados e são necessárias menos eDTUs totais**. Estas distinções nos preços e na partilha das eDTUs são as bases do potencial de poupança nos custos que os conjuntos podem proporcionar.

As seguintes regras gerais relacionadas à contagem de bancos de dados e à utilização do banco de dados ajudam a garantir que um pool proporcione um custo reduzido em comparação com o uso de tamanhos de computação para bancos de dados individuais.

### <a name="minimum-number-of-databases"></a>Número mínimo de bases de dados

Se a quantidade agregada de recursos para bancos de dados individuais for maior que 1,5 x os recursos necessários para o pool, um pool elástico será mais econômico.

***Exemplo de modelo de compra baseado em DTU***<br>
Pelo menos dois bancos de dados S3 ou, pelo menos, 15 bancos de dados S0 são necessários para que um pool de eDTU de 100 seja mais econômico do que usar tamanhos de computação para bancos de dados individuais.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Número máximo de bases de dados com picos simultâneos

Ao compartilhar recursos, nem todos os bancos de dados em um pool podem usar simultaneamente recursos até o limite disponível para bancos de dados individuais. Quanto menos bancos de dados que estiverem em pico simultâneos, menor será o número de recursos do pool e mais econômico será o pool. Em geral, não mais de 2/3 (ou 67%) dos bancos de dados no pool devem ser simultaneamente picos no limite de seus recursos.

***Exemplo de modelo de compra baseado em DTU***

Para reduzir os custos de três bases de dados S3 num conjunto de 200 eDTU, duas bases de dados, no máximo, podem ter o pico de utilização ao mesmo tempo. Caso contrário, se mais de duas destas quatro bases de dados S3 tiverem o pico em simultâneo, o conjunto terá de ser dimensionado para mais de 200 eDTUs. Se o pool for redimensionado para mais de 200 eDTUs, mais bancos de dados S3 precisarão ser adicionados ao pool para manter os custos menores do que os tamanhos de computação para bancos de dados individuais.

Tenha em conta que este exemplo não considera a utilização de outras bases de dados do conjunto. Se todas as bases de dados tiverem alguma utilização num determinado momento, menos de 2/3 (67%) das bases de dados podem ter o pico em simultâneo.

### <a name="resource-utilization-per-database"></a>Utilização de recursos por banco de dados

Uma grande diferença entre o pico e a utilização média das bases de dados indica períodos longos de pouca utilização e curtos períodos de elevada utilização. Este padrão de utilização é ideal para partilhar recursos entre bases de dados. A inclusão de bases de dados num conjunto deve ser considerada quando o pico de utilização for cerca de 1,5 vezes superior à utilização média.

**Exemplo de modelo de compra baseado em DTU**: Uma base de dados S3 que tenha como pico 100 DTUs e utilize, em média, 67 DTUs ou menos, é uma boa candidata para partilhar eDTUs num conjunto. Em alternativa, uma base de dados S1 que tenha como pico 20 DTUs e utilize, em média, 13 DTUs ou menos, é uma boa candidata para um conjunto.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Como fazer escolher o tamanho de pool correto

O melhor tamanho para um pool depende dos recursos agregados necessários para todos os bancos de dados no pool. Isso envolve a determinação do seguinte:

- Máximo de recursos utilizados por todos os bancos de dados no pool (o máximo de DTUs ou máximo de vCores, dependendo da sua escolha de modelo de origem).
- Bytes de armazenamento máximos utilizados por todas as bases de dados do conjunto.

Para as camadas de serviço disponíveis para cada modelo de recurso, consulte o [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) ou o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

Nos casos em que não pode utilizar as ferramentas, as instruções passo a passo seguintes podem ajudá-lo a prever se um conjunto tem uma relação de custo-eficácia melhor do que as bases de dados individuais.

1. Estime o eDTUs ou o vCores necessário para o pool da seguinte maneira:

   Para o modelo de compra baseado em DTU: Máx. (<*Número Total de DBs* X *utilização média de DTUs por DB*>,<br>  
   <*Número de DBs com pico em simultâneo* X *utilização de pico de DTUs por DB*)

   Para o modelo de compra baseado em vCore: MAX (<*número total de bancos de* *BD X utilização média de vCore por banco*de >,<br>  
   <*Número de bancos de os de pico simultâneos* *Pico de utilização de vCore X por BD*)

2. Calcule o espaço de armazenamento necessário para o conjunto ao adicionar o número de bytes de que todas as bases de dados do conjunto precisam. Em seguida, determine o tamanho do conjunto de eDTUs que disponibiliza esta quantidade de armazenamento.
3. Para o modelo de compra baseado em DTU, tire o maior das estimativas de eDTU da etapa 1 e da etapa 2. Para o modelo de compra baseado em vCore, pegue a estimativa vCore da etapa 1.
4. Consulte a [página de preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/) e localize o menor tamanho de pool que é maior que a estimativa da etapa 3.
5. Compare o preço do pool da etapa 5 com o preço de usar os tamanhos de computação apropriados para bancos de dados individuais.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Usando outros recursos do banco de dados SQL com pools elásticos

### <a name="elastic-jobs-and-elastic-pools"></a>Trabalhos elásticos e pools elásticos

Com um conjunto, as tarefas de gestão são simplificadas através da execução de scripts em **[tarefas elásticas](elastic-jobs-overview.md)** . Uma tarefa elástica elimina a maior parte da monotonia associada a grandes números de bases de dados.

Para obter mais informações sobre outras ferramentas de base de dados para trabalhar com várias bases de dados, veja [Aumentar horizontalmente com a Base de Dados SQL do Azure](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opções de continuidade de negócios para bancos de dados em um pool elástico

As bases de dados de conjunto suportam geralmente as mesmas [funcionalidades de continuidade de negócio](sql-database-business-continuity.md) que estão disponíveis para as bases de dados individuais.

- **Restauração point-in-time**

  A restauração pontual usa backups automáticos de banco de dados para recuperar um banco de dados em um pool para um ponto específico no tempo. Veja [Restauro para um ponto anterior no tempo](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Restauração geográfica**

  A restauração geográfica fornecerá a opção de recuperação padrão quando um banco de dados não estiver disponível devido a um incidente na região em que o banco de dados está hospedado. Veja [Restaurar uma Base de Dados SQL do Azure ou fazer a ativação pós-falha para uma secundária](sql-database-disaster-recovery.md)

- **Georreplicação ativa**

  Para aplicativos que têm requisitos de recuperação mais agressivos do que a restauração geográfica pode oferecer, configure [a replicação geográfica ativa](sql-database-active-geo-replication.md) ou um [grupo de failover automático](sql-database-auto-failover-group.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Criando um novo pool elástico do banco de dados SQL usando o portal do Azure

Há duas maneiras de criar um pool elástico no portal do Azure.

1. Você pode criar um pool elástico pesquisando o **pool elástico do SQL** no **Marketplace** ou clicando em **+ Adicionar** na folha de procura de pools elásticos do SQL. Você pode especificar um servidor novo ou existente por meio deste fluxo de trabalho de provisionamento de pool.
2. Ou você pode criar um pool elástico navegando até um SQL Server existente e clicando em **criar pool** para criar um pool diretamente nesse servidor. A única diferença aqui é ignorar a etapa em que você especifica o servidor durante o fluxo de trabalho de provisionamento do pool.

> [!NOTE]
> Você pode criar vários pools em um servidor, mas não pode adicionar bancos de dados de servidores diferentes no mesmo pool.

A camada de serviço do pool determina os recursos disponíveis para os elásticos no pool e a quantidade máxima de recursos disponíveis para cada banco de dados. Para obter detalhes, consulte limites de recursos para pools elásticos no [modelo de DTU](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). Para limites de recursos baseados em vCore para pools elásticos, consulte [limites de recursos baseados em VCORE – pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md).

Para configurar os recursos e os preços do pool, clique em **Configurar pool**. Em seguida, selecione uma camada de serviço, adicione bancos de dados ao pool e configure os limites de recurso para o pool e seus bancos de dados.

Quando tiver concluído a configuração do pool, você poderá clicar em ' Aplicar ', nomear o pool e clicar em ' OK ' para criar o pool.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Monitorar um pool elástico e seus bancos de dados

No portal do Azure, você pode monitorar a utilização de um pool elástico e os bancos de dados dentro desse pool. Você também pode fazer um conjunto de alterações em seu pool elástico e enviar todas as alterações ao mesmo tempo. Essas alterações incluem adicionar ou Remover bancos de dados, alterar as configurações de pool elástico ou alterar suas configurações de banco de dados.

Para começar a monitorar seu pool elástico, localize e abra um pool elástico no Portal. Primeiro, você verá uma tela que fornece uma visão geral do status do pool elástico. Isto inclui:

- Gráficos de monitoramento mostrando o uso de recursos do pool elástico
- Alertas e recomendações recentes, se disponíveis, para o pool elástico

O gráfico a seguir mostra um exemplo de pool elástico:

![Exibição do pool](./media/sql-database-elastic-pool-manage-portal/basic.png)

Se desejar obter mais informações sobre o pool, você pode clicar em qualquer uma das informações disponíveis nesta visão geral. Clicar no gráfico de **utilização de recursos** levará você para o modo de exibição de monitoramento do Azure, no qual você pode personalizar as métricas e a janela de tempo mostradas no gráfico. Ao clicar em qualquer notificação disponível, você será levado a uma folha que mostra os detalhes completos desse alerta ou recomendação.

Se você quiser monitorar os bancos de dados dentro de seu pool, poderá clicar em utilização de **recursos de Database** na seção **monitoramento** do menu de recursos à esquerda.

![Página de utilização de recursos do banco de dados](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Para personalizar a exibição do gráfico

Você pode editar o gráfico e a página métrica para exibir outras métricas, como porcentagem de CPU, porcentagem de e/s de dados e porcentagem de e/s de log usadas.

No formulário **Editar gráfico** , você pode selecionar um intervalo de tempo fixo ou clicar em **personalizado** para selecionar qualquer janela de 24 horas nas últimas duas semanas e, em seguida, selecionar os recursos a serem monitorados.

### <a name="to-select-databases-to-monitor"></a>Para selecionar os bancos de dados a serem monitorados

Por padrão, o gráfico na folha de **utilização de recursos do banco de dados** mostrará os 5 principais bancos por DTU ou CPU (dependendo de sua camada de serviço). Você pode alternar os bancos de dados neste gráfico selecionando e desmarcando bancos de dados da lista abaixo do gráfico por meio das caixas de seleção à esquerda.

Você também pode selecionar mais métricas para exibir lado a lado nesta tabela de banco de dados para obter uma visão mais completa do desempenho de seus bancos de dados.

Para obter mais informações, consulte [criar alertas do banco de dados SQL no portal do Azure](sql-database-insights-alerts-portal.md).

## <a name="customer-case-studies"></a>Casos práticos de clientes

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  O SnelStart usou pools elásticos com o banco de dados SQL do Azure para expandir rapidamente seus serviços de negócios a uma taxa de 1.000 novos bancos de dados SQL do Azure por mês.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  O Umbraco usa pools elásticos com o banco de dados SQL do Azure para provisionar e dimensionar rapidamente os serviços para milhares de locatários na nuvem.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)    

   O Daxko/CSI usa pools elásticos com o banco de dados SQL do Azure para acelerar seu ciclo de desenvolvimento e aprimorar seus serviços e desempenho do cliente.   

## <a name="next-steps"></a>Passos Seguintes

- Para dimensionar pools elásticos [](sql-database-elastic-pool-scale.md) , consulte dimensionar pools elásticos e [dimensionar um pool elástico – código de exemplo](scripts/sql-database-monitor-and-scale-pool-powershell.md)
- Para um vídeo, consulte [curso de vídeo da Microsoft Virtual Academy sobre recursos elásticos do banco de dados SQL do Azure](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
- Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).
- Para obter um tutorial de SaaS usando pools elásticos, consulte [introdução ao aplicativo SaaS Wingtip](sql-database-wtp-overview.md).
