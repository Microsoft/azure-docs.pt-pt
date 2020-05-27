---
title: Transações distribuídas entre bases de dados de nuvem
description: Visão geral das transações de base de dados elásticas com base de dados Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 7568a8a80550677e1608e1a8a52ebeacce765c81
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826422"
---
# <a name="distributed-transactions-across-cloud-databases"></a>Transações distribuídas entre bases de dados de nuvem

As transações elásticas de bases de dados para a Base de Dados Azure SQL (SQL DB) permitem-lhe executar transações que abrangem várias bases de dados em SQL DB. As transações elásticas de bases de dados para O DB SQL estão disponíveis para aplicações .NET utilizando ADO .NET e integrar-se com a experiência de programação familiar utilizando as classes [System.Transaction.](https://msdn.microsoft.com/library/system.transactions.aspx) Para obter a biblioteca, consulte [.NET Framework 4.6.1 (Instalador web)](https://www.microsoft.com/download/details.aspx?id=49981).

No local, tal cenário normalmente exigia executar o Microsoft Distributed Transaction Coordinator (MSDTC). Uma vez que o MSDTC não está disponível para aplicação Platform-as-a-Service no Azure, a capacidade de coordenar transações distribuídas foi agora diretamente integrada no SQL DB. As aplicações podem ligar-se a qualquer Base de Dados SQL para lançar transações distribuídas, e uma das bases de dados coordenará de forma transparente a transação distribuída, como mostra o seguinte valor. 

  ![Transações distribuídas com base de dados Azure SQL utilizando transações elásticas de bases de dados ][1]

## <a name="common-scenarios"></a>Cenários comuns

As transações elásticas de bases de dados para O DB SQL permitem que as aplicações efaçam alterações atómicas aos dados armazenados em várias bases de dados SQL diferentes. A pré-visualização centra-se nas experiências de desenvolvimento do lado do cliente em C# e .NET. Uma experiência do lado do servidor usando T-SQL está planeada para um tempo posterior.  
As transações de bases de dados elásticas visam os seguintes cenários:

* Aplicações multi-bases de dados em Azure: Com este cenário, os dados são verticalmente divididos em várias bases de dados em SQL DB de tal forma que diferentes tipos de dados residem em diferentes bases de dados. Algumas operações requerem alterações aos dados que são mantidos em duas ou mais bases de dados. A aplicação utiliza transações elásticas de bases de dados para coordenar as alterações nas bases de dados e garantir a atómico.
* Aplicações de base de dados em Azure: Com este cenário, o nível de dados utiliza a biblioteca de [clientes da Base](sql-database-elastic-database-client-library.md) de Dados Elástica sharding ou auto-sharding para dividir horizontalmente os dados em muitas bases de dados em SQL DB. Um caso de uso proeminente é a necessidade de realizar alterações atómicas para uma aplicação de multi-inquilinos esfartoquando as mudanças abrangem os inquilinos. Pense, por exemplo, numa transferência de um inquilino para outro, ambos a residir em bases de dados diferentes. Um segundo caso é um sharding de grãos finos para acomodar as necessidades de capacidade de um grande inquilino, o que, por sua vez, normalmente implica que algumas operações atómicas precisam de se estender por várias bases de dados utilizadas para o mesmo inquilino. Um terceiro caso são atualizações atómicas a dados de referência que são replicados através de bases de dados. As operações atómicas, transacionadas, ao longo destas linhas podem agora ser coordenadas em várias bases de dados utilizando a pré-visualização.
  As transações elásticas de base de dados utilizam o compromisso em duas fases para garantir a atómico transação através das bases de dados. É um bom ajuste para transações que envolvem menos de 100 bases de dados de cada vez numa única transação. Estes limites não são aplicados, mas é de esperar que as taxas de desempenho e de sucesso das transações elásticas de bases de dados sofram quando excedem estes limites.

## <a name="installation-and-migration"></a>Instalação e migração

As capacidades para transações elásticas de bases de dados em SQL DB são fornecidas através de atualizações para o Sistema .Data.dll e System.Transactions.dll. Os DLLs asseguram que o compromisso em duas fases seja utilizado sempre que necessário para garantir a atómico. Para começar a desenvolver aplicações utilizando transações elásticas de base de dados, instale [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) ou uma versão posterior. Ao executar uma versão anterior do quadro .NET, as transações não promoverão uma transação distribuída e será levantada uma exceção.

Após a instalação, pode utilizar as APIs de transação distribuídas no System.Transações com ligações ao SQL DB. Se tiver aplicações MSDTC existentes utilizando estas APIs, basta reconstruir as suas aplicações existentes para .NET 4.6 após a instalação do Quadro 4.6.1. Se os seus projetos visarem .NET 4.6, utilizarão automaticamente os DLLs atualizados da nova versão Framework e as chamadas API de transação distribuídas em combinação com ligações ao SQL DB terão agora sucesso.

Lembre-se que as transações elásticas de base de dados não requerem a instalação do MSDTC. Em vez disso, as transações de base de dados elásticas são geridas diretamente por e dentro do SQL DB. Isto simplifica significativamente os cenários de nuvem, uma vez que uma implantação do MSDTC não é necessária para utilizar transações distribuídas com SQL DB. A secção 4 explica mais detalhadamente como implementar transações elásticas de base de dados e a estrutura necessária .NET juntamente com as suas aplicações em nuvem para o Azure.

## <a name="development-experience"></a>Experiência de desenvolvimento

### <a name="multi-database-applications"></a>Aplicações multi-bases de dados

O seguinte código de amostra utiliza a experiência de programação familiar com .NET System.Transactions. A classe TransactionScope estabelece uma transação ambiente em .NET. (Uma "transação ambiente" é uma que vive no fio atual.) Todas as ligações abertas no âmbito do TransactionScope participam na transação. Se participarem diferentes bases de dados, a transação é automaticamente elevada a uma transação distribuída. O resultado da transação é controlado definindo o âmbito de aplicação para completar para indicar um compromisso.

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

### <a name="sharded-database-applications"></a>Aplicações de base de dados estoradas

As transações elásticas de bases de dados para o SQL DB também suportam a coordenação de transações distribuídas onde utiliza o método OpenConnectionForKey da biblioteca de clientes de base de dados elástica para abrir ligações para um nível de dados escalado. Considere os casos em que precisa de garantir a consistência transacional para alterações em vários valores-chave diferentes. As ligações aos fragmentos que acolhem os diferentes valores-chave são intermediadas utilizando o OpenConnectionForKey. No caso geral, as ligações podem ser para diferentes fragmentos, de modo a garantir que as garantias transacionais exijam uma transação distribuída. A amostra de código que se segue ilustra esta abordagem. Assume que uma variável chamada shardmap é usada para representar um mapa de fragmentos da biblioteca de clientes de base de dados elástica:

    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }


## <a name="net-installation-for-azure-cloud-services"></a>instalação .NET para serviços de nuvem azure

O Azure oferece várias ofertas para acolher aplicações .NET. Uma comparação das diferentes ofertas está disponível na [comparação azure App Service, Cloud Services e Virtual Machines.](/azure/architecture/guide/technology-choices/compute-decision-tree) Se o osso hóspede da oferta for inferior a .NET 4.6.1 necessário para transações elásticas, você precisa atualizar o oss o hóspede para 4.6.1. 

Para os Serviços de Aplicações Azure, as atualizações para o osso de hóspedes não são atualmente suportadas. Para máquinas virtuais Azure, basta entrar no VM e executar o instalador para a mais recente estrutura .NET. Para o Azure Cloud Services, é necessário incluir a instalação de uma versão mais recente .NET nas tarefas de arranque da sua implementação. Os conceitos e passos estão documentados em [Instalação .NET numa função](../cloud-services/cloud-services-dotnet-install-dotnet.md)de serviço em nuvem .  

Note que o instalador para .NET 4.6.1 pode necessitar de mais armazenamento temporário durante o processo de enestação de botas nos serviços de nuvem Azure do que o instalador para .NET 4.6. Para garantir uma instalação bem sucedida, é necessário aumentar o armazenamento temporário para o seu serviço de nuvem Azure no seu ficheiro ServiceDefinition.csdef na secção LocalResources e nas configurações ambientais da sua tarefa de arranque, como mostra a seguinte amostra:

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

## <a name="transactions-across-multiple-servers"></a>Transações em vários servidores

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

As transações de bases de dados elásticas são suportadas em diferentes servidores de base de dados SQL na Base de Dados Azure SQL. Quando as transações atravessam os limites do servidor da Base de Dados SQL, os servidores participantes precisam primeiro de ser introduzidos numa relação de comunicação mútua. Uma vez estabelecida a relação de comunicação, qualquer base de dados em qualquer um dos dois servidores pode participar em transações elásticas com bases de dados do outro servidor. Com transações abrangendo mais de dois servidores de base de dados SQL, uma relação de comunicação precisa de estar em vigor para qualquer par de servidores de base de dados SQL.

Utilize os seguintes cmdlets PowerShell para gerir relações de comunicação de servidores cruzados para transações elásticas de bases de dados:

* **New-AzSqlServerCommunicationLink**: Use este cmdlet para criar uma nova relação de comunicação entre dois servidores de base de dados SQL na Base de Dados Azure SQL. A relação é simétrica, o que significa que ambos os servidores podem iniciar transações com o outro servidor.
* **Get-AzSqlServerCommunicationLink**: Use este cmdlet para recuperar as relações de comunicação existentes e as suas propriedades.
* **Remove-AzSqlServerCommunicationLink**: Utilize este cmdlet para remover uma relação de comunicação existente. 

## <a name="monitoring-transaction-status"></a>Monitorizar o estado das transações

Utilize pontos de vista de gestão dinâmica (DMVs) em SQL DB para monitorizar o estado e o progresso das suas transações de base de dados elásticas em curso. Todos os DMVs relacionados com transações são relevantes para transações distribuídas em SQL DB. Pode encontrar aqui a lista correspondente de DMVs: [Pontos e funções de gestão dinâmica relacionadas com transações (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Estes DMVs são particularmente úteis:

* **sys.dm \_ \_ \_ transações ativas tran**: Listas atualmente ativas e o seu estado. A coluna UOW (Unidade de Trabalho) pode identificar as diferentes transações infantis que pertencem à mesma transação distribuída. Todas as transações dentro da mesma transação distribuída têm o mesmo valor da UOW. Consulte a [documentação do DMV](https://msdn.microsoft.com/library/ms174302.aspx) para obter mais informações.
* **sys.dm \_ \_ \_ transtransas**de base de dados tran : Fornece informações adicionais sobre transações, tais como colocação da transação no registo. Consulte a [documentação do DMV](https://msdn.microsoft.com/library/ms186957.aspx) para obter mais informações.
* **sys.dm \_ \_ tran locks**: Fornece informações sobre os cadeados que são atualmente detidos por transações em curso. Consulte a [documentação do DMV](https://msdn.microsoft.com/library/ms190345.aspx) para obter mais informações.

## <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se atualmente às transações elásticas de bases de dados em SQL DB:

* Apenas são suportadas transações através de bases de dados em SQL DB. Outros fornecedores de recursos [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) e bases de dados fora do SQL DB não podem participar em transações elásticas de bases de dados. Isto significa que as transações elásticas de base de dados não podem estender-se nas instalações do SQL Server e da Base de Dados Azure SQL. Para transações distribuídas nas instalações, continue a utilizar a MSDTC. 
* Apenas são suportadas transações coordenadas pelo cliente a partir de uma aplicação .NET. O suporte do lado do servidor para T-SQL, como START DISTRIBUTED TRANSACTION, está planeado, mas ainda não está disponível. 
* As transações nos serviços wcf não são suportadas. Por exemplo, tem um método de serviço WCF que executa uma transação. A apresentação da chamada dentro de um âmbito de transação falhará como [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Passos seguintes

Para perguntas, contacte-nos no Microsoft Q&Uma página de perguntas para a Base de [Dados SQL](https://docs.microsoft.com/answers/topics/azure-sql-database.html) e para pedidos de funcionalidades, por favor adicione-as ao fórum de feedback da Base de [Dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png
