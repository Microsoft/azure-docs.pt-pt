---
title: Controlos de Acesso à Rede
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Visão geral de como gerir e controlar o acesso à rede para a Base de Dados Azure SQL e para a Azure Synapse Analytics (anteriormente SQL Data Warehouse).
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
ms.openlocfilehash: 4afb6844512bd59a5c377d826267a748837ed855
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952000"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-network-access-controls"></a>Azure SQL Database e Azure Synapse Analytics controlos de acesso à rede

Quando cria um servidor SQL lógico a partir do [portal Azure](single-database-create-quickstart.md) sql Database e Azure Synapse Analytics, o resultado é um ponto final público no formato, *yourservername.database.windows.net*.

Pode utilizar os seguintes controlos de acesso à rede para permitir o acesso seletivamente a uma base de dados através do ponto final público:

- Permitir serviços Azure: Quando definido para ON, outros recursos dentro do limite Azure, por exemplo uma Máquina Virtual Azure, podem aceder à Base de Dados SQL
- Regras de firewall IP: Utilize esta funcionalidade para permitir explicitamente ligações a partir de um endereço IP específico, por exemplo, a partir de máquinas no local

Também pode permitir o acesso privado à base de dados a partir de [redes virtuais](../../virtual-network/virtual-networks-overview.md) através de:

- Regras de firewall de rede virtual: Utilize esta funcionalidade para permitir o tráfego a partir de uma rede virtual específica dentro do limite Azure
- Link Privado: Utilize esta funcionalidade para criar um ponto final privado para [o servidor SQL lógico](logical-servers.md) dentro de uma rede virtual específica

> [!IMPORTANT]
> Este artigo *não* se aplica à **SQL Managed Instance**. Para obter mais informações sobre a configuração da rede, consulte [a ligação ao Azure SQL Managed Instance](../managed-instance/connect-application-instance.md) .

Veja o vídeo abaixo para obter uma explicação de alto nível destes controlos de acesso e o que fazem:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Permitir serviços Azure

Por predefinição durante a criação de um novo servidor lógico SQL [a partir do portal Azure,](single-database-create-quickstart.md)esta definição está definida para **OFF**. Esta definição aparece quando a conectividade é permitida usando o ponto final de serviço público.

Também pode alterar esta definição através do painel de firewall depois de o servidor lógico SQL ser criado da seguinte forma.
  
![Screenshot da firewall do servidor de gestão][2]

Quando definido para **ON,** o seu servidor permite comunicações a partir de todos os recursos dentro do limite Azure, que podem ou não fazer parte da sua subscrição.

Em muitos casos, a definição **ON** é mais permissiva do que a maioria dos clientes quer. É melhor definir esta definição para **OFF** e substituí-la por regras de firewall IP mais restritivas ou regras de firewall de rede virtual. 

No entanto, ao fazê-lo afeta as seguintes funcionalidades que funcionam em máquinas virtuais em Azure que não fazem parte da sua rede virtual e, portanto, ligam-se à base de dados através de um endereço IP Azure:

### <a name="import-export-service"></a>Serviço de Exportação de Importações

O Serviço de Exportação de Importações não funciona quando **o acesso aos serviços Azure** está definido para **OFF**. No entanto, pode contornar o problema [executando manualmente sqlpackage.exe a partir de um VM Azure ou realizando a exportação](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) diretamente no seu código utilizando a API DACFx.

### <a name="data-sync"></a>Sincronização de Dados

Para utilizar a funcionalidade de sincronização de dados com **o Permitir o acesso aos serviços Azure definidos** para **OFF,** é necessário criar entradas individuais de regras de firewall para [adicionar endereços IP](firewall-create-server-level-portal-quickstart.md) da tag de serviço **Sql** para a região que hospeda a base de dados **Hub.**
Adicione estas regras de firewall ao nível do servidor aos servidores que hospedam bases de dados **do Hub** e **dos Membros** (que podem estar em diferentes regiões)

Utilize o seguinte script PowerShell para gerar endereços IP correspondentes à tag de serviço SQL para a região dos EUA

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
> Get-AzNetworkServiceTag devolve a gama global de Tag de Serviço SQL, apesar de especificar o parâmetro Localização. Certifique-se de filtrar para a região que acolhe a base de dados Hub usada pelo seu grupo de sincronização

Note que a saída do script PowerShell está na notação de encaminhamento de Inter-Domain (CIDR) sem classe. Isto precisa de ser convertido para um formato de endereço IP iniciar e terminar usando [Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) como este:

```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Pode utilizar este script adicional do PowerShell para converter todos os endereços IP do FORMATO CIDR para o formato de endereço IP iniciar e terminar.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```

Agora pode adicionar estas como regras de firewall distintas e, em seguida, definir **Permitir que os serviços Azure acedam ao servidor**  off.

## <a name="ip-firewall-rules"></a>Regras de firewall IP

A firewall baseada em IP é uma característica do servidor lógico SQL em Azure que impede todo o acesso ao seu servidor até adicionar explicitamente [endereços IP](firewall-create-server-level-portal-quickstart.md) das máquinas do cliente.

## <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual

Além das regras ip, a firewall do servidor permite definir *regras de rede virtuais*.  
Para saber mais, consulte [os pontos finais e regras do serviço de rede virtual para a Base de Dados Azure SQL](vnet-service-endpoint-rule-overview.md) ou veja este vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

### <a name="azure-networking-terminology"></a>Terminologia de rede Azure

Esteja atento aos seguintes termos de Rede Azure enquanto explora as regras de firewall de rede virtual

**Rede virtual:** Pode ter redes virtuais associadas à sua subscrição Azure

**Sub-rede:** Uma rede virtual contém **sub-redes.** Quaisquer máquinas virtuais Azure (VMs) que tenha estão atribuídas a sub-redes. Uma sub-rede pode conter vários VMs ou outros nós computacional. Os nós computacional que estão fora da sua rede virtual não podem aceder à sua rede virtual a menos que configuure a sua segurança para permitir o acesso.

**Ponto final do serviço de rede virtual:** Um [ponto final de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md) é uma sub-rede cujos valores de propriedade incluem um ou mais nomes formais de tipo de serviço Azure. Neste artigo estamos interessados no nome do **tipo Microsoft.Sql,** que se refere ao serviço Azure chamado SQL Database.

**Regra de rede virtual:** Uma regra de rede virtual para o seu servidor é uma sub-rede que está listada na lista de controlo de acesso (ACL) do seu servidor. Para estar na ACL para a sua base de dados na Base de Dados SQL, a sub-rede deve conter o nome do tipo **Microsoft.Sql.** Uma regra de rede virtual diz ao seu servidor para aceitar comunicações de todos os nó que estão na sub-rede.

## <a name="ip-vs-virtual-network-firewall-rules"></a>Regras de firewall de rede virtual

A firewall Azure SQL Database permite especificar as gamas de endereços IP a partir das quais as comunicações são aceites na Base de Dados SQL. Esta abordagem é boa para endereços IP estáveis que estão fora da rede privada Azure. No entanto, as máquinas virtuais (VMs) dentro da rede privada Azure estão configuradas com endereços IP *dinâmicos.* Os endereços IP dinâmicos podem ser alterados quando o seu VM é reiniciado e, por sua vez, invalidar a regra de firewall baseada em IP. Seria uma loucura especificar um endereço IP dinâmico numa regra de firewall, num ambiente de produção.

Pode contornar esta limitação obtendo um endereço IP *estático* para o seu VM. Para mais informações, consulte [Criar uma máquina virtual com um endereço IP público estático utilizando o portal Azure](../../virtual-network/virtual-network-deploy-static-pip-arm-portal.md). No entanto, a abordagem ip estática pode tornar-se difícil de gerir, e é dispendiosa quando feita em escala.

As regras de rede virtual são mais fáceis de estabelecer e gerir o acesso a partir de uma sub-rede específica que contém os seus VMs.

> [!NOTE]
> Ainda não é possível ter base de dados SQL numa sub-rede. Se o seu servidor fosse um nó numa sub-rede na sua rede virtual, todos os nós dentro da rede virtual poderiam comunicar com a sua Base de Dados SQL. Neste caso, os seus VMs podem comunicar com a SQL Database sem precisar de quaisquer regras de rede virtuais ou regras de IP.

## <a name="private-link"></a>Ligação Privada

O Private Link permite-lhe ligar-se a um servidor através de um **ponto final privado.** Um ponto final privado é um endereço IP privado dentro de uma [rede virtual](../../virtual-network/virtual-networks-overview.md) específica e sub-rede.

## <a name="next-steps"></a>Passos seguintes

- Para iniciar rapidamente a criação de uma regra de firewall IP de nível de servidor, consulte [Criar uma base de dados na Base de Dados SQL](single-database-create-quickstart.md).

- Para iniciar rapidamente a criação de uma regra de firewall de rede virtual ao nível do servidor, consulte [os pontos finais e regras do serviço de rede virtual para a Base de Dados Azure SQL](vnet-service-endpoint-rule-overview.md).

- Para obter ajuda para se ligar a uma base de dados na Base de Dados SQL a partir de aplicações de código aberto ou de terceiros, consulte [as amostras de código quickstart do Cliente para a Base de Dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- Para obter informações sobre portas adicionais que possa ter de abrir, consulte a **Base de Dados SQL: Fora vs secção interna** de Portos para além de [1433 para ADO.NET 4.5 e SQL Database](adonet-v12-develop-direct-route-ports.md)

- Para uma visão geral da Conectividade da Base de Dados Azure SQL, consulte [Azure SQL Connectivity Architecture](connectivity-architecture.md)

- Para obter uma visão geral da segurança da Base de Dados Azure SQL, consulte [a sua base de dados](security-overview.md)

<!--Image references-->
[1]: media/quickstart-create-single-database/new-server2.png
[2]: media/quickstart-create-single-database/manage-server-firewall.png
 
