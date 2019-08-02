---
title: Transações distribuídas entre bases de dados de nuvem
description: Visão geral de transações de banco de dados elástico com Banco de dados SQL do Azure
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
ms.openlocfilehash: 3ca3e9074f28d66068d49b80915e98600759d9be
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568281"
---
# <a name="distributed-transactions-across-cloud-databases"></a>Transações distribuídas entre bases de dados de nuvem

As transações de banco de dados elástico para o banco de dados SQL do Azure (BD SQL) permitem que você execute transações que abrangem vários bancos de dados no BD SQL. As transações de banco de dados elástico para o BD SQL estão disponíveis para aplicativos .NET usando ADO .NET e se integram à experiência de programação familiar usando as classes [System. Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) . Para obter a biblioteca, consulte [.NET Framework 4.6.1 (instalador da Web)](https://www.microsoft.com/download/details.aspx?id=49981).

No local, um cenário como esse geralmente exigia a execução do Microsoft Coordenador de Transações Distribuídas (MSDTC). Como o MSDTC não está disponível para aplicativos de plataforma como serviço no Azure, a capacidade de coordenar transações distribuídas agora foi diretamente integrada ao banco de dado SQL. Aplicativos podem se conectar a qualquer banco de dados SQL para iniciar transações distribuídas, e um dos bancos de dados coordenará de forma transparente a transação distribuída, conforme mostrado na figura a seguir. 

  ![Transações distribuídas com o banco de dados SQL do Azure usando transações de banco de dados elástico ][1]

## <a name="common-scenarios"></a>Cenários comuns

As transações de banco de dados elástico para o BD SQL permitem que os aplicativos façam alterações atômicas nos dados armazenados em vários bancos de dado SQL diferentes. A visualização se concentra nas experiências de desenvolvimento do lado do C# cliente no e no .net. Uma experiência do servidor usando o T-SQL é planejada para um momento posterior.  
As transações de banco de dados elástico visam os seguintes cenários:

* Aplicativos de bancos de dados múltiplos no Azure: Com esse cenário, os dados são particionados verticalmente em vários bancos de dados no BD SQL, de modo que os diferentes tipos de dado residam em bancos diferentes. Algumas operações exigem alterações nos dados que são mantidos em dois ou mais bancos de dado. O aplicativo usa transações de banco de dados elástico para coordenar as alterações entre os bancos e garantir a atomicidade.
* Aplicativos de banco de dados fragmentados no Azure: Com esse cenário, a camada de dados usa a [biblioteca de cliente do banco](sql-database-elastic-database-client-library.md) de dado elástico ou a autofragmentação para particionar horizontalmente os dados em vários bancos de dado no BD SQL. Um caso de uso proeminente é a necessidade de executar alterações atômicas para um aplicativo multilocatário fragmentado quando as alterações abrangem locatários. Considere uma instância de uma transferência de um locatário para outro, que residem em bancos de dados diferentes. Um segundo caso é a fragmentação refinada para acomodar as necessidades de capacidade para um locatário grande que, por sua vez, normalmente implica que algumas operações atômicas precisam ser ampliadas em vários bancos de dados usados para o mesmo locatário. Um terceiro caso são as atualizações atômicas para fazer referência a dados que são replicados em bancos de dado. As operações atômicas, transacionadas, juntamente com essas linhas, agora podem ser coordenadas em vários bancos de dados usando a visualização.
  As transações de banco de dados elástico usam a confirmação de duas fases para garantir a atomicidade da transação entre bancos de dados. É uma boa opção para transações que envolvem menos de 100 bancos de dados por vez em uma única transação. Esses limites não são impostos, mas um deve esperar taxas de desempenho e êxito para que as transações de banco de dados elástico sejam prejudicadas ao exceder esses limites.

## <a name="installation-and-migration"></a>Instalação e migração

Os recursos para transações de banco de dados elástico no BD SQL são fornecidos por meio de atualizações para as bibliotecas .NET System. Data. dll e System. Transactions. dll. As DLLs garantem que a confirmação de duas fases seja usada quando necessário para garantir a atomicidade. Para começar a desenvolver aplicativos usando transações de banco de dados elástico, instale [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) ou uma versão posterior. Quando executado em uma versão anterior do .NET Framework, as transações não serão promovidas para uma transação distribuída e uma exceção será gerada.

Após a instalação, você pode usar as APIs de transação distribuída no System. Transactions com conexões com o banco de BD SQL. Se você tiver aplicativos existentes do MSDTC usando essas APIs, basta recompilar seus aplicativos existentes para o .NET 4,6 depois de instalar a estrutura 4.6.1. Se seus projetos tiverem como destino o .NET 4,6, eles usarão automaticamente as DLLs atualizadas da nova versão da estrutura e as chamadas da API de transação distribuída em combinação com as conexões com o banco de BD SQL agora serão bem sucedidos.

Lembre-se de que as transações de banco de dados elástico não exigem a instalação do MSDTC. Em vez disso, as transações de banco de dados elástico são gerenciadas diretamente pelo e no BD SQL. Isso simplifica significativamente os cenários de nuvem, já que uma implantação do MSDTC não é necessária para usar transações distribuídas com o banco de dado SQL. A seção 4 explica em mais detalhes como implantar transações de banco de dados elástico e o .NET Framework necessário junto com seus aplicativos de nuvem para o Azure.

## <a name="development-experience"></a>Experiência de desenvolvimento

### <a name="multi-database-applications"></a>Aplicativos de bancos de dados múltiplos

O código de exemplo a seguir usa a experiência de programação familiar com o .NET System. Transactions. A classe TransactionScope estabelece uma transação de ambiente no .NET. (Uma "transação de ambiente" é aquela que reside no thread atual.) Todas as conexões abertas no TransactionScope participam da transação. Se diferentes bancos de dados participarem, a transação será automaticamente elevada para uma transação distribuída. O resultado da transação é controlado pela definição do escopo a ser concluído para indicar uma confirmação.

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

### <a name="sharded-database-applications"></a>Aplicativos de banco de dados fragmentados

As transações de banco de dados elástico para o BD SQL também oferecem suporte à coordenação de transações distribuídas, em que você usa o método OpenConnectionForKey da biblioteca de cliente do banco de dados elástico para abrir conexões para uma camada expandida. Considere os casos em que você precisa garantir a consistência transacional para alterações em vários valores de chave de fragmentação diferentes. As conexões com os fragmentos que hospedam os diferentes valores de chave de fragmentação são orientadas usando OpenConnectionForKey. No caso geral, as conexões podem ser para diferentes fragmentos, de modo a garantir que as garantias transacionais exijam uma transação distribuída. O exemplo de código a seguir ilustra essa abordagem. Ele pressupõe que uma variável chamada shardmap é usada para representar um mapa de fragmentos da biblioteca de cliente do banco de dados elástico:

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


## <a name="net-installation-for-azure-cloud-services"></a>Instalação do .NET para serviços de nuvem do Azure

O Azure fornece várias ofertas para hospedar aplicativos .NET. Uma comparação das diferentes ofertas está disponível nos comparações de [serviço Azure app, serviços de nuvem e máquinas virtuais](/azure/architecture/guide/technology-choices/compute-decision-tree). Se o sistema operacional convidado da oferta for menor do que o .NET 4.6.1 necessário para transações elásticas, você precisará atualizar o sistema operacional convidado para 4.6.1. 

Para serviços Azure Apps, atualmente não há suporte para atualizações para o SO convidado. Para máquinas virtuais do Azure, basta fazer logon na VM e executar o instalador para o .NET Framework mais recente. Para os serviços de nuvem do Azure, você precisa incluir a instalação de uma versão mais recente do .NET nas tarefas de inicialização da sua implantação. Os conceitos e as etapas são documentados em [instalar o .net em uma função de serviço de nuvem](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Observe que o instalador do .NET 4.6.1 pode exigir mais armazenamento temporário durante o processo de inicialização nos serviços de nuvem do Azure do que o instalador para .NET 4,6. Para garantir uma instalação bem-sucedida, você precisa aumentar o armazenamento temporário para o serviço de nuvem do Azure em seu arquivo. csdef de sua definição na seção LocalResources e as configurações de ambiente da sua tarefa de inicialização, conforme mostrado no exemplo a seguir:

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
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

Transações de banco de dados elástico têm suporte em diferentes servidores de banco de dados SQL no banco de dados SQL do Azure. Quando as transações cruzam limites do servidor do banco de dados SQL, os servidores participantes precisam primeiro ser inseridos em uma relação de comunicação mútua. Depois que a relação de comunicação tiver sido estabelecida, qualquer banco de dados em qualquer um dos dois servidores poderá participar de transações elásticas com bancos de dados do outro servidor. Com transações que abrangem mais de dois servidores de banco de dados SQL, uma relação de comunicação precisa estar em vigor para qualquer par de servidores de banco de dados SQL.

Use os seguintes cmdlets do PowerShell para gerenciar relações de comunicação entre servidores para transações de banco de dados elástico:

* **New-AzSqlServerCommunicationLink**: Use este cmdlet para criar uma nova relação de comunicação entre dois servidores de banco de dados SQL no banco de dados SQL do Azure. A relação é simétrica, o que significa que ambos os servidores podem iniciar transações com o outro servidor.
* **Get-AzSqlServerCommunicationLink**: Use este cmdlet para recuperar relações de comunicação existentes e suas propriedades.
* **Remove-AzSqlServerCommunicationLink**: Use este cmdlet para remover uma relação de comunicação existente. 

## <a name="monitoring-transaction-status"></a>Status da transação de monitoramento

Use DMVs (exibições de gerenciamento dinâmico) no BD SQL para monitorar o status e o progresso de suas transações de banco de dados elástico contínuas. Todas as DMVs relacionadas a transações são relevantes para transações distribuídas no BD SQL. Você pode encontrar a lista correspondente de DMVs aqui: [Funções e exibições de gerenciamento dinâmico relacionadas à transação (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Essas DMVs são particularmente úteis:

* **sys.dm\_tran\_active\_transactions**: Lista as transações atualmente ativas e seu status. A coluna UOW (unidade de trabalho) pode identificar as diferentes transações filho que pertencem à mesma transação distribuída. Todas as transações na mesma transação distribuída têm o mesmo valor UOW. Consulte a [documentação da DMV](https://msdn.microsoft.com/library/ms174302.aspx) para obter mais informações.
* **sys.dm\_tran\_database\_transactions**: Fornece informações adicionais sobre transações, como o posicionamento da transação no log. Consulte a [documentação da DMV](https://msdn.microsoft.com/library/ms186957.aspx) para obter mais informações.
* **bloqueios\_do sys.dm\_Tran**: Fornece informações sobre os bloqueios atualmente mantidos por transações em andamento. Consulte a [documentação da DMV](https://msdn.microsoft.com/library/ms190345.aspx) para obter mais informações.

## <a name="limitations"></a>Limitações

As seguintes limitações atualmente se aplicam a transações de banco de dados elástico no BD SQL:

* Somente as transações entre bancos de dados no BD SQL têm suporte. Outros provedores de recursos [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) e bancos de dados fora do BD SQL não podem participar de transações de banco de dados elástico. Isso significa que as transações de banco de dados elástico não podem ser ampliadas na SQL Server local e no banco de dados SQL do Azure. Para transações distribuídas no local, continue a usar o MSDTC. 
* Somente as transações coordenadas pelo cliente de um aplicativo .NET têm suporte. O suporte do lado do servidor para o T-SQL, como iniciar transação DISTRIBUÍda, é planejado, mas ainda não está disponível. 
* Não há suporte para transações entre serviços WCF. Por exemplo, você tem um método de serviço WCF que executa uma transação. Colocar a chamada em um escopo de transação falhará como um [System. ServiceModel. ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Passos Seguintes

Para dúvidas, entre em contato conosco no fórum do [banco de dados SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) e para solicitações de recursos, adicione-os ao [Fórum de comentários do banco de dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png
