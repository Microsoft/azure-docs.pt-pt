---
title: Transações distribuídas entre bases de dados de nuvem
description: Descrição geral das transações da base de dados elástica com a base de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: d7865d394dfc955a7b24115e747dd77352d89e3d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901921"
---
# <a name="distributed-transactions-across-cloud-databases"></a>Transações distribuídas entre bases de dados de nuvem

Transações de base de dados elástica de base de dados do Azure SQL (SQL DB) permitem-lhe executar transações que abrangem várias bases de dados no SQL DB. Transações de base de dados elástica para a BD SQL estão disponíveis para aplicativos .NET usando o ADO .NET e integrar com a através de experiência de programação familiar a [System. Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) classes. Para obter a biblioteca, consulte [.NET Framework 4.6.1 (instalador Web)](https://www.microsoft.com/download/details.aspx?id=49981).

No local, um cenário desse tipo normalmente necessário executar o coordenador de transações distribuídas ' (MSDTC) da Microsoft. Uma vez que o MSDTC não está disponível para aplicações de plataforma-como-serviço no Azure, a capacidade de coordenar transações distribuídas agora foi diretamente integrada para o SQL DB. Aplicativos podem se conectar a qualquer base de dados SQL para iniciar transações distribuídas, e uma das bases de dados transparente coordenará a transação distribuída, conforme mostrado na figura a seguir. 

  ![Transações distribuídas com a base de dados do SQL Azure usando transações da base de dados elástica ][1]

## <a name="common-scenarios"></a>Cenários comuns

Transações de bases de dados elásticas para o SQL DB permitem aplicações fazer alterações atômicas para dados armazenados em vários diferentes bancos de dados do SQL. A pré-visualização se concentra em experiências de desenvolvimento do lado do cliente em c# e .NET. Uma experiência de lado do servidor com o T-SQL está prevista para um horário posterior.  
Transações de bases de dados elásticas destina-se os seguintes cenários:

* Aplicações de múltiplas base de dados no Azure: Com este cenário, os dados são particionados verticalmente em várias bases de dados no SQL DB, de modo a que os diferentes tipos de dados residem em diferentes bases de dados. Algumas operações requerem alterações aos dados que são mantidos em duas ou mais bases de dados. O aplicativo usa transações da base de dados elástica para coordenar as alterações em bancos de dados e certifique-se de atomicidade.
* Aplicações de base de dados em partição horizontal no Azure: Com este cenário, a camada de dados utiliza o [biblioteca de clientes de bases de dados elásticas](sql-database-elastic-database-client-library.md) ou self-fragmentação partição horizontal os dados em muitas bases de dados no SQL DB. Um caso de uso proeminente é a necessidade de fazer alterações de atômicas para uma aplicação em partição horizontal do multi-inquilino quando alterações span inquilinos. Imagine por exemplo uma transferência de um inquilino para outro, ambos os que residem em diferentes bases de dados. Um segundo caso é a fragmentação refinada para acomodar as necessidades de capacidade para um inquilino de grandes dimensões que por sua vez, normalmente, implica que algumas operações atómicas precisa apareça em vários bancos de dados utilizados para o mesmo inquilino. Um terceiro caso é atômicas atualizações para os dados que são replicados entre bases de dados de referência. Operações atômicas, transacionadas, juntamente com estas linhas agora podem ser coordenadas entre várias bases de dados a utilizar a pré-visualização.
  Transações de bases de dados elásticas utilizam confirmação de duas fases para se certificar de atomicidade transações entre bases de dados. É uma boa opção para transações que envolvem menos de 100 bases de dados ao mesmo tempo numa única transação. Estes limites não são impostos, mas um deve esperar, desempenho e taxas de êxito para transações da base de dados elástica afetado quando exceder estes limites.

## <a name="installation-and-migration"></a>Instalação e migração

As capacidades para as transações de bases de dados elásticas SQL DB são fornecidas por meio de atualizações para as bibliotecas .NET Transactions. dll e. As DLLs Certifique-se de que confirmação de duas fases é utilizada quando necessário para se certificar de atomicidade. Para começar a desenvolver aplicativos usando transações da base de dados elástica, instale [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) ou uma versão posterior. Quando em execução numa versão anterior do .NET framework, as transações falhará promover a uma transação distribuída e vai ser gerada uma exceção.

Após a instalação, pode usar a transação distribuída APIs System. Transactions com ligações a BD SQL. Se tiver aplicativos existentes do MSDTC utilizando essas APIs, apenas recompilar os aplicativos existentes para o .NET 4.6 depois de instalar o 4.6.1 Framework. Se seus projetos de destino .NET 4.6, eles irão utilizar automaticamente as DLLs atualizadas da nova versão do Framework e transações distribuídas a chamadas de API em combinação com ligações ao SQL DB agora terá êxito.

Lembre-se de que as transações de bases de dados elásticas não requerem instalação MSDTC. Em vez disso, as transações de bases de dados elásticas são geridas diretamente pelo e dentro de BD SQL. Isso simplifica significativamente a cenários de nuvem, uma vez que uma implementação do MSDTC não é necessária utilizar transações distribuídas com a BD SQL. Secção 4 explica mais detalhadamente como implementar as transações de bases de dados elásticas e o .NET framework necessário em conjunto com as suas aplicações na cloud para o Azure.

## <a name="development-experience"></a>Experiência de desenvolvimento

### <a name="multi-database-applications"></a>Aplicações de múltiplas base de dados

O código de exemplo seguinte utiliza a experiência de programação familiar com System. Transactions do .NET. A classe TransactionScope estabelece uma transação de ambiente no .NET. (Uma "transação de ambiente" é aquele que reside no thread atual.) Todas as ligações abertas no TransactionScope participam na transação. Se os diferentes bancos de dados participam, a transação seja automaticamente elevada a uma transação distribuída. O resultado da transação é controlado através da definição do escopo para concluir para indicar uma consolidação.

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

### <a name="sharded-database-applications"></a>Aplicações de base de dados em partição horizontal

Transações de bases de dados elásticas para o SQL DB também suportam a coordenação de transações distribuídas em que usar o método de OpenConnectionForKey da biblioteca de clientes de bases de dados elásticas para abrir ligações para um dados aumentados horizontalmente camadas. Considere casos em que precisa para garantir a consistência transacional para alterações em vários valores de chave de fragmentação diferentes. Ligações para as partições horizontais que aloja os valores de chave de fragmentação diferentes são mediadas OpenConnectionForKey a utilizar. No caso geral, as ligações podem ser a partições horizontais diferentes, de modo a que garantir garantias transacionais requer uma transação distribuída. O exemplo de código a seguir ilustra essa abordagem. Parte do princípio de que uma variável chamada shardmap é usada para representar um mapa de partições horizontais da biblioteca de cliente de bases de dados elásticas:

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


## <a name="net-installation-for-azure-cloud-services"></a>Instalação do .NET para serviços Cloud do Azure

O Azure fornece várias ofertas para alojar aplicações .NET. Uma comparação das ofertas diferentes está disponível no [comparação do serviço de aplicações do Azure, serviços Cloud e máquinas virtuais](../app-service/overview-compare.md). Se o SO convidado da oferta é menor do que o .NET 4.6.1 necessários para transações elásticas, terá de atualizar o SO convidado para 4.6.1. 

Para serviços de aplicações do Azure, as atualizações para o SO convidado não são atualmente suportadas. Para máquinas de virtuais do Azure, basta iniciar sessão na VM e execute o instalador para o .NET framework mais recente. Serviços Cloud do Azure, terá de incluir a instalação de uma versão mais recente do .NET para as tarefas de arranque da sua implementação. Os conceitos e passos documentados em [instalar o .NET numa função de serviço Cloud](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Tenha em atenção que o instalador para o .NET 4.6.1 pode exigir mais armazenamento temporário durante o processo de inicialização nos serviços cloud do Azure que o instalador para .NET 4.6. Para garantir uma instalação com êxito, precisa aumentar o armazenamento temporário para o seu serviço cloud do Azure no seu ficheiro servicedefinition. Csdef a secção de LocalResources e as definições de ambiente de sua tarefa de arranque, como mostrado no exemplo a seguir:

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

## <a name="transactions-across-multiple-servers"></a>Transações entre vários servidores

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é suportado pelo SQL Database do Azure, mas todo o desenvolvimento futuro é para o módulo de Az.Sql. Para estes cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Transações de bases de dados elásticas são suportadas em diferentes servidores de base de dados SQL na base de dados do Azure SQL. Quando as transações ultrapassam os limites do servidor de base de dados SQL, os servidores participantes primeiro tem de ser introduzidos numa relação de comunicação bidirecional. Assim que tiver sido estabelecida a relação de comunicação, qualquer base de dados em qualquer um dos dois servidores pode participar em transações elásticas com bases de dados de outro servidor. Com transações que abrange mais de dois servidores de base de dados SQL, uma relação de comunicação tem de estar no local para qualquer par de servidores de base de dados SQL.

Utilize os seguintes cmdlets do PowerShell para gerir as relações de comunicação entre servidores de transações de bases de dados elásticas:

* **New-AzSqlServerCommunicationLink**: Utilize este cmdlet para criar uma nova relação de comunicação entre dois servidores de base de dados SQL na base de dados do Azure SQL. A relação é simétrica, que significa que ambos os servidores podem iniciar transações com o outro servidor.
* **Get-AzSqlServerCommunicationLink**: Utilize este cmdlet para obter as relações de comunicação existentes e as respetivas propriedades.
* **Remove-AzSqlServerCommunicationLink**: Utilize este cmdlet para remover uma relação de comunicação existente. 

## <a name="monitoring-transaction-status"></a>Monitorização do Estado de transação

Utilize vistas de gestão dinâmica (DMVs) no SQL DB para monitorizar o estado e o progresso das suas transações de bases de dados elásticas em curso. Todas as DMVs relacionados com transações são relevantes para transações distribuídas na BD SQL. Pode encontrar a lista correspondente de DMVs aqui: [Transações relacionadas a funções (Transact-SQL) e exibições de gerenciamento dinâmico](https://msdn.microsoft.com/library/ms178621.aspx).

Esses DMVs são particularmente úteis:

* **sys.dm\_tran\_active\_transactions**: Apresenta uma lista de transações atualmente ativas e o respetivo estado. A coluna UOW (unidade de trabalho) pode identificar as transações de diferentes filho que pertencem à mesma transação distribuída. Todas as transações na mesma transação distribuída realizar o mesmo valor UOW. Consulte a [documentação de DMV](https://msdn.microsoft.com/library/ms174302.aspx) para obter mais informações.
* **sys.dm\_tran\_database\_transactions**: Fornece informações adicionais sobre transações, como posicionamento da transação no registo. Consulte a [documentação de DMV](https://msdn.microsoft.com/library/ms186957.aspx) para obter mais informações.
* **sys.dm\_tran\_locks**: Fornece informações sobre os bloqueios que atualmente são mantidas por transações em curso. Consulte a [documentação de DMV](https://msdn.microsoft.com/library/ms190345.aspx) para obter mais informações.

## <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se atualmente a transações de bases de dados elásticas na BD SQL:

* Somente as transações entre bases de dados no SQL DB são suportadas. Outros [X / abra XA](https://en.wikipedia.org/wiki/X/Open_XA) bases de dados fora da BD SQL e fornecedores de recursos não podem participar em transações de bases de dados elásticas. Isso significa que transações de bases de dados elásticas não podem esticar no local do SQL Server e base de dados do Azure SQL. Para as transações distribuídas no local, continue a utilizar o MSDTC. 
* Coordenar a ação de cliente somente as transações de um aplicativo do .NET são suportadas. Suporte de servidor para T-SQL, tais como iniciar a transação distribuída está planeada, mas ainda não está disponível. 
* Não são suportadas transações em todos os serviços do WCF. Por exemplo, tem um método de serviço do WCF que executa uma transação. Colocar a chamada dentro de um âmbito de transação falhará como um [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Passos Seguintes

Para fazer perguntas, contacte-no [fórum de base de dados SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) e para pedidos de funcionalidades, adicione-os para o [fórum de comentários de base de dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png
