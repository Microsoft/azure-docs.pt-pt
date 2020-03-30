---
title: Grupos de ativação pós-falha
description: Os grupos de falha automática são uma funcionalidade de Base de Dados SQL que permite gerir a replicação e a falha automática/coordenada de um grupo de bases de dados num servidor de base de dados SQL ou em todas as bases de dados em instância gerida.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 2/10/2020
ms.openlocfilehash: 6d87d3373711d12df3f2cced26ef35ae951ad41e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269838"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Utilize grupos de falha automática para permitir falhas transparentes e coordenadas de várias bases de dados

Os grupos de falha automática são uma funcionalidade de Base de Dados SQL que permite gerir a replicação e a falha de um conjunto de bases de dados num servidor de base de dados SQL ou em todas as bases de dados numa instância gerida para outra região. É uma abstração declarativa em cima da característica [de geo-replicação ativa](sql-database-active-geo-replication.md) existente, projetada para simplificar a implantação e gestão de bases de dados geo-replicadas em escala. Pode iniciar a falha manualmente ou pode delegá-la para o serviço SQL Database com base numa política definida pelo utilizador. Esta última opção permite-lhe recuperar automaticamente várias bases de dados relacionadas numa região secundária após uma falha catastrófica ou outro evento não planeado que resulte na perda total ou parcial da disponibilidade do serviço de base de dados SQL na região primária. Um grupo failover pode incluir uma ou várias bases de dados, normalmente usadas pela mesma aplicação. Além disso, pode utilizar as bases de dados secundárias legíveis para descarregar cargas de trabalho de consulta de leitura. Como os grupos de falha automática envolvem várias bases de dados, estas bases de dados devem ser configuradas no servidor primário. Os grupos de falha automática suportam a replicação de todas as bases de dados do grupo para apenas um servidor secundário numa região diferente.

> [!NOTE]
> Ao trabalhar com bases de dados individuais ou agréns num servidor de base de dados SQL e pretender vários secundários nas mesmas regiões ou regiões diferentes, utilize [geo-replicação ativa.](sql-database-active-geo-replication.md) 

Quando se está a utilizar grupos de falha automática com uma política automática de failover, qualquer falha que tenha impacto numa ou várias bases de dados do grupo resulta em falha automática. Normalmente, estes são incidentes que não podem ser auto-atenuados pelas operações automáticas de alta disponibilidade incorporadas. Os exemplos de disparos de failover incluem um incidente causado por um anel de inquilino SQL ou anel de controlo sendo para baixo devido a uma fuga de memória de kernel oss em vários nós computacionais, ou um incidente causado por um ou mais anéis de inquilino sendo parabaixo porque um cabo de rede errado foi cortado durante um cabo de rede errado durante um cabo de rede errado foi cortado durante um cabo de rede errado durante um cabo de rede errado durante um cabo de rede errado durante um cabo de rede errado durante um cabo de rede errado durante um cabo de rede errado foi cortado durante um cabo de rede errado durante um cabo de rede errado durante um cabo de rede errado durante um cabo de rede errado durante um cabo de rede errado durante um cabo de rede errado durante um cabo de rede errado durante um cabo de rede errado durante um cabo de rede errado durante um cabo de rede errado durante um cabo desmantelamento de hardware de rotina.  Para mais informações, consulte a Alta Disponibilidade da Base de [Dados SQL](sql-database-high-availability.md).

Além disso, os grupos de auto-failover fornecem pontos finais de leitura e ouvintes que permanecem inalterados durante as failovers. Quer utilize ativação manual ou automática de failover, a falha comuta todas as bases de dados secundárias do grupo para primária. Após a falha da base de dados, o registo dNS é automaticamente atualizado para redirecionar os pontos finais para a nova região. Para obter os dados específicos de RPO e RTO, consulte [a visão geral da continuidade do negócio](sql-database-business-continuity.md).

Quando estiver a utilizar grupos de falha automática com uma política de failover automática, qualquer falha que tenha impacto nas bases de dados no servidor de base de dados SQL ou na instância gerida resulta em falha automática. Pode gerir o grupo de falhas automáticas utilizando:

- [Portal Azure](sql-database-implement-geo-distributed-database.md)
- [Azure CLI: Failover Group](scripts/sql-database-add-single-db-to-failover-group-cli.md)
- [PowerShell: Failover Group](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [REST API: Failover group](/rest/api/sql/failovergroups).

Após a falha, certifique-se de que os requisitos de autenticação do seu servidor e base de dados estão configurados na nova primária. Para mais detalhes, consulte a segurança da Base de [Dados SQL após a recuperação](sql-database-geo-replication-security-config.md)de desastres .

Para alcançar a continuidade real do negócio, adicionar redundância na base de dados entre datacenters é apenas uma parte da solução. Recuperar uma aplicação (serviço) de ponta a ponta após uma falha catastrófica requer a recuperação de todos os componentes que constituam o serviço e quaisquer serviços dependentes. Exemplos destes componentes incluem o software cliente (por exemplo, um navegador com um JavaScript personalizado), extremidades frontais web, armazenamento e DNS. É fundamental que todos os componentes sejam resilientes às mesmas falhas e estejam disponíveis dentro do objetivo de tempo de recuperação (RTO) da sua aplicação. Por isso, é necessário identificar todos os serviços dependentes e compreender as garantias e capacidades que eles fornecem. Em seguida, deve tomar as medidas adequadas para garantir que o seu serviço funciona durante a falha dos serviços de que depende. Para obter mais informações sobre a conceção de soluções para a recuperação de desastres, consulte [O Desenho de Soluções cloud para recuperação de desastres Utilizando a geo-replicação ativa](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Terminologia e capacidades de grupo de auto-failover

- **Grupo Failover (FOG)**

  Um grupo failover é um grupo nomeado de bases de dados gerido por um único servidor de base de dados SQL ou dentro de uma única instância gerida que pode falhar como uma unidade para outra região no caso de todas ou algumas bases de dados primárias ficarem indisponíveis devido a uma falha na região primária. Quando criado para casos geridos, um grupo failover contém todas as bases de dados dos utilizadores na instância e, portanto, apenas um grupo de failover pode ser configurado numa instância.
  
  > [!IMPORTANT]
  > O nome do grupo failover deve ser `.database.windows.net` globalmente único dentro do domínio.

- **Servidores da Base de Dados SQL**

     Com servidores de base de dados SQL, algumas ou todas as bases de dados de utilizadores de um único servidor de base de dados SQL podem ser colocadas num grupo de falhas. Além disso, um servidor de base de dados SQL suporta vários grupos de falhas num único servidor de base de dados SQL.

- **Primária**

  O servidor de base de dados SQL ou instância gerida que acolhe as bases de dados primárias no grupo failover.

- **Secundária**

  O servidor de base de dados SQL ou instância gerida que acolhe as bases de dados secundárias do grupo failover. O secundário não pode estar na mesma região que as primárias.

- **Adicionar bases de dados únicas ao grupo failover**

  Pode colocar várias bases de dados únicas no mesmo servidor de base de dados SQL no mesmo grupo de failover. Se adicionar uma única base de dados ao grupo failover, cria automaticamente uma base de dados secundária utilizando a mesma edição e tamanho de cálculo no servidor secundário.  Especificou esse servidor quando o grupo failover foi criado. Se adicionar uma base de dados que já tem uma base de dados secundária no servidor secundário, essa ligação de geo-replicação é herdada pelo grupo. Quando adiciona uma base de dados que já tem uma base de dados secundária num servidor que não faz parte do grupo failover, é criado um novo secundário no servidor secundário.

  > [!IMPORTANT]
  > Certifique-se de que o servidor secundário não tem uma base de dados com o mesmo nome, a menos que seja uma base de dados secundária existente. Em grupos de failover para casos geridos todas as bases de dados dos utilizadores são replicadas. Não é possível escolher um subconjunto de bases de dados de utilizadores para replicação no grupo failover.

- **Adicionar bases de dados em piscina elástica para falhar grupo**

  Pode colocar todas ou várias bases de dados dentro de uma piscina elástica no mesmo grupo de failover. Se a base de dados primária estiver numa piscina elástica, a secundária é automaticamente criada na piscina elástica com o mesmo nome (piscina secundária). Deve certificar-se de que o servidor secundário contém um pool elástico com o mesmo nome e capacidade livre suficiente para acolher as bases de dados secundárias que serão criadas pelo grupo failover. Se adicionar uma base de dados na piscina que já tem uma base de dados secundária na piscina secundária, essa ligação de geo-replicação é herdada pelo grupo. Quando se adiciona uma base de dados que já tem uma base de dados secundária num servidor que não faz parte do grupo failover, é criado um novo secundário no pool secundário.
  
- **Sementeing inicial** 

  Ao adicionar bases de dados, piscinas elásticas ou instâncias geridas a um grupo de failover, há uma fase inicial de sementeir antes do início da replicação de dados. A fase inicial de sementesão é a operação mais longa e cara. Uma vez que a sementeing inicial esteja concluída, os dados são sincronizados e, em seguida, apenas alterações de dados subsequentes são replicadas. O tempo que a semente inicial leva a concluir depende do tamanho dos seus dados, do número de bases de dados replicadas e da velocidade da ligação entre as entidades do grupo failover. Em circunstâncias normais, a velocidade típica de sementesão é de 50-500 GB por hora para uma única base de dados ou piscina elástica, e 18-35 GB por hora para um caso gerido. A sementesão é realizada para todas as bases de dados em paralelo. Pode utilizar a velocidade de sementeira indicada, juntamente com o número de bases de dados e o tamanho total dos dados para estimar quanto tempo demorará a fase inicial de sementeira até que a replicação de dados comece.

  Para os casos geridos, a velocidade da ligação da Rota Expresso entre as duas instâncias também deve ser considerada ao estimar o tempo da fase inicial de sementeira. Se a velocidade da ligação entre as duas instâncias for mais lenta do que a necessária, é provável que o tempo de semente seja notavelmente afetado. Pode utilizar a velocidade de sementeira indicada, o número de bases de dados, o tamanho total dos dados e a velocidade de ligação para estimar quanto tempo demorará a fase inicial de sementeira até que a replicação de dados comece. Por exemplo, para uma única base de dados de 100 GB, a fase inicial das sementes demoraria entre 2,8 e 5,5 horas se o link for capaz de empurrar 35 GB por hora. Se o link só puder transferir 10 GB por hora, então a sementede de uma base de dados de 100 GB levará cerca de 10 horas. Se existirem várias bases de dados para replicar, a sementeira será executada paralelamente e, quando combinada com uma velocidade de ligação lenta, a fase inicial de sementeir pode demorar consideravelmente mais tempo, especialmente se a sementeira paralela de dados de todas as bases de dados exceder a disponível largura de banda de ligação. Se a largura de banda da rede entre duas instâncias for limitada e estiver a adicionar múltiplas instâncias geridas a um grupo de failover, considere adicionar múltiplas instâncias geridas ao grupo failover sequencialmente, um a um.

  
- **Zona DNS**

  Um ID único que é gerado automaticamente quando uma nova instância é criada. Para este caso, é previsto um certificado de vários domínios (SAN) para este caso para autenticar as ligações do cliente a qualquer instância da mesma zona DNS. Os dois casos geridos no mesmo grupo de failover devem partilhar a zona DNS.
  
  > [!NOTE]
  > Não é necessário um ID de zona DNS para grupos de failover criados para servidores de base de dados SQL.

- **Failover grupo ler ouvinte de leitura**

  Um registo DNS CNAME que aponta para o URL da atual primária. É criado automaticamente quando o grupo failover é criado e permite que a carga de trabalho SQL de leitura-escrita reconecte-se transparentemente à base de dados primária quando o primário muda após a falha. Quando o grupo failover é criado num servidor de base de dados SQL, `<fog-name>.database.windows.net`o registo DNS CNAME para o URL ouvinte é formado como . Quando o grupo failover é criado numa instância gerida, o registo DNS `<fog-name>.zone_id.database.windows.net`CNAME para o URL ouvinte é formado como .

- **Ouvinte de grupo failover apenas**

  Formou-se um registo DNS CNAME que aponta para o ouvinte que apenas lê que aponta para o URL do secundário. É criado automaticamente quando o grupo failover é criado e permite que a carga de trabalho SQL apenas para leitura se conectem transparentemente ao secundário usando as regras especificadas de equilíbrio de carga. Quando o grupo failover é criado num servidor de base de dados SQL, `<fog-name>.secondary.database.windows.net`o registo DNS CNAME para o URL ouvinte é formado como . Quando o grupo failover é criado numa instância gerida, o registo DNS `<fog-name>.zone_id.secondary.database.windows.net`CNAME para o URL ouvinte é formado como .

- **Política automática de failover**

  Por padrão, um grupo de failover é configurado com uma política de failover automática. O serviço de base de dados SQL dispara falhas após a falha ser detetada e o período de carência expirou. O sistema deve verificar se a paralisação não pode ser atenuada pela infraestrutura incorporada de alta disponibilidade do serviço de base de [dados SQL](sql-database-high-availability.md) devido à dimensão do impacto. Se pretender controlar o fluxo de trabalho de failover da aplicação, pode desligar a falha automática.
  
  > [!NOTE]
  > Uma vez que a verificação da escala da paralisação e a rapidez com que pode ser atenuada envolve ações humanas por parte da equipa de operações, o período de carência não pode ser fixado abaixo de uma hora.  Esta limitação aplica-se a todas as bases de dados do grupo failover, independentemente do seu estado de sincronização de dados. 

- **Política de reprovação apenas de leitura**

  Por predefinição, a falha do ouvinte só de leitura é desativada. Garante que o desempenho do primário não é afetado quando o secundário está offline. No entanto, também significa que as sessões de leitura não poderão ligar-se até que a secundária seja recuperada. Se não puder tolerar o tempo de inatividade para as sessões apenas de leitura e estiver bem para usar temporariamente o principal para o tráfego de leitura e leitura `AllowReadOnlyFailoverToPrimary` em detrimento da potencial degradação do desempenho do primário, pode ativar o failover para o ouvinte apenas de leitura configurando a propriedade. Nesse caso, o tráfego apenas de leitura será automaticamente redirecionado para o primário se o secundário não estiver disponível.

- **Falha planeada**

   O failover planeado executa uma sincronização completa entre bases de dados primárias e secundárias antes que o secundário mude para o papel principal. Isto não garante nenhuma perda de dados. A failover planeada é usada nos seguintes cenários:

  - Realizar exercícios de recuperação de desastres (DR) em produção quando a perda de dados não é aceitável
  - Desloque as bases de dados para uma região diferente
  - Devolva as bases de dados à região primária depois de atenuada a paralisação (recuo).

- **Ativação pós-falha não planeada**

   Falha não planeada ou forçada muda imediatamente o secundário para o papel primário sem qualquer sincronização com o primário. Esta operação resultará na perda de dados. A falha não planeada é usada como um método de recuperação durante as interrupções quando o primário não é acessível. Quando a primária original estiver novamente on-line, reconectar-se-á automaticamente sem sincronização e tornar-se-á um novo secundário.

- **Falha manual**

  Pode iniciar a falha manualmente a qualquer momento, independentemente da configuração automática de failover. Se a política de failover automática não estiver configurada, é necessária uma falha manual para recuperar bases de dados no grupo failover para o secundário. Pode iniciar uma falha forçada ou amigável (com sincronização completa de dados). Este último poderia ser utilizado para deslocar as primárias para a região secundária. Quando a falha é concluída, os registos DNS são automaticamente atualizados para garantir a conectividade com as novas primárias

- **Período de graça com perda de dados**

  Uma vez que as bases de dados primárias e secundárias são sincronizadas usando replicação assíncrona, a falha pode resultar na perda de dados. Pode personalizar a política de failover automática para refletir a tolerância da sua aplicação à perda de dados. Ao configurar, `GracePeriodWithDataLossHours`pode controlar o tempo que o sistema espera antes de dar início à falha que é suscetível de resultar na perda de dados.

- **Múltiplos grupos de failover**

  Pode configurar vários grupos de failover para o mesmo par de servidores para controlar a escala de falhas. Cada grupo falha de forma independente. Se a sua aplicação multi-arrendatária utilizar piscinas elásticas, pode usar esta capacidade para misturar bases de dados primárias e secundárias em cada piscina. Desta forma, pode reduzir o impacto de uma paragem para apenas metade dos inquilinos.

  > [!NOTE]
  > A Instância Gerida não suporta vários grupos de failover.
  
## <a name="permissions"></a>Permissões

As permissões para um grupo failover são geridas através do [controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md). A função [SQL Server Contributor](../role-based-access-control/built-in-roles.md#sql-server-contributor) tem todas as permissões necessárias para gerir grupos de failover.

### <a name="create-failover-group"></a>Criar grupo failover

Para criar um grupo de failover, precisa de acesso de escrita RBAC aos servidores primários e secundários e a todas as bases de dados do grupo failover. Para um caso gerido, é necessário que o RBAC escreva acesso à instância gerida primária e secundária, mas as permissões nas bases de dados individuais não são relevantes, uma vez que as bases de dados de instâncias geridas individuais não podem ser adicionadas ou removidas de um grupo de falhas. 

### <a name="update-a-failover-group"></a>Atualizar um grupo de failover

Para atualizar um grupo de failover, precisa de acesso de escrita RBAC ao grupo failover, e todas as bases de dados no servidor primário atual ou instância gerida.  

### <a name="failover-a-failover-group"></a>Failover um grupo de failover

Para falhar sobre um grupo de failover, precisa de acesso de escrita RBAC ao grupo failover no novo servidor primário ou instância gerida.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Boas práticas de utilização de grupos failover com bases de dados únicas e piscinas elásticas

O grupo de falha automática deve ser configurado no servidor principal da Base de Dados SQL e conectá-lo-á ao servidor de base de dados SQL secundário numa região do Azure diferente. Os grupos podem incluir todas ou algumas bases de dados nestes servidores. O diagrama seguinte ilustra uma configuração típica de uma aplicação de nuvem geo-redundante usando várias bases de dados e grupo de falha automática.

![falha automática](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> Consulte adicionar uma única base de dados a um grupo de [failover](sql-database-single-database-failover-group-tutorial.md) para um tutorial detalhado passo a passo adicionando uma única base de dados a um grupo de failover.

Ao conceber um serviço com continuidade empresarial em mente, siga estas orientações gerais:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Usando um ou vários grupos de failover para gerir falhas de várias bases de dados

Um ou muitos grupos de failover podem ser criados entre dois servidores em diferentes regiões (servidores primários e secundários). Cada grupo pode incluir uma ou várias bases de dados que são recuperadas como uma unidade no caso de todas ou algumas bases de dados primárias ficarem indisponíveis devido a uma falha na região primária. O grupo failover cria uma base de dados geosecundária com o mesmo objetivo de serviço que o primário. Se adicionar uma relação geo-replicação existente ao grupo failover, certifique-se de que o geo-secundário está configurado com o mesmo nível de serviço e tamanho de computação que o principal.
  
> [!IMPORTANT]
> A criação de grupos de failover entre dois servidores em diferentes subscrições não é atualmente suportado para bases de dados individuais e piscinas elásticas. Se deslocar o servidor primário ou secundário para uma subscrição diferente após a criação do grupo failover, pode resultar em falhas nos pedidos de failover e outras operações.

### <a name="using-read-write-listener-for-oltp-workload"></a>Utilização de ouvinte sonorizador de leitura para a carga de trabalho oLTP

Ao executar as operações `<fog-name>.database.windows.net` OLTP, utilize como URL do servidor e as ligações são automaticamente direcionadas para o primário. Este URL não muda após a falha. Note que a falha envolve a atualização do registo DNS para que as ligações do cliente sejam redirecionadas para a nova primária apenas após a renovação da cache DNS do cliente.

### <a name="using-read-only-listener-for-read-only-workload"></a>Utilização de ouvintes apenas de leitura para a carga de trabalho apenas de leitura

Se tiver uma carga de trabalho de leitura logicamente isolada que seja tolerante a certas tensões de dados, pode utilizar a base de dados secundária na aplicação. Para sessões de `<fog-name>.secondary.database.windows.net` leitura, utilize como URL do servidor e a ligação é automaticamente direcionada para o secundário. Recomenda-se também que indique na ligação `ApplicationIntent=ReadOnly`a utilização de uma intenção de leitura de cordas utilizando . Se pretender garantir que a carga de trabalho apenas para leitura pode voltar a ligar-se `AllowReadOnlyFailoverToPrimary` após a falha ou no caso de o servidor secundário ficar offline, certifique-se de configurar a propriedade da política de failover.

### <a name="preparing-for-performance-degradation"></a>Preparação para a degradação do desempenho

Uma aplicação típica do Azure utiliza vários serviços Azure e consiste em múltiplos componentes. A falha automatizada do grupo failover é desencadeada com base apenas no estado dos componentes Azure SQL. Outros serviços azure na região primária podem não ser afetados pela paralisação e os seus componentes podem ainda estar disponíveis nessa região. Uma vez que as bases de dados primárias mudem para a região DR, a latência entre os componentes dependentes pode aumentar. Para evitar o impacto de uma maior latência no desempenho da aplicação, garanta o despedimento de todos os componentes da aplicação na região DR e siga estas [orientações de segurança](#failover-groups-and-network-security)da rede .

### <a name="preparing-for-data-loss"></a>Preparação para a perda de dados

Se for detetada uma paragem, o SQL aguarda `GracePeriodWithDataLossHours`o período por que especificou . O valor padrão é de 1 hora. Se não puder pagar a perda `GracePeriodWithDataLossHours` de dados, certifique-se de que se fixa a um número suficientemente grande, como 24 horas. Utilize o failover do grupo manual para falhar do secundário para o primário.

> [!IMPORTANT]
> Piscinas elásticas com 800 ou menos DTUs e mais de 250 bases de dados usando geo-replicação podem encontrar problemas, incluindo falhas planeadas mais longas e desempenho degradado.  Estas questões são mais propensas a ocorrer para a escrita de cargas de trabalho intensivas, quando os pontos finais de geo-replicação são amplamente separados por geografia, ou quando vários pontos finais secundários são usados para cada base de dados.  Os sintomas destas questões são indicados quando o lag de geo-replicação aumenta com o tempo.  Este lag pode ser monitorizado utilizando [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Se estas questões ocorrerem, então as mitigações incluem aumentar o número de DTUs do pool, ou reduzir o número de bases de dados geo-replicadas no mesmo pool.

### <a name="changing-secondary-region-of-the-failover-group"></a>Alteração da região secundária do grupo failover

Para ilustrar a sequência de mudança, assumimos que o servidor A é o servidor principal, o servidor B é o servidor secundário existente, e o servidor C é o novo secundário na terceira região.  Para fazer a transição, siga estes passos:

1.  Criar secundários adicionais de cada base de dados no servidor A para o servidor C utilizando a [geo-replicação ativa](sql-database-active-geo-replication.md). Cada base de dados no servidor A terá dois secundários, um no servidor B e outro no servidor C. Isto garantirá que as bases de dados primárias permaneçam protegidas durante a transição.
2.  Elimine o grupo failover. Neste momento, os logins estarão a falhar. Isto porque os pseudónimos da SQL para os ouvintes do grupo failover foram apagados e o gateway não reconhecerá o nome de grupo failover.
3.  Recrie o grupo failover com o mesmo nome entre os servidores A e C. Neste ponto, os logins deixarão de falhar.
4.  Adicione todas as bases de dados primárias no servidor A ao novo grupo failover.
5.  Servidor B. Todas as bases de dados em B serão eliminadas automaticamente. 


### <a name="changing-primary-region-of-the-failover-group"></a>Alteração da região primária do grupo failover

Para ilustrar a sequência de mudança, assumiremos que o servidor A é o servidor principal, o servidor B é o servidor secundário existente, e o servidor C é o novo primário na terceira região.  Para fazer a transição, siga estes passos:

1.  Realizar uma falha planeada para mudar o servidor primário para B. Server A tornar-se-á o novo servidor secundário. A falha pode resultar em vários minutos de tempo de paragem. O tempo real dependerá do tamanho do grupo failover.
2.  Crie secundários adicionais de cada base de dados no servidor B para o servidor C utilizando a [geo-replicação ativa](sql-database-active-geo-replication.md). Cada base de dados do servidor B terá dois secundários, um no servidor A e outro no servidor C. Isto garantirá que as bases de dados primárias permaneçam protegidas durante a transição.
3.  Elimine o grupo failover. Neste momento, os logins estarão a falhar. Isto porque os pseudónimos da SQL para os ouvintes do grupo failover foram apagados e o gateway não reconhecerá o nome de grupo failover.
4.  Recrie o grupo failover com o mesmo nome entre os servidores A e C. Neste ponto, os logins deixarão de falhar.
5.  Adicione todas as bases de dados primárias em B ao novo grupo de failover. 
6.  Execute uma falha planeada do grupo failover para mudar B e C. Agora o servidor C tornar-se-á o primário e B - o secundário. Todas as bases de dados secundárias do servidor A serão automaticamente ligadas às primárias em C. Tal como no passo 1, a falha pode resultar em vários minutos de tempo de paragem.
6.  Largue o servidor A. Todas as bases de dados em A serão eliminadas automaticamente.

> [!IMPORTANT]
> Quando o grupo failover é eliminado, os registos DNS para os pontos finais do ouvinte também são eliminados. Nessa altura, existe uma probabilidade não nula de outra pessoa criar um grupo de falhas ou pseudónimo de servidor com o mesmo nome, o que o impedirá de o voltar a usar. Para minimizar o risco, não use nomes genéricos de grupos de failover.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>As melhores práticas de utilização de grupos de failover com instâncias geridas

O grupo de falha automática deve ser configurado na instância primária e ligá-lo-á à instância secundária numa região de Azure diferente.  Todas as bases de dados do caso serão replicadas para a instância secundária.

O diagrama seguinte ilustra uma configuração típica de uma aplicação de nuvem geo-redundante usando instância gerida e grupo de falha automática.

![falha automática](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> Consulte adicionar instância gerida a um grupo de [failover](sql-database-managed-instance-failover-group-tutorial.md) para um tutorial detalhado passo a passo adicionando uma instância gerida para usar o grupo failover.

Se a sua aplicação utilizar a instância gerida como o nível de dados, siga estas orientações gerais ao conceber a continuidade do negócio:

### <a name="creating-the-secondary-instance"></a>Criando a instância secundária 

Para garantir a conectividade não interrompida à instância primária após a falha, tanto as instâncias primárias como secundárias devem estar na mesma zona DNS. Garantirá que o mesmo certificado de vários domínios (SAN) pode ser utilizado para autenticar as ligações do cliente a qualquer uma das duas instâncias do grupo failover. Quando a sua aplicação estiver pronta para a implantação da produção, crie uma instância secundária numa região diferente e certifique-se de que partilha a zona DNS com a instância primária. Pode fazê-lo especificando um `DNS Zone Partner` parâmetro opcional utilizando o portal Azure, PowerShell ou a API REST.

> [!IMPORTANT]
> A primeira instância criada na sub-rede determina a zona dNS para todos os casos subsequentes na mesma sub-rede. Isto significa que duas instâncias da mesma sub-rede não podem pertencer a diferentes zonas de DNS.

Para obter mais informações sobre a criação da instância secundária na mesma zona de DNS que a instância primária, consulte [Criar uma instância secundária gerida](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance).

### <a name="enabling-replication-traffic-between-two-instances"></a>Permitir o tráfego de replicação entre duas instâncias

Como cada instância está isolada no seu próprio VNet, o tráfego em duas direções entre estes VNets deve ser permitido. Ver [Gateway Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Criar um grupo de falhas entre casos geridos em diferentes subscrições

Pode criar um grupo de falhas entre casos geridos em duas subscrições diferentes. Ao utilizar a API PowerShell, pode `PartnerSubscriptionId` fazê-lo especificando o parâmetro para a instância secundária. Ao utilizar a API REST, cada `properties.managedInstancePairs` id de instância incluída no parâmetro pode ter o seu próprio ID de subscrição.
  
> [!IMPORTANT]
> O portal Azure não suporta a criação de grupos failover em diferentes subscrições. Além disso, para os grupos de failover existentes em diferentes subscrições e/ou grupos de recursos, a falha não pode ser iniciada manualmente através do portal a partir da instância primária. Em vez disso, inicie-o a partir da instância geo-secundária.

### <a name="managing-failover-to-secondary-instance"></a>Gerir a falha para a instância secundária

O grupo failover gerirá a falha de todas as bases de dados no caso. Quando um grupo é criado, cada base de dados do caso será automaticamente geo-replicada para a instância secundária. Não é possível utilizar grupos de failover para iniciar uma falha parcial de um subconjunto das bases de dados.

> [!IMPORTANT]
> Se uma base de dados for removida da instância primária, também será deixada automaticamente na instância geo secundária.

### <a name="using-read-write-listener-for-oltp-workload"></a>Utilização de ouvinte sonorizador de leitura para a carga de trabalho oLTP

Ao executar as operações `<fog-name>.zone_id.database.windows.net` OLTP, utilize como URL do servidor e as ligações são automaticamente direcionadas para o primário. Este URL não muda após a falha. O failover envolve atualizar o registo DNS, pelo que as ligações do cliente são redirecionadas para a nova primária apenas após a renovação da cache DNS do cliente. Uma vez que a instância secundária partilha a zona DNS com a principal, a aplicação do cliente poderá voltar a ligar-se à mesma utilizando o mesmo certificado SAN.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Utilização de ouvintes apenas de leitura para ligar à instância secundária

Se tiver uma carga de trabalho de leitura logicamente isolada que seja tolerante a certas tensões de dados, pode utilizar a base de dados secundária na aplicação. Para se ligar diretamente ao secundário geo-replicado, utilize `server.secondary.zone_id.database.windows.net` como URL do servidor e a ligação é feita diretamente ao secundário geo-replicado.

> [!NOTE]
> Em certos níveis de serviço, a Base de Dados Azure SQL suporta a utilização de [réplicas apenas](sql-database-read-scale-out.md) de leitura `ApplicationIntent=ReadOnly` para carregar cargas de trabalho de consulta de leitura de equilíbrio utilizando a capacidade de uma réplica apenas para leitura e utilizando o parâmetro na cadeia de ligação. Quando configurar um secundário geo-replicado, pode utilizar esta capacidade para se ligar a uma réplica apenas de leitura no local primário ou no local geo-replicado.
> - Para se ligar a uma réplica apenas `<fog-name>.zone_id.database.windows.net`de leitura no local primário, utilize .
> - Para se ligar a uma réplica apenas `<fog-name>.secondary.zone_id.database.windows.net`para leitura no local secundário, utilize .

### <a name="preparing-for-performance-degradation"></a>Preparação para a degradação do desempenho

Uma aplicação típica do Azure utiliza vários serviços Azure e consiste em múltiplos componentes. A falha automatizada do grupo failover é desencadeada com base apenas no estado dos componentes Azure SQL. Outros serviços azure na região primária podem não ser afetados pela paralisação e os seus componentes podem ainda estar disponíveis nessa região. Uma vez que as bases de dados primárias mudem para a região DR, a latência entre os componentes dependentes pode aumentar. Para evitar o impacto de uma maior latência no desempenho da aplicação, garanta o despedimento de todos os componentes da aplicação na região DR e siga estas [orientações de segurança](#failover-groups-and-network-security)da rede .

### <a name="preparing-for-data-loss"></a>Preparação para a perda de dados

Se for detetada uma falha, a SQL aciona automaticamente a falha de leitura-escrita se não houver perda de dados para o melhor dos nossos conhecimentos. Caso contrário, aguarda pelo período que `GracePeriodWithDataLossHours`especificou por . Se especificado, `GracePeriodWithDataLossHours`prepare-se para a perda de dados. Em geral, durante as interrupções, o Azure favorece a disponibilidade. Se não puder pagar a perda de dados, certifique-se de definir gracePeriodWithDataLossHours para um número suficientemente grande, como 24 horas.

A atualização DNS do ouvinte de leitura-escrita acontecerá imediatamente após o início da falha. Esta operação não resultará em perda de dados. No entanto, o processo de troca de funções de base de dados pode demorar até 5 minutos em condições normais. Até que esteja concluída, algumas bases de dados na nova instância primária continuarão a ser apenas leituras. Se a falha for iniciada utilizando o PowerShell, toda a operação é sincronizada. Se for iniciado utilizando o portal Azure, a UI indicará o estado de conclusão. Se for iniciado utilizando a API REST, utilize o mecanismo de sondagem padrão do Gestor de Recursos Azure para monitorizar a conclusão.

> [!IMPORTANT]
> Utilize o failover do grupo manual para mover as primárias de volta para o local original. Quando a interrupção que causou a falha for atenuada, pode mover as suas bases de dados primárias para o local original. Para isso, deve iniciar a falha manual do grupo.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Alteração da região secundária do grupo failover

Vamos supor que o exemplo A é o exemplo principal, a instância B é a instância secundária existente, e a instância C é a nova instância secundária na terceira região.  Para fazer a transição, siga estes passos:

1.  Crie a instância C com o mesmo tamanho que A e na mesma zona DNS. 
2.  Elimine o grupo de falhas entre as instâncias A e B. Neste momento, os logins estarão a falhar porque os pseudónimos do SQL para os ouvintes do grupo failover foram apagados e o gateway não reconhecerá o nome de grupo failover. As bases de dados secundárias serão desligadas das primárias e tornar-se-ão bases de dados de leitura. 
3.  Crie um grupo de failover com o mesmo nome entre os exemplos A e C. Siga as instruções em [grupo failover com tutorial](sql-database-managed-instance-failover-group-tutorial.md)de instância gerida . Trata-se de uma operação de tamanho de dados e completa-se quando todas as bases de dados da instância A forem semeadas e sincronizadas.
4.  Eliminar a instância B se não for necessário para evitar encargos desnecessários.

> [!NOTE]
> Após o passo 2 e até que o passo 3 esteja concluído, as bases de dados em caso A permanecerão desprotegidas de uma falha catastrófica da instância A.

### <a name="changing-primary-region-of-the-failover-group"></a>Alteração da região primária do grupo failover

Vamos assumir que o exemplo A é a instância primária, a instância B é a instância secundária existente, e a instância C é a nova instância primária na terceira região.  Para fazer a transição, siga estes passos:

1.  Crie a instância C com o mesmo tamanho que B e na mesma zona DNS. 
2.  Ligue-se à instância B e falhe manualmente para mudar a instância primária para B. A instância A passará a ser a nova instância secundária automaticamente.
3.  Elimine o grupo de falhas entre as instâncias A e B. Neste momento, os logins estarão a falhar porque os pseudónimos do SQL para os ouvintes do grupo failover foram apagados e o gateway não reconhecerá o nome de grupo failover. As bases de dados secundárias serão desligadas das primárias e tornar-se-ão bases de dados de leitura. 
4.  Crie um grupo de failover com o mesmo nome entre os exemplos A e C. Siga as instruções no [grupo failover com tutorial](sql-database-managed-instance-failover-group-tutorial.md)de instância gerida . Trata-se de uma operação de tamanho de dados e completa-se quando todas as bases de dados da instância A forem semeadas e sincronizadas.
5.  Eliminar a instância A se não for necessário para evitar encargos desnecessários.

> [!NOTE] 
> Após o passo 3 e até que o passo 4 esteja concluído, as bases de dados, por exemplo, A permanecerão desprotegidas de uma falha catastrófica da instância A.

> [!IMPORTANT]
> Quando o grupo failover é eliminado, os registos DNS para os pontos finais do ouvinte também são eliminados. Nessa altura, existe uma probabilidade não nula de outra pessoa criar um grupo de falhas ou pseudónimo de servidor com o mesmo nome, o que o impedirá de o voltar a usar. Para minimizar o risco, não use nomes genéricos de grupos de failover.

## <a name="failover-groups-and-network-security"></a>Grupos failover e segurança da rede

Para algumas aplicações, as regras de segurança exigem que o acesso à rede ao nível de dados esteja restrito a um componente ou componentes específicos, tais como um VM, serviço web, etc. Esta exigência apresenta alguns desafios para o design de continuidade empresarial e a utilização dos grupos failover. Considere as seguintes opções ao implementar tal acesso restrito.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Utilização de grupos failover e regras de rede virtual

Se estiver a utilizar [pontos finais e regras](sql-database-vnet-service-endpoint-rule-overview.md) de serviço de Rede Virtual para restringir o acesso à sua base de dados SQL, esteja ciente de que cada ponto final do serviço de Rede Virtual se aplica a apenas a uma região do Azure. O objetivo final não permite que outras regiões aceitem a comunicação da sub-rede. Portanto, apenas as aplicações do cliente implantadas na mesma região podem ligar-se à base de dados primária. Uma vez que o failover resulta em que as sessões de clientes da SQL sejam reencaminhadas para um servidor numa região diferente (secundária), estas sessões falharão se forem originadas de um cliente fora daquela região. Por essa razão, a política de failover automática não pode ser ativada se os servidores participantes estiverem incluídos nas regras da Rede Virtual. Para suportar a falha manual, siga estes passos:

1. Forneça as cópias redundantes dos componentes frontais da sua aplicação (serviço web, máquinas virtuais, etc.) na região secundária
2. Configure as regras da [rede virtual](sql-database-vnet-service-endpoint-rule-overview.md) individualmente para servidor primário e secundário
3. Ativar a [falha frontal utilizando uma configuração](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime) do gestor de tráfego
4. Inicie a falha manual quando a interrupção for detetada. Esta opção é otimizada para as aplicações que requerem latência consistente entre a parte frontal e o nível de dados e suporta a recuperação quando ou a frente, o nível de dados ou ambos são impactados pela paragem.

> [!NOTE]
> Se estiver a utilizar o **ouvinte apenas** para carregar uma carga de trabalho apenas de leitura, certifique-se de que esta carga de trabalho é executada num VM ou noutro recurso na região secundária para que possa ligar-se à base de dados secundária.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Utilização de grupos failover e regras de firewall de base de dados SQL

Se o seu plano de continuidade de negócio sofre a falha utilizando grupos com falha automática, pode restringir o acesso à sua base de dados SQL utilizando as regras tradicionais de firewall. Para suportar a falha automática, siga estes passos:

1. [Criar um IP público](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Crie um equilibrador](../load-balancer/quickstart-load-balancer-standard-public-portal.md) de carga público e atribua-lhe o IP público.
3. [Crie uma rede virtual e as máquinas virtuais](../load-balancer/quickstart-load-balancer-standard-public-portal.md) para os seus componentes front-end
4. [Crie um grupo](../virtual-network/security-overview.md) de segurança de rede e configure as ligações de entrada.
5. Certifique-se de que as ligações de saída estão abertas à base de dados Azure SQL utilizando a etiqueta de [serviço](../virtual-network/security-overview.md#service-tags)'Sql'.
6. Crie uma regra de firewall de base de [dados SQL](sql-database-firewall-configure.md) para permitir o tráfego de entrada a partir do endereço IP público que cria no passo 1.

Para obter mais informações sobre como configurar o acesso de saída e o IP a utilizar nas regras de firewall, consulte as ligações de saída do [balancer de carga](../load-balancer/load-balancer-outbound-connections.md).

A configuração acima irá garantir que a falha automática não bloqueia as ligações dos componentes frontais e assume que a aplicação pode tolerar a latência mais longa entre a extremidade frontal e o nível de dados.

> [!IMPORTANT]
> Para garantir a continuidade do negócio para interrupções regionais, deve garantir o despedimento geográfico tanto para os componentes frontais como para as bases de dados.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Permitir a geo-replicação entre as instâncias geridas e os seus VNets

Quando se cria um grupo de falhas entre casos geridos primários e secundários em duas regiões diferentes, cada instância é isolada utilizando uma rede virtual independente. Para permitir o tráfego de replicação entre estes VNets, certifique-se de que estes pré-requisitos são cumpridos:

1. Os dois casos geridos têm de estar em diferentes regiões de Azure.
2. Os dois casos geridos têm de ser o mesmo nível de serviço e têm o mesmo tamanho de armazenamento.
3. A sua instância secundária gerida deve estar vazia (sem bases de dados de utilizadores).
4. As redes virtuais utilizadas pelas instâncias geridas precisam de ser ligadas através de um [Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [de Uma Rota Expresso.](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) Quando duas redes virtuais se ligarem através de uma rede no local, certifique-se de que não existe nenhuma regra de firewall bloqueando as portas 5022 e 11000-11999. A VNet Peering global não é apoiada.
5. Os dois VNets de instância gerido não podem ter endereços IP sobrepostos.
6. É necessário criar os seus Grupos de Segurança de Rede (NSG) de modo a que as portas 5022 e o intervalo 11000~12000 estejam abertos de entrada e saída para ligações a partir da subnet da outra instância gerida. Isto é para permitir o tráfego de replicação entre os casos.

   > [!IMPORTANT]
   > Regras de segurança nsg mal configuradas levam a operações de cópia de base de dados presas.

7. A instância secundária é configurada com o ID correto da zona DNS. A zona DNS é uma propriedade de um caso gerido e cluster virtual, e o seu ID está incluído no endereço de nome do anfitrião. O ID da zona é gerado como uma cadeia aleatória quando a primeira instância gerida é criada em cada VNet e o mesmo ID é atribuído a todas as outras instâncias na mesma sub-rede. Uma vez atribuída, a zona DNS não pode ser modificada. As instâncias geridas incluídas no mesmo grupo de failover devem partilhar a zona DNS. Realiza-o passando o ID da zona da instância primária como o valor do parâmetro DnsZonePartner ao criar a instância secundária. 

   > [!NOTE]
   > Para um tutorial detalhado sobre a configuração de grupos de failover com instância gerida, consulte [adicionar uma instância gerida a um grupo de failover](sql-database-managed-instance-failover-group-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Atualizar ou degradar uma base de dados primária

Pode atualizar ou desvalorizar uma base de dados primária para um tamanho de computação diferente (dentro do mesmo nível de serviço, não entre o Propósito Geral e o Business Critical) sem desligar quaisquer bases de dados secundárias. Ao atualizar, recomendamos que atualize primeiro todas as bases de dados secundárias e, em seguida, atualize as primárias. Ao degradar, inverta a ordem: desagrade primeiro a primária e, em seguida, desagrade todas as bases de dados secundárias. Quando atualiza ou desvaloriza a base de dados para um nível de serviço diferente, esta recomendação é aplicada.

Esta sequência é recomendada especificamente para evitar o problema em que o secundário de um SKU inferior fica sobrecarregado e deve ser resselado durante um processo de atualização ou downgrade. Também poderia evitar o problema fazendo a leitura primária apenas, em detrimento de impactar todas as cargas de trabalho de leitura-escrita contra as primárias.

> [!NOTE]
> Se criou uma base de dados secundária como parte da configuração do grupo failover, não é aconselhável desvalorizar a base de dados secundária. Isto é para garantir que o seu nível de dados tem capacidade suficiente para processar a sua carga de trabalho regular após a ativação da falha.

## <a name="preventing-the-loss-of-critical-data"></a>Prevenção da perda de dados críticos

Devido à elevada latência de redes de área ampla, a cópia contínua utiliza um mecanismo de replicação assíncrono. A replicação assíncrona torna alguma perda de dados inevitável se ocorrer uma falha. No entanto, algumas aplicações podem não necessitar de perda de dados. Para proteger estas atualizações críticas, um desenvolvedor de aplicações pode ligar para o procedimento do sistema [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) imediatamente após a emissão. A `sp_wait_for_database_copy_sync` chamada bloqueia o fio de chamada até que a última transação cometida tenha sido transmitida para a base de dados secundária. No entanto, não aguarda que as transações transmitidas sejam reproduzidas e cometidas no secundário. `sp_wait_for_database_copy_sync`é vias para uma ligação de cópia contínua específica. Qualquer utilizador com os direitos de ligação à base de dados primária pode chamar este procedimento.

> [!NOTE]
> `sp_wait_for_database_copy_sync`evita a perda de dados após a falha, mas não garante a sincronização total para o acesso à leitura. O atraso causado `sp_wait_for_database_copy_sync` por uma chamada de procedimento pode ser significativo e depende da dimensão do registo de transações no momento da chamada.

## <a name="failover-groups-and-point-in-time-restore"></a>Grupos de failover e restauro ponto-a-tempo

Para obter informações sobre a utilização de restauro pontual com grupos failover, consulte [Point in Time Recovery (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="limitations-of-failover-groups"></a>Limitações de grupos de failover

Esteja atento às seguintes limitações:

- O grupo Failover não pode ser criado entre dois servidores ou instâncias nas mesmas regiões do Azure.
- O grupo failover não pode ser renomeado. Terá de apagar o grupo e recriá-lo com um nome diferente. 
- O renome da base de dados não é suportado por exemplos no grupo failover. Terá de eliminar temporariamente o grupo failover para poder renomear uma base de dados.

## <a name="programmatically-managing-failover-groups"></a>Gestão programática de grupos de failover

Como discutido anteriormente, grupos de auto-failover e geo-replicação ativa também podem ser geridos programáticamente usando o Azure PowerShell e a API REST. As tabelas seguintes descrevem o conjunto de comandos disponíveis. A geo-replicação ativa inclui um conjunto de APIs do Gestor de Recursos Azure para gestão, incluindo a Base de [Dados Azure SQL REST API](https://docs.microsoft.com/rest/api/sql/) e [os cmdlets Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Estas APIs exigem a utilização de grupos de recursos e apoiam a segurança baseada em funções (RBAC). Para obter mais informações sobre como implementar funções de acesso, consulte [o Controlo de Acesso baseado em Funções azure](../role-based-access-control/overview.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Gerir a falha da base de dados SQL com bases de dados únicas e piscinas elásticas

| Cmdlet | Descrição |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Este comando cria um grupo de failover e regista-o em servidores primários e secundários|
| [Remover-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Remove um grupo de failover do servidor |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Recupera a configuração de um grupo de failover |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Modifica a configuração de um grupo de failover |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Desencadeia falha de um grupo de failover para o servidor secundário |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Adiciona uma ou mais bases de dados a um grupo de falhas|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Gerir grupos de falhas na base de dados SQL com instâncias geridas

| Cmdlet | Descrição |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Este comando cria um grupo de failover e regista-o em casos primários e secundários|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Modifica a configuração de um grupo de failover|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Recupera a configuração de um grupo de failover|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Desencadeia a falha de um grupo de falhas para a instância secundária|
| [Remover-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Remove um grupo de failover|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Gerir a falha da base de dados SQL com bases de dados únicas e piscinas elásticas

| Comando | Descrição |
| --- | --- |
| [az sql failover-grupo criar](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Este comando cria um grupo de failover e regista-o em servidores primários e secundários|
| [az sql failover-grupo excluir](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Remove um grupo de failover do servidor |
| [az sql failover-grupo show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Recupera uma configuração de grupo de failover |
| [az sql failover-grupo atualização](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Modifica a configuração de um grupo de failover e/ou adiciona uma ou mais bases de dados a um grupo de failover|
| [az sql failover-grupo set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Desencadeia falha de um grupo de failover para o servidor secundário |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Gerir grupos de falhas na base de dados SQL com instâncias geridas

| Comando | Descrição |
| --- | --- |
| [az sql instância-failover-grupo criar](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | Este comando cria um grupo de failover e regista-o em casos primários e secundários |
| [az sql instância-failover-grupo atualização](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | Modifica a configuração de um grupo de failover|
| [az sql instância-failover-grupo show](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | Recupera a configuração de um grupo de failover|
| [az sql instância-failover-grupo set-primary](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | Desencadeia a falha de um grupo de falhas para a instância secundária|
| [az sql instância-failover-grupo excluir](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | Remove um grupo de failover |

* * *

> [!IMPORTANT]
> Para obter um script de amostra, consulte [Configure e failover um grupo failover para uma única base de dados](scripts/sql-database-add-single-db-to-failover-group-powershell.md).

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API: Gerir grupos de falhas na base de dados SQL com bases de dados individuais e reunidas

| API | Descrição |
| --- | --- |
| [Criar ou atualizar o Grupo failover](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Cria ou atualiza um grupo de failover |
| [Eliminar o Grupo Failover](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Remove um grupo de failover do servidor |
| [Failover (Planeado)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Dispara falha do servidor primário atual para o servidor secundário com sincronização completa de dados.|
| [Falha de força permite a perda de dados](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Dispara falha do servidor primário atual para o servidor secundário sem sincronizar dados. Esta operação pode resultar em perda de dados. |
| [Obter Grupo Failover](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Recupera a configuração de um grupo de falhas. |
| [Lista de grupos de failover por servidor](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Lista os grupos de failover num servidor. |
| [Atualizar Failover Group](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Atualiza a configuração de um grupo de falhas. |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>REST API: Gerir grupos de failover com instâncias geridas

| API | Descrição |
| --- | --- |
| [Criar ou atualizar o Grupo failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Cria ou atualiza a configuração de um grupo de failover |
| [Eliminar o Grupo Failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Remove um grupo de failover da instância |
| [Failover (Planeado)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Desencadeia falhas desde a instância primária atual até este caso com sincronização completa de dados. |
| [Falha de força permite a perda de dados](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Desencadeia a falha da instância primária atual para a instância secundária sem sincronizar os dados. Esta operação pode resultar em perda de dados. |
| [Obter Grupo Failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | recupera a configuração de um grupo de failover. |
| [Lista de Grupos failover - Lista por localização](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Lista os grupos de failover num local. |

## <a name="next-steps"></a>Passos seguintes

- Para tutoriais detalhados, consulte
    - [Adicione uma única base de dados a um grupo de failover](sql-database-single-database-failover-group-tutorial.md)
    - [Adicionar um conjunto elástico a um grupo de ativação pós-falha](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Adicione uma instância gerida a um grupo de failover](sql-database-managed-instance-failover-group-tutorial.md)
- Para scripts de amostra, consulte:
  - [Utilize o PowerShell para configurar a geo-replicação ativa para uma única base de dados na Base de Dados Azure SQL](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Use powerShell para configurar geo-replicação ativa para uma base de dados pooled em Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Use o PowerShell para adicionar uma base de dados única Azure SQL a um grupo de failover](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- Para uma visão geral da continuidade do negócio e cenários, consulte a [visão geral da continuidade do Negócio](sql-database-business-continuity.md)
- Para saber sobre cópias de segurança automatizadas da Base de Dados Azure SQL, consulte cópias de segurança automatizadas da Base de [Dados SQL](sql-database-automated-backups.md).
- Para aprender a utilizar cópias de segurança automatizadas para recuperação, consulte [Restaurar uma base de dados das cópias de segurança iniciadas](sql-database-recovery-using-backups.md)pelo serviço .
- Para conhecer os requisitos de autenticação de um novo servidor primário e base de dados, consulte a segurança da Base de [Dados SQL após](sql-database-geo-replication-security-config.md)a recuperação de desastres .
