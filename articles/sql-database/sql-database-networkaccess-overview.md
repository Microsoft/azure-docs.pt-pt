---
title: Controles de acesso à rede
description: Visão geral dos controles de acesso à rede para o banco de dados SQL do Azure e data warehouse para gerenciar o acesso e configurar um banco de dados único ou em pool.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 16de1d9fcf86459b6bcadd9d8c372e436aad0915
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802945"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Controles de acesso à rede do banco de dados SQL do Azure e do data warehouse

> [!NOTE]
> Este artigo aplica-se ao SQL Server do Azure e ao banco de dados SQL e SQL Data Warehouse bancos que são criados no SQL Server do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.

> [!IMPORTANT]
> Este artigo não *se aplica* a **instância gerenciada do banco de dados SQL do Azure**. para obter mais informações sobre a configuração de rede, consulte [conectando-se a um instância gerenciada](sql-database-managed-instance-connect-app.md) .

Quando você cria uma nova SQL Server [do Azure do portal do Azure](sql-database-single-database-get-started.md), o resultado é um ponto de extremidade público no formato *yourservername.Database.Windows.net*. Por design, todo o acesso ao ponto de extremidade público é negado. Você pode usar os seguintes controles de acesso à rede para permitir seletivamente o acesso ao banco de dados SQl por meio do ponto de extremidade público
- Permitir serviços do Azure:-quando definido como ativado, outros recursos dentro do limite do Azure, por exemplo, uma máquina virtual do Azure, podem acessar o banco de dados SQL

- Regras de firewall de IP:-Use esse recurso para permitir explicitamente conexões de um endereço IP específico, por exemplo, de computadores locais.

- Regras de firewall de rede virtual:-Use esse recurso para permitir o tráfego de uma rede virtual específica dentro do limite do Azure

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Permitir serviços do Azure 
Durante a criação de uma nova SQL Server do Azure [do portal do Azure](sql-database-single-database-get-started.md), essa configuração é deixada desmarcada.

 ![Captura de tela da nova criação do servidor][1]

Você também pode alterar essa configuração por meio do painel Firewall depois que a SQL Server do Azure for criada da seguinte maneira.
  
 ![Captura de tela de gerenciar o Firewall do servidor][2]

Quando definido como **no** Azure SQL Server permite a comunicação de todos os recursos dentro do limite do Azure, que pode ou não fazer parte de sua assinatura.

Em muitos casos, a configuração **on** é mais permissiva do que a maioria dos clientes desejam. Eles podem querer definir essa configuração como **off** e substituí-la por regras de firewall IP mais restritivas ou regras de firewall de rede virtual. Isso afeta os seguintes recursos:

### <a name="import-export-service"></a>Importar serviço de exportação

O serviço de exportação de importação do banco de dados SQL do Azure é executado em VMs no Azure. Essas VMs não estão em sua VNet e, portanto, obtêm um IP do Azure ao se conectarem ao banco de dados. Ao remover **permitir que os serviços do Azure acessem o servidor** , essas VMs não poderão acessar seus bancos de dados.
Você pode contornar o problema executando a importação ou exportação de BACPAC diretamente em seu código usando a API DACFx.

### <a name="sql-database-query-editor"></a>Editor de consultas do banco de dados SQL

O editor de consultas do banco de dados SQL do Azure é implantado em VMs no Azure. Essas VMs não estão em sua VNet. Portanto, as VMs obtêm um IP do Azure ao se conectarem ao seu banco de dados. Ao remover **permitir que os serviços do Azure acessem o servidor**, essas VMs não poderão acessar seus bancos de dados.

### <a name="table-auditing"></a>Auditoria de tabela

No momento, há duas maneiras de habilitar a auditoria no banco de dados SQL. A auditoria de tabela falha depois que você habilita pontos de extremidade de serviço no SQL Server do Azure. A mitigação aqui é mudar para a auditoria de BLOB.

### <a name="impact-on-data-sync"></a>Impacto na sincronização de dados

O banco de dados SQL do Azure tem o recurso de sincronização de data que se conecta aos seus bancos de dado usando IPs do Azure. Ao usar os pontos de extremidade de serviço, você desligará a opção **permitir que os serviços do Azure acessem** o acesso do servidor ao servidor do banco de dados SQL e interromperão o recurso de sincronização.

## <a name="ip-firewall-rules"></a>Regras de firewall de IP
O firewall baseado em IP é um recurso do Azure SQL Server que impede todo o acesso ao seu servidor de banco de dados até que você adicione explicitamente os [endereços IP](sql-database-server-level-firewall-rule.md) dos computadores cliente.


## <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual

Além das regras de IP, o firewall de SQL Server do Azure permite que você defina *regras de rede virtual*.  
Para saber mais, consulte [pontos de extremidade de serviço de rede virtual e regras para o banco de dados SQL do Azure](sql-database-vnet-service-endpoint-rule-overview.md) ou assista a este vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Terminologia de rede do Azure  
Esteja atento aos seguintes termos de rede do Azure ao explorar as regras de firewall de rede virtual

**Rede virtual:** Você pode ter redes virtuais associadas à sua assinatura do Azure 

**Sub-rede:** Uma rede virtual contém **sub-redes**. Todas as VMs (máquinas virtuais) do Azure que você tem são atribuídas a sub-redes. Uma sub-rede pode conter várias VMs ou outros nós de computação. Os nós de computação que estão fora da sua rede virtual não podem acessar sua rede virtual, a menos que você configure sua segurança para permitir o acesso.

**Ponto de extremidade de serviço de rede virtual:** Um [ponto de extremidade de serviço de rede virtual] [VM-Virtual-Network-Service-Endpoint-Overview-649d] é uma sub-rede cujos valores de propriedade incluem um ou mais nomes formais de tipo de serviço do Azure. Neste artigo, estamos interessados no nome do tipo do **Microsoft. SQL**, que se refere ao serviço do Azure chamado banco de dados SQL.

**Regra de rede virtual:** Uma regra de rede virtual para o servidor do banco de dados SQL é uma sub-rede listada na lista de controle de acesso (ACL) do seu servidor do banco de dados SQL. Para estar na ACL do seu banco de dados SQL, a sub-rede deve conter o nome do tipo **Microsoft. SQL** . Uma regra de rede virtual instrui o servidor do banco de dados SQL a aceitar comunicações de cada nó que está na sub-rede.


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP versus regras de firewall de rede virtual

O Firewall do Azure SQL Server permite que você especifique os intervalos de endereços IP dos quais as comunicações são aceitas no banco de dados SQL. Essa abordagem é adequada para endereços IP estáveis que estão fora da rede privada do Azure. No entanto, as máquinas virtuais (VMs) na rede privada do Azure são configuradas com endereços IP *dinâmicos* . Os endereços IP dinâmicos podem mudar quando sua VM é reiniciada e, por sua vez, invalidar a regra de firewall baseada em IP. Seria ilusório especificar um endereço IP dinâmico em uma regra de firewall em um ambiente de produção.

Você pode contornar essa limitação obtendo um endereço IP *estático* para sua VM. Para obter detalhes, consulte [configurar endereços IP privados para uma máquina virtual usando o portal do Azure] [VM-configure-Private-IP-addresses-for-a-virtual-machine-using-the-Azure-portal-321w]. No entanto, a abordagem de IP estático pode se tornar difícil de gerenciar e é dispendiosa quando feita em escala. 

As regras de rede virtual são uma alternativa mais fácil de estabelecer e gerenciar o acesso de uma sub-rede específica que contém suas VMs.

> [!NOTE]
> Você ainda não pode ter o banco de dados SQL em uma sub-rede. Se o servidor do banco de dados SQL do Azure era um nó em uma sub-rede em sua rede virtual, todos os nós na rede virtual podem se comunicar com o banco de dados SQL. Nesse caso, suas VMs podem se comunicar com o banco de dados SQL sem a necessidade de nenhuma regra de rede virtual ou regras de IP.

## <a name="next-steps"></a>Passos seguintes

- Para obter um guia de início rápido sobre como criar uma regra de firewall de IP no nível de servidor, consulte [criar um banco de dados SQL do Azure](sql-database-single-database-get-started.md).

- Para obter um início rápido sobre como criar uma regra de firewall de vnet no nível de servidor, consulte [pontos de extremidade de serviço de rede virtual e regras para o banco de dados SQL do Azure](sql-database-vnet-service-endpoint-rule-overview.md).

- Para obter ajuda com a conexão com um banco de dados SQL do Azure de software livre ou aplicativos de terceiros, consulte [exemplos de código de início rápido do cliente para banco de dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- Para obter informações sobre portas adicionais que talvez precisem ser abertas, consulte a seção **banco de dados SQL: fora versus dentro** de [portas além de 1433 para ADO.NET 4,5 e banco de dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md)

- Para obter uma visão geral da conectividade do banco de dados SQL do Azure, consulte [arquitetura de conectividade do SQL do Azure](sql-database-connectivity-architecture.md)

- Para obter uma visão geral da segurança do banco de dados SQL do Azure, consulte [protegendo seu banco de dados](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png
