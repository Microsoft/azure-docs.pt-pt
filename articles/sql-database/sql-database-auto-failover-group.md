---
title: Grupos de failover-banco de dados SQL do Azure | Microsoft Docs
description: Grupos de failover automático são um recurso de banco de dados SQL que permite que você gerencie a replicação e o failover automático/coordenado de um grupo de bancos de dados em um servidor de banco de dados SQL ou em todos os bancos na instância gerenciada.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 10/09/2019
ms.openlocfilehash: b876fba2ae10c4f8b973ad1bb0c98bfa95c7f481
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249320"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Usar grupos de failover automático para habilitar o failover transparente e coordenado de vários bancos de dados

Os grupos de failover automático são um recurso de banco de dados SQL que permite que você gerencie a replicação e o failover de um grupo de bancos de dados em um servidor de banco de dados SQL ou de todos os bancos em uma instância gerenciada para outra região. É uma abstração declarativa sobre o recurso de [replicação geográfica ativa](sql-database-active-geo-replication.md) existente, projetado para simplificar a implantação e o gerenciamento de bancos de dados replicados geograficamente em escala. Você pode iniciar o failover manualmente ou pode delegá-lo ao serviço do banco de dados SQL com base em uma política definida pelo usuário. A última opção permite que você recupere automaticamente vários bancos de dados relacionados em uma região secundária após uma falha catastrófica ou outro evento não planejado que resulta em perda total ou parcial da disponibilidade do serviço de banco de dados SQL na região primária. Um grupo de failover pode incluir um ou vários bancos de dados, normalmente usados pelo mesmo aplicativo. Além disso, você pode usar os bancos de dados secundários legíveis para descarregar cargas de trabalho de consulta somente leitura. Como os grupos de failover automático envolvem vários bancos de dados, esses bancos de dados devem ser configurados no servidor primário. Os grupos de failover automático dão suporte à replicação de todos os bancos de dados no grupo para apenas um servidor secundário em uma região diferente.

> [!NOTE]
> Ao trabalhar com bancos de dados individuais ou em pool em um servidor de banco de dados SQL e você quiser vários secundários nas mesmas ou em regiões diferentes, use a [replicação geográfica ativa](sql-database-active-geo-replication.md). 

Quando você estiver usando grupos de failover automático com política de failover automático, qualquer interrupção que afete um ou vários bancos de dados no grupo resultará em failover automático. Normalmente, esses são incidentes que não podem ser autoatenuados pelas operações de alta disponibilidade automáticas internas. Os exemplos de gatilhos de failover incluem um incidente causado por um anel de locatário ou anel de controle do SQL que está sendo inativo devido a um vazamento de memória do kernel do sistema operacional em vários nós de computação ou a um incidente causado por um ou mais anéis de locatário sendo desativados porque um cabo de rede incorreto foi cortado durante ro descomissionamento de hardware utine.  Para obter mais informações, consulte [alta disponibilidade do banco de dados SQL](sql-database-high-availability.md).

Além disso, os grupos de failover automático fornecem pontos de extremidade de ouvinte de leitura/gravação e somente leitura que permanecem inalterados durante os failovers. Se você usar a ativação manual ou automática de failover, o failover alternará todos os bancos de dados secundários no grupo para primário. Após a conclusão do failover de banco de dados, o registro DNS é atualizado automaticamente para redirecionar os pontos de extremidade para a nova região. Para obter os dados de RPO e RTO específicos, consulte [visão geral da continuidade dos negócios](sql-database-business-continuity.md).

Quando você estiver usando grupos de failover automático com a política de failover automático, qualquer interrupção que afete os bancos de dados no servidor do banco de dados SQL ou na instância gerenciada resultará em failover automático. Você pode gerenciar o grupo de failover automático usando:

- O [portal do Azure](sql-database-implement-geo-distributed-database.md)
- [PowerShell: grupo de failover](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [API REST: grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups).

Após o failover, verifique se os requisitos de autenticação do servidor e do banco de dados estão configurados no novo primário. Para obter detalhes, consulte [segurança do banco de dados SQL após a recuperação de desastre](sql-database-geo-replication-security-config.md).

Para obter continuidade de negócios real, a adição de redundância de banco de dados entre data centers é apenas parte da solução. Recuperar um aplicativo (serviço) de ponta a ponta após uma falha catastrófica exige a recuperação de todos os componentes que constituem o serviço e quaisquer serviços dependentes. Exemplos desses componentes incluem o software cliente (por exemplo, um navegador com um JavaScript personalizado), front-ends da Web, armazenamento e DNS. É fundamental que todos os componentes sejam resilientes às mesmas falhas e fiquem disponíveis dentro do RTO (objetivo de tempo de recuperação) do seu aplicativo. Portanto, você precisa identificar todos os serviços dependentes e entender as garantias e os recursos que eles fornecem. Em seguida, você deve tomar as medidas adequadas para garantir que seu serviço funcione durante o failover dos serviços dos quais ela depende. Para obter mais informações sobre como criar soluções para recuperação de desastres, consulte [Projetando soluções de nuvem para recuperação de desastres usando a replicação geográfica ativa](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Recursos e terminologia do grupo de failover automático

- **Grupo de failover (neblina)**

  Um grupo de failover é um grupo nomeado de bancos de dados gerenciados por um único servidor de banco de dados SQL ou em uma única instância gerenciada que pode fazer failover como uma unidade para outra região caso todos ou alguns bancos de dados primários fiquem indisponíveis devido a uma interrupção na região primária. Quando criadas para instâncias gerenciadas, um grupo de failover contém todos os bancos de dados de usuário na instância e, portanto, apenas um grupo de failover pode ser configurado em uma instância do.
  
  > [!IMPORTANT]
  > O nome do grupo de failover deve ser globalmente exclusivo dentro do domínio `.database.windows.net`.

- **Servidores de banco de dados SQL**

     Com servidores de banco de dados SQL, alguns ou todos os bancos de dados de usuário em um único servidor de banco de dados SQL podem ser colocados em um grupo de failover. Além disso, um servidor de banco de dados SQL dá suporte a vários grupos de failover em um único servidor de banco de dados SQL.

- **Primary**

  O servidor de banco de dados SQL ou a instância gerenciada que hospeda os bancos dos dados primários no grupo de failover.

- **Secundário**

  O servidor de banco de dados SQL ou a instância gerenciada que hospeda os bancos dos dados secundários no grupo de failover. O secundário não pode estar na mesma região que o primário.

- **Adicionando bancos de dados individuais ao grupo de failover**

  Você pode colocar vários bancos de dados individuais no mesmo servidor de banco de dados SQL no mesmo grupo de failover. Se você adicionar um banco de dados individual ao grupo de failover, ele criará automaticamente um banco de dados secundário usando a mesma edição e o mesmo tamanho de computação no servidor secundário.  Você especificou esse servidor quando o grupo de failover foi criado. Se você adicionar um banco de dados que já tem um banco de dados secundário no servidor secundário, esse link de replicação geográfica será herdado pelo grupo. Quando você adiciona um banco de dados que já tem um banco de dados secundário em um servidor que não faz parte do grupo de failover, um novo secundário é criado no servidor secundário.
  
  > [!IMPORTANT]
  > Em uma instância gerenciada, todos os bancos de dados de usuário são replicados. Você não pode escolher um subconjunto de bancos de dados de usuário para replicação no grupo de failover.

- **Adicionando bancos de dados no pool elástico ao grupo de failover**

  Você pode colocar todos ou vários bancos de dados em um pool elástico no mesmo grupo de failover. Se o banco de dados primário estiver em um pool elástico, o secundário será criado automaticamente no pool elástico com o mesmo nome (pool secundário). Você deve garantir que o servidor secundário contenha um pool elástico com o mesmo nome exato e capacidade livre suficiente para hospedar os bancos de dados secundários que serão criados pelo grupo de failover. Se você adicionar um banco de dados no pool que já tem um banco de dados secundário no pool secundário, esse link de replicação geográfica será herdado pelo grupo. Quando você adiciona um banco de dados que já tem um banco de dados secundário em um servidor que não faz parte do grupo de failover, um novo secundário é criado no pool secundário.
  
- **Zona DNS**

  Uma ID exclusiva que é gerada automaticamente quando uma nova instância é criada. Um certificado de vários domínios (SAN) para essa instância é provisionado para autenticar as conexões de cliente com qualquer instância na mesma zona DNS. As duas instâncias gerenciadas no mesmo grupo de failover devem compartilhar a zona DNS. 
  
  > [!NOTE]
  > Uma ID de zona DNS não é necessária para grupos de failover criados para servidores de banco de dados SQL.

- **Ouvinte de leitura/gravação do grupo de failover**

  Um registro DNS CNAME que aponta para a URL primária atual. Ele é criado automaticamente quando o grupo de failover é criado e permite que a carga de trabalho de leitura/gravação do SQL Reconecte-se de forma transparente ao banco de dados primário quando o primário é alterado após o failover. Quando o grupo de failover é criado em um servidor de banco de dados SQL, o registro DNS CNAME da URL do ouvinte é formado como `<fog-name>.database.windows.net`. Quando o grupo de failover é criado em uma instância gerenciada, o registro DNS CNAME para a URL do ouvinte é formado como `<fog-name>.zone_id.database.windows.net`.

- **Ouvinte somente leitura do grupo de failover**

  Um registro DNS CNAME formado que aponta para o ouvinte somente leitura que aponta para a URL secundária. Ele é criado automaticamente quando o grupo de failover é criado e permite que a carga de trabalho SQL somente leitura se conecte de forma transparente ao secundário usando as regras de balanceamento de carga especificadas. Quando o grupo de failover é criado em um servidor de banco de dados SQL, o registro DNS CNAME da URL do ouvinte é formado como `<fog-name>.secondary.database.windows.net`. Quando o grupo de failover é criado em uma instância gerenciada, o registro DNS CNAME para a URL do ouvinte é formado como `<fog-name>.zone_id.secondary.database.windows.net`.

- **Política de failover automático**

  Por padrão, um grupo de failover é configurado com uma política de failover automático. O serviço do banco de dados SQL dispara o failover depois que a falha é detectada e o período de carência expirou. O sistema deve verificar se a interrupção não pode ser mitigada pela [infraestrutura interna de alta disponibilidade do serviço do banco de dados SQL](sql-database-high-availability.md) devido à escala do impacto. Se você quiser controlar o fluxo de trabalho de failover do aplicativo, poderá desativar o failover automático.

- **Política de failover somente leitura**

  Por padrão, o failover do ouvinte somente leitura está desabilitado. Ele garante que o desempenho do primário não seja afetado quando o secundário estiver offline. No entanto, isso também significa que as sessões somente leitura não serão capazes de se conectar até que o secundário seja recuperado. Se você não puder tolerar o tempo de inatividade para as sessões somente leitura e estiver OK para usar temporariamente o primário para tráfego somente leitura e de leitura/gravação às custas da degradação de desempenho potencial do primário, você poderá habilitar o failover para o ouvinte somente leitura Configurando a propriedade `AllowReadOnlyFailoverToPrimary`. Nesse caso, o tráfego somente leitura será redirecionado automaticamente para o primário se o secundário não estiver disponível.

- **Failover planejado**

   O failover planejado executa a sincronização completa entre bancos de dados primários e secundários antes dos comutadores secundários para a função primária. Isso não garante nenhuma perda de dados. O failover planejado é usado nos seguintes cenários:

  - Executar análises de DR (recuperação de desastre) em produção quando a perda de dados não for aceitável
  - Realocar os bancos de dados para uma região diferente
  - Retornar os bancos de dados para a região primária após a interrupção ser mitigada (failback).

- **Failover não planejado**

   O failover não planejado ou forçado alterna imediatamente o secundário para a função primária sem nenhuma sincronização com o primário. Esta operação resultará em perda de dados. O failover não planejado é usado como um método de recuperação durante interrupções quando o primário não está acessível. Quando o primário original estiver online novamente, ele será reconectado automaticamente sem sincronização e se tornará um novo secundário.

- **Failover manual**

  Você pode iniciar o failover manualmente a qualquer momento, independentemente da configuração de failover automático. Se a política de failover automático não estiver configurada, o failover manual será necessário para recuperar bancos de dados no grupo de failover para o secundário. Você pode iniciar um failover forçado ou amigável (com sincronização de dados completa). O último pode ser usado para realocar o primário para a região secundária. Quando o failover é concluído, os registros DNS são atualizados automaticamente para garantir a conectividade com o novo primário

- **Período de carência com perda de dados**

  Como os bancos de dados primários e secundários são sincronizados usando a replicação assíncrona, o failover pode resultar em perda de dados. Você pode personalizar a política de failover automático para refletir a tolerância do seu aplicativo à perda de dados. Ao configurar `GracePeriodWithDataLossHours`, você pode controlar quanto tempo o sistema aguarda antes de iniciar o failover que provavelmente resultará em perda de dados.

- **Vários grupos de failover**

  Você pode configurar vários grupos de failover para o mesmo par de servidores para controlar a escala de failovers. Cada grupo falha de forma independente. Se o aplicativo multilocatário usar pools elásticos, você poderá usar esse recurso para misturar bancos de dados primários e secundários em cada pool. Dessa forma, você pode reduzir o impacto de uma interrupção para apenas metade dos locatários.

  > [!NOTE]
  > Instância Gerenciada não dá suporte a vários grupos de failover.
  
## <a name="permissions"></a>Permissões
As permissões para um grupo de failover são gerenciadas por [RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md). A função [colaborador de SQL Server](../role-based-access-control/built-in-roles.md#sql-server-contributor) tem todas as permissões necessárias para gerenciar grupos de failover. 

### <a name="create-failover-group"></a>Criar grupo de failover
Para criar um grupo de failover, você precisa de acesso de gravação de RBAC para os servidores primários e secundários e para todos os bancos de dados no grupo de failover. Para uma instância gerenciada, você precisa de acesso de gravação de RBAC para a instância gerenciada primária e secundária, mas as permissões em bancos de dados individuais não são relevantes, pois bancos de dados individuais de instância gerenciada não podem ser adicionados ou removidos de um grupo de failover. 

### <a name="update-a-failover-group"></a>Atualizar um grupo de failover
Para atualizar um grupo de failover, você precisa de acesso de gravação do RBAC para o grupo de failover e de todos os bancos de dados no servidor primário atual ou instância gerenciada.  

### <a name="failover-a-failover-group"></a>Failover de um grupo de failover
Para fazer failover de um grupo de failover, você precisa de acesso de gravação de RBAC ao grupo de failover no novo servidor primário ou instância gerenciada. 

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Práticas recomendadas de uso de grupos de failover com bancos de dados individuais e pools elásticos

O grupo de failover automático deve ser configurado no servidor do banco de dados SQL primário e o conectará ao servidor do banco de dados SQL secundário em uma região do Azure diferente. Os grupos podem incluir todos ou alguns bancos de dados nesses servidores. O diagrama a seguir ilustra uma configuração típica de um aplicativo de nuvem com redundância geográfica usando vários bancos de dados e o grupo de failover automático.

![failover automático](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> Consulte [Adicionar um único banco de dados a um grupo de failover](sql-database-single-database-failover-group-tutorial.md) para obter um tutorial passo a passo detalhado adicionando um banco de dados individual a um grupo de failover. 


Ao projetar um serviço com a continuidade dos negócios em mente, siga estas diretrizes gerais:

- **Usar um ou vários grupos de failover para gerenciar o failover de vários bancos de dados**

  Um ou vários grupos de failover podem ser criados entre dois servidores em regiões diferentes (servidores primários e secundários). Cada grupo pode incluir um ou vários bancos de dados que são recuperados como uma unidade no caso de todos ou de alguns bancos de dados primários ficarem indisponíveis devido a uma interrupção na região primária. O grupo de failover cria um banco de dados geográfico secundário com o mesmo objetivo de serviço que o primário. Se você adicionar uma relação de replicação geográfica existente ao grupo de failover, verifique se o secundário geográfico está configurado com a mesma camada de serviço e o mesmo tamanho de computação que o primário.

- **Usar ouvinte de leitura/gravação para carga de trabalho OLTP**

  Ao executar operações OLTP, use `<fog-name>.database.windows.net` como a URL do servidor e as conexões serão direcionadas automaticamente para o primário. Essa URL não é alterada após o failover. Observe que o failover envolve a atualização do registro DNS para que as conexões de cliente sejam redirecionadas para o novo primário somente depois que o cache DNS do cliente for atualizado.

- **Usar ouvinte somente leitura para carga de trabalho somente leitura**

  Se você tiver uma carga de trabalho somente leitura logicamente isolada que seja tolerante a determinada desatualização de dados, poderá usar o banco de dado secundário no aplicativo. Para sessões somente leitura, use `<fog-name>.secondary.database.windows.net` como a URL do servidor e a conexão será direcionada automaticamente para o secundário. Também é recomendável que você indique na tentativa de leitura da cadeia de conexão usando `ApplicationIntent=ReadOnly`. Se você quiser garantir que a carga de trabalho somente leitura possa se reconectar após o failover ou, caso o servidor secundário fique offline, certifique-se de configurar a propriedade `AllowReadOnlyFailoverToPrimary` da política de failover. 

- **Estar preparado para degradação de desempenho**

  A decisão de failover do SQL é independente do restante do aplicativo ou de outros serviços usados. O aplicativo pode ser "misto" com alguns componentes em uma região e outros em outro. Para evitar a degradação, garanta a implantação de aplicativo redundante na região de DR e siga estas [diretrizes de segurança de rede](#failover-groups-and-network-security).

  > [!NOTE]
  > O aplicativo na região de DR não precisa usar uma cadeia de conexão diferente.  

- **Preparar-se para perda de dados**

  Se uma interrupção for detectada, o SQL aguardará o período especificado por `GracePeriodWithDataLossHours`. O valor padrão é 1 hora. Se você não puder perder dados, certifique-se de definir `GracePeriodWithDataLossHours` para um número suficientemente grande, como 24 horas. Use o failover de grupo manual para fazer failback do secundário para o primário.

  > [!IMPORTANT]
  > Pools elásticos com 800 ou menos DTUs e mais de 250 bancos de dados usando a replicação geográfica podem encontrar problemas, incluindo failovers planejados mais longos e desempenho degradado.  Esses problemas têm maior probabilidade de ocorrer para cargas de trabalho com uso intensivo de gravação, quando os pontos de extremidade de replicação geográfica são amplamente separados por geografia ou quando vários pontos de extremidade secundários são usados para cada banco de dados.  Os sintomas desses problemas são indicados quando o retardo de replicação geográfica aumenta ao longo do tempo.  Essa latência pode ser monitorada usando [Sys. dm _geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Se esses problemas ocorrerem, as atenuações incluem o aumento do número de DTUs de pool ou a redução do número de bancos de dados replicados geograficamente no mesmo pool.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Práticas recomendadas de uso de grupos de failover com instâncias gerenciadas

O grupo de failover automático deve ser configurado na instância primária e será conectado à instância secundária em uma região do Azure diferente.  Todos os bancos de dados na instância serão replicados para a instância secundária. 

O diagrama a seguir ilustra uma configuração típica de um aplicativo de nuvem com redundância geográfica usando a instância gerenciada e o grupo de failover automático.

![failover automático](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> Consulte [Adicionar instância gerenciada a um grupo de failover](sql-database-managed-instance-failover-group-tutorial.md) para obter um tutorial passo a passo detalhado adicionando uma instância gerenciada para usar o grupo de failover. 

Se seu aplicativo usar a instância gerenciada como a camada de dados, siga estas diretrizes gerais ao projetar para continuidade dos negócios:

- **Criar a instância secundária na mesma zona DNS que a instância primária**

  Para garantir a conectividade não interrompida com a instância primária após o failover, as instâncias primária e secundária devem estar na mesma zona DNS. Ele garantirá que o mesmo certificado de vários domínios (SAN) possa ser usado para autenticar as conexões do cliente com uma das duas instâncias no grupo de failover. Quando seu aplicativo estiver pronto para implantação de produção, crie uma instância secundária em uma região diferente e verifique se ela compartilha a zona DNS com a instância primária. Você pode fazer isso especificando um parâmetro opcional `DNS Zone Partner` usando o portal do Azure, o PowerShell ou a API REST. 

> [!IMPORTANT]
> A primeira instância criada na sub-rede determina a zona DNS para todas as instâncias subsequentes na mesma sub-rede. Isso significa que duas instâncias da mesma sub-rede não podem pertencer a diferentes zonas DNS.   

  Para obter mais informações sobre como criar a instância secundária na mesma zona DNS que a instância primária, consulte [criar uma instância gerenciada secundária](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance).

- **Habilitar o tráfego de replicação entre duas instâncias**

  Como cada instância é isolada em sua própria VNet, o tráfego bidirecional entre esses VNets deve ser permitido. Consulte [Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- **Criar um grupo de failover entre instâncias gerenciadas em assinaturas diferentes**

  Você pode criar um grupo de failover entre instâncias gerenciadas em duas assinaturas diferentes. Ao usar a API do PowerShell, você pode fazer isso especificando o parâmetro `PartnerSubscriptionId` para a instância secundária. Ao usar a API REST, cada ID de instância incluída no parâmetro `properties.managedInstancePairs` pode ter sua própria SubscriptionId. 
  
  > [!IMPORTANT]
  > O portal do Azure não oferece suporte a grupos de failover em assinaturas diferentes.

  
- **Configurar um grupo de failover para gerenciar o failover de toda a instância**

  O grupo de failover gerenciará o failover de todos os bancos de dados na instância do. Quando um grupo é criado, cada banco de dados na instância será replicado geograficamente automaticamente para a instância secundária. Você não pode usar grupos de failover para iniciar um failover parcial de um subconjunto dos bancos de dados.

  > [!IMPORTANT]
  > Se um banco de dados for removido da instância primária, ele também será descartado automaticamente na instância secundária geográfica.

- **Usar ouvinte de leitura/gravação para carga de trabalho OLTP**

  Ao executar operações OLTP, use `<fog-name>.zone_id.database.windows.net` como a URL do servidor e as conexões serão direcionadas automaticamente para o primário. Essa URL não é alterada após o failover. O failover envolve a atualização do registro DNS, portanto, as conexões de cliente são redirecionadas para o novo primário somente depois que o cache DNS do cliente é atualizado. Como a instância secundária compartilha a zona DNS com o primário, o aplicativo cliente poderá se reconectar a ela usando o mesmo certificado de SAN.

- **Conectar-se diretamente a um secundário replicado geograficamente para consultas somente leitura**

  Se você tiver uma carga de trabalho somente leitura logicamente isolada que seja tolerante a determinada desatualização de dados, poderá usar o banco de dado secundário no aplicativo. Para se conectar diretamente ao secundário replicado geograficamente, use `server.secondary.zone_id.database.windows.net` como a URL do servidor e a conexão é feita diretamente para o secundário replicado geograficamente.

  > [!NOTE]
  > Em determinadas camadas de serviço, o banco de dados SQL do Azure dá suporte ao uso de [réplicas somente leitura](sql-database-read-scale-out.md) para balancear a carga de cargas de trabalho de consulta somente leitura usando a capacidade de uma réplica somente leitura e usando o parâmetro `ApplicationIntent=ReadOnly` na cadeia de conexão. Quando você tiver configurado um secundário replicado geograficamente, poderá usar essa capacidade para se conectar a uma réplica somente leitura no local primário ou no local com replicação geográfica.
  > - Para se conectar a uma réplica somente leitura no local principal, use `<fog-name>.zone_id.database.windows.net`.
  > - Para se conectar a uma réplica somente leitura no local secundário, use `<fog-name>.secondary.zone_id.database.windows.net`.

- **Estar preparado para degradação de desempenho**

  A decisão de failover do SQL é independente do restante do aplicativo ou de outros serviços usados. O aplicativo pode ser "misto" com alguns componentes em uma região e outros em outro. Para evitar a degradação, garanta a implantação de aplicativo redundante na região de DR e siga estas [diretrizes de segurança de rede](#failover-groups-and-network-security).

- **Preparar-se para perda de dados**

  Se uma interrupção for detectada, o SQL disparará o failover de leitura-gravação automaticamente se não houver nenhuma perda de dados para o melhor de nosso conhecimento. Caso contrário, ele aguarda o período especificado por `GracePeriodWithDataLossHours`. Se você especificou `GracePeriodWithDataLossHours`, esteja preparado para perda de dados. Em geral, durante interrupções, o Azure favorece a disponibilidade. Se você não puder perder dados, certifique-se de definir GracePeriodWithDataLossHours como um número suficientemente grande, como 24 horas.

  A atualização de DNS do ouvinte de leitura/gravação ocorrerá imediatamente após o failover ser iniciado. Esta operação não resultará em perda de dados. No entanto, o processo de troca de funções de banco de dados pode levar até 5 minutos em condições normais. Até que ele seja concluído, alguns bancos de dados na nova instância primária ainda serão somente leitura. Se o failover for iniciado usando o PowerShell, toda a operação será síncrona. Se ele for iniciado usando o portal do Azure, a interface do usuário indicará o status de conclusão. Se ele for iniciado usando a API REST, use o mecanismo de sondagem do Azure Resource Manager padrão para monitorar a conclusão.

  > [!IMPORTANT]
  > Use o failover de grupo manual para mover os primários de volta para o local original. Quando a interrupção que causou o failover é atenuada, você pode mover seus bancos de dados primários para o local original. Para fazer isso, você deve iniciar o failover manual do grupo.

- **Reconhecer limitações conhecidas de grupos de failover**

  Renomeação de banco de dados e redimensionamento de instância não têm suporte para instâncias no grupo de failover. Você precisará excluir temporariamente o grupo de failover para poder realizar essas ações.

## <a name="failover-groups-and-network-security"></a>Segurança de rede e grupos de failover

Para alguns aplicativos, as regras de segurança exigem que o acesso à rede para a camada de dados seja restrito a um componente ou componentes específicos, como uma VM, um serviço Web, etc. Esse requisito apresenta alguns desafios para o design de continuidade de negócios e o uso dos grupos de failover. Considere as seguintes opções ao implementar esse acesso restrito.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Usando grupos de failover e regras de rede virtual

Se você estiver usando pontos de extremidade de [serviço de rede virtual e regras](sql-database-vnet-service-endpoint-rule-overview.md) para restringir o acesso ao banco de dados SQL, lembre-se de que cada ponto de extremidade de serviço de rede virtual se aplica a apenas uma região do Azure. O ponto de extremidade não permite que outras regiões aceitem comunicação da sub-rede. Portanto, somente os aplicativos cliente implantados na mesma região podem se conectar ao banco de dados primário. Como o failover resulta nas sessões do cliente SQL sendo redirecionadas para um servidor em uma região diferente (secundária), essas sessões falharão se forem originadas de um cliente fora dessa região. Por esse motivo, a política de failover automático não poderá ser habilitada se os servidores participantes estiverem incluídos nas regras de rede virtual. Para dar suporte ao failover manual, siga estas etapas:

1. Provisione as cópias redundantes dos componentes front-end do seu aplicativo (serviço Web, máquinas virtuais etc.) na região secundária
2. Configurar as [regras de rede virtual](sql-database-vnet-service-endpoint-rule-overview.md) individualmente para o servidor primário e secundário
3. Habilitar o [failover de front-end usando uma configuração do Gerenciador de tráfego](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Inicie o failover manual quando a interrupção for detectada. Essa opção é otimizada para os aplicativos que exigem latência consistente entre o front-end e a camada de dados e dá suporte à recuperação quando o front-end, a camada de dados ou ambos são afetados pela interrupção.

> [!NOTE]
> Se você estiver usando o **ouvinte somente leitura** para balancear a carga de uma carga de trabalho somente leitura, verifique se essa carga de trabalho é executada em uma VM ou em outro recurso na região secundária para que ela possa se conectar ao banco de dados secundário.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Usando grupos de failover e regras de firewall do banco de dados SQL

Se seu plano de continuidade de negócios exigir failover usando grupos com failover automático, você poderá restringir o acesso ao banco de dados SQL usando as regras de firewall tradicionais.  Para dar suporte ao failover automático, siga estas etapas:

1. [Criar um IP público](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Crie um balanceador de carga público](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) e ATRIBUA o IP público a ele.
3. [Criar uma rede virtual e as máquinas virtuais](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) para seus componentes de front-end
4. [Crie um grupo de segurança de rede](../virtual-network/security-overview.md) e configure conexões de entrada.
5. Verifique se as conexões de saída estão abertas para o banco de dados SQL do Azure usando a [marca de serviço](../virtual-network/security-overview.md#service-tags)' SQL '.
6. Crie uma [regra de firewall do banco de dados SQL](sql-database-firewall-configure.md) para permitir o tráfego de entrada do endereço IP público que você criou na etapa 1.

Para obter mais informações sobre como configurar o acesso de saída e qual IP usar nas regras de firewall, consulte [conexões de saída do balanceador de carga](../load-balancer/load-balancer-outbound-connections.md).

A configuração acima garantirá que o failover automático não bloqueie as conexões dos componentes front-end e assuma que o aplicativo pode tolerar a latência mais longa entre o front-end e a camada de dados.

> [!IMPORTANT]
> Para garantir a continuidade dos negócios para interrupções regionais, você deve garantir a redundância geográfica para os componentes front-end e os bancos de dados.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Habilitando a replicação geográfica entre instâncias gerenciadas e suas VNets

Quando você configura um grupo de failover entre instâncias gerenciadas primárias e secundárias em duas regiões diferentes, cada instância é isolada usando uma rede virtual independente. Para permitir o tráfego de replicação entre esses VNets, verifique se esses pré-requisitos foram atendidos:

1. As duas instâncias gerenciadas precisam estar em regiões diferentes do Azure.
1. As duas instâncias gerenciadas precisam ser da mesma camada de serviço e ter o mesmo tamanho de armazenamento. 
1. Sua instância gerenciada secundária deve estar vazia (nenhum banco de dados de usuário).
1. As redes virtuais usadas pelas instâncias gerenciadas precisam ser conectadas por meio de um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou de uma rota expressa. Quando duas redes virtuais se conectam por meio de uma rede local, verifique se não há nenhuma regra de firewall bloqueando as portas 5022 e 11000-11999. O emparelhamento VNet global não tem suporte.
1. Os dois VNets de instância gerenciada não podem ter endereços IP sobrepostos.
1. Você precisa configurar seus NSG (grupos de segurança de rede) de modo que as portas 5022 e o intervalo 11000 ~ 12000 sejam abertos de entrada e saída para conexões de outra sub-rede em instância gerenciada. Isso é para permitir o tráfego de replicação entre as instâncias

   > [!IMPORTANT]
   > Regras de segurança NSG mal configuradas levam a operações de cópia de banco de dados presas.

7. A instância secundária está configurada com a ID de zona DNS correta. A zona DNS é uma propriedade de uma instância gerenciada e sua ID é incluída no endereço de nome do host. A ID da zona é gerada como uma cadeia de caracteres aleatória quando a primeira instância gerenciada é criada em cada VNet e a mesma ID é atribuída a todas as outras instâncias na mesma sub-rede. Uma vez atribuída, a zona DNS não pode ser modificada. As instâncias gerenciadas incluídas no mesmo grupo de failover devem compartilhar a zona DNS. Isso é feito passando a ID da zona da instância primária como o valor do parâmetro DnsZonePartner ao criar a instância secundária. 

   > [!NOTE]
   > Para obter um tutorial detalhado sobre como configurar grupos de failover com a instância gerenciada, consulte [Adicionar uma instância gerenciada a um grupo de failover](sql-database-managed-instance-failover-group-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Atualizando ou fazendo downgrade de um banco de dados primário

Você pode atualizar ou fazer downgrade de um banco de dados primário para um tamanho de computação diferente (dentro da mesma camada de serviço, não entre Uso Geral e Comercialmente Crítico) sem desconectar nenhum banco de dados secundário. Ao atualizar, recomendamos que você atualize todos os bancos de dados secundários primeiro e, em seguida, atualize o primário. Ao fazer downgrade, inverta o pedido: faça o downgrade do primário primeiro e, em seguida, downgrade todos os bancos de dados secundários. Quando você atualiza ou faz downgrade do banco de dados para uma camada de serviço diferente, essa recomendação é imposta.

Essa sequência é recomendada especificamente para evitar o problema em que o secundário em um SKU inferior é sobrecarregado e deve ser propagado durante um processo de atualização ou de downgrade. Você também pode evitar o problema tornando o primário somente leitura, às custas de afetar todas as cargas de trabalho de leitura/gravação no primário. 

> [!NOTE]
> Se você criou o banco de dados secundário como parte da configuração do grupo de failover, não é recomendável fazer downgrade do banco de dados secundário. Isso é para garantir que a camada de dados tenha capacidade suficiente para processar a carga de trabalho regular após a ativação do failover.

## <a name="preventing-the-loss-of-critical-data"></a>Evitando a perda de dados críticos

Devido à alta latência de redes de longa distância, a cópia contínua usa um mecanismo de replicação assíncrona. A replicação assíncrona torna inevitável alguma perda de dados se ocorrer uma falha. No entanto, alguns aplicativos podem não exigir perda de dados. Para proteger essas atualizações críticas, um desenvolvedor de aplicativos pode chamar o procedimento do sistema [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) imediatamente após confirmar a transação. Chamar `sp_wait_for_database_copy_sync` bloqueia o thread de chamada até que a última transação confirmada seja transmitida para o banco de dados secundário. No entanto, ele não aguarda que as transações transmitidas sejam reproduzidas e confirmadas no secundário. `sp_wait_for_database_copy_sync` está no escopo de um link de cópia contínua específico. Qualquer usuário com os direitos de conexão para o banco de dados primário pode chamar este procedimento.

> [!NOTE]
> `sp_wait_for_database_copy_sync` impede a perda de dados após o failover, mas não garante a sincronização completa para acesso de leitura. O atraso causado por uma chamada de procedimento `sp_wait_for_database_copy_sync` pode ser significativo e depende do tamanho do log de transações no momento da chamada.

## <a name="failover-groups-and-point-in-time-restore"></a>Grupos de failover e restauração pontual

Para obter informações sobre como usar a restauração pontual com grupos de failover, consulte [PITR (recuperação pontual)](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="programmatically-managing-failover-groups"></a>Gerenciando programaticamente grupos de failover

Conforme discutido anteriormente, os grupos de failover automático e a replicação geográfica ativa também podem ser gerenciados programaticamente usando Azure PowerShell e a API REST. As tabelas a seguir descrevem o conjunto de comandos disponíveis. A replicação geográfica ativa inclui um conjunto de APIs de Azure Resource Manager para gerenciamento, incluindo a [API REST do banco de dados SQL do Azure](https://docs.microsoft.com/rest/api/sql/) e os [cmdlets Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Essas APIs exigem o uso de grupos de recursos e oferecem suporte a RBAC (segurança baseada em função). Para obter mais informações sobre como implementar funções de acesso, consulte [controle de acesso baseado em função do Azure](../role-based-access-control/overview.md).

### <a name="powershell-manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>PowerShell: gerenciar failover de banco de dados SQL com pools elásticos individuais

| Cmdlet | Descrição |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Este comando cria um grupo de failover e o registra em servidores primários e secundários|
| [Remove-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Remove o grupo de failover do servidor e exclui todos os bancos de dados secundários incluídos no grupo |
| [Get-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Recupera a configuração do grupo de failover |
| [Set-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Modifica a configuração do grupo de failover |
| [Switch-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Dispara o failover do grupo de failover para o servidor secundário |
| [Add-AzSqlDatabaseToFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Adiciona um ou mais bancos de dados a um grupo de failover do banco de dados SQL do Azure|
|  | |

> [!IMPORTANT]
> Para obter um script de exemplo, consulte [configurar e fazer failover de um grupo de failover para um único banco de dados](scripts/sql-database-add-single-db-to-failover-group-powershell.md).
>

### <a name="powershell-managing-sql-database-failover-groups-with-managed-instances"></a>PowerShell: Gerenciando grupos de failover do banco de dados SQL com instâncias gerenciadas 

| Cmdlet | Descrição |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Este comando cria um grupo de failover e o registra em servidores primários e secundários|
| [Set-AzSqlDatabaseInstanceFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Modifica a configuração do grupo de failover|
| [Get-AzSqlDatabaseInstanceFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Recupera a configuração do grupo de failover|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Dispara o failover do grupo de failover para o servidor secundário|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Remove um grupo de failover|
|  | |

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>API REST: gerenciar grupos de failover do banco de dados SQL com bancos de dados únicos e em pool

| API | Descrição |
| --- | --- |
| [Criar ou atualizar grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Criar ou atualizar um grupo de failover |
| [Excluir grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Remove o grupo de failover do servidor |
| [Failover (planejado)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Faz failover do servidor primário atual para este servidor. |
| [Forçar failover permitir perda de dados](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |aflige o servidor primário atual para este servidor. Essa operação pode resultar em perda de dados. |
| [Obter grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Obtém um grupo de failover. |
| [Listar grupos de failover por servidor](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Lista os grupos de failover em um servidor. |
| [Atualizar grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Atualiza um grupo de failover. |
|  | |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>API REST: gerenciar grupos de failover com instâncias gerenciadas

| API | Descrição |
| --- | --- |
| [Criar ou atualizar grupo de failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Criar ou atualizar um grupo de failover |
| [Excluir grupo de failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Remove o grupo de failover do servidor |
| [Failover (planejado)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Faz failover do servidor primário atual para este servidor. |
| [Forçar failover permitir perda de dados](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |aflige o servidor primário atual para este servidor. Essa operação pode resultar em perda de dados. |
| [Obter grupo de failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | Obtém um grupo de failover. |
| [Listar grupos de failover-listar por local](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Lista os grupos de failover em um local. |

## <a name="next-steps"></a>Passos seguintes

- Para obter tutoriais detalhados, consulte
    - [Adicionar um banco de dados individual a um grupo de failover](sql-database-single-database-failover-group-tutorial.md)
    - [Adicionar pool elástico a um grupo de failover](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Adicionar uma instância gerenciada a um grupo de failover](sql-database-managed-instance-failover-group-tutorial.md)
- Para scripts de exemplo, consulte:
  - [Usar o PowerShell para configurar a replicação geográfica ativa para um banco de dados individual no banco de dados SQL do Azure](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Usar o PowerShell para configurar a replicação geográfica ativa para um banco de dados em pool no banco de dados SQL do Azure](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Usar o PowerShell para adicionar um banco de dados individual do banco de dados SQL do Azure a um grupo de failover](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- Para uma visão geral e cenários de continuidade de negócios, consulte [visão geral da continuidade de negócios](sql-database-business-continuity.md)
- Para saber mais sobre backups automatizados do banco [de dados SQL](sql-database-automated-backups.md)
- Para saber mais sobre como usar backups automatizados para recuperação, consulte [restaurar um banco de dados dos backups iniciados pelo serviço](sql-database-recovery-using-backups.md).
- Para saber mais sobre os requisitos de autenticação para um novo servidor primário e banco de dados, consulte [segurança do banco de dados SQL após a recuperação de desastre](sql-database-geo-replication-security-config.md).
