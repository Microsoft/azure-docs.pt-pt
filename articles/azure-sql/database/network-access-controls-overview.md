---
title: Controlos de Acesso à Rede
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Visão geral de como gerir e controlar o acesso à rede para a Base de Dados Azure SQL e para o Armazém de Dados SQL (anteriormente SQL Data Warehouse).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 95fa7a22f88d8c6a53a6459e0f5d6a123b2f728b
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045579"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-network-access-controls"></a>Azure SQL Database e Azure Synapse Analytics controlos de acesso à rede
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Quando cria um servidor SQL lógico a partir do [portal Azure](single-database-create-quickstart.md) para a Base de Dados Azure SQL e a Azure Synapse Analytics, o resultado é um ponto final público no formato, *yourservername.database.windows.net*.

Pode utilizar os seguintes controlos de acesso à rede para permitir seletivamente o acesso a uma base de dados através do ponto final do público:

- Permitir serviços Azure: Quando definido para ON, outros recursos dentro do limite Azure, por exemplo uma máquina virtual Azure, podem aceder à Base de Dados SQL
- Regras de firewall IP: Utilize esta funcionalidade para permitir explicitamente ligações a partir de um endereço IP específico, por exemplo, a partir de máquinas no local

Também pode permitir o acesso privado à base de dados a partir de [Redes Virtuais](../../virtual-network/virtual-networks-overview.md) através de:

- Regras de firewall da Rede Virtual: Utilize esta funcionalidade para permitir o tráfego de uma rede virtual específica dentro do limite Azure
- Link Privado: Utilize esta funcionalidade para criar um ponto final privado para [servidor SQL lógico](logical-servers.md) dentro de uma rede virtual específica

> [!IMPORTANT]
> Este artigo *não* se aplica à **Instância Gerida sQL**. Para obter mais informações sobre a configuração de rede, consulte [a ligação à Instância Gerida azure SQL](../managed-instance/connect-application-instance.md) .

Veja o vídeo abaixo para obter uma explicação de alto nível destes controlos de acesso e do que eles fazem:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Permitir serviços Azure

Durante a criação de um novo servidor Lógico SQL [a partir do portal Azure,](single-database-create-quickstart.md)esta definição não é controlada.

Também pode alterar esta definição através do painel de firewall depois de o servidor Lógico SQL ser criado da seguinte forma.
  
![Screenshot da firewall do servidor de gestão][2]

Quando definido para **ON,** o seu servidor permite comunicações de todos os recursos dentro do limite Azure, que podem ou não fazer parte da sua subscrição.

Em muitos casos, a definição **de ON** é mais permissiva do que a maioria dos clientes quer. É melhor definir esta definição para **OFF** e substituí-la por regras de firewall IP mais restritivas ou regras de firewall da Rede Virtual. 

No entanto, fazê-lo afeta as seguintes funcionalidades que funcionam em máquinas virtuais em Azure que não fazem parte do seu VNet e, portanto, ligam-se à base de dados através de um endereço IP Azure:

### <a name="import-export-service"></a>Serviço de Exportação de Importação

O Serviço de Exportação de Importação não funciona quando **o acesso aos serviços Azure** está definido para **off**. No entanto, pode contornar o problema [executando manualmente sqlpackage.exe a partir de um VM Azure ou realizando a exportação](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) diretamente no seu código utilizando a API DACFx.

### <a name="data-sync"></a>Sincronização de Dados

Para utilizar a funcionalidade de sincronização de dados com **permitir o acesso aos serviços Azure** definidos para **OFF,** é necessário criar entradas individuais de regras de firewall para [adicionar endereços IP](firewall-create-server-level-portal-quickstart.md) a partir da etiqueta de serviço **Sql** para a região que acolhe a base de dados **Hub.**
Adicione estas regras de firewall ao nível do servidor aos servidores que hospedam bases de dados **hub** e **membros** (que podem estar em diferentes regiões)

Utilize o seguinte script PowerShell para gerar endereços IP correspondentes à etiqueta de serviço SQL para a região dos EUA Ocidentais

```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> Get-AzNetworkServiceTag devolve a gama global de etiqueta de serviço SQL, apesar de especificar o parâmetro de localização. Certifique-se de filtrar para a região que acolhe a base de dados hub utilizada pelo seu grupo de sincronização

Note que a saída do script PowerShell está na notação de Encaminhamento Inter-Domínio Sem Classe (CIDR) e esta precisa de ser convertida para um formato de endereço IP inicial e final utilizando [get-iPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) como este:

```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Pode utilizar este script powerShell adicional para converter todos os endereços IP do CIDR para iniciar e terminar o formato de endereço IP.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```

Agora pode adicioná-las como regras de firewall distintas e, em seguida, definir os serviços de Permitir que os **serviços do Azure acedam ao servidor** para OFF.

## <a name="ip-firewall-rules"></a>Regras de firewall IP

Firewall baseado ip é uma característica do servidor lógico SQL em Azure que impede todo o acesso ao seu servidor de base de dados até adicionar explicitamente [endereços IP](firewall-create-server-level-portal-quickstart.md) das máquinas clientes.

## <a name="virtual-network-firewall-rules"></a>Regras de firewall da Rede Virtual

Além das regras IP, a firewall do servidor permite definir *regras de rede virtuais*.  
Para saber mais, consulte pontos finais e regras do serviço de Rede Virtual para a Base de [Dados Azure SQL](vnet-service-endpoint-rule-overview.md) ou veja este vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

### <a name="azure-networking-terminology"></a>Terminologia azure networking

Esteja atento aos seguintes termos de Networking Azure enquanto explora as regras de firewall da Rede Virtual

**Rede virtual:** Pode ter redes virtuais associadas à sua subscrição Azure

**Subnet:** Uma rede virtual contém **subredes.** Quaisquer máquinas virtuais Azure (VMs) que tenha sacado a subredes. Uma subnet pode conter vários VMs ou outros nódosos de computação. Os nódosos computacionais que estão fora da sua rede virtual não podem aceder à sua rede virtual a menos que configure a sua segurança para permitir o acesso.

Ponto final do serviço de **rede virtual:** Um ponto final de [serviço de Rede Virtual](../../virtual-network/virtual-network-service-endpoints-overview.md) é uma subnet cujos valores de propriedade incluem um ou mais nomes formais do tipo de serviço Azure. Neste artigo estamos interessados no nome tipo **microsoft.Sql**, que se refere ao serviço Azure chamado SQL Database.

**Regra da rede virtual:** Uma regra de rede virtual para o seu servidor é uma sub-rede que está listada na lista de controlo de acesso (ACL) do seu servidor. Para estar na ACL para a sua Base de Dados SQL, a sub-rede deve conter o nome do tipo **Microsoft.Sql.** Uma regra de rede virtual diz ao seu servidor para aceitar comunicações de cada nó que está na subnet.

## <a name="ip-vs-virtual-network-firewall-rules"></a>Regras de firewall IP vs. Rede Virtual

A firewall azure SQL Database permite especificar intervalos de endereçoIP a partir dos quais as comunicações são aceites na Base de Dados SQL. Esta abordagem é boa para endereços IP estáveis que estão fora da rede privada Azure. No entanto, as máquinas virtuais (VMs) dentro da rede privada Azure estão configuradas com endereços IP *dinâmicos.* Os endereços IP dinâmicos podem alterar-se quando o seu VM é reiniciado e, por sua vez, invalidar a regra de firewall baseada em IP. Seria uma loucura especificar um endereço IP dinâmico numa regra de firewall, num ambiente de produção.

Pode contornar esta limitação obtendo um endereço IP *estático* para o seu VM. Para mais detalhes, consulte os [endereços IP privados para uma máquina virtual utilizando o portal Azure](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). No entanto, a abordagem ip estática pode tornar-se difícil de gerir, e é dispendiosa quando feita em escala.

As regras de rede virtual são alternativas mais fáceis de estabelecer e gerir o acesso a partir de uma subnet específica que contém os seus VMs.

> [!NOTE]
> Ainda não é possível ter base de dados SQL numa sub-rede. Se o seu servidor fosse um nó numa subnet na sua rede virtual, todos os nós dentro da rede virtual poderiam comunicar com a sua Base de Dados SQL. Neste caso, os seus VMs poderiam comunicar com a Base de Dados SQL sem precisar de quaisquer regras de rede virtual ou regras ip.

## <a name="private-link"></a>Ligação Privada

O Private Link permite-lhe ligar-se a um servidor através de um **ponto final privado**. Um ponto final privado é um endereço IP privado dentro de uma [rede virtual](../../virtual-network/virtual-networks-overview.md) específica e subnet.

## <a name="next-steps"></a>Próximos passos

- Para um início rápido na criação de uma regra de firewall IP ao nível do servidor, consulte Criar uma base de dados na Base de [Dados SQL](single-database-create-quickstart.md).

- Para um início rápido na criação de uma regra de firewall Vnet ao nível do servidor, consulte pontos finais de serviço de rede virtual e regras para a Base de [Dados Azure SQL](vnet-service-endpoint-rule-overview.md).

- Para obter ajuda na ligação a uma base de dados na Base de Dados SQL a partir de aplicações de código aberto ou de terceiros, consulte as amostras de código de arranque rápido do Cliente para a Base de [Dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- Para obter informações sobre portas adicionais que poderá necessitar de abrir, consulte a Base de **Dados SQL: Fora vs secção interna** de Portas para além de [1433 para ADO.NET base de dados 4.5 e SQL](adonet-v12-develop-direct-route-ports.md)

- Para uma visão geral da Conectividade azure SQL Database, consulte [Azure SQL Connectivity Architecture](connectivity-architecture.md)

- Para obter uma visão geral da segurança da Base de Dados Azure SQL, consulte [A segurança da sua base de dados](security-overview.md)

<!--Image references-->
[1]: media/quickstart-create-single-database/new-server2.png
[2]: media/quickstart-create-single-database/manage-server-firewall.png
