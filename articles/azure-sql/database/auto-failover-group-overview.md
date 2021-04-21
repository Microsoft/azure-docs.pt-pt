---
title: Grupos de ativação pós-falha automática
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Os grupos de falha automática permitem-lhe gerir a replicação e o failover automático/coordenado de um grupo de bases de dados num servidor ou em todas as bases de dados num caso gerido.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 03/26/2021
ms.openlocfilehash: f3bc1dfcfeeb6dda110f71ed7a1c53909153cf00
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762160"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Utilize grupos de falha automática para permitir a falha transparente e coordenada de várias bases de dados
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A funcionalidade grupos de falha automática permite-lhe gerir a replicação e falha de um grupo de bases de dados num servidor ou em todas as bases de dados, numa instância gerida para outra região. É uma abstração declarativa em cima da característica [de geo-replicação ativa](active-geo-replication-overview.md) existente, projetada para simplificar a implementação e gestão de bases de dados geo-replicadas em escala. Pode iniciar o failover manualmente ou pode deleitá-lo ao serviço Azure com base numa política definida pelo utilizador. Esta última opção permite-lhe recuperar automaticamente várias bases de dados relacionadas numa região secundária após uma falha catastrófica ou outro evento não planeado que resulte em perda total ou parcial da disponibilização de SQL Database ou SQL Managed Instance na região primária. Um grupo de failover pode incluir uma ou várias bases de dados, normalmente utilizadas pela mesma aplicação. Além disso, pode utilizar as bases de dados secundárias legíveis para descarregar cargas de trabalho de consulta apenas de leitura. Como os grupos de falha automática envolvem várias bases de dados, estas bases de dados devem ser configuradas no servidor primário. Os grupos de falha automática suportam a replicação de todas as bases de dados do grupo para apenas um servidor secundário ou instância numa região diferente.

> [!NOTE]
> Se pretender vários secundários da Base de Dados Azure SQL nas mesmas regiões ou diferentes, utilize [a geo-replicação ativa](active-geo-replication-overview.md).

Quando se utiliza grupos de falha automática com política automática de failover, qualquer interrupção que tenha impacto numa ou em várias bases de dados do grupo resulta em falha automática. Tipicamente, estes são incidentes que não podem ser auto-atenuados pelas operações automáticas de alta disponibilidade incorporadas. Os exemplos de gatilhos de failover incluem um incidente causado por um anel de inquilino da Base de Dados SQL ou anel de controlo que está a ser reduzido devido a uma fuga de memória de kernel de OS em vários nós computativos, ou um incidente causado por um ou mais anéis de inquilinos ter sido abatido porque um cabo de rede errado foi cortado durante o desmantelamento de hardware de rotina.  Para mais informações, consulte [a Base de Dados SQL alta disponibilidade.](high-availability-sla.md)

Além disso, os grupos de falha automática fornecem pontos finais de leitura e leitura que permanecem inalterados durante as falhas. Quer utilize ativação manual ou automática de falha, a falha de substituição muda todas as bases de dados secundárias do grupo para as primárias. Após a conclusão da falha da base de dados, o registo DNS é automaticamente atualizado para redirecionar os pontos finais para a nova região. Para obter os dados específicos de RPO e RTO, consulte [a visão geral da continuidade do negócio.](business-continuity-high-availability-disaster-recover-hadr-overview.md)

Quando está a utilizar grupos de falha automática com política de falha automática, qualquer falha que tenha impacto nas bases de dados de um servidor ou em casos geridos resulta em falha automática. Pode gerir o grupo de falha automática utilizando:

- [Portal do Azure](geo-distributed-application-configure-tutorial.md)
- [Azure CLI: Grupo Failover](scripts/add-database-to-failover-group-cli.md)
- [PowerShell: Grupo failover](scripts/add-database-to-failover-group-powershell.md)
- [REST API: Grupo failover](/rest/api/sql/failovergroups)

Após falha, certifique-se de que os requisitos de autenticação da sua base de dados e servidor, ou exemplo, estão configurados na nova primária. Para mais informações, consulte [a segurança da Base de Dados SQL após a recuperação de desastres.](active-geo-replication-security-configure.md)

Para alcançar a continuidade real do negócio, adicionar redundância de base de dados entre datacenters é apenas uma parte da solução. A recuperação de uma aplicação (serviço) de ponta a ponta após uma falha catastrófica requer a recuperação de todos os componentes que constituem o serviço e quaisquer serviços dependentes. Exemplos destes componentes incluem o software do cliente (por exemplo, um navegador com um JavaScript personalizado), extremidades frontais web, armazenamento e DNS. É fundamental que todos os componentes sejam resistentes às mesmas falhas e estejam disponíveis dentro do objetivo de tempo de recuperação (RTO) da sua aplicação. Por isso, é necessário identificar todos os serviços dependentes e compreender as garantias e capacidades que proporcionam. Em seguida, deve tomar as medidas adequadas para garantir que o seu serviço funciona durante a falha dos serviços de que depende. Para obter mais informações sobre a conceção de soluções para a recuperação de [desastres, consulte a conceção de soluções cloud para recuperação de desastres utilizando a geo-replicação ativa.](designing-cloud-solutions-for-disaster-recovery.md)

## <a name="terminology-and-capabilities"></a>Terminologia e capacidades

- **Grupo failover (FOG)**

  Um grupo de failover é um grupo de bases de dados nomeado gerido por um único servidor ou dentro de um caso gerido que pode falhar como uma unidade para outra região no caso de todas ou algumas bases de dados primárias ficarem indisponíveis devido a uma falha na região primária. Quando é criado para o SQL Managed Instance, um grupo de failover contém todas as bases de dados dos utilizadores no caso e, portanto, apenas um grupo de failover pode ser configurado em uma instância.
  
  > [!IMPORTANT]
  > O nome do grupo de failover deve ser globalmente único dentro do `.database.windows.net` domínio.

- **Servidores**

     Com servidores, algumas ou todas as bases de dados dos utilizadores num servidor podem ser colocadas num grupo de falha. Além disso, um servidor suporta vários grupos de falha num único servidor.

- **Principal**

  O servidor ou caso gerido que hospeda as bases de dados primárias no grupo de failover.

- **Secundária**

  O servidor ou instância gerida que hospeda as bases de dados secundárias no grupo de failover. O secundário não pode estar na mesma região que as primárias.

- **Adicionar bases de dados únicas ao grupo failover**

  Pode colocar várias bases de dados individuais no mesmo servidor no mesmo grupo de falha. Se adicionar uma única base de dados ao grupo de failover, cria automaticamente uma base de dados secundária utilizando a mesma edição e tamanho de cálculo no servidor secundário.  Especificou o servidor quando o grupo de failover foi criado. Se adicionar uma base de dados que já tenha uma base de dados secundária no servidor secundário, essa ligação de geo-replicação é herdada pelo grupo. Quando adiciona uma base de dados que já tem uma base de dados secundária num servidor que não faz parte do grupo de failover, um novo secundário é criado no servidor secundário.

  > [!IMPORTANT]
  > Certifique-se de que o servidor secundário não tem uma base de dados com o mesmo nome, a menos que seja uma base de dados secundária existente. Em grupos de failover para SQL Managed Instance, todas as bases de dados dos utilizadores são replicadas. Não é possível escolher um subconjunto de bases de dados de utilizadores para replicação no grupo de failover.

- **Adicionar bases de dados em piscina elástica para grupo failover**

  Pode colocar todas ou várias bases de dados dentro de uma piscina elástica no mesmo grupo de failover. Se a base de dados primária estiver numa piscina elástica, a secundária é automaticamente criada na piscina elástica com o mesmo nome (piscina secundária). Deve certificar-se de que o servidor secundário contém uma piscina elástica com o mesmo nome exato e capacidade livre suficiente para hospedar as bases de dados secundárias que serão criadas pelo grupo failover. Se adicionar uma base de dados na piscina que já tem uma base de dados secundária na piscina secundária, essa ligação de geo-replicação é herdada pelo grupo. Quando se adiciona uma base de dados que já tem uma base de dados secundária num servidor que não faz parte do grupo de failover, é criado um novo secundário no pool secundário.
  
- **Sementeira inicial**

  Ao adicionar bases de dados, piscinas elásticas ou casos geridos a um grupo de failover, existe uma fase inicial de sementeira antes do início da replicação de dados. A fase inicial de sementeira é a operação mais longa e cara. Uma vez concluída a sementeira inicial, os dados são sincronizados e apenas as alterações de dados subsequentes são replicadas. O tempo que a semente inicial leva a completar depende do tamanho dos seus dados, do número de bases de dados replicadas e da velocidade da ligação entre as entidades do grupo de failover. Em circunstâncias normais, a velocidade de sementeira possível é de até 500 GB por hora para a Base de Dados SQL, e até 360 GB por hora para SQL Managed Instance. A sementeira é realizada para todas as bases de dados em paralelo.

  Para a SQL Managed Instance, considere a velocidade da ligação da Rota Expressa entre as duas instâncias ao estimar o tempo da fase inicial de sementeira. Se a velocidade da ligação entre as duas instâncias for mais lenta do que o necessário, é provável que o tempo de semente seja notavelmente afetado. Pode utilizar a velocidade de sementeira declarada, o número de bases de dados, o tamanho total dos dados e a velocidade da ligação para estimar quanto tempo demorará a fase inicial de sementeira antes do início da replicação dos dados. Por exemplo, para uma única base de dados de 100 GB, a fase inicial das sementes demoraria cerca de 1,2 horas se o link fosse capaz de empurrar 84 GB por hora, e se não existissem outras bases de dados semeadas. Se o link só puder transferir 10 GB por hora, então a sementeira de uma base de dados de 100 GB demorará cerca de 10 horas. Se existirem múltiplas bases de dados para replicar, a sementeira será executada em paralelo e, quando combinada com uma velocidade de ligação lenta, a fase inicial de sementeira pode demorar consideravelmente mais tempo, especialmente se a sementeira paralela de dados de todas as bases de dados exceder a largura de banda de ligação disponível. Se a largura de banda da rede entre duas instâncias for limitada e estiver a adicionar múltiplas instâncias geridas a um grupo de failover, considere adicionar várias instâncias geridas ao grupo de failover sequencialmente, uma a uma. Dado um gateway de tamanho adequado SKU entre as duas instâncias geridas, e se a largura de banda da rede corporativa permitir, é possível atingir velocidades até 360 GB por hora.  

- **Zona DNS**

  Um ID único que é gerado automaticamente quando um novo SQL Managed Instance é criado. Um certificado de vários domínios (SAN) para este caso é a provisionado para autenticar as ligações do cliente a qualquer instância na mesma zona de DNS. Os dois casos geridos no mesmo grupo de insuitários devem partilhar a zona de DNS.
  
  > [!NOTE]
  > Não é necessário um ID de zona DE DNS para grupos de failover criados para a Base de Dados SQL.

- **Ouvinte de leitura de grupo failover**

  Um registo DNS CNAME que aponta para o URL primário atual. É criado automaticamente quando o grupo de failover é criado e permite que a carga de trabalho de leitura-escrita reconecte-se transparentemente à base de dados primária quando o primário muda após o failover. Quando o grupo de failover é criado num servidor, o registo DNS CNAME para o URL do ouvinte é formado como `<fog-name>.database.windows.net` . Quando o grupo de failover é criado numa SQL Managed Instance, o registo DNS CNAME para o URL do ouvinte é formado como `<fog-name>.<zone_id>.database.windows.net` .

- **Ouvinte de leitura de grupo de failover**

  Formou-se um registo DONS CNAME que aponta para o ouvinte apenas de leitura que aponta para o URL do secundário. É criado automaticamente quando o grupo de failover é criado e permite que a carga de trabalho SQL apenas de leitura se conecte transparentemente ao secundário usando as regras de equilíbrio de carga especificadas. Quando o grupo de failover é criado num servidor, o registo DNS CNAME para o URL do ouvinte é formado como `<fog-name>.secondary.database.windows.net` . Quando o grupo de failover é criado numa SQL Managed Instance, o registo DNS CNAME para o URL do ouvinte é formado como `<fog-name>.secondary.<zone_id>.database.windows.net` .

- **Política automática de failover**

  Por predefinição, um grupo de incumprimento é configurado com uma política automática de incumprimento. O Azure desencadeia o failover após a falha ser detetada e o período de carência expirou. O sistema deve verificar se a paralisação não pode ser atenuada pela [infraestrutura](high-availability-sla.md) de alta disponibilidade incorporada devido à dimensão do impacto. Se pretender controlar o fluxo de trabalho de falha da aplicação ou manualmente, pode desligar a falha automática.
  
  > [!NOTE]
  > Uma vez que a verificação da escala da paralisação e a rapidez com que pode ser atenuada envolve ações humanas pela equipa de operações, o período de carência não pode ser definido abaixo de uma hora. Esta limitação aplica-se a todas as bases de dados do grupo de failover, independentemente do seu estado de sincronização de dados.

- **Política de failover só de leitura**

  Por predefinição, a falha do ouvinte apenas de leitura é desativada. Garante que o desempenho do primário não é impactado quando o secundário está offline. No entanto, também significa que as sessões só de leitura não poderão ser ligadas até que o secundário seja recuperado. Se não conseguir tolerar tempo de inatividade para as sessões de leitura e puder utilizar o principal para tráfego apenas de leitura e leitura em detrimento da potencial degradação do desempenho da primária, pode permitir o failover para o ouvinte apenas de leitura configurando a `AllowReadOnlyFailoverToPrimary` propriedade. Nesse caso, o tráfego só de leitura será automaticamente redirecionado para o primário se o secundário não estiver disponível.

  > [!NOTE]
  > A `AllowReadOnlyFailoverToPrimary` propriedade só tem efeito se a política de failover automática estiver ativada e uma falha automática foi desencadeada pela Azure. Nesse caso, se a propriedade estiver definida para True, a nova primária servirá sessões de leitura e leitura.

- **Falha planeada**

   O failover planeado realiza uma sincronização completa entre bases de dados primárias e secundárias antes que o secundário mude para o papel primário. Isto garante que não há perda de dados. O failover previsto é utilizado nos seguintes cenários:

  - Realizar exercícios de recuperação de desastres (DR) na produção quando a perda de dados não for aceitável
  - Realojar as bases de dados para uma região diferente
  - Devolva as bases de dados à região primária depois de a paralisação ter sido atenuada (falha)

- **Ativação pós-falha não planeada**

   O fracasso não planeado ou forçado muda imediatamente o secundário para o papel primário sem qualquer sincronização com o primário. Esta operação resultará na perda de dados. O failover não planeado é utilizado como um método de recuperação durante as interrupções quando o primário não está acessível. Quando o primário original estiver novamente on-line, ele reconecta-se automaticamente sem sincronização e tornar-se-á um novo secundário.

- **Falha manual**

  Pode iniciar a falha manualmente a qualquer momento, independentemente da configuração automática de falha. Se a política de falha automática não for configurada, é necessário um failover manual para recuperar as bases de dados do grupo de failover para o secundário. Pode iniciar uma falha forçada ou amigável (com sincronização completa de dados). Este último poderia ser usado para deslocalizar as primárias para a região secundária. Quando o failover estiver concluído, os registos DNS são automaticamente atualizados para garantir a conectividade com o novo primário.

- **Período de graça com perda de dados**

  Como as bases de dados primárias e secundárias são sincronizadas usando a replicação assíncronea, a falha pode resultar na perda de dados. Pode personalizar a política de failover automática para refletir a tolerância da sua aplicação à perda de dados. Ao `GracePeriodWithDataLossHours` configurar, é possível controlar quanto tempo o sistema aguarda antes de iniciar a falha que é suscetível de resultar na perda de dados.

- **Vários grupos de failover**

  Pode configurar vários grupos de failover para o mesmo par de servidores para controlar o âmbito das falhas. Cada grupo falha de forma independente. Se a sua aplicação multi-arrendatário utilizar piscinas elásticas, pode usar esta capacidade para misturar bases de dados primárias e secundárias em cada piscina. Desta forma, pode reduzir o impacto de uma paralisação para apenas metade dos inquilinos.

  > [!NOTE]
  > A SQL Managed Instance não suporta vários grupos de failover.
  
## <a name="permissions"></a>Permissões

As permissões para um grupo de failover são geridas através do [controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md). A [função sql Server Contributor](../../role-based-access-control/built-in-roles.md#sql-server-contributor) tem todas as permissões necessárias para gerir grupos de failover.

### <a name="create-failover-group"></a>Criar grupo de failover

Para criar um grupo de failover, você precisa do Azure RBAC escrever acesso aos servidores primário e secundário, e a todas as bases de dados do grupo failover. Para uma sql Managed Instance, você precisa do Azure RBAC escrever acesso à primeira e secundária SQL Managed Instance, mas as permissões em bases de dados individuais não são relevantes, porque bases de dados individuais de SqL Managed Instance não podem ser adicionadas ou removidas de um grupo de failover.

### <a name="update-a-failover-group"></a>Atualizar um grupo de failover

Para atualizar um grupo de failover, precisa do Azure RBAC escrever acesso ao grupo de failover, e todas as bases de dados no servidor primário atual ou no caso gerido.  

### <a name="fail-over-a-failover-group"></a>Falha sobre um grupo de falhanços

Para falhar sobre um grupo de failover, você precisa que o Azure RBAC escreva acesso ao grupo de failover no novo servidor primário ou instância gerida.

## <a name="best-practices-for-sql-database"></a>Melhores práticas para base de dados SQL

O grupo de falha automática deve ser configurado no servidor primário e conectá-lo ao servidor secundário numa região de Azure diferente. Os grupos podem incluir todas ou algumas bases de dados nestes servidores. O diagrama seguinte ilustra uma configuração típica de uma aplicação de nuvem geo-redundante utilizando várias bases de dados e grupo de falha automática.

![O diagrama mostra uma configuração típica de uma aplicação de nuvem geo-redundante utilizando várias bases de dados e grupo de falha automática.](./media/auto-failover-group-overview/auto-failover-group.png)

> [!NOTE]
> Consulte [a Add SQL Database a um grupo de failover](failover-group-add-single-database-tutorial.md) para um tutorial detalhado passo a passo adicionando uma base de dados na Base de Dados SQL a um grupo de failover.

Ao conceber um serviço com continuidade de negócios em mente, siga estas orientações gerais:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Utilizando um ou vários grupos de failover para gerir o failover de várias bases de dados

Um ou muitos grupos de failover podem ser criados entre dois servidores em diferentes regiões (servidores primários e secundários). Cada grupo pode incluir uma ou várias bases de dados que são recuperadas como uma unidade no caso de todas ou algumas bases de dados primárias ficarem indisponíveis devido a uma falha na região primária. O grupo de failover cria uma base de dados geocondusídua com o mesmo objetivo de serviço que o principal. Se adicionar uma relação de geo-replicação existente ao grupo failover, certifique-se de que o geo-secundário está configurado com o mesmo nível de serviço e tamanho de cálculo que o principal.
  
> [!IMPORTANT]
> A criação de grupos de failover entre dois servidores em diferentes subscrições não é atualmente suportada para a Base de Dados Azure SQL. Se mover o servidor primário ou secundário para uma subscrição diferente após a criação do grupo de failover, poderá resultar em falhas nos pedidos de incumprimento e noutras operações.

### <a name="using-read-write-listener-for-oltp-workload"></a>Utilização de ouvintes de leitura-escrita para carga de trabalho OLTP

Ao executar operações OLTP, utilize `<fog-name>.database.windows.net` como URL do servidor e as ligações são automaticamente direcionadas para o principal. Esta URL não muda após o failover. Note que a falha envolve a atualização do registo dns para que as ligações do cliente sejam redirecionadas para a nova primária apenas após a atualização da cache DNS do cliente.

### <a name="using-read-only-listener-for-read-only-workload"></a>Usando o ouvinte apenas para a carga de trabalho apenas de leitura

Se tiver uma carga de trabalho apenas de leitura logicamente isolada que seja tolerante a determinadas condições de dados, pode utilizar a base de dados secundária na aplicação. Para sessões de leitura, utilize `<fog-name>.secondary.database.windows.net` como URL do servidor e a ligação é automaticamente direcionada para o secundário. Recomenda-se também que indique a intenção de leitura na cadeia de ligação utilizando `ApplicationIntent=ReadOnly` .

> [!NOTE]
> Nos níveis de serviço Premium, Business Critical e Hyperscale, a SQL Database suporta a utilização de [réplicas apenas de leitura](read-scale-out.md) para descarregar cargas de trabalho de consulta apenas de leitura, utilizando o `ApplicationIntent=ReadOnly` parâmetro na cadeia de ligação. Quando tiver configurado uma base de dados secundária georreplicada, poderá utilizar esta capacidade para se ligar a uma réplica só de leitura na localização primária ou na localização georreplicada.
>
> - Para ligar a uma réplica apenas de leitura no local primário, utilize `ApplicationIntent=ReadOnly` e `<fog-name>.database.windows.net` .
> - Para ligar a uma réplica apenas de leitura no local secundário, utilize `ApplicationIntent=ReadOnly` e `<fog-name>.secondary.database.windows.net` .

### <a name="preparing-for-performance-degradation"></a>Preparação para a degradação do desempenho

Uma aplicação típica do Azure utiliza vários serviços Azure e consiste em múltiplos componentes. O failover automatizado do grupo de failover é desencadeado com base apenas no estado dos componentes Azure SQL. Outros serviços Azure na região primária podem não ser afetados pela paralisação e os seus componentes podem ainda estar disponíveis nessa região. Uma vez que as bases de dados primárias mudam para a região DR, a latência entre os componentes dependentes pode aumentar. Para evitar o impacto de uma maior latência no desempenho da aplicação, garantir o despedimento de todos os componentes da aplicação na região DR e seguir estas [orientações de segurança da rede](#failover-groups-and-network-security).

### <a name="preparing-for-data-loss"></a>Preparação para a perda de dados

Se for detetada uma paragem, o Azure aguarda o período por que especificou `GracePeriodWithDataLossHours` . O valor predefinido é de 1 hora. Se não puder pagar a perda de dados, certifique-se de que se define `GracePeriodWithDataLossHours` para um número suficientemente grande, como 24 horas. Utilize o grupo manual falhando de volta do secundário para o primário.

> [!IMPORTANT]
> Piscinas elásticas com 800 ou menos DTUs e mais de 250 bases de dados utilizando a geo-replicação podem encontrar problemas, incluindo falhas planeadas mais longas e desempenho degradado.  Estas questões são mais propensas a ocorrer para escrever cargas de trabalho intensivas, quando os pontos finais de geo-replicação são amplamente separados pela geografia, ou quando vários pontos finais secundários são usados para cada base de dados.  Os sintomas destas questões são indicados quando o atraso da geo-replicação aumenta com o tempo.  Este lag pode ser monitorizado utilizando [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Se estas questões ocorrerem, então as mitigações incluem o aumento do número de DTUs em piscina, ou a redução do número de bases de dados geo-replicadas na mesma piscina.

### <a name="changing-secondary-region-of-the-failover-group"></a>Alteração da região secundária do grupo de failover

Para ilustrar a sequência de alteração, assumiremos que o servidor A é o servidor primário, o servidor B é o servidor secundário existente, e o servidor C é o novo secundário na terceira região.  Para fazer a transição, siga estes passos:

1. Criar secundários adicionais de cada base de dados no servidor A para o servidor C utilizando [a geo-replicação ativa](active-geo-replication-overview.md). Cada base de dados no servidor A terá dois secundários, um no servidor B e outro no servidor C. Isto garantirá que as bases de dados primárias permaneçam protegidas durante a transição.
2. Elimine o grupo de failover. Neste momento, os logins vão falhar. Isto porque os pseudónimos SQL para os ouvintes do grupo de failover foram eliminados e o gateway não reconhecerá o nome do grupo failover.
3. Recossar o grupo de failover com o mesmo nome entre os servidores A e C. Neste momento, os logins deixarão de falhar.
4. Adicione todas as bases de dados primárias no servidor A ao novo grupo de failover.
5. Deixe cair o servidor B. Todas as bases de dados em B serão eliminadas automaticamente.

### <a name="changing-primary-region-of-the-failover-group"></a>Alteração da região primária do grupo de failover

Para ilustrar a sequência de alterações, assumiremos que o servidor A é o servidor primário, o servidor B é o servidor secundário existente, e o servidor C é o novo primário na terceira região.  Para fazer a transição, siga estes passos:

1. Executar uma falha planeada para mudar o servidor primário para B. O Servidor A tornar-se-á o novo servidor secundário. O failover pode resultar em vários minutos de tempo de paragem. O tempo real dependerá do tamanho do grupo de falhanços.
2. Criar secundários adicionais de cada base de dados no servidor B para o servidor C utilizando [a geo-replicação ativa](active-geo-replication-overview.md). Cada base de dados no servidor B terá dois secundários, um no servidor A e outro no servidor C. Isto garantirá que as bases de dados primárias permaneçam protegidas durante a transição.
3. Elimine o grupo de failover. Neste momento, os logins vão falhar. Isto porque os pseudónimos SQL para os ouvintes do grupo de failover foram eliminados e o gateway não reconhecerá o nome do grupo failover.
4. Recossar o grupo de failover com o mesmo nome entre os servidores B e C. Neste momento, os logins deixarão de falhar.
5. Adicione todas as bases de dados primárias em B ao novo grupo de failover.
6. Execute uma falha planeada do grupo de failover para mudar B e C. Agora o servidor C tornar-se-á o principal e o B - o secundário. Todas as bases de dados secundárias do servidor A serão automaticamente ligadas às primárias em C. Tal como no passo 1, a falha pode resultar em vários minutos de tempo de paragem.
7. Largue o servidor A. Todas as bases de dados em A serão eliminadas automaticamente.

> [!IMPORTANT]
> Quando o grupo de failover é eliminado, os registos DNS para os pontos finais do ouvinte também são eliminados. Nessa altura, existe uma probabilidade não-zero de alguém criar um pseudónimo de grupo de failover ou servidor com o mesmo nome, o que o impedirá de o voltar a usar. Para minimizar o risco, não utilize nomes genéricos de grupos falhados.

## <a name="best-practices-for-sql-managed-instance"></a>Melhores práticas para sql Gestão de Instâncias

O grupo de ativação pós-falha automática tem de ser configurado na instância primária e será ligado à instância secundária numa região do Azure diferente.  Todas as bases de dados na instância serão replicadas para a instância secundária.

O diagrama seguinte ilustra uma configuração típica de uma aplicação de nuvem geo-redundante utilizando instâncias geridas e grupo de falha automática.

![diagrama de falha automática](./media/auto-failover-group-overview/auto-failover-group-mi.png)

> [!NOTE]
> Consulte [a instância gerida add a um grupo de failover](../managed-instance/failover-group-add-instance-tutorial.md) para um tutorial detalhado passo a passo adicionando um SQL Managed Instance para usar o grupo failover.

Se a sua aplicação utilizar a SQL Managed Instance como o nível de dados, siga estas orientações gerais ao conceber a continuidade do negócio:

### <a name="creating-the-secondary-instance"></a>Criando a instância secundária

Para garantir a conectividade não interrompida à primeira sql Gestd Instance após o fracasso, tanto as instâncias primárias como secundárias devem estar na mesma zona de DNS. Garantirá que o mesmo certificado de vários domínios (SAN) possa ser utilizado para autenticar as ligações do cliente a qualquer uma das duas instâncias do grupo de failover. Quando a sua aplicação estiver pronta para implantação de produção, crie uma instância gerida de SQL secundária numa região diferente e certifique-se de que partilha a zona de DNS com a primeira SQL Managed Instance. Pode fazê-lo especificando o parâmetro opcional durante a criação. Se estiver a utilizar o PowerShell ou a API REST, o nome do parâmetro opcional é `DNS Zone Partner` , e o nome do campo opcional correspondente no portal Azure é Primeira Instância Gerida.

> [!IMPORTANT]
> A primeira instância gerida criada na sub-rede determina a zona de DNS para todas as instâncias subsequentes na mesma sub-rede. Isto significa que duas instâncias da mesma sub-rede não podem pertencer a diferentes zonas dns.

Para obter mais informações sobre a criação da instância gerida secundária do SQL na mesma zona de DNS que a instância primária, consulte [Criar um caso secundário gerido](../managed-instance/failover-group-add-instance-tutorial.md#create-a-secondary-managed-instance).

### <a name="using-geo-paired-regions"></a>Utilizando regiões geo emparelhadas

Implementar ambas as instâncias geridas em [regiões emparelhadas](../../best-practices-availability-paired-regions.md) por razões de desempenho. Os casos geridos que residem em regiões geo-emparelhadas têm um desempenho muito melhor em comparação com as regiões não remuneradas. 

### <a name="enabling-replication-traffic-between-two-instances"></a>Permitir o tráfego de replicação entre duas instâncias

Como cada instância está isolada no seu próprio VNet, deve ser permitido tráfego de duas direções entre estes VNets. Ver [gateway Azure VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Criar um grupo de ativação pós-falha entre instâncias geridas em subscrições diferentes

Pode criar um grupo de failover entre as Instâncias Geridas SQL em duas subscrições diferentes, desde que as subscrições estejam associadas ao mesmo [Azure Ative Directory Tenant](../../active-directory/fundamentals/active-directory-whatis.md#terminology). Ao utilizar a PowerShell API, pode fazê-lo especificando o `PartnerSubscriptionId` parâmetro para a instância gerida do SQL secundário. Ao utilizar a API REST, cada ID de instância incluído no parâmetro pode ter a `properties.managedInstancePairs` sua própria subscriçãoID.
  
> [!IMPORTANT]
> O portal Azure não suporta a criação de grupos de failover em diferentes subscrições. Além disso, para os grupos de failover existentes em diferentes subscrições e/ou grupos de recursos, a falha não pode ser iniciada manualmente através do portal a partir da primeira sql Managed Instance. Em vez disso, inicie-o na instância de georreplicação secundária.

### <a name="managing-failover-to-secondary-instance"></a>Gerir a ativação pós-falha para a instância secundária

O grupo de ativação pós-falha vai gerir a ativação pós-falha de todas as bases de dados no SQL Managed Instance. Quando um grupo é criado, cada base de dados na instância será replicado geograficamente de forma automática para o SQL Managed Instance secundário. Não pode utilizar grupos de ativação pós-falha para iniciar uma ativação pós-falha parcial de um subconjunto de bases de dados.

> [!IMPORTANT]
> Se uma base de dados for removida da primeira sql Gestd Instance, também será eliminada automaticamente na instância gerida geo-secundária do SQL.

### <a name="using-read-write-listener-for-oltp-workload"></a>Utilização de ouvintes de leitura-escrita para carga de trabalho OLTP

Ao executar operações OLTP, utilize `<fog-name>.zone_id.database.windows.net` como URL do servidor e as ligações são automaticamente direcionadas para o principal. Esta URL não muda após o failover. A falha envolve a atualização do registo dns, pelo que as ligações do cliente são redirecionadas para a nova primária apenas após a atualização da cache DNS do cliente. Como a instância secundária partilha a zona de DNS com a primária, a aplicação do cliente poderá reconectar-se com ela usando o mesmo certificado SAN.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Usando o ouvinte apenas para ligar à instância secundária

Se tiver uma carga de trabalho apenas de leitura logicamente isolada que seja tolerante a determinadas condições de dados, pode utilizar a base de dados secundária na aplicação. Para ligar diretamente ao secundário geo-replicado, utilize `<fog-name>.secondary.<zone_id>.database.windows.net` como URL do servidor e a ligação é feita diretamente ao secundário geo-replicado.

> [!NOTE]
> No nível Business Critical, a SQL Managed Instance suporta o uso de [réplicas apenas de leitura](read-scale-out.md) para descarregar cargas de trabalho de consulta apenas de leitura, utilizando o `ApplicationIntent=ReadOnly` parâmetro na cadeia de ligação. Quando tiver configurado uma base de dados secundária georreplicada, poderá utilizar esta capacidade para se ligar a uma réplica só de leitura na localização primária ou na localização georreplicada.
>
> - Para ligar a uma réplica apenas de leitura no local primário, utilize `ApplicationIntent=ReadOnly` e `<fog-name>.<zone_id>.database.windows.net` .
> - Para ligar a uma réplica apenas de leitura no local secundário, utilize `ApplicationIntent=ReadOnly` e `<fog-name>.secondary.<zone_id>.database.windows.net` .

### <a name="preparing-for-performance-degradation"></a>Preparação para a degradação do desempenho

Uma aplicação típica do Azure utiliza vários serviços Azure e consiste em múltiplos componentes. O failover automatizado do grupo de failover é desencadeado com base apenas no estado dos componentes Azure SQL. Outros serviços Azure na região primária podem não ser afetados pela paralisação e os seus componentes podem ainda estar disponíveis nessa região. Uma vez que as bases de dados primárias mudam para a região secundária, a latência entre os componentes dependentes pode aumentar. Para evitar o impacto de uma maior latência no desempenho da aplicação, garantir o despedimento de todos os componentes da aplicação na região secundária e falhar sobre os componentes da aplicação juntamente com a base de dados. No momento da configuração, siga [as diretrizes de segurança](#failover-groups-and-network-security) da rede para garantir a conectividade à base de dados na região secundária.

### <a name="preparing-for-data-loss"></a>Preparação para a perda de dados

Se for detetada uma falha de leitura, uma falha de leitura é desencadeada se não houver perda de dados, o melhor que sabemos. Caso contrário, o failover é adiado pelo período especificado através do seu uso `GracePeriodWithDataLossHours` . Se `GracePeriodWithDataLossHours` especificar, esteja preparado para a perda de dados. Em geral, durante as paragens, o Azure favorece a disponibilidade. Se não puder pagar a perda de dados, certifique-se de que define o GracePeriodWithDataLossHours para um número suficientemente grande, como 24 horas, ou desative a falha automática.

A atualização do DNS do ouvinte de leitura-escrita ocorrerá imediatamente após o início do failover. Esta operação não resultará na perda de dados. No entanto, o processo de comutação das funções de base de dados pode demorar até 5 minutos em condições normais. Até que esteja concluída, algumas bases de dados na nova instância primária continuarão a ser apenas lidas. Se for iniciada uma falha utilizando o PowerShell, a operação para mudar a função de réplica primária é sincronizada. Se for iniciado usando o portal Azure, o UI indicará o estado de conclusão. Se for iniciado com a API REST, utilize o mecanismo de votação padrão do Gestor de Recursos Azure para monitorizar a conclusão.

> [!IMPORTANT]
> Utilize o grupo manual falha para mover as primárias de volta para o local original. Quando a paragem que causou a falha for atenuada, pode mover as suas bases de dados primárias para a localização original. Para isso, deve iniciar a falha manual do grupo.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Alteração da região secundária do grupo de failover

Vamos supor que o caso A é o caso principal, o caso B é a instância secundária existente, e o exemplo C é a nova instância secundária na terceira região.  Para fazer a transição, siga estes passos:

1. Criar o caso C com o mesmo tamanho que A e na mesma zona de DNS.
2. Eliminar o grupo de failover entre as instâncias A e B. Neste momento, os logins vão falhar porque os pseudónimos SQL para os ouvintes do grupo de failover foram eliminados e o gateway não reconhecerá o nome do grupo failover. As bases de dados secundárias serão desligadas das primárias e tornar-se-ão bases de dados de leitura- escrita.
3. Crie um grupo de failover com o mesmo nome entre os exemplos A e C. Siga as instruções em [grupo de failover com tutorial sql Managed Instance](../managed-instance/failover-group-add-instance-tutorial.md). Esta é uma operação de tamanho de dados e completará quando todas as bases de dados do exemplo A forem semeadas e sincronizadas.
4. Eliminar a instância B se não for necessário para evitar cargas desnecessárias.

> [!NOTE]
> Após o passo 2 e até que o passo 3 seja concluído, as bases de dados, no caso A, permanecerão desprotegidas de uma falha catastrófica do exemplo A.

### <a name="changing-primary-region-of-the-failover-group"></a>Alteração da região primária do grupo de failover

Vamos assumir que o exemplo A é o caso principal, o caso B é o caso secundário existente, e o exemplo C é o novo caso primário na terceira região.  Para fazer a transição, siga estes passos:

1. Criar o caso C com o mesmo tamanho que B e na mesma zona de DNS.
2. Ligue-se à instância B e falhe manualmente para mudar a instância primária para B. A instance A tornar-se-á automaticamente a nova instância secundária.
3. Eliminar o grupo de failover entre as instâncias A e B. Neste momento, os logins vão falhar porque os pseudónimos SQL para os ouvintes do grupo de failover foram eliminados e o gateway não reconhecerá o nome do grupo failover. As bases de dados secundárias serão desligadas das primárias e tornar-se-ão bases de dados de leitura- escrita.
4. Criar um grupo de failover com o mesmo nome entre os exemplos A e C. Siga as instruções do [grupo de failover com tutor de instância gerida](../managed-instance/failover-group-add-instance-tutorial.md). Esta é uma operação de tamanho de dados e completará quando todas as bases de dados do exemplo A forem semeadas e sincronizadas.
5. Eliminar o caso A se não for necessário para evitar cargas desnecessárias.

> [!CAUTION]
> Após o passo 3 e até que o passo 4 seja concluído, as bases de dados, no caso A, permanecerão desprotegidas de uma falha catastrófica do exemplo A.

> [!IMPORTANT]
> Quando o grupo de failover é eliminado, os registos DNS para os pontos finais do ouvinte também são eliminados. Nessa altura, existe uma probabilidade não-zero de alguém criar um pseudónimo de grupo de failover ou servidor com o mesmo nome, o que o impedirá de o voltar a usar. Para minimizar o risco, não utilize nomes genéricos de grupos falhados.

### <a name="enable-scenarios-dependent-on-objects-from-the-system-databases"></a>Ativar cenários dependentes de objetos das bases de dados do sistema
As bases de dados do sistema não são replicadas para a instância secundária num grupo de failover. Para ativar cenários que dependam de objetos a partir das bases de dados do sistema, no caso secundário, certifique-se de criar os mesmos objetos no secundário. Por exemplo, se pretender utilizar os mesmos logins na instância secundária, certifique-se de criá-los com o SID idêntico. 
```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
``` 


## <a name="failover-groups-and-network-security"></a>Grupos de failover e segurança da rede

Para algumas aplicações, as regras de segurança exigem que o acesso à rede ao nível dos dados seja restrito a um componente ou componentes específicos, tais como um VM, serviço web, etc. Esta exigência apresenta alguns desafios para a conceção da continuidade do negócio e para a utilização dos grupos de failover. Considere as seguintes opções ao implementar tal acesso restrito.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Utilização de grupos de failover e regras de rede virtual

Se estiver a utilizar [pontos finais e regras de serviço de Rede Virtual](vnet-service-endpoint-rule-overview.md) para restringir o acesso à sua base de dados na Base de Dados SQL ou na SQL Managed Instance, saiba que cada ponto final de serviço de rede virtual se aplica apenas a uma região de Azure. O ponto final não permite que outras regiões aceitem a comunicação a partir da sub-rede. Portanto, apenas as aplicações de clientes implementadas na mesma região podem ligar-se à base de dados primária. Uma vez que o failover resulta na reencaminhação de sessões de clientes da SQL Database para um servidor numa região diferente (secundária), estas sessões falharão se forem originadas por um cliente fora dessa região. Por essa razão, a política de failover automática não pode ser ativada se os servidores ou instâncias participantes estiverem incluídos nas regras da Rede Virtual. Para apoiar o failover manual, siga estes passos:

1. Provisão de cópias redundantes dos componentes frontais da sua aplicação (serviço web, máquinas virtuais, etc.) na região secundária
2. Configurar as [regras de rede virtual](vnet-service-endpoint-rule-overview.md) individualmente para servidor primário e secundário
3. Ativar o [failover frontal utilizando uma configuração de gestor de tráfego](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Inicie a falha manual quando a paragem for detetada. Esta opção é otimizada para as aplicações que requerem latência consistente entre a extremidade frontal e o nível de dados e suporta a recuperação quando ambos os lados da frente, nível de dados ou ambos são impactados pela paralisação.

> [!NOTE]
> Se estiver a utilizar o **ouvinte apenas para** equilibrar uma carga de trabalho apenas de leitura, certifique-se de que esta carga de trabalho é executada num VM ou noutro recurso na região secundária para que possa ligar-se à base de dados secundária.

### <a name="use-failover-groups-and-firewall-rules"></a>Utilize grupos de failover e regras de firewall

Se o seu plano de continuidade de negócios necessitar de falha usando grupos com falha automática, pode restringir o acesso à sua base de dados na Base de Dados SQL utilizando as regras tradicionais de firewall. Para apoiar o failover automático, siga estes passos:

1. [Criar um IP público](../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Crie um equilibrador de carga pública](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) e atribua-lhe o IP público.
3. [Crie uma rede virtual e as máquinas virtuais](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) para os seus componentes frontais
4. [Crie um grupo de segurança de rede](../../virtual-network/network-security-groups-overview.md) e configuure as ligações de entrada.
5. Certifique-se de que as ligações de saída estão abertas à Base de Dados Azure SQL utilizando [a etiqueta de serviço](../../virtual-network/network-security-groups-overview.md#service-tags)'Sql'.
6. Crie uma [regra de firewall SQL Database](firewall-configure.md) para permitir o tráfego de entrada a partir do endereço IP público que cria no passo 1.

Para obter mais informações sobre como configurar o acesso de saída e o IP a utilizar nas regras de firewall, consulte as [ligações de saída do balanceador de carga](../../load-balancer/load-balancer-outbound-connections.md).

A configuração acima irá garantir que a falha automática não bloqueie as ligações dos componentes frontais e pressupõe que a aplicação pode tolerar a latência mais longa entre a extremidade frontal e o nível de dados.

> [!IMPORTANT]
> Para garantir a continuidade do negócio para interrupções regionais, deve garantir redundância geográfica tanto para os componentes frontais como para as bases de dados.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Ativar a replicação geográfica entre instâncias geridas e as VNets

Quando se cria um grupo de failover entre as instâncias geridas primárias e secundárias do SQL em duas regiões diferentes, cada instância é isolada usando uma rede virtual independente. Para permitir o tráfego de replicação entre estes VNets, garantir que estes pré-requisitos são cumpridos:

- Os dois casos de SQL Managed Instance têm de ser em diferentes regiões do Azure.
- As duas instâncias da SQL Managed Instance têm de ser o mesmo nível de serviço e ter o mesmo tamanho de armazenamento.
- A sua instância secundária de SQL Managed Instance deve estar vazia (sem bases de dados de utilizadores).
- As redes virtuais utilizadas pelos casos de SQL Managed Instance precisam de ser conectadas através de uma [Gateway VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [Via Expresso.](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) Quando duas redes virtuais se ligam através de uma rede no local, verifique se não há nenhuma regra de firewall a bloquear as portas 5022 e 11000-11999. O VNet Peering global é suportado com a limitação descrita na nota abaixo.

   > [!IMPORTANT]
   > [Em 22/09/2020, anunciámos o peering de rede virtual global para clusters virtuais recém-criados](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Tal significa que o peering de rede virtual global é suportado para as instâncias do SQL Managed Instance criadas em sub-redes vazias após a data de anúncio, bem como para todas as instâncias geridas subsequentes criadas nessas sub-redes. Para todos os outros SQL, o suporte de observação está limitado às redes da mesma região devido aos [constrangimentos da rede virtual global de observação.](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) Consulte também a secção relevante das [Redes Virtuais Azure frequentemente fez perguntas](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) a artigo para mais detalhes. 

- Os dois VNets de Instância Gerida SQL não podem ter endereços IP sobrepostos.
- Precisa de configurar os Grupos de Segurança de Rede (NSG) para que as portas 5022 e o intervalo 11000~12000 tenham a entrada e a saída abertas para as ligações da sub-rede da outra instância gerida. Deste modo, permite o tráfego de replicação entre as instâncias.

   > [!IMPORTANT]
   > Regras de segurança NSG mal configuradas levam a operações de cópia de base de dados.

- A instância gerida secundária sql está configurada com o ID da zona DNS correto. A zona de DNS é uma propriedade de um SQL Managed Instance e cluster virtual subjacente, e o seu ID está incluído no endereço do nome anfitrião. O ID da zona é gerado como uma cadeia aleatória quando o primeiro SQL Managed Instance é criado em cada VNet e o mesmo ID é atribuído a todas as outras instâncias na mesma sub-rede. Uma vez atribuída, a zona DNS não pode ser modificada. As ocorrências geridas sql incluídas no mesmo grupo de failover devem partilhar a zona de DNS. Consegue-o ao passar o ID da zona da primeira instância como o valor do parâmetro DnsZonePartner ao criar a instância secundária.

   > [!NOTE]
   > Para obter um tutorial detalhado sobre a configuração de grupos de failover com SQL Managed Instance, consulte [adicionar uma SQL Managed Instance a um grupo de failover](../managed-instance/failover-group-add-instance-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Atualizar ou degradar uma base de dados primária

Pode atualizar ou desclassificar uma base de dados primária para um tamanho de computação diferente (dentro do mesmo nível de serviço, não entre o Final Geral e a Critical De Negócios) sem desligar quaisquer bases de dados secundárias. Ao atualizar, recomendamos que atualize todas as bases de dados secundárias primeiro e, em seguida, atualize as primárias. Ao reduzir, inverta a ordem: desclasse primeiro as primárias e, em seguida, desclasse todas as bases de dados secundárias. Quando atualizar a base de dados ou mudar para um escalão de serviço superior ou inferior, será aplicada esta recomendação.

Esta sequência é recomendada especificamente para evitar o problema em que a instância secundária num SKU inferior fica sobrecarregada e tem de ser novamente propagada durante um processo de atualização ou de mudança para uma versão anterior. Também pode evitar o problema ao tornar a instância primária só de leitura, em detrimento de afetar todas as cargas de trabalho de leitura/gravação na instância primária.

> [!NOTE]
> Se criou uma base de dados secundária como parte da configuração do grupo de failover, não é aconselhável desclassificar a base de dados secundária. Isto é para garantir que o seu nível de dados tem capacidade suficiente para processar a sua carga de trabalho regular após a ativação da falha.

## <a name="preventing-the-loss-of-critical-data"></a>Prevenção da perda de dados críticos

Devido à elevada latência de amplas redes de área, a cópia contínua utiliza um mecanismo de replicação assíncronos. A replicação assíncronia torna a perda de dados inevitável se ocorrer uma falha. No entanto, algumas aplicações podem não requerer perda de dados. Para proteger estas atualizações críticas, um desenvolvedor de aplicações pode ligar para o procedimento do sistema [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) imediatamente após a efeção da transação. A chamada `sp_wait_for_database_copy_sync` bloqueia o fio de chamada até que a última transação comprometida tenha sido transmitida para a base de dados secundária. No entanto, não espera que as transações transmitidas sejam repetidas e cometidas no secundário. `sp_wait_for_database_copy_sync` é traçado para uma ligação de cópia contínua específica. Qualquer utilizador com os direitos de ligação à base de dados primária pode ligar para este procedimento.

> [!NOTE]
> `sp_wait_for_database_copy_sync` previne a perda de dados após o failover, mas não garante a sincronização total para o acesso à leitura. O atraso causado por uma `sp_wait_for_database_copy_sync` chamada de procedimento pode ser significativo e depende do tamanho do registo de transações no momento da chamada.

## <a name="failover-groups-and-point-in-time-restore"></a>Grupos de failover e restauro pontual

Para obter informações sobre a utilização de pontos no tempo, consulte [Point in Time Recovery (PITR)](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations-of-failover-groups"></a>Limitações dos grupos de failover

Esteja atento às seguintes limitações:

- Os grupos de failover não podem ser criados entre dois servidores ou instâncias nas mesmas regiões do Azure.
- Os grupos de failover não podem ser renomeados. Terá de eliminar o grupo e reu criá-lo com um nome diferente.
- O renomeador da base de dados não é suportado por exemplos no grupo failover. Terá de eliminar temporariamente o grupo de failover para poder mudar o nome de uma base de dados.
- As bases de dados do sistema não são replicadas para a instância secundária num grupo de failover. Portanto, os cenários que dependem de objetos das bases de dados do sistema serão impossíveis na instância secundária, a menos que os objetos sejam criados manualmente no secundário.

## <a name="programmatically-managing-failover-groups"></a>Gestão programática de grupos de failover

Tal como discutido anteriormente, os grupos de auto-failover e a geo-replicação ativa também podem ser geridos programáticamente utilizando a Azure PowerShell e a API REST. As tabelas seguintes descrevem o conjunto de comandos disponíveis. A geo-replicação ativa inclui um conjunto de APIs do Gestor de Recursos Azure para gestão, incluindo os cmdlets [AZURE SQL Database REST](/rest/api/sql/) E [Azure PowerShell](/powershell/azure/). Estas APIs requerem a utilização de grupos de recursos e apoiam o controlo de acesso baseado em funções Azure (Azure RBAC). Para obter mais informações sobre como implementar funções de acesso, consulte [o controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="manage-sql-database-failover"></a>Gerir a falha da base de dados SQL

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | Descrição |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Este comando cria um grupo de failover e regista-o em servidores primários e secundários|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Remove um grupo de failover do servidor |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Recupera a configuração de um grupo de failover |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Modifica a configuração de um grupo de failover |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Desencadeia falha de um grupo de failover para o servidor secundário |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Adiciona uma ou mais bases de dados a um grupo de failover|

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

| Comando | Descrição |
| --- | --- |
| [az sql failover grupo criar](/cli/azure/sql/failover-group#az_sql_failover_group_create) |Este comando cria um grupo de failover e regista-o em servidores primários e secundários|
| [az sql failover grupo delete](/cli/azure/sql/failover-group#az_sql_failover_group_delete) | Remove um grupo de failover do servidor |
| [az sql failover grupo show](/cli/azure/sql/failover-group#az_sql_failover_group_show) | Recupera uma configuração de grupo de failover |
| [az sql failover-group update](/cli/azure/sql/failover-group#az_sql_failover_group_update) |Modifica a configuração e/ou adiciona uma ou mais bases de dados a um grupo de failover|
| [az sql failover grupo set-primary](/cli/azure/sql/failover-group#az_sql_failover_group_set_primary) | Desencadeia falha de um grupo de failover para o servidor secundário |

# <a name="rest-api"></a>[Repouso API](#tab/rest-api)

| API | Descrição |
| --- | --- |
| [Criar ou atualizar grupo de failover](/rest/api/sql/failovergroups/createorupdate) | Cria ou atualiza um grupo de failover |
| [Eliminar Grupo Failover](/rest/api/sql/failovergroups/delete) | Remove um grupo de failover do servidor |
| [Failover (Planeado)](/rest/api/sql/failovergroups/failover) | Desencadeia falhas do servidor primário atual para o servidor secundário com sincronização completa de dados.|
| [Falha de força permite perda de dados](/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Desencadeia o failover do servidor primário atual para o servidor secundário sem sincronizar dados. Esta operação pode resultar em perda de dados. |
| [Get Failover Group](/rest/api/sql/failovergroups/get) | Recupera a configuração de um grupo de failover. |
| [Lista grupos de failover por servidor](/rest/api/sql/failovergroups/listbyserver) | Lista os grupos de failover num servidor. |
| [Grupo de Falha de Atualização](/rest/api/sql/failovergroups/update) | Atualiza a configuração de um grupo de failover. |

---

### <a name="manage-sql-managed-instance-failover"></a>Gerir o falha da sql Managed Instance


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | Descrição |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Este comando cria um grupo de failover e regista-o em instâncias primárias e secundárias|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Modifica a configuração de um grupo de failover|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Recupera a configuração de um grupo de failover|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Desencadeia falha de um grupo de failover para a instância secundária|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Remove um grupo de failover|


# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

| Comando | Descrição |
| --- | --- |
| [az sql failover grupo criar](/cli/azure/sql/failover-group#az_sql_failover_group_create) |Este comando cria um grupo de failover e regista-o em servidores primários e secundários|
| [az sql failover grupo delete](/cli/azure/sql/failover-group#az_sql_failover_group_delete) | Remove um grupo de failover do servidor |
| [az sql failover grupo show](/cli/azure/sql/failover-group#az_sql_failover_group_show) | Recupera uma configuração de grupo de failover |
| [az sql failover-group update](/cli/azure/sql/failover-group#az_sql_failover_group_update) |Modifica a configuração e/ou adiciona uma ou mais bases de dados a um grupo de failover|
| [az sql failover grupo set-primary](/cli/azure/sql/failover-group#az_sql_failover_group_set_primary) | Desencadeia falha de um grupo de failover para o servidor secundário |

# <a name="rest-api"></a>[Repouso API](#tab/rest-api)

| API | Descrição |
| --- | --- |
| [Criar ou atualizar grupo de failover](/rest/api/sql/instancefailovergroups/createorupdate) | Cria ou atualiza a configuração de um grupo de failover |
| [Eliminar Grupo Failover](/rest/api/sql/instancefailovergroups/delete) | Remove um grupo de failover da instância |
| [Failover (Planeado)](/rest/api/sql/instancefailovergroups/failover) | Desencadeia falhas da atual instância primária para este caso com sincronização completa de dados. |
| [Falha de força permite perda de dados](/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Desencadeia o failover da instância primária atual para a instância secundária sem sincronizar dados. Esta operação pode resultar em perda de dados. |
| [Get Failover Group](/rest/api/sql/instancefailovergroups/get) | recupera a configuração de um grupo de failover. |
| [Grupos de Failover lista - Lista por Localização](/rest/api/sql/instancefailovergroups/listbylocation) | Lista os grupos de failover num local. |

---

## <a name="next-steps"></a>Passos seguintes

- Para tutoriais detalhados, consulte
  - [Adicione a Base de Dados SQL a um grupo de failover](failover-group-add-single-database-tutorial.md)
  - [Adicionar um conjunto elástico a um grupo de ativação pós-falha](failover-group-add-elastic-pool-tutorial.md)
  - [Adicione uma SQL Managed Instance a um grupo de failover](../managed-instance/failover-group-add-instance-tutorial.md)
- Para os scripts de amostra, consulte:
  - [Utilize o PowerShell para configurar a geo-replicação ativa para a Base de Dados Azure SQL](scripts/setup-geodr-and-failover-database-powershell.md)
  - [Utilize o PowerShell para configurar a geo-replicação ativa para uma base de dados agimiuada na Base de Dados Azure SQL](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
  - [Use powerShell para adicionar uma base de dados Azure SQL a um grupo de failover](scripts/add-database-to-failover-group-powershell.md)
- Para uma visão geral de continuidade do negócio e cenários, consulte [a visão geral da continuidade do Negócio](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Para obter mais sobre cópias de segurança automatizadas da Base de Dados Azure SQL, consulte [backups automatizados da BASE de Dados SQL](automated-backups-overview.md).
- Para obter a utilização de cópias de segurança automatizadas para recuperação, consulte [Restaurar uma base de dados a partir das cópias de segurança iniciadas pelo serviço.](recovery-using-backups.md)
- Para obter mais sobre os requisitos de autenticação de um novo servidor primário e base de dados, consulte a [segurança da Base de Dados SQL após a recuperação de desastres](active-geo-replication-security-configure.md).
