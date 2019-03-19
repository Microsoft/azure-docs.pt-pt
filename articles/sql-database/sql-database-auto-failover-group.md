---
title: Grupos de ativação pós-falha - base de dados SQL do Azure | Documentos da Microsoft
description: Grupos de ativação pós-falha automática é uma funcionalidade de base de dados SQL que lhe permite gerir a replicação e ativação pós-falha automática / coordenada de um grupo de bases de dados num servidor de base de dados SQL ou todas as bases de dados na instância gerida.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 7bfed1144ebfc69ed51b7bbc1adf78538ed28425
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861082"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Utilizar grupos de ativação pós-falha automática para ativar a ativação pós-falha transparente e coordenada de várias bases de dados

Grupos de ativação pós-falha automática é uma funcionalidade de base de dados SQL permite-lhe gerir a replicação e ativação pós-falha de um grupo de bases de dados num servidor de base de dados SQL ou todas as bases de dados numa instância gerida para outra região (atualmente em pré-visualização pública para a instância gerida). Ele usa a mesma tecnologia subjacente [georreplicação ativa](sql-database-active-geo-replication.md). Pode iniciar a ativação pós-falha manualmente ou pode delegá-lo para o serviço de base de dados SQL com base numa política definida pelo utilizador. A última opção permite-lhe recuperar automaticamente de várias bases de dados relacionados numa região secundária, após uma falha catastrófica ou outro evento não planeado, que resulta em perda total ou parcial de disponibilidade do serviço de base de dados SQL na região primária. Além disso, pode utilizar as bases de dados secundárias legíveis para a descarga de cargas de trabalho de consulta só de leitura. Uma vez que os grupos de ativação pós-falha automática envolvem várias bases de dados, esses bancos de dados tem de ser configurados no servidor primário. Os servidores primários e secundários para as bases de dados no grupo de ativação pós-falha têm de estar na mesma subscrição. Grupos de ativação pós-falha automática suportam a replicação de todas as bases de dados no grupo de apenas um servidor secundário numa região diferente.

> [!NOTE]
> Ao trabalhar com bancos de dados individuais ou agrupados num servidor de base de dados SQL e quiser que várias bases de dados secundárias nas regiões idêntica ou diferentes, utilize [georreplicação ativa](sql-database-active-geo-replication.md).

Quando estiver a utilizar grupos de ativação pós-falha automática com a política de ativação pós-falha automática, qualquer falha que afeta uma ou várias das bases de dados os resultados de grupo na ativação pós-falha automática. Além disso, os grupos de ativação pós-falha automática fornecem leitura / escrita e pontos finais de serviço de escuta só de leitura que permanecem inalterados durante as ativações pós-falha. Se usa a ativação de ativação pós-falha manual ou automática, ativação pós-falha muda todas as bases de dados secundárias no grupo principal. Depois de concluída a ativação pós-falha de base de dados, o registo DNS é atualizado automaticamente para redirecionar os pontos finais para a nova região. Para os dados RPO e RTO específicos, consulte [descrição geral da continuidade do negócio](sql-database-business-continuity.md).

Quando estiver a utilizar grupos de ativação pós-falha automática com a política de ativação pós-falha automática, qualquer falha que afeta as bases de dados no servidor de base de dados SQL ou geridos resultados de instância na ativação pós-falha automática. Pode gerir através do grupo de ativação pós-falha automática:

- O [portal do Azure](sql-database-implement-geo-distributed-database.md)
- [PowerShell: Grupo de ativação pós-falha](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [REST API: Grupo de ativação pós-falha](https://docs.microsoft.com/rest/api/sql/failovergroups).

Após a ativação pós-falha, certifique-se de que os requisitos de autenticação para o seu servidor e base de dados são configurados na nova principal. Para obter detalhes, consulte [segurança de base de dados SQL após a recuperação após desastre](sql-database-geo-replication-security-config.md).

Para alcançar a continuidade do negócio real, a adição de redundância da base de dados entre datacenters é apenas uma parte da solução. Recuperação de uma aplicativo (serviço)-a-ponto após uma falha catastrófica requer a recuperação de todos os componentes que constituem o serviço e quaisquer serviços dependentes. Exemplos desses componentes incluem o software de cliente (por exemplo, um navegador com um JavaScript personalizada), o front-ends web, o armazenamento e o DNS. É fundamental que todos os componentes são resilientes a falhas do mesmo e fiquem disponíveis dentro de objetivo de tempo de recuperação (RTO) da sua aplicação. Portanto, precisa identificar todos os serviços dependentes e compreender as garantias e capacidades que fornecem. Em seguida, tem de seguir passos adequados para garantir que as suas funções de serviço durante a ativação pós-falha dos serviços do qual depende. Para obter mais informações sobre as soluções de conceção para recuperação após desastre, veja [conceber soluções de Cloud para recuperação de desastres usando replicação geográfica activa](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Terminologia do grupo de ativação pós-falha automática e capacidades

- **Grupo de ativação pós-falha**

  Um grupo de ativação pós-falha é um grupo de bases de dados geridas por um único servidor de base de dados SQL ou dentro de uma única instância gerida que pode efetuar a ativação pós-falha como uma unidade para outra região no caso de todos ou alguns bases de dados primárias ficam indisponíveis devido a uma falha na região primária.

  - **Servidores de base de dados SQL**

     Com os servidores de base de dados SQL, algumas ou todas as bases de dados do usuário num único servidor de base de dados SQL podem ser colocadas num grupo de ativação pós-falha. Além disso, um servidor de base de dados SQL suporta vários grupos de ativação pós-falha num único servidor de base de dados SQL.

  - **Instâncias geridas**
  
     Com a instância gerida, um grupo de ativação pós-falha contém todas as bases de dados de utilizador na instância gerida e, portanto, uma instância gerida suporta apenas um grupo de ativação pós-falha único.

- **Primário**

  O servidor de base de dados SQL ou a instância gerida que aloja as bases de dados primárias do grupo de ativação pós-falha.

- **Secundário**

  O servidor de base de dados SQL ou a instância gerida que aloja as bases de dados secundárias no grupo de ativação pós-falha. Secundário não pode estar na mesma região que o primário.

- **Adicionar bases de dados individuais ao grupo de ativação pós-falha**

  Pode colocar várias bases de dados individuais no mesmo servidor de base de dados SQL no mesmo grupo de ativação pós-falha. Se adicionar uma base de dados para o grupo de ativação pós-falha, este cria automaticamente uma base de dados secundária com o mesmo tamanho de edição e de computação no servidor secundário.  Esse servidor que especificou quando foi criado o grupo de ativação pós-falha. Se adicionar uma base de dados que já tenha uma base de dados secundária no servidor secundário, essa ligação de georreplicação é herdada pelo grupo. Quando adiciona uma base de dados que já tenha uma base de dados secundário num servidor que não faz parte do grupo de ativação pós-falha, um secundário novo é criado no servidor secundário.
  
> [!IMPORTANT]
  > Na instância gerida, todas as bases de dados do utilizador são replicadas. Não é possível escolher um subconjunto de bases de dados de utilizador para a replicação no grupo de ativação pós-falha.

- **Adicionar bases de dados num conjunto elástico ao grupo de ativação pós-falha**

  Pode colocar todas ou várias bases de dados dentro de um conjunto elástico no mesmo grupo de ativação pós-falha. Se a base de dados primária num conjunto elástico, secundário é criado automaticamente no conjunto elástico com o mesmo nome (conjunto secundário). Tem de se certificar de que o servidor secundário contém um conjunto elástico com o mesmo nome exato e a capacidade livre suficiente para alojar as bases de dados secundários que vão ser criadas pelo grupo de ativação pós-falha. Se adicionar uma base de dados no conjunto que já tem uma base de dados secundária no agrupamento de secundário, essa ligação de georreplicação é herdada pelo grupo. Quando adiciona uma base de dados que já tenha uma base de dados secundário num servidor que não faz parte do grupo de ativação pós-falha, é criado um secundário novo no agrupamento de secundário.
  
  - **Serviço de escuta de leitura / escrita de grupo de ativação pós-falha**

  Um registo CNAME de DNS formado que aponta para o URL da principal atual. Ele permite que os aplicativos de SQL de leitura / escrita de forma transparente voltar a ligar à base de dados primária quando primário é alterado após a ativação pós-falha.

  - **Servidor de base de dados SQL registo CNAME de DNS para o serviço de escuta de leitura / escrita**

     Num servidor de base de dados SQL, o registo CNAME no DNS para o grupo de ativação pós-falha que aponta para URL o primário atual está formado como `failover-group-name.database.windows.net`.

  - **Gerido registo CNAME de DNS de instância para o serviço de escuta de leitura / escrita**

     Na instância gerida, o registo CNAME no DNS para o grupo de ativação pós-falha que aponta para URL o primário atual está formado como `failover-group-name.zone_id.database.windows.net`.

- **Escuta de só de leitura do grupo de ativação pós-falha**

  Um registo CNAME de DNS formado que aponta para o serviço de escuta só de leitura que aponta para URL o secundário. Ele permite que os aplicativos de SQL só de leitura ligar de forma transparente para o secundário com as regras de balanceamento de carga especificadas.

  - **Servidor de base de dados SQL registo CNAME de DNS para o serviço de escuta só de leitura**

     Num servidor de base de dados SQL, o registo CNAME no DNS para o serviço de escuta só de leitura que aponta para URL o secundário está formado como `failover-group-name.secondary.database.windows.net`.

  - **Gerido registo CNAME de DNS de instância para o serviço de escuta só de leitura**

     Na instância gerida, o registo CNAME no DNS para o serviço de escuta só de leitura que aponta para URL o secundário está formado como `failover-group-name.zone_id.database.windows.net`.

- **Política de ativação pós-falha automática**

  Por predefinição, um grupo de ativação pós-falha está configurado com uma política de ativação pós-falha automática. O serviço de base de dados SQL aciona a ativação pós-falha depois da falha é detetada e o período de tolerância expirou. O sistema tem de verificar que a falha não pode ser mitigada ao incorporada [infraestrutura de elevada disponibilidade do serviço de base de dados SQL](sql-database-high-availability.md) devido a escala do impacto. Se pretender controlar o fluxo de trabalho de ativação pós-falha da aplicação, pode desativar a ativação pós-falha automática.

- **Política de ativação pós-falha só de leitura**

  Por predefinição, a ativação pós-falha do serviço de escuta só de leitura está desativada. Ele garante que o desempenho dos principais não é afetado quando secundário está offline. No entanto, isso também significa que as sessões de só de leitura não será possível se conectar até que o elemento secundário for recuperado. Se não pode tolerar o período de indisponibilidade para as sessões de só de leitura e OK para usar temporariamente a principal para tráfego de só de leitura e leitura / escrita às custas da degradação do desempenho potencial dos principais, pode ativar a ativação pós-falha para o serviço de escuta só de leitura. Nesse caso o tráfego de só de leitura será automaticamente redirecionado para o primário se secundário não está disponível.

- **Ativação pós-falha planeada**

   Ativação pós-falha planeada executa uma sincronização completa entre bases de dados primárias e secundárias antes dos comutadores secundários para a função primária. Isso garante sem perda de dados. Ativação pós-falha planeada é utilizada nos seguintes cenários:

  - Executar testes de recuperação (DR) após desastre na produção, quando a perda de dados não é aceitável
  - Reposicionar as bases de dados para uma região diferente
  - Devolva as bases de dados para a região primária após a falha foi Atenuado (reativação pós-falha).

- **Ativação pós-falha não planeada**

   Ativação pós-falha não planeada ou forçada muda imediatamente o secundário para a função principal sem qualquer sincronização com o principal. Esta operação irá resultar na perda de dados. Ativação pós-falha não planeada é utilizada como um método de recuperação durante as falhas quando o principal não está acessível. Quando o original principal esteja novamente online, irá automaticamente voltar a ligar sem sincronização e se tornar um secundário novo.

- **Ativação pós-falha manual**

  Pode iniciar manualmente a ativação pós-falha a qualquer momento, independentemente da configuração de ativação pós-falha automática. Se não estiver configurada a política de ativação pós-falha automática, a ativação pós-falha manual é necessário para recuperar bases de dados no grupo de ativação pós-falha para o secundário. Pode iniciar a ativação pós-falha forçada ou amigável (com a sincronização de dados completa). O último pode ser utilizado para a alteração da localização primária para a região secundária. Quando concluir a ativação pós-falha, os registos DNS são automaticamente atualizados para garantir a conectividade com a nova principal

- **Período de tolerância de perda de dados**

  Uma vez que as bases de dados primárias e secundárias são sincronizadas com a replicação assíncrona, a ativação pós-falha pode resultar em perda de dados. Pode personalizar a política de ativação pós-falha automática para refletir a tolerância de seu aplicativo à perda de dados. Configurando **GracePeriodWithDataLossHours**, pode controlar o tempo que o sistema aguarda antes de iniciar a ativação pós-falha que é provável que a perda de dados de resultado.

- **Vários grupos de ativação pós-falha**

  Pode configurar vários grupos de ativação pós-falha para o mesmo par de servidores para controlar a escala de ativações pós-falha. Cada grupo faz a ativação pós-falha independentemente. Se a sua aplicação multi-inquilino utiliza conjuntos elásticos, pode utilizar esta capacidade misturar bases de dados primários e secundários em cada agrupamento. Dessa forma pode reduzir o impacto de uma indisponibilidade para apenas metade dos inquilinos.

  > [!IMPORTANT]
  > Instância gerida não suporta vários grupos de ativação pós-falha.
  
## <a name="permissions"></a>Permissões
Permissões para um grupo de ativação pós-falha são geridas através do [controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md). O [contribuinte do SQL Server](../role-based-access-control/built-in-roles.md#sql-server-contributor) função tem todas as permissões necessárias para gerir grupos de ativação pós-falha. 

### <a name="create-failover-group"></a>Criar grupo de ativação pós-falha
Para criar um grupo de ativação pós-falha, precisa de acesso de escrita do RBAC para ambos os servidores primários e secundários e para todas as bases de dados no grupo de ativação pós-falha. Para uma instância gerida, precisa de acesso de escrita do RBAC para a primária e secundária gerido instância, mas as permissões nas bases de dados individuais não são relevantes, uma vez que bases de dados individuais de instância gerida não podem ser adicionados ou removidos de um grupo de ativação pós-falha. 

### <a name="update-a-failover-group"></a>Atualizar um grupo de ativação pós-falha
Para atualizar um grupo de ativação pós-falha, terá de RBAC acesso de escrita para o grupo de ativação pós-falha e todas as bases de dados no servidor primário atual ou a instância gerida.  

### <a name="failover-a-failover-group"></a>Ativação pós-falha de um grupo de ativação pós-falha
Para efetuar a ativação pós-falha de um grupo de ativação pós-falha, precisa de acesso de escrita do RBAC para o grupo de ativação pós-falha no novo servidor primário ou instância gerida. 

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Melhores práticas de utilização de grupos de ativação pós-falha com bases de dados individuais e conjuntos elásticos

O grupo de ativação pós-falha automática tem de ser configurado no servidor de base de dados SQL principal e ligá-la para o servidor de base de dados SQL secundário numa região diferente do Azure.  Os grupos podem incluir todas ou algumas bases de dados nestes servidores. O diagrama seguinte ilustra uma configuração típica de um aplicativo de cloud com redundância geográfica usando várias bases de dados e o grupo de ativação pós-falha automática.

![ativação pós-falha automática](./media/sql-database-auto-failover-group/auto-failover-group.png)

Ao projetar um serviço com a continuidade do negócio em mente, siga estas Diretrizes gerais:

- **Utilizar um ou vários grupos de ativação pós-falha para gerir a ativação pós-falha de várias bases de dados**

  Um ou vários grupos de ativação pós-falha podem ser criados entre dois servidores em diferentes regiões (servidores primário e secundário). Cada grupo pode conter uma ou várias bases de dados que são recuperados como uma unidade no caso de todos ou alguns bases de dados primárias ficam indisponíveis devido a uma falha na região primária. O grupo de ativação pós-falha cria a base de dados geo-secundária com o mesmo objetivo de serviço como principal. Se adicionar uma relação de georreplicação existente para o grupo de ativação pós-falha, certifique-se de que GEO-secundária está configurado com o mesmo escalão de serviço e o tamanho de computação como principal.

- **Utilizar o serviço de escuta de leitura / escrita para a carga de trabalho OLTP**

  Quando executar operações de OLTP, utilize `failover-group-name.database.windows.net` que o servidor de URL e as ligações automaticamente são direcionadas para o primário. Este URL não é alterada após a ativação pós-falha. Tenha em atenção de que a ativação pós-falha envolve a atualizar que o registo DNS para que as ligações de cliente são redirecionadas para a nova principal apenas depois do cache DNS do cliente é atualizado.

- **Utilizar o serviço de escuta só de leitura para a carga de trabalho só de leitura**

  Se tiver uma isolada logicamente só de leitura carga de trabalho que forem tolerantes a determinados limitada de dados, pode utilizar a base de dados secundária no aplicativo. Para sessões de só de leitura, utilize `failover-group-name.secondary.database.windows.net` que o servidor o URL e a ligação é automaticamente direcionado para o secundário. Também é recomendável que indique na cadeia de ligação ler intenção através de **ApplicationIntent = só de leitura**.

- **Esteja preparado para degradação de desempenho**

  Decisão de ativação pós-falha SQL é independente do restante do aplicativo ou outros serviços utilizados. O aplicativo pode ser "misturar" com alguns componentes numa região e alguns em outro. Para evitar a degradação, certifique-se a implementação da aplicação redundante na região DR e siga estes [diretrizes de segurança de rede](#failover-groups-and-network-security).

  > [!NOTE]
  > A aplicação na região DR não tem de utilizar uma cadeia de ligação diferente.  

- **Preparar para a perda de dados**

  Se for detetada uma falha, o SQL espera para o período especificado por **GracePeriodWithDataLossHours**. O valor predefinido é 1 hora. Se não podem estar perda de dados, certifique-se definir **GracePeriodWithDataLossHours** para um número grande o suficiente, por exemplo, 24 horas. Utilize o manual do grupo de ativação pós-falha para reativação pós-falha a partir de secundário para o primário.

> [!IMPORTANT]
> Conjuntos elásticos com DTUs 800 ou menos e mais de 250 bancos de dados com georreplicação poderão encontrar problemas, incluindo já as ativações pós-falha planeadas e degradação do desempenho.  Esses problemas têm maior probabilidade de ocorrer para cargas de trabalho intensivas de escrita, quando os pontos finais de georreplicação amplamente são separados por geografia, ou quando vários pontos de extremidade secundários são utilizados para cada base de dados.  Os sintomas desses problemas são indicados quando o desfasamento de georreplicação aumenta ao longo do tempo.  Este atraso pode ser monitorizado com [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Se ocorrer estes problemas, atenuações incluem aumento do número de DTUs do conjunto, ou reduzir o número de bases de dados georreplicada no mesmo conjunto.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Melhores práticas de utilização de grupos de ativação pós-falha com instâncias geridas

O grupo de ativação pós-falha automática tem de ser configurado na instância principal e ligá-la para a instância secundária numa região diferente do Azure.  Todas as bases de dados na instância serão replicadas para a instância secundária. O diagrama seguinte ilustra uma configuração típica de um aplicativo de cloud com redundância geográfica usando a instância gerida e grupo de ativação pós-falha automática.

![ativação pós-falha automática](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!IMPORTANT]
> Grupos de ativação pós-falha automática para a instância gerida está em pré-visualização pública.

Se a sua aplicação utilizar a instância gerida como a camada de dados, siga estas Diretrizes gerais quando conceber para continuidade do negócio:

- **Criar a segunda instância na mesma zona de DNS como a instância principal**

  Quando é criada uma nova instância, um id exclusivo é automaticamente gerado como a zona DNS e incluído no nome DNS da instância. Um domínio multi (SAN) de certificado para esta instância é aprovisionada com o campo de SAN na forma de `zone_id.database.windows.net`. Este certificado pode ser utilizado para autenticar as ligações de cliente para uma instância na mesma zona de DNS. Para garantir a conectividade não é interrompido para a instância primária após a ativação pós-falha a primária e secundária instâncias tem de ser na mesma zona de DNS. Quando o aplicativo está pronto para implantação de produção, criar uma instância secundária numa região diferente e certifique-se de que partilha a zona DNS com a instância primária. Isso é feito ao especificar um `DNS Zone Partner` parâmetro opcional com o portal do Azure, PowerShell ou a API REST.

  Para obter mais informações sobre como criar a segunda instância na mesma zona de DNS como a instância principal, consulte [gerenciamento de grupos de ativação pós-falha com instâncias de geridas (pré-visualização)](#powershell-managing-failover-groups-with-managed-instances-preview).

- **Ativar o tráfego de replicação entre duas instâncias**

  Uma vez que cada instância é isolada na sua própria VNet, o tráfego de dois unidirecional entre nestas VNets têm de ser permitido. Consulte [gateway de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- **Configurar um grupo de ativação pós-falha para gerir a ativação pós-falha de toda instância**

  O grupo de ativação pós-falha irão gerir a ativação pós-falha de todas as bases de dados na instância. Quando é criado um grupo, cada base de dados na instância será automaticamente georreplicado para a instância secundária. Não é possível utilizar grupos de ativação pós-falha para iniciar uma ativação pós-falha parcial de um subconjunto das bases de dados.

  > [!IMPORTANT]
  > Se uma base de dados é removido da instância principal, ele também é largado automaticamente na instância secundária de georreplicação.

- **Utilizar o serviço de escuta de leitura / escrita para a carga de trabalho OLTP**

  Quando executar operações de OLTP, utilize `failover-group-name.zone_id.database.windows.net` que o servidor de URL e as ligações automaticamente são direcionadas para o primário. Este URL não é alterada após a ativação pós-falha. A ativação pós-falha envolve a atualizar o registo DNS, para que as ligações de cliente são redirecionadas para a nova principal apenas depois do cache DNS do cliente é atualizado. Uma vez que a instância secundária compartilha a zona DNS com o principal, a aplicação cliente poderá voltar a ligar ao mesmo com o mesmo certificado de SAN.

- **Ligar diretamente a georreplicação secundária para consultas só de leitura**

  Se tiver uma isolada logicamente só de leitura carga de trabalho que forem tolerantes a determinados limitada de dados, pode utilizar a base de dados secundária no aplicativo. Para ligar diretamente para a secundária georreplicada, utilize `server.secondary.zone_id.database.windows.net` que o servidor de URL e a ligação é efetuada diretamente para a georreplicação secundária.

  > [!NOTE]
  > Em determinadas camadas de serviços, a base de dados SQL do Azure suporta a utilização de [réplicas só de leitura](sql-database-read-scale-out.md) carregar saldo consulta só de leitura cargas de trabalho utilizar a capacidade de uma réplica só de leitura e a utilizar o `ApplicationIntent=ReadOnly` parâmetro na ligação cadeia de caracteres. Quando tiver configurado uma secundária georreplicada, pode utilizar esta capacidade para ligar a qualquer uma réplica só de leitura na localização primária ou na localização georreplicado.
  > - Para ligar a uma réplica só de leitura na localização primária, utilize `failover-group-name.zone_id.database.windows.net`.
  > - Para ligar a uma réplica só de leitura na localização secundária, utilize `failover-group-name.secondary.zone_id.database.windows.net`.

- **Esteja preparado para degradação de desempenho**

  Decisão de ativação pós-falha SQL é independente do restante do aplicativo ou outros serviços utilizados. O aplicativo pode ser "misturar" com alguns componentes numa região e alguns em outro. Para evitar a degradação, certifique-se a implementação da aplicação redundante na região DR e siga estes [diretrizes de segurança de rede](#failover-groups-and-network-security).

- **Preparar para a perda de dados**

  Se for detetada uma falha, o SQL aciona automaticamente ativação pós-falha de leitura e escrita se houver perda de dados para o melhor dos nossos dados de conhecimento. Caso contrário, ele aguarda o período especificado pelo `GracePeriodWithDataLossHours`. Se tiver especificado `GracePeriodWithDataLossHours`, estar preparado para perda de dados. Em geral, durante as falhas, o Azure favorece disponibilidade. Se não podem estar perda de dados, certifique-se de definir GracePeriodWithDataLossHours como um número grande o suficiente, por exemplo, 24 horas.

  A atualização DNS do serviço de escuta de leitura / escrita acontecerá imediatamente após a ativação pós-falha é iniciada. Esta operação não irá resultar na perda de dados. No entanto, o processo de mudança de funções de base de dados pode demorar até 5 minutos em condições normais. Até estar concluída, algumas bases de dados na nova instância principal continuará a ser só de leitura. Se a ativação pós-falha é iniciada com o PowerShell, em seguida, toda a operação é síncrona. Se for iniciada através do portal do Azure, em seguida, a interface do Usuário irá indicar o estado de conclusão. Se for iniciada através da API REST, utilize o mecanismo de consulta padrão do Azure Resource Manager para monitorizar para conclusão.

  > [!IMPORTANT]
  > Utilize o manual do grupo de ativação pós-falha para mover cores primárias de volta para a localização original. Quando a indisponibilidade que causou a ativação pós-falha é atenuada, pode mover as bases de dados primárias para a localização original. Para fazer isso deve iniciar a ativação pós-falha manual do grupo.

## <a name="failover-groups-and-network-security"></a>Grupos de ativação pós-falha e de segurança de rede

Para alguns aplicativos, que as regras de segurança requerem que o acesso à rede para a camada de dados está restrito a um componente específico ou de componentes, como uma VM, web service etc. Este requisito apresenta alguns desafios para o design de continuidade do negócio e a utilização dos grupos de ativação pós-falha. Deve considerar as seguintes opções ao implementar esse acesso restrito.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Utilização de grupos de ativação pós-falha e regras de rede virtual

Se estiver a utilizar [pontos finais de serviço de rede Virtual e regras](sql-database-vnet-service-endpoint-rule-overview.md) para restringir o acesso à base de dados SQL, esteja ciente de que ponto final de serviço de cada rede Virtual aplica-se apenas numa região do Azure. O ponto final não ativa noutras regiões aceitar comunicações de sub-rede. Por conseguinte, apenas as aplicações de cliente implementadas na mesma região podem ligar à base de dados primária. Uma vez que a ativação pós-falha resulta em sessões de cliente SQL que está a ser reencaminhadas para um servidor numa região diferente (secundária), estas sessões irão falhar se originado de um cliente fora dessa região. Por esse motivo, a política de ativação pós-falha automática não pode ser ativada se os servidores participantes estão incluídos nas regras de rede Virtual. Para suportar a ativação pós-falha manual, siga estes passos:

1. Aprovisionar as cópias redundantes dos componentes front-end da sua aplicação (serviço web, máquinas virtuais etc.) na região secundária
2. Configurar o [regras de rede virtual](sql-database-vnet-service-endpoint-rule-overview.md) individualmente para o servidor primário e secundário
3. Ativar o [ativação pós-falha de front-end a utilizar uma configuração do Gestor de tráfego](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Inicie ativação pós-falha manual quando a indisponibilidade for detectada. Esta opção está otimizada para as aplicações que necessitam de latência consistente entre o front-end e a camada de dados e suporta a recuperação quando os front-end, camada de dados ou ambos são afetados pela falha.

> [!NOTE]
> Se estiver a utilizar o **escuta só de leitura** para balanceamento de carga uma carga de trabalho só de leitura, certifique-se de que esta carga de trabalho é executada numa VM ou outros recursos na região secundária, de modo que consegue estabelecer ligação à base de dados secundário.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Utilização de grupos de ativação pós-falha e regras de firewall de base de dados SQL

Se o seu plano de continuidade de negócio necessita de ativação pós-falha, utilização de grupos com a ativação pós-falha automática, pode restringir o acesso à base de dados SQL com as regras de firewall tradicional.  Para suportar a ativação pós-falha automática, siga estes passos:

1. [Criar um IP público](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Criar um balanceador de carga público](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) e atribuir o IP público à mesma.
3. [Criar uma rede virtual e as máquinas virtuais](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) para os componentes de front-end
4. [Criar grupo de segurança de rede](../virtual-network/security-overview.md) e configurar ligações de entrada.
5. Certifique-se de que as ligações de saída são abertas à base de dados SQL do Azure com o 'Sql' [etiqueta de serviço](../virtual-network/security-overview.md#service-tags).
6. Criar uma [regra de firewall de base de dados SQL](sql-database-firewall-configure.md) para permitir tráfego de entrada do endereço IP público que criou no passo 1.

Para obter mais informações sobre como configurar o acesso de saída e o IP para utilizar nas regras da firewall, consulte [ligações de saída do Balanceador de carga](../load-balancer/load-balancer-outbound-connections.md).

A configuração acima serão Certifique-se de que a ativação pós-falha automática não irá bloquear ligações a partir de componentes front-end e parte do princípio de que a aplicação pode tolerar a latência mais tempo entre o front-end e a camada de dados.

> [!IMPORTANT]
> Para garantir a continuidade do negócio para falhas regionais tem de garantir redundância geográfica para componentes front-end e as bases de dados.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Ativar a georreplicação entre instâncias geridas e as VNets

Quando configurar um grupo de ativação pós-falha entre a primárias e secundárias instâncias geridas em duas regiões diferentes, cada instância é isolada usando uma VNet independente. Para permitir o tráfego de replicação nestas VNets, certifique-se de seguir estes pré-requisitos são cumpridos:

1. As duas instâncias geridas tem de estar em diferentes regiões do Azure.
2. Sua secundário tem de estar vazio (não existem bases de dados do utilizador).
3. As instâncias de geridas primária e secundária tem de estar no mesmo grupo de recursos.
4. As VNets que as instâncias geridas fazem parte de precisam estar conectados por meio de um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Não é suportado para o global VNet Peering.
5. As duas VNets de instância gerida não pode ter a sobreposição de endereços IP.
6. É necessário configurar a sua rede grupos de segurança (NSG) desse tipo que portas 5022 e o intervalo de 11000 ~ 12000 são conexões abertas de entrada e saídas para a partir da sub-rede geridos instanciados. Isso é para permitir o tráfego de replicação entre as instâncias

    > [!IMPORTANT]
    > Configurado incorretamente oportunidades potenciais de regras do NSG segurança para operações de cópia da base de dados paralisado.

7. Tem de configurar o parceiro de zona DNS na instância secundária. Uma zona DNS é uma propriedade de uma instância gerida. Ele representa a parte do nome de anfitrião que segue o nome de instância gerida e que precede o `.database.windows.net` prefixo. Este é gerado como cadeia de caracteres aleatória durante a criação da primeira instância gerida em cada VNet. A zona DNS não pode ser modificada após a criação de instância gerida e todas as instâncias geridas na mesma sub-rede partilham o mesmo valor de zona DNS. Para a configuração do grupo de ativação pós-falha de instância gerida, a instância gerida primária e a instância gerida secundário têm de partilhar o mesmo valor de zona DNS. Para tal, especificando o parâmetro DnsZonePartner ao criar a instância gerida secundário. A propriedade de parceiro de zona DNS define a instância gerida para partilhar um grupo de ativação pós-falha de instância com. Ao passar para o id de recurso de outra instância gerida, como a entrada de DnsZonePartner, a instância gerida, atualmente a ser criada herda o mesmo valor de zona DNS de parceiro de instância gerida.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Atualizar ou fazer downgrade uma base de dados primária

Pode atualizar ou mudar a versão de uma base de dados principal para um tamanho de computação diferentes (dentro do mesmo escalão de serviço, não entre fins gerais e crítico para a empresa) sem desligar a quaisquer bases de dados secundários. Ao atualizar, recomendamos que Atualize todas as bases de dados secundárias primeiro e, em seguida, atualizar o primário. Ao fazer downgrade, inverter a ordem: mudar para a versão principal primeiro e, em seguida, mudar todas as bases de dados secundários. Quando atualizar ou mudar para a versão da base de dados para um escalão de serviço diferente, esta recomendação é imposta.

Esta sequência recomenda-se especificamente para evitar o problema em que o secundário num SKU inferior fica sobrecarregado e tem de ser propagados novamente durante um processo de atualização ou a mudança para versão anterior. Também pode evitar o problema, tornando o primário só de leitura, às custas de afetar todas as cargas de trabalho de leitura e escrita em relação a principal. 

> [!NOTE]
> Se criou a base de dados secundária como parte da configuração do grupo de ativação pós-falha não é recomendado para mudar para a versão da base de dados secundário. Isso é para garantir que sua camada de dados tem capacidade suficiente para processar a carga de trabalho regular depois de ativação pós-falha está ativado.

## <a name="preventing-the-loss-of-critical-data"></a>Impedir a perda de dados críticos

Devido a alta latência das redes de longa distância, a cópia contínua utiliza um mecanismo de replicação assíncrona. Replicação assíncrona faz alguma perda de dados inevitável se ocorrer uma falha. No entanto, alguns aplicativos podem exigir sem perda de dados. Para proteger estas atualizações críticas, um desenvolvedor de aplicativos pode chamar o [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) procedimento de sistema imediatamente após a confirmação da transação. A invocar **sp_wait_for_database_copy_sync** bloqueia o thread de chamada até que a última transação ocorrida foi transmitida para a base de dados secundário. No entanto, não espere para as transações transmitidas para serem reproduzidos e confirmada no secundário. **sp_wait_for_database_copy_sync** tem um âmbito para uma ligação de cópia contínua específico. Qualquer utilizador com os direitos de ligação para base de dados primária pode chamar este procedimento.

> [!NOTE]
> **sp_wait_for_database_copy_sync** impede que a perda de dados após a ativação pós-falha, mas não garante a sincronização completa para acesso de leitura. O atraso causado por um **sp_wait_for_database_copy_sync** chamada de procedimento pode ser significativa e depende do tamanho do registo de transação no momento da chamada.

## <a name="failover-groups-and-point-in-time-restore"></a>Grupos de ativação pós-falha e restauro de ponto no tempo

Para obter informações sobre como utilizar o restauro de ponto no tempo com grupos de ativação pós-falha, consulte [ponto no tempo de recuperação (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="programmatically-managing-failover-groups"></a>Gerir programaticamente os grupos de ativação pós-falha

Como discutido anteriormente, grupos de ativação pós-falha automática e o Active Directory georreplicação também pode ser gerida através de programação com o Azure PowerShell e a API REST. As tabelas seguintes descrevem o conjunto de comandos disponíveis. Replicação geográfica activa inclui um conjunto de APIs do Azure Resource Manager para a gestão, incluindo o [API REST da base de dados SQL do Azure](https://docs.microsoft.com/rest/api/sql/) e [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Essas APIs requerem a utilização de grupos de recursos e suportam a segurança baseada em funções (RBAC). Para obter mais informações sobre como implementar funções de acesso, consulte [controlo de acesso](../role-based-access-control/overview.md).

### <a name="powershell-manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>PowerShell: Gerir a ativação pós-falha da base de dados do SQL com conjuntos elásticos e bases de dados individuais

| Cmdlet | Descrição |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Este comando cria um grupo de ativação pós-falha e regista-o nos servidores primário e secundários|
| [Remove-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Remove o grupo de ativação pós-falha do servidor e elimina todas as bases de dados secundárias incluído o grupo |
| [Get-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém a configuração do grupo de ativação pós-falha |
| [Set-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Modifica a configuração do grupo de ativação pós-falha |
| [Switch-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Ativação pós-falha de acionadores de grupo de ativação pós-falha para o servidor secundário |
| [Add-AzSqlDatabaseToFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Adiciona um ou mais bases de dados a um grupo de ativação pós-falha da base de dados do Azure SQL|
|  | |

> [!IMPORTANT]
> Para um script de exemplo, consulte [configurar e ativação pós-falha de um grupo de ativação pós-falha para uma base de dados](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md).
>

### <a name="powershell-managing-failover-groups-with-managed-instances-preview"></a>PowerShell: Gerir grupos de ativação pós-falha com instâncias de geridas (pré-visualização)

#### <a name="install-the-newest-pre-release-version-of-powershell"></a>Instalar a versão de pré-lançamento mais recente do PowerShell

1. Atualize o módulo PowerShellGet 1.6.5 (ou versão de pré-visualização mais recente). Ver [site de pré-visualização do PowerShell](https://www.powershellgallery.com/packages/AzureRM.Sql/4.11.6-preview).

   ```PowerShell
      install-module PowerShellGet -MinimumVersion 1.6.5 -force
   ```

2. Numa nova janela do PowerShell, execute os seguintes comandos:

   ```PowerShell
      import-module PowerShellGet
      get-module PowerShellGet #verify version is 1.6.5 (or newer)
      install-module azurerm.sql -RequiredVersion 4.5.0-preview -AllowPrerelease –Force
      import-module azurerm.sql
   ```

#### <a name="powershell-commandlets-to-create-an-instance-failover-group"></a>Commandlets do PowerShell para criar um grupo de ativação pós-falha de instância

| API | Descrição |
| --- | --- |
| New-AzSqlDatabaseInstanceFailoverGroup |Este comando cria um grupo de ativação pós-falha e regista-o nos servidores primário e secundários|
| Set-AzSqlDatabaseInstanceFailoverGroup |Modifica a configuração do grupo de ativação pós-falha|
| Get-AzSqlDatabaseInstanceFailoverGroup |Obtém a configuração do grupo de ativação pós-falha|
| Switch-AzSqlDatabaseInstanceFailoverGroup |Ativação pós-falha de acionadores de grupo de ativação pós-falha para o servidor secundário|
| Remove-AzSqlDatabaseInstanceFailoverGroup | Remove um grupo de ativação pós-falha|

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API: Gerir grupos de ativação pós-falha de base de dados SQL com bases de dados únicos e em pool

| API | Descrição |
| --- | --- |
| [Criar ou atualizar o grupo de ativação pós-falha](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Cria ou atualiza um grupo de ativação pós-falha |
| [Eliminar grupo de ativação pós-falha](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Remove o grupo de ativação pós-falha do servidor |
| [Ativação pós-falha (planeada)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Efetua a ativação pós-falha do servidor primário atual para este servidor. |
| [Permitir a ativação pós-falha forçada perda de dados](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |aflige através do servidor primário atual para este servidor. Esta operação poderá resultar em perda de dados. |
| [Obter grupo de ativação pós-falha](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Obtém um grupo de ativação pós-falha. |
| [Lista de grupos de ativação pós-falha por servidor](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Lista os grupos de ativação pós-falha num servidor. |
| [Grupo de ativação pós-falha de atualização](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Atualiza um grupo de ativação pós-falha. |
|  | |

### <a name="rest-api-manage-failover-groups-with-managed-instances-preview"></a>REST API: Gerir grupos de ativação pós-falha com instâncias de geridas (pré-visualização)

| API | Descrição |
| --- | --- |
| [Criar ou atualizar o grupo de ativação pós-falha](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Cria ou atualiza um grupo de ativação pós-falha |
| [Eliminar grupo de ativação pós-falha](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Remove o grupo de ativação pós-falha do servidor |
| [Ativação pós-falha (planeada)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Efetua a ativação pós-falha do servidor primário atual para este servidor. |
| [Permitir a ativação pós-falha forçada perda de dados](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |aflige através do servidor primário atual para este servidor. Esta operação poderá resultar em perda de dados. |
| [Obter grupo de ativação pós-falha](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | Obtém um grupo de ativação pós-falha. |
| [Grupos de ativação pós-falha de lista - lista por localização](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Lista os grupos de ativação pós-falha num local. |

## <a name="next-steps"></a>Passos Seguintes

- Para scripts de exemplo, consulte:
  - [Configurar e efetuar a ativação pós-falha de uma base de dados através de georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Configurar e efetuar a ativação pós-falha de uma base de dados agrupada através de georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Configurar e efetuar a ativação pós-falha de um grupo de ativação pós-falha para uma base de dados](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- Para uma visão geral de continuidade de negócio e cenários, consulte [descrição geral da continuidade de negócio](sql-database-business-continuity.md)
- Para saber mais sobre SQL do Azure, base de dados automatizada de cópias de segurança, consulte [cópias de segurança automatizadas de base de dados SQL](sql-database-automated-backups.md).
- Para saber mais sobre a utilização de cópias de segurança automatizadas para recuperação, veja [restaurar uma base de dados a partir de cópias de segurança iniciadas pelo serviço](sql-database-recovery-using-backups.md).
- Para saber mais sobre os requisitos de autenticação para um novo servidor primário e a base de dados, veja [segurança de base de dados SQL após a recuperação após desastre](sql-database-geo-replication-security-config.md).
