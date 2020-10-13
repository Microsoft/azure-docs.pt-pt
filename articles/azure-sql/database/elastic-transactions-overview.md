---
title: Transações distribuídas através de bases de dados em nuvem (pré-visualização)
description: Visão geral das transações elásticas de base de dados com base de dados Azure SQL.
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
ms.openlocfilehash: 60f6863bbe051338308c30e22c6969d84670dc64
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91409737"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>Transações distribuídas através de bases de dados em nuvem (pré-visualização)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

As transações elásticas de base de dados para Azure SQL Database permitem executar transações que abrangem várias bases de dados na Base de Dados SQL. As transações elásticas de base de dados para a Base de Dados SQL estão disponíveis para aplicações .NET utilizando ADO.NET e integram-se com a experiência de programação familiar utilizando as classes [System.Transaction.](https://msdn.microsoft.com/library/system.transactions.aspx) Para obter a biblioteca, consulte [.NET Framework 4.6.1 (Instalador web)](https://www.microsoft.com/download/details.aspx?id=49981).

Nas instalações, tal cenário requer normalmente a execução do Coordenador de Transações Distribuídas da Microsoft (MSDTC). Uma vez que o MSDTC não está disponível para a aplicação Plataforma-as-a-Service em Azure, a capacidade de coordenar transações distribuídas foi agora integrada diretamente na Base de Dados SQL. As aplicações podem ligar-se a qualquer base de dados na Base de Dados SQL para lançar transações distribuídas, e uma das bases de dados coordenará de forma transparente a transação distribuída, como mostra o seguinte valor.

  ![Transações distribuídas com Base de Dados Azure SQL utilizando transações de bases de dados elásticas ][1]

## <a name="common-scenarios"></a>Cenários comuns

As transações elásticas de bases de dados para a Base de Dados SQL permitem que as aplicações efevam alterações atómicas aos dados armazenados em várias bases de dados diferentes na Base de Dados SQL. A pré-visualização centra-se nas experiências de desenvolvimento do lado do cliente em C# e .NET. Uma experiência do lado do servidor usando T-SQL está planeada para uma hora posterior.  
As transações elásticas de base de dados visam os seguintes cenários:

* Aplicações multi-base de base de dados em Azure: Com este cenário, os dados são verticalmente divididos em várias bases de dados na Base de Dados SQL de modo a que diferentes tipos de dados residam em diferentes bases de dados. Algumas operações requerem alterações aos dados, que são mantidos em duas ou mais bases de dados. A aplicação utiliza transações elásticas de base de dados para coordenar as alterações entre bases de dados e garantir a atomicidade.
* Aplicações de base de dados em Azure: Com este cenário, o nível de dados utiliza a [biblioteca de clientes Elastic Database](elastic-database-client-library.md) ou auto-fragmentos para dividir horizontalmente os dados em muitas bases de dados na Base de Dados SQL. Um caso de uso proeminente é a necessidade de realizar alterações atómicas para uma aplicação de multi-inquilinos em pedaços quando as mudanças abrangem os inquilinos. Pense por exemplo numa transferência de um inquilino para outro, ambos residentes em diferentes bases de dados. Um segundo caso é o fragmento fino para acomodar as necessidades de capacidade de um grande inquilino, o que, por sua vez, normalmente implica que algumas operações atómicas precisam de se estender por várias bases de dados utilizadas para o mesmo inquilino. Um terceiro caso são atualizações atómicas para dados de referência que são replicados em bases de dados. Operações atómicas, transacionadas, ao longo destas linhas podem agora ser coordenadas em várias bases de dados utilizando a pré-visualização.
  As transações elásticas de base de dados utilizam o compromisso bifástico para garantir a atomicidade das transações através das bases de dados. É um bom ajuste para transações que envolvem menos de 100 bases de dados de cada vez dentro de uma única transação. Estes limites não são aplicados, mas é de esperar que as taxas de desempenho e sucesso para as transações de bases de dados elásticas sofram quando excedem estes limites.

## <a name="installation-and-migration"></a>Instalação e migração

As capacidades para transações de bases de dados elásticas na Base de Dados SQL são fornecidas através de atualizações às bibliotecas .NET System.Data.dll e System.Transactions.dll. Os DLLs asseguram que o compromisso bifástico é utilizado sempre que necessário para garantir a atomicidade. Para começar a desenvolver aplicações utilizando transações elásticas de base de dados, instale [o Quadro .NET 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) ou uma versão posterior. Ao executar uma versão anterior do quadro .NET, as transações não promoverão uma transação distribuída e uma exceção será levantada.

Após a instalação, pode utilizar as APIs de transação distribuídas no System.Transactions com ligações à Base de Dados SQL. Se tiver aplicações MSDTC existentes utilizando estas APIs, basta reconstruir as aplicações existentes para .NET 4.6 após a instalação do Quadro 4.6.1. Se os seus projetos tiverem como alvo .NET 4.6, utilizarão automaticamente os DLLs atualizados a partir da nova versão Framework e as chamadas de API de transação distribuída em combinação com as ligações à Base de Dados SQL serão agora bem sucedidas.

Lembre-se que as transações elásticas da base de dados não requerem a instalação do MSDTC. Em vez disso, as transações de base de dados elásticas são geridas diretamente por e dentro da Base de Dados SQL. Isto simplifica significativamente os cenários em nuvem uma vez que uma implementação de MSDTC não é necessária para usar transações distribuídas com base de dados SQL. A secção 4 explica mais detalhadamente como implementar transações elásticas de base de dados e a estrutura .NET necessária juntamente com as suas aplicações em nuvem para o Azure.

## <a name="development-experience"></a>Experiência de desenvolvimento

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

As transações elásticas de base de dados para a Base de Dados SQL também suportam a coordenação de transações distribuídas onde utiliza o método OpenConnectionForKey da biblioteca de clientes de base de dados elástica para abrir ligações para um nível de dados escalonado. Considere os casos em que precisa de garantir a consistência transacional para alterações em vários valores chave de fragmentos diferentes. As ligações aos fragmentos que hospedam os diferentes valores-chave de fragmentos são intermediadas através do OpenConnectionForKey. No caso geral, as ligações podem ser de diferentes fragmentos, de modo a que garantir garantias transacionais requer uma transação distribuída.
A seguinte amostra de código ilustra esta abordagem. Assume que uma variável chamada shardmap é usada para representar um mapa de fragmentos da biblioteca de clientes de base de dados elástica:

```csharp
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
```

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

## <a name="transactions-across-multiple-servers"></a>Transações em vários servidores

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

As transações de base de dados elásticas são suportadas em diferentes servidores na Base de Dados Azure SQL. Quando as transações cruzam os limites do servidor, os servidores participantes precisam primeiro de ser introduzidos numa relação de comunicação mútua. Uma vez estabelecida a relação de comunicação, qualquer base de dados em qualquer um dos dois servidores pode participar em transações elásticas com bases de dados do outro servidor. Com transações abrangendo mais de dois servidores, uma relação de comunicação precisa estar em vigor para qualquer par de servidores.

Utilize os seguintes cmdlets PowerShell para gerir as relações de comunicação entre servidores para transações elásticas de base de dados:

* **New-AzSqlServerCommunicationLink**: Utilize este cmdlet para criar uma nova relação de comunicação entre dois servidores na Base de Dados Azure SQL. A relação é simétrica, o que significa que ambos os servidores podem iniciar transações com o outro servidor.
* **Get-AzSqlServerCommunicationLink**: Utilize este cmdlet para recuperar as relações de comunicação existentes e as suas propriedades.
* **Remove-AzSqlServerCommunicationLink**: Utilize este cmdlet para remover uma relação de comunicação existente.

## <a name="monitoring-transaction-status"></a>Monitorização do estado das transações

Utilize Vistas dinâmicas de gestão (DMVs) na Base de Dados SQL para monitorizar o estado e o progresso das suas transações de bases de dados elásticas em curso. Todos os DMVs relacionados com transações são relevantes para transações distribuídas na Base de Dados SQL. Pode encontrar aqui a lista correspondente de DMVs: [Vistas e funções de gestão dinâmica relacionadas com transações (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Estes DMVs são particularmente úteis:

* **sys.dm trans \_ \_ \_ transações ativas**trans: Lista as transações atualmente ativas e o seu estado. A coluna UOW (Unidade de Trabalho) pode identificar as diferentes transações infantis que pertencem à mesma transação distribuída. Todas as transações dentro da mesma transação distribuída têm o mesmo valor UOW. Consulte a documentação do [DMV](https://msdn.microsoft.com/library/ms174302.aspx) para obter mais informações.
* **sys.dm \_ \_ transações de base de \_ dados de trans**: Fornece informações adicionais sobre transações, tais como a colocação da transação no registo. Consulte a documentação do [DMV](https://msdn.microsoft.com/library/ms186957.aspx) para obter mais informações.
* **sys.dm tran \_ \_ locks**: Fornece informações sobre as fechaduras que são atualmente detidas por transações em curso. Consulte a documentação do [DMV](https://msdn.microsoft.com/library/ms190345.aspx) para obter mais informações.

## <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se atualmente às transações elásticas de base de dados na Base de Dados SQL:

* Apenas são suportadas transações através de bases de dados na Base de Dados SQL. Outros fornecedores de recursos [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) e bases de dados fora da BASE de Dados SQL não podem participar em transações de bases de dados elásticas. Isto significa que as transações elásticas de base de dados não podem estender-se nas instalações do SQL Server e da Azure SQL Database. Para transações distribuídas nas instalações, continue a utilizar o MSDTC.
* Apenas são suportadas transações coordenadas pelo cliente a partir de uma aplicação .NET. O suporte do lado do servidor para T-SQL, como BEGIN DISTRIBUTED TRANSACTION, está planeado, mas ainda não está disponível.
* As transações nos serviços do WCF não são suportadas. Por exemplo, tem um método de serviço WCF que executa uma transação. A possibilidade de a chamada ser executada num âmbito de transação falhará como [system.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Passos seguintes

Para obter perguntas, contacte-nos na [página de perguntas do Microsoft Q&Uma página de perguntas para a Base de Dados SQL](https://docs.microsoft.com/answers/topics/azure-sql-database.html). Para pedidos de funcionalidades, adicione-os ao fórum de feedback da [Base de Dados SQL.](https://feedback.azure.com/forums/217321-sql-database/)

<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
 