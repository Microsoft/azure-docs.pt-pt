---
title: Projetar serviços disponíveis globalmente usando o banco de dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre o design de aplicativos para serviços altamente disponíveis usando o banco de dados SQL do Azure.
keywords: recuperação de desastre na nuvem, soluções de recuperação de desastre, backup de dados de aplicativo, replicação geográfica, planejamento de continuidade de negócios
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
ms.date: 12/04/2018
ms.openlocfilehash: a79fa40568502a73194e467de2227d54931d0100
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568950"
---
# <a name="designing-globally-available-services-using-azure-sql-database"></a>Projetando serviços disponíveis globalmente usando o banco de dados SQL do Azure

Ao criar e implantar serviços de nuvem com o banco de dados SQL do Azure, você usa a [replicação geográfica ativa](sql-database-active-geo-replication.md) ou [grupos de failover automático](sql-database-auto-failover-group.md) para fornecer resiliência a interrupções regionais e falhas catastróficas. O mesmo recurso permite que você crie aplicativos distribuídos globalmente otimizados para acesso local aos dados. Este artigo discute os padrões de aplicativo comuns, incluindo os benefícios e as compensações de cada opção.

> [!NOTE]
> Se você estiver usando bancos de dados Premium ou Comercialmente Crítico e pools elásticos, poderá torná-los resilientes a interrupções regionais, convertendo-os para a configuração de implantação com redundância de zona. Consulte [bancos de dados com redundância de zona](sql-database-high-availability.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Cenário 1: Usando duas regiões do Azure para continuidade dos negócios com tempo de inatividade mínimo

Nesse cenário, os aplicativos têm as seguintes características:

* O aplicativo está ativo em uma região do Azure
* Todas as sessões de banco de dados exigem acesso de leitura e gravação (RW) aos dados
* A camada da Web e a camada de dados devem ser posicionadas para reduzir o custo de latência e tráfego
* Fundamentalmente, o tempo de inatividade é um risco comercial maior para esses aplicativos do que a perda de dados

Nesse caso, a topologia de implantação do aplicativo é otimizada para lidar com desastres regionais quando todos os componentes do aplicativo precisam fazer failover juntos. O diagrama a seguir mostra essa topologia. Para redundância geográfica, os recursos do aplicativo são implantados na região A e B. No entanto, os recursos na região B não são utilizados até a região A falhar. Um grupo de failover é configurado entre as duas regiões para gerenciar conectividade de banco de dados, replicação e failover. O serviço Web em ambas as regiões é configurado para acessar o banco de dados por meio do ouvinte  **&lt;de leitura-&gt;gravação failover-Group-Name. Database.Windows.net** (1). O Gerenciador de tráfego é configurado para usar o [método de roteamento de prioridade](../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> O [Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md) é usado em todo este artigo apenas para fins ilustrativos. Você pode usar qualquer solução de balanceamento de carga que dê suporte ao método de roteamento de prioridade.

O diagrama a seguir mostra essa configuração antes de uma interrupção:

![Cenário 1. Configuração antes da interrupção.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Após uma interrupção na região primária, o serviço do banco de dados SQL detecta que o banco de dados primário não está acessível e dispara o failover para a região secundária com base nos parâmetros da política de failover automático (1). Dependendo do SLA do seu aplicativo, você pode configurar um período de carência que controla o tempo entre a detecção da interrupção e o próprio failover. É possível que o Gerenciador de tráfego inicie o failover do ponto de extremidade antes que o grupo de failover dispare o failover do banco de dados. Nesse caso, o aplicativo Web não pode se reconectar imediatamente ao banco de dados. Mas as reconexões serão automaticamente bem sucedidos assim que o failover de banco de dados for concluído. Quando a região com falha é restaurada e novamente online, a primária antiga se reconecta automaticamente como um novo secundário. O diagrama a seguir ilustra a configuração após o failover.

> [!NOTE]
> Todas as transações confirmadas após o failover são perdidas durante a reconexão. Após a conclusão do failover, o aplicativo na região B é capaz de reconectar e reiniciar o processamento das solicitações do usuário. O aplicativo Web e o banco de dados primário agora estão na região B e permanecem colocalizados.

![Cenário 1. Configuração após o failover](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Se ocorrer uma interrupção na região B, o processo de replicação entre o banco de dados primário e o secundário será suspenso, mas o link entre os dois permanecerá intacto (1). O tráfego gerenciado detecta que a conectividade com a região B está quebrada e marca o aplicativo Web do ponto de extremidade 2 como degradado (2). O desempenho do aplicativo não é afetado nesse caso, mas o banco de dados torna-se exposto e, portanto, com um risco maior de perda de dado no caso de A região A falhar em sucessão.

> [!NOTE]
> Para a recuperação de desastres, recomendamos a configuração com a implantação de aplicativo limitada a duas regiões. Isso ocorre porque a maioria das geografias do Azure tem apenas duas regiões. Essa configuração não protege seu aplicativo contra uma falha catastrófica simultânea de ambas as regiões. Em um evento improvável de tal falha, você pode recuperar seus bancos de dados em uma terceira região usando [a operação de restauração geográfica](sql-database-disaster-recovery.md#recover-using-geo-restore).
>

 Depois que a interrupção for atenuada, o banco de dados secundário será ressincronizado automaticamente com o primário. Durante a sincronização, o desempenho do primário pode ser afetado. O impacto específico depende da quantidade de dados que o novo primário adquiriu desde o failover. O diagrama a seguir ilustra uma interrupção na região secundária:

![Cenário 1. Configuração após uma interrupção na região secundária.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

As principais **vantagens** desse padrão de design são:

* O mesmo aplicativo Web é implantado em ambas as regiões sem nenhuma configuração específica de região e não requer lógica adicional para gerenciar o failover.
* O desempenho do aplicativo não é afetado pelo failover, pois o aplicativo Web e o banco de dados estão sempre colocalizados.

A principal **desvantagem** é que os recursos do aplicativo na região B são subutilizados na maior parte do tempo.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Cenário 2: Regiões do Azure para continuidade dos negócios com preservação máxima de dados

Essa opção é mais adequada para aplicativos com as seguintes características:

* Qualquer perda de dados é de alto risco para os negócios. O failover de banco de dados só poderá ser usado como último recurso se a interrupção for causada por uma falha catastrófica.
* O aplicativo dá suporte aos modos somente leitura e leitura/gravação de operações e pode operar em "modo somente leitura" por um período de tempo.

Nesse padrão, o aplicativo alterna para o modo somente leitura quando as conexões de leitura/gravação começam a obter erros de tempo limite. O aplicativo Web é implantado em ambas as regiões e inclui uma conexão com o ponto de extremidade do ouvinte de leitura/gravação e uma conexão diferente com o ponto de extremidade de ouvinte somente leitura (1). O perfil do Gerenciador de tráfego deve usar o [Roteamento de prioridade](../traffic-manager/traffic-manager-configure-priority-routing-method.md). O [monitoramento do ponto de extremidade](../traffic-manager/traffic-manager-monitoring.md) deve ser habilitado para o ponto de extremidade do aplicativo em cada região (2).

O diagrama a seguir ilustra essa configuração antes de uma interrupção:

![Cenário 2. Configuração antes da interrupção.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Quando o Gerenciador de tráfego detecta uma falha de conectividade na região A, ele alterna automaticamente o tráfego do usuário para a instância do aplicativo na região B. Com esse padrão, é importante que você defina o período de carência com perda de dados com um valor suficientemente alto, por exemplo, 24 horas. Isso garante que a perda de dados seja evitada se a interrupção for atenuada dentro desse período. Quando o aplicativo Web na região B é ativado, as operações de leitura/gravação começam a falhar. Nesse ponto, ele deve mudar para o modo somente leitura (1). Nesse modo, as solicitações são roteadas automaticamente para o banco de dados secundário. Se a interrupção for causada por uma falha catastrófica, provavelmente ela não poderá ser atenuada dentro do período de carência. Quando ele expira, o grupo de failover dispara o failover. Depois disso, o ouvinte de leitura/gravação fica disponível e as conexões com ele param de falhar (2). O diagrama a seguir ilustra os dois estágios do processo de recuperação.

> [!NOTE]
> Se a interrupção na região primária for atenuada dentro do período de carência, o Gerenciador de tráfego detectará a restauração da conectividade na região primária e alternará o tráfego do usuário para a instância do aplicativo na região A. Essa instância do aplicativo é retomada e opera no modo de leitura/gravação usando o banco de dados primário na região A, conforme ilustrado pelo diagrama anterior.

![Cenário 2. Estágios de recuperação de desastre.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Se ocorrer uma interrupção na região B, o Gerenciador de tráfego detectará a falha do ponto de extremidade Web-App-2 na região B e a marca degradada (1). Enquanto isso, o grupo de failover alterna o ouvinte somente leitura para a região A (2). Essa interrupção não afeta a experiência do usuário final, mas o banco de dados primário é exposto durante a interrupção. O diagrama a seguir ilustra uma falha na região secundária:

![Cenário 2. Interrupção da região secundária.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Depois que a interrupção for atenuada, o banco de dados secundário será imediatamente sincronizado com o primário e o ouvinte somente leitura será revertido para o banco de dados secundário na região B. Durante a sincronização, o desempenho do primário pode ser ligeiramente afetado dependendo da quantidade de dados que precisam ser sincronizados.

Esse padrão de design tem várias **vantagens**:

* Isso evita a perda de dados durante as interrupções temporárias.
* O tempo de inatividade depende apenas da rapidez com que o Gerenciador de tráfego detecta a falha de conectividade, que é configurável.

A **desvantagem** é que o aplicativo deve ser capaz de operar no modo somente leitura.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Cenário 3: Realocação de aplicativos para uma geografia diferente sem perda de dados e tempo de inatividade quase zero

Nesse cenário, o aplicativo tem as seguintes características:

* Os usuários finais acessam o aplicativo de geografias diferentes
* O aplicativo inclui cargas de trabalho somente leitura que não dependem da sincronização completa com as atualizações mais recentes
* O acesso de gravação aos dados deve ter suporte na mesma geografia para a maioria dos usuários
* A latência de leitura é crítica para a experiência do usuário final

Para atender a esses requisitos, você precisa garantir que o dispositivo de usuário **sempre** se conecte ao aplicativo implantado na mesma geografia para as operações somente leitura, como procurar dados, análises, etc. Enquanto isso, as operações de OLTP são processadas na mesma geografia na **maior parte do tempo**. Por exemplo, durante o dia, as operações OLTP são processadas na mesma geografia, mas, durante as horas de folga, elas poderiam ser processadas em uma geografia diferente. Se a atividade do usuário final ocorrer principalmente durante o horário de trabalho, você poderá garantir o desempenho ideal para a maioria dos usuários na maior parte do tempo. O diagrama a seguir mostra essa topologia.

Os recursos do aplicativo devem ser implantados em cada geografia em que você tem demanda de uso substancial. Por exemplo, se seu aplicativo for usado ativamente no Estados Unidos, União Europeia e Sul Ásia Oriental o aplicativo deverá ser implantado em todas essas regiões geográficas. O banco de dados primário deve ser alternado dinamicamente de uma geografia para a próxima no final do horário de trabalho. Esse método é chamado de "Siga o sol". A carga de trabalho OLTP sempre se conecta ao banco de dados por meio do ouvinte  **&lt;de leitura&gt;/gravação failover-Group-Name. Database.Windows.net** (1). A carga de trabalho somente leitura se conecta diretamente ao banco de dados local usando o servidor de pontos de extremidade  **&lt;Server-Name&gt;. Database.Windows.net** (2). O Gerenciador de tráfego é configurado com o [método de roteamento de desempenho](../traffic-manager/traffic-manager-configure-performance-routing-method.md). Ele garante que o dispositivo do usuário final esteja conectado ao serviço Web na região mais próxima. O Gerenciador de tráfego deve ser configurado com o monitoramento de ponto de extremidade habilitado para cada ponto de extremidade do serviço Web (3).

> [!NOTE]
> A configuração do grupo de failover define qual região é usada para failover. Como o novo primário está em uma geografia diferente, o failover resulta em latência mais longa para cargas de trabalho OLTP e somente leitura até que a região afetada fique online novamente.

![Cenário 3. Configuração com primário no leste dos EUA.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

No final do dia (por exemplo, em horário local 23h), os bancos de dados ativos devem ser alternados para a próxima região (Europa Setentrional). Essa tarefa pode ser totalmente automatizada usando o [serviço de agendamento do Azure](../scheduler/scheduler-intro.md).  A tarefa envolve as seguintes etapas:

* Alterne o servidor primário no grupo de failover para Europa Setentrional usando o failover amigável (1)
* Remover o grupo de failover entre Leste dos EUA e Europa Setentrional
* Crie um novo grupo de failover com o mesmo nome, mas entre Europa Setentrional e Ásia Oriental (2).
* Adicione o primário em Europa Setentrional e secundário em Ásia Oriental a esse grupo de failover (3).

O diagrama a seguir ilustra a nova configuração após o failover planejado:

![Cenário 3. Transição do primário para o Europa Setentrional.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Se ocorrer uma interrupção em Europa Setentrional por exemplo, o failover automático do banco de dados será iniciado pelo grupo de failover, o que efetivamente resultará na movimentação do aplicativo para a próxima região à frente da agenda (1).  Nesse caso, o leste dos EUA é a única região secundária restante até que Europa Setentrional esteja online novamente. As duas regiões restantes atendem aos clientes em todas as três geografias alternando funções. O Agendador do Azure precisa ser ajustado de acordo. Como as regiões restantes recebem tráfego de usuário adicional da Europa, o desempenho do aplicativo é afetado não apenas pela latência adicional, mas também por um número maior de conexões de usuário final. Quando a interrupção é atenuada no Europa Setentrional, o banco de dados secundário é imediatamente sincronizado com o primário atual. O diagrama a seguir ilustra uma interrupção no Europa Setentrional:

![Cenário 3. Interrupção no Europa Setentrional.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Você pode reduzir o tempo em que a experiência do usuário final na Europa é degradada pela latência longa. Para fazer isso, você deve implantar proativamente uma cópia de aplicativo e criar os bancos de dados secundários em outra região local (Europa Ocidental) como uma substituição da instância do aplicativo offline no Europa Setentrional. Quando o último estiver online novamente, você poderá decidir se deseja continuar usando Europa Ocidental ou remover a cópia do aplicativo e voltar a usar Europa Setentrional.

Os principais **benefícios** desse design são:

* A carga de trabalho de aplicativo somente leitura acessa os dados na região do armário em todos os momentos.
* A carga de trabalho do aplicativo de leitura/gravação acessa os dados na região mais próxima durante o período da atividade mais alta em cada Geografia
* Como o aplicativo é implantado em várias regiões, ele pode sobreviver a uma perda de uma das regiões sem nenhum tempo de inatividade significativo.

Mas há algumas compensações:

* Uma interrupção regional resulta na geografia a ser afetada por latência mais longa. As cargas de trabalho de leitura/gravação e somente leitura são servidas pelo aplicativo em uma geografia diferente.
* As cargas de trabalho somente leitura devem se conectar a um ponto de extremidade diferente em cada região.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planejamento de continuidade de negócios: Escolha um design de aplicativo para recuperação de desastre na nuvem

Sua estratégia específica de recuperação de desastre na nuvem pode combinar ou estender esses padrões de design para atender melhor às necessidades do seu aplicativo.  Conforme mencionado anteriormente, a estratégia escolhida é baseada no SLA que você deseja oferecer aos seus clientes e à topologia de implantação do aplicativo. Para ajudar a orientar sua decisão, a tabela a seguir compara as opções com base no objetivo de ponto de recuperação (RPO) e no tempo de recuperação estimado (ERT).

| Padrão | RPO | ERT |
|:--- |:--- |:--- |
| Implantação ativa-passiva para recuperação de desastre com acesso ao banco de dados colocalizado |Acesso de leitura/gravação < 5 s |Tempo de detecção de falha + TTL do DNS |
| Implantação ativa-ativa para balanceamento de carga de aplicativo |Acesso de leitura/gravação < 5 s |Tempo de detecção de falha + TTL do DNS |
| Implantação ativa-passiva para preservação de dados |Acesso somente leitura < 5 s | Acesso somente leitura = 0 |
||Acesso de leitura/gravação = zero | Acesso de leitura/gravação = tempo de detecção de falha + período de carência com perda de dados |
|||

## <a name="next-steps"></a>Passos Seguintes

* Para uma visão geral e cenários de continuidade de negócios, consulte [visão geral](sql-database-business-continuity.md) da continuidade de negócios
* Para saber mais sobre a replicação geográfica ativa, consulte [replicação geográfica ativa](sql-database-active-geo-replication.md).
* Para saber mais sobre grupos de failover automático, consulte [grupos de failover automático](sql-database-auto-failover-group.md).
* Para obter informações sobre a replicação geográfica ativa com pools elásticos, consulte estratégias de recuperação de desastres do [pool elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
