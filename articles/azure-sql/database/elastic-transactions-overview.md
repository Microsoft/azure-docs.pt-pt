---
title: Transações distribuídas entre bases de dados da cloud (pré-visualização)
description: Visão geral das transações elásticas de base de dados com base de dados Azure SQL e Azure SQL Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5504b9bc87f78682ff584006255d4e75e5e69fa7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793352"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>Transações distribuídas entre bases de dados da cloud (pré-visualização)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

As transações elásticas de base de dados para Azure SQL Database e Azure SQL Managed Instance permitem-lhe executar transações que abrangem várias bases de dados. As transações de bases de dados elásticas estão disponíveis para aplicações .NET utilizando ADO.NET e integram-se com a experiência de programação familiar utilizando as classes [System.Transaction.](/dotnet/api/system.transactions) Para obter a biblioteca, consulte [.NET Framework 4.6.1 (Instalador web)](https://www.microsoft.com/download/details.aspx?id=49981).
Adicionalmente, para as transações distribuídas por Instâncias Geridas estão disponíveis na [Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql).

Nas instalações, tal cenário requer normalmente a execução do Coordenador de Transações Distribuídas da Microsoft (MSDTC). Uma vez que o MSDTC não está disponível para aplicação Plataforma-as-a-Service em Azure, a capacidade de coordenar transações distribuídas foi agora integrada diretamente na Base de Dados de SQL ou Em Instância Gerida. As aplicações podem ligar-se a qualquer base de dados para lançar transações distribuídas, e uma das bases de dados ou servidores coordenará de forma transparente a transação distribuída, como mostra o seguinte valor.

Neste documento, "transações distribuídas" e "transações de base de dados elásticas" são considerados sinónimos e serão utilizados intercambiavelmente.

  ![Transações distribuídas com Base de Dados Azure SQL utilizando transações de bases de dados elásticas ][1]

## <a name="common-scenarios"></a>Cenários comuns

As transações de bases de dados elásticas permitem que as aplicações efevam alterações atómicas aos dados armazenados em várias bases de dados diferentes. A pré-visualização centra-se nas experiências de desenvolvimento do lado do cliente em C# e .NET. Uma experiência do lado do servidor (código escrito em procedimentos armazenados ou scripts do lado do servidor) utilizando [o Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) está disponível apenas para Instância Gerida.
> [!IMPORTANT]
> Na pré-visualização, a execução de transações de base de dados elásticas entre a Base de Dados Azure SQL e a Azure SQL Managed Instance não é suportada no momento. A transação de bases de dados elásticas só pode abranger um conjunto de bases de dados SQL ou um conjunto de Instâncias Geridas.

As transações elásticas de base de dados visam os seguintes cenários:

* Aplicações multi-base de dados em Azure: Com este cenário, os dados são verticalmente divididos em várias bases de dados na Base de Dados SQL ou Instância Gerida de modo a que diferentes tipos de dados residam em diferentes bases de dados. Algumas operações requerem alterações aos dados, que são mantidos em duas ou mais bases de dados. A aplicação utiliza transações elásticas de base de dados para coordenar as alterações entre bases de dados e garantir a atomicidade.
* Aplicações de base de dados em Azure: Com este cenário, o nível de dados utiliza a [biblioteca de clientes Elastic Database](elastic-database-client-library.md) ou auto-fragmentos para dividir horizontalmente os dados em muitas bases de dados em SQL Database ou Managed Instance. Um caso de uso proeminente é a necessidade de realizar alterações atómicas para uma aplicação de multi-inquilinos em pedaços quando as mudanças abrangem os inquilinos. Pense por exemplo numa transferência de um inquilino para outro, ambos residentes em diferentes bases de dados. Um segundo caso é o fragmento fino para acomodar as necessidades de capacidade de um grande inquilino, o que, por sua vez, normalmente implica que algumas operações atómicas precisam de se estender por várias bases de dados utilizadas para o mesmo inquilino. Um terceiro caso são atualizações atómicas para dados de referência que são replicados em bases de dados. Operações atómicas, transacionadas, ao longo destas linhas podem agora ser coordenadas em várias bases de dados utilizando a pré-visualização.
  As transações elásticas de base de dados utilizam duas fases de compromisso para garantir a atomicidade de transações através das bases de dados. É um bom ajuste para transações que envolvem menos de 100 bases de dados de cada vez dentro de uma única transação. Estes limites não são aplicados, mas é de esperar que as taxas de desempenho e sucesso para as transações de bases de dados elásticas sofram quando excedem estes limites.

## <a name="installation-and-migration"></a>Instalação e migração

As capacidades para transações de bases de dados elásticas são fornecidas através de atualizações às bibliotecas .NET System.Data.dll e System.Transactions.dll. Os DLLs asseguram que o compromisso bifástico é utilizado sempre que necessário para garantir a atomicidade. Para começar a desenvolver aplicações utilizando transações elásticas de base de dados, instale [o Quadro .NET 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) ou uma versão posterior. Ao executar uma versão anterior do quadro .NET, as transações não promoverão uma transação distribuída e uma exceção será levantada.

Após a instalação, pode utilizar as APIs de transação distribuídas no System.Transactions com ligações à Base de Dados SQL e à Instância Gerida. Se tiver aplicações MSDTC existentes utilizando estas APIs, reconstrua as suas aplicações existentes para .NET 4.6 após a instalação do Quadro 4.6.1. Se os seus projetos tiverem como alvo .NET 4.6, utilizarão automaticamente os DLLs atualizados a partir da nova versão Framework e as chamadas de API de transação distribuída em combinação com as ligações à Base de Dados SQL ou à Gestão de Instâncias serão agora bem sucedidas.

Lembre-se que as transações elásticas da base de dados não requerem a instalação do MSDTC. Em vez disso, as transações elásticas de base de dados são geridas diretamente por e dentro do serviço. Isto simplifica significativamente os cenários em nuvem uma vez que uma implementação de MSDTC não é necessária para usar transações distribuídas com base de dados SQL ou Instância Gerida. A secção 4 explica mais detalhadamente como implementar transações elásticas de base de dados e a estrutura .NET necessária juntamente com as suas aplicações em nuvem para o Azure.

## <a name="net-installation-for-azure-cloud-services"></a>Instalação .NET para Serviços Azure Cloud

A Azure oferece várias ofertas para hospedar aplicações .NET. Uma comparação das diferentes ofertas está disponível na [comparação de Azure App Service, Cloud Services e Virtual Machines.](/azure/architecture/guide/technology-choices/compute-decision-tree) Se o SO convidado da oferta for menor do que .NET 4.6.1 necessário para transações elásticas, você precisa atualizar o SO convidado para 4.6.1.

Para o Azure App Service, as atualizações para o SO convidado não são suportadas atualmente. Para máquinas virtuais Azure, basta iniciar sessão no VM e executar o instalador para obter a mais recente estrutura .NET. Para o Azure Cloud Services, é necessário incluir a instalação de uma versão mais recente .NET nas tarefas de arranque da sua implementação. Os conceitos e passos estão documentados em [Install .NET on a Cloud Service Role](../../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Note que o instalador para .NET 4.6.1 pode necessitar de um armazenamento mais temporário durante o processo de armadilhagem de botas nos serviços de nuvem Azure do que o instalador para .NET 4.6. Para garantir uma instalação bem sucedida, é necessário aumentar o armazenamento temporário para o seu serviço de nuvem Azure no seu ficheiro ServiceDefinition.csdef na secção LocalResources e nas definições ambientais da sua tarefa de arranque, como mostra a seguinte amostra:

```xml
    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
```

## <a name="net-development-experience"></a>experiência de desenvolvimento .NET

### <a name="multi-database-applications"></a>Aplicações multi-base de dados

O seguinte código de amostra utiliza a experiência de programação familiar com .NET System.Transactions. A classe TransactionScope estabelece uma transação ambiente em .NET. (Uma "transação ambiente" é uma que vive no fio atual.) Todas as ligações abertas no TransactionScope participam na transação. Se diferentes bases de dados participarem, a transação é automaticamente elevada a uma transação distribuída. O resultado da transação é controlado definindo o âmbito de aplicação para completar para indicar um compromisso.

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

### <a name="sharded-database-applications"></a>Aplicações de base de dados de sharded

As transações elásticas de base de dados para a Base de Dados Escoval e Instância Gerida também suportam a coordenação de transações distribuídas onde utiliza o método OpenConnectionForKey da biblioteca de clientes de base de dados elástica para abrir ligações para um nível de dados escalonado. Considere os casos em que precisa de garantir a consistência transacional para alterações em vários valores chave de fragmentos diferentes. As ligações aos fragmentos que hospedam os diferentes valores-chave de fragmentos são intermediadas através do OpenConnectionForKey. No caso geral, as ligações podem ser de diferentes fragmentos, de modo a que garantir garantias transacionais requer uma transação distribuída.
A seguinte amostra de código ilustra esta abordagem. Assume que uma variável chamada shardmap é usada para representar um mapa de fragmentos da biblioteca de clientes de base de dados elástica:

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

## <a name="transact-sql-development-experience"></a>Experiência de desenvolvimento Transact-SQL

Umas transações distribuídas pelo lado do servidor utilizando o Transact-SQL estão disponíveis apenas para Azure SQL Managed Instance. A transação distribuída só pode ser executada entre Instâncias Geridas que pertencem ao mesmo [grupo de confiança do Servidor.](../managed-instance/server-trust-group-overview.md) Neste cenário, as Instâncias Geridas precisam de usar [o servidor ligado](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine#TsqlProcedure) para se referirem mutuamente.

A amostra seguinte Código Transact-SQL utiliza [START TRANSAÇÃO DISTRIBUÍDA](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) PARA iniciar transação distribuída.

```Transact-SQL

    -- Configure the Linked Server
    -- Add one Azure SQL Managed Instance as Linked Server
    EXEC sp_addlinkedserver
        @server='RemoteServer', -- Linked server name
        @srvproduct='',
        @provider='sqlncli', -- SQL Server Native Client
        @datasrc='managed-instance-server.46e7afd5bc81.database.windows.net' -- Managed Instance endpoint

    -- Add credentials and options to this Linked Server
    EXEC sp_addlinkedsrvlogin
        @rmtsrvname = 'RemoteServer', -- Linked server name
        @useself = 'false',
        @rmtuser = '<login_name>',         -- login
        @rmtpassword = '<secure_password>' -- password

    USE AdventureWorks2012;
    GO
    SET XACT_ABORT ON;
    GO
    BEGIN DISTRIBUTED TRANSACTION;
    -- Delete candidate from local instance.
    DELETE AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    -- Delete candidate from remote instance.
    DELETE RemoteServer.AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    COMMIT TRANSACTION;
    GO
```

## <a name="combining-net-and-transact-sql-development-experience"></a>Combinando experiência de desenvolvimento .NET e Transact-SQL

.NET aplicações que utilizam as classes System.Transaction podem combinar classe TransactionScope com declaração Transact-SQL BEGIN DISTRIBUTED TRANSACTION. Dentro do TransactionScope, a transação interna que executa BEGIN DITRIBUTED TRANSACTION será explicitamente promovida para transação distribuída. Além disso, quando o segundo SqlConnecton for aberto dentro do TransactionScope será implicitamente promovido a transação distribuída. Uma vez iniciada a transação distribuída, todos os pedidos de transações subsequentes, sejam eles provenientes de .NET ou Transact-SQL, juntar-se-ão à transação distribuída pelos pais. Como consequência, todos os âmbitos de transação aninhados iniciados pela declaração BEGIN acabarão na mesma transação e as declarações COMMIT/ROLLBACK terão o seguinte efeito sobre o resultado global:
 * A declaração do COMMIT não terá qualquer efeito sobre o âmbito de transação iniciado pela declaração BEGIN, ou seja, nenhum resultado será cometido antes de o método Complete() ser invocado no objeto TransactionScope. Se o objeto TransactionScope for destruído antes de ser concluído, todas as alterações efetuadas dentro do âmbito são reviradas.
 * A declaração rollback fará com que todo o TransactionScope recue. Quaisquer tentativas de alistar novas transações dentro do TransactionScope falharão posteriormente, bem como tentará invocar Complete() no objeto TransactionScope.

Aqui está um exemplo onde a transação é explicitamente promovida para transação distribuída com Transact-SQL.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
        
            // Transaction is here promoted to distributed by BEGIN statement
            //
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            // ...
        }
     
        using (SqlConnection conn2 = new SqlConnection(DB1_ConnectionString)
        {
            conn2.Open();
            // ...
        }
        
        s.Complete();
    }
```

O exemplo seguinte mostra uma transação que é implicitamente promovida a transação distribuída uma vez que o segundo SqlConnecton foi iniciado dentro do TransactionScope.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
            // ...
        }
        
        using (SqlConnection conn = new SqlConnection(DB1_ConnectionString)
        {
            // Because this is second SqlConnection within TransactionScope transaction is here implicitly promoted distributed.
            //
            conn.Open(); 
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            Helper.ExecuteNonQueryOnOpenConnection(conn, lsQuery);
            // ...
        }
        
        s.Complete();
    }
```

## <a name="transactions-across-multiple-servers-for-azure-sql-database"></a>Transações em vários servidores para Azure SQL Database

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

As transações de base de dados elásticas são suportadas em diferentes servidores na Base de Dados Azure SQL. Quando as transações cruzam os limites do servidor, os servidores participantes precisam primeiro de ser introduzidos numa relação de comunicação mútua. Uma vez estabelecida a relação de comunicação, qualquer base de dados em qualquer um dos dois servidores pode participar em transações elásticas com bases de dados do outro servidor. Com transações abrangendo mais de dois servidores, uma relação de comunicação precisa estar em vigor para qualquer par de servidores.

Utilize os seguintes cmdlets PowerShell para gerir as relações de comunicação entre servidores para transações elásticas de base de dados:

* **New-AzSqlServerCommunicationLink**: Utilize este cmdlet para criar uma nova relação de comunicação entre dois servidores na Base de Dados Azure SQL. A relação é simétrica, o que significa que ambos os servidores podem iniciar transações com o outro servidor.
* **Get-AzSqlServerCommunicationLink**: Utilize este cmdlet para recuperar as relações de comunicação existentes e as suas propriedades.
* **Remove-AzSqlServerCommunicationLink**: Utilize este cmdlet para remover uma relação de comunicação existente.

## <a name="transactions-across-multiple-servers-for-azure-sql-managed-instance"></a>Transações em vários servidores para Azure SQL Managed Instance

As transações distribuídas são suportadas em diferentes servidores em Azure SQL Managed Instance. Quando as transações cruzam os limites da Instância Gerida, as instâncias participantes devem primeiro ser inseridas numa relação de segurança mútua e de comunicação. Isto é feito através da criação de um [Server Trust Group](../managed-instance/server-trust-group-overview.md), que pode ser feito no portal Azure. Se as instâncias geridas não [estiverem](../../virtual-network/virtual-network-peering-overview.md) na mesma rede Virtual, então é necessário criar um perito em rede virtual e as regras de entrada e saída do grupo de segurança da Rede têm de permitir as portas 5024 e 11000-12000 em todas as redes virtuais participantes.

  ![Grupos de confiança do servidor no Portal Azure][3]

Após o diagrama mostra o Server Trust Group com Instâncias Geridas que podem executar transações distribuídas com .NET ou Transact-SQL.

  ![Transações distribuídas com Azure SQL Managed Instance usando transações elásticas][2]

## <a name="monitoring-transaction-status"></a>Monitorização do estado das transações

Utilize vistas de gestão dinâmica (DMVs) para monitorizar o estado e o progresso das suas transações de bases de dados elásticas em curso. Todos os DMVs relacionados com transações são relevantes para transações distribuídas em SQL Database e Managed Instance. Pode encontrar aqui a lista correspondente de DMVs: [Vistas e funções de gestão dinâmica relacionadas com transações (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql).

Estes DMVs são particularmente úteis:

* **sys.dm trans \_ \_ \_ transações ativas** trans: Lista as transações atualmente ativas e o seu estado. A coluna UOW (Unidade de Trabalho) pode identificar as diferentes transações infantis que pertencem à mesma transação distribuída. Todas as transações dentro da mesma transação distribuída têm o mesmo valor UOW. Para mais informações, consulte a documentação do [DMV.](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql)
* **sys.dm \_ \_ transações de base de \_ dados de trans**: Fornece informações adicionais sobre transações, tais como a colocação da transação no registo. Para mais informações, consulte a documentação do [DMV.](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql)
* **sys.dm tran \_ \_ locks**: Fornece informações sobre as fechaduras que são atualmente detidas por transações em curso. Para mais informações, consulte a documentação do [DMV.](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql)

## <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se atualmente às transações elásticas de base de dados na Base de Dados SQL:

* Apenas são suportadas transações através de bases de dados na Base de Dados SQL. Outros fornecedores de recursos [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) e bases de dados fora da BASE de Dados SQL não podem participar em transações de bases de dados elásticas. Isto significa que as transações elásticas de base de dados não podem estender-se nas instalações do SQL Server e da Azure SQL Database. Para transações distribuídas nas instalações, continue a utilizar o MSDTC.
* Apenas são suportadas transações coordenadas pelo cliente a partir de uma aplicação .NET. O suporte do lado do servidor para T-SQL, como BEGIN DISTRIBUTED TRANSACTION, está planeado, mas ainda não está disponível.
* As transações nos serviços do WCF não são suportadas. Por exemplo, tem um método de serviço WCF que executa uma transação. A possibilidade de a chamada ser executada num âmbito de transação falhará como [system.ServiceModel.ProtocolException](/dotnet/api/system.servicemodel.protocolexception).

As seguintes limitações aplicam-se atualmente às transações distribuídas em Instância Gerida:

* Apenas são suportadas transações através de bases de dados em Casos Geridos. Outros fornecedores de recursos [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) e bases de dados fora da Azure SQL Managed Instance não podem participar em transações distribuídas. Isto significa que as transações distribuídas não podem estender-se nas instalações SQL Server e Azure SQL Managed Instance. Para transações distribuídas nas instalações, continue a utilizar o MSDTC.
* As transações nos serviços do WCF não são suportadas. Por exemplo, tem um método de serviço WCF que executa uma transação. A possibilidade de a chamada ser executada num âmbito de transação falhará como [system.ServiceModel.ProtocolException](/dotnet/api/system.servicemodel.protocolexception).
* A Azure SQL Managed Instance deve fazer parte de um [grupo de confiança do Servidor](../managed-instance/server-trust-group-overview.md) para participar em transações distribuídas.
* As limitações dos [grupos de confiança do Servidor](../managed-instance/server-trust-group-overview.md) afetam as transações distribuídas.
* As instâncias geridas que participam em transações distribuídas precisam de ter conectividade sobre os pontos finais privados (utilizando o endereço IP privado da rede virtual onde são implantados) e precisam de ser mutuamente referenciados utilizando FQDNs privados. As aplicações do cliente podem utilizar transações distribuídas em pontos finais privados. Além disso, nos casos em que a Transact-SQL aproveita os servidores ligados referentes a pontos finais privados, as aplicações do cliente também podem utilizar transações distribuídas em pontos finais públicos. Esta limitação é explicada no diagrama seguinte.
  ![Limitação da conectividade do ponto final privado][4]
## <a name="next-steps"></a>Passos seguintes

* Para obter perguntas, contacte-nos na página [de perguntas do Microsoft Q&Uma página de perguntas para a Base de Dados SQL](/answers/topics/azure-sql-database.html).
* Para pedidos de funcionalidades, adicione-os ao fórum de feedback da [Base de Dados SQL](https://feedback.azure.com/forums/217321-sql-database/) ou [ao fórum de instância gerida.](https://feedback.azure.com/forums/915676-sql-managed-instance)



<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
[2]: ./media/elastic-transactions-overview/sql-mi-distributed-transactions.png
[3]: ./media/elastic-transactions-overview/server-trust-groups-azure-portal.png
[4]: ./media/elastic-transactions-overview/managed-instance-distributed-transactions-private-endpoint-limitations.png
