---
title: Link privado para o banco de dados SQL do Azure e data warehouse | Microsoft Docs
description: Visão geral do recurso de ponto de extremidade privado
author: rohitnayakmsft
ms.author: rohitna
ms.service: sql-database
ms.topic: overview
ms.reviewer: vanto
ms.date: 09/17/2019
ms.openlocfilehash: 3adcccde89823f865535c9e1350ac4c3e541eef6
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "71066260"
---
# <a name="private-link-for-azure-sql-database-and-data-warehouse-preview"></a>Link privado para o banco de dados SQL do Azure e data warehouse (visualização)

O link privado permite que você se conecte a vários serviços de PaaS no Azure por meio de um **ponto de extremidade privado**. Para obter uma lista dos serviços de PaaS que dão suporte à funcionalidade de link privado, vá para a página de [documentação do link privado](../private-link/index.yml) . Um ponto de extremidade privado é um endereço IP privado em uma [VNet](../virtual-network/virtual-networks-overview.md) e sub-rede específicas. 

> [!IMPORTANT]
> Este artigo aplica-se ao SQL Server do Azure e ao banco de dados SQL e SQL Data Warehouse bancos que são criados no SQL Server do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse. Este artigo não *se aplica* a uma implantação de **instância gerenciada** no banco de dados SQL do Azure.

## <a name="data-exfiltration-prevention"></a>Prevenção de vazamento de dados

Data vazamento no banco de dados SQL do Azure ocorre quando um usuário autorizado, como um administrador de banco de dados, é capaz de extrair dados de um sistema e movê-los para outro local ou sistema fora da organização. Por exemplo, o usuário move os dados para uma conta de armazenamento pertencente a terceiros.

Considere um cenário com um usuário executando o SQL Server Management Studio (SSMS) dentro de uma VM do Azure conectando-se a um banco de dados SQL. Este banco de dados SQL está no data center do oeste dos EUA. O exemplo a seguir mostra como limitar o acesso com pontos de extremidade públicos no banco de dados SQL usando controles de acesso à rede.

1. Desabilite todo o tráfego de serviço do Azure para o banco de dados SQL por meio do ponto de extremidade público Configurando **permitir que os**serviços do Azure Certifique-se de que nenhum endereço IP seja permitido nas regras de firewall no nível do servidor e do banco de dados. Para obter mais informações, consulte [banco de dados SQL do Azure e controles de acesso à rede data warehouse](sql-database-networkaccess-overview.md).
1. Só permita o tráfego para o banco de dados SQL usando o endereço IP privado da VM. Para obter mais informações, consulte os artigos sobre as regras de [ponto de extremidade de serviço](sql-database-vnet-service-endpoint-rule-overview.md) e [Firewall VNet](sql-database-firewall-configure.md).
1. Na VM do Azure, restrinja o escopo da conexão de saída usando [NSGs (grupos de segurança de rede)](../virtual-network/manage-network-security-group.md) e marcas de serviço da seguinte maneira
    - Especifique uma regra de NSG para permitir o tráfego para a marca de serviço = SQL. Westus-permitindo somente a conexão com o banco de dados SQL no oeste dos EUA
    - Especifique uma regra de NSG (com **prioridade mais alta**) para negar o tráfego para a marca de serviço = SQL negando conexões ao banco de dados SQL em todas as regiões

No final desta configuração, a VM do Azure pode se conectar somente a bancos de dados SQL na região oeste dos EUA. No entanto, a conectividade não é restrita a um único banco de dados SQL. A VM ainda pode se conectar a qualquer banco de dados SQL na região oeste dos EUA, incluindo os bancos de dados que não fazem parte da assinatura. Embora tenhamos reduzido o escopo dos dados vazamento no cenário acima para uma região específica, nós não o eliminamos completamente.

Com o link privado, os clientes agora podem configurar controles de acesso à rede como NSGs para restringir o acesso ao ponto de extremidade privado. Os recursos de PaaS individuais do Azure são então mapeados para pontos de extremidade privados específicos. Um insider mal-intencionado só pode acessar o recurso de PaaS mapeado (por exemplo, um banco de dados SQL) e nenhum outro recurso. 

## <a name="on-premises-connectivity-over-private-peering"></a>Conectividade local sobre emparelhamento privado

Quando os clientes se conectam ao ponto de extremidade público de computadores locais, seu endereço IP precisa ser adicionado ao firewall baseado em IP usando uma [regra de firewall de nível de servidor](sql-database-server-level-firewall-rule.md). Embora esse modelo funcione bem para permitir o acesso a computadores individuais para cargas de trabalho de desenvolvimento ou teste, é difícil gerenciar em um ambiente de produção.

Com o link privado, os clientes podem habilitar o acesso entre instalações ao ponto de extremidade privado usando o [ExpressRoute](../expressroute/expressroute-introduction.md), o emparelhamento privado ou o túnel de VPN. Os clientes podem então desabilitar todo o acesso por meio do ponto de extremidade público e não usar o firewall baseado em IP para permitir qualquer endereço IP.

Com o link privado, os clientes podem habilitar o acesso entre instalações ao ponto de extremidade privado usando o túnel de VPN ou o emparelhamento privado do ER (Express Route). Em seguida, eles podem desabilitar todo o acesso por meio do ponto de extremidade público e não usar o firewall baseado em IP.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Como configurar o link privado para o banco de dados SQL do Azure 

### <a name="creation-process"></a>Processo de criação
Os pontos de extremidade privados podem ser criados usando o portal, o PowerShell ou o CLI do Azure:
- [Portal](../private-link/create-private-endpoint-portal.md)
- [PowerShell](../private-link/create-private-endpoint-powershell.md)
- [CLI](../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Processo de aprovação
Depois que o administrador de rede cria o ponto de extremidade privado (PE), o administrador do SQL pode gerenciar a conexão de ponto de extremidade privado (PEC) para o banco de dados SQL.

1. Navegue até o recurso do SQL Server na portal do Azure.

    - (1) selecione as conexões de ponto de extremidade privado no painel esquerdo
    - (2) mostra uma lista de todas as conexões de ponto de extremidade privado (PECs)
    - (3) o ponto de extremidade particular correspondente (PE) criado ![Screenshot de todos os PECs @ no__t-1

1. Selecione um PEC individual na lista selecionando-o.
![Screenshot o PEC selecionado @ no__t-1

1. O administrador do SQL pode optar por aprovar ou rejeitar um PEC e, opcionalmente, adicionar uma resposta de texto curto.
![Screenshot da aprovação de PEC @ no__t-1

1. Após a aprovação ou rejeição, a lista refletirá o estado apropriado junto com o texto de resposta.
![Screenshot de todas as PECs após a aprovação @ no__t-1

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Casos de uso de link privado para o banco de dados SQL do Azure 

Os clientes podem se conectar ao ponto de extremidade privado da mesma VNet, rede virtual emparelhada na mesma região ou via conexão VNet a VNet entre regiões. Além disso, os clientes podem se conectar do local usando o ExpressRoute, o emparelhamento privado ou o túnel de VPN. Veja abaixo um diagrama simplificado que mostra os casos de uso comuns.

 ![Diagrama de opções de conectividade][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network-vnet"></a>Testar a conectividade com o banco de dados SQL de uma VM do Azure na mesma rede virtual (VNet)

Para este cenário, suponha que você criou uma VM (máquina virtual) do Azure executando o Windows Server 2016. 

1. [Inicie uma sessão de área de trabalho remota (RDP) e conecte-se à máquina virtual](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. Em seguida, você pode fazer algumas verificações de conectividade básicas para garantir que a VM esteja se conectando ao banco de dados SQL por meio do ponto de extremidade privado usando as seguintes ferramentas:
    1. Estabelecer
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Verificar a conectividade usando telnet

O [cliente Telnet](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) é um recurso do Windows que pode ser usado para testar a conectividade. Dependendo da versão do sistema operacional Windows, talvez seja necessário habilitar esse recurso explicitamente. 

Abra uma janela de prompt de comando depois de ter instalado o Telnet. Execute o comando Telnet e especifique o endereço IP e o ponto de extremidade privado do banco de dados SQL.

```
>telnet 10.1.1.5 1433
```

Quando o Telnet se conectar com êxito, você verá uma tela em branco na janela de comando, como a imagem abaixo:

 ![Diagrama de Telnet][2]

### <a name="check-connectivity-using-psping"></a>Verificar a conectividade usando Psping

[Psping](/sysinternals/downloads/psping) pode ser usado da seguinte maneira para verificar se a conexão de ponto de extremidade privada (PEC) está escutando conexões na porta 1433.

Execute psping da seguinte maneira fornecendo o FQDN para o servidor do banco de dados SQL e a porta 1433:

```
>psping.exe mysqldbsrvr.database.windows.net:1433

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.6.1.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.6.1.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49958: 2.28ms
```

A saída mostra que Psping poderia executar ping no endereço IP privado associado ao PEC.

### <a name="check-connectivity-using-nmap"></a>Verificar a conectividade usando nmap

O Nmap (mapeador de rede) é uma ferramenta livre e de código aberto usada para a descoberta de rede e a auditoria de segurança. Para obter mais informações e o link de download, visite https://nmap.org. Você pode usar essa ferramenta para garantir que o ponto de extremidade privado esteja escutando conexões na porta 1433.

Execute nmap da seguinte maneira fornecendo o intervalo de endereços da sub-rede que hospeda o ponto de extremidade privado.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

O resultado mostra que um endereço IP está ativo; que corresponde ao endereço IP para o ponto de extremidade privado.


### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>Verificar a conectividade usando o SQL Server Management Studio (SSMS)

A última etapa é usar o [SSMS para se conectar ao banco de dados SQL](sql-database-connect-query-ssms.md). Depois de se conectar ao banco de dados SQL usando o SSMS, verifique se você está se conectando a partir do endereço IP privado da VM do Azure executando a seguinte consulta:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````
> [!NOTE]
> Na visualização, as conexões com o ponto de extremidade privado dão suporte somente ao **proxy** como a [política de conexão](sql-database-connectivity-architecture.md#connection-policy)


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Conectando de uma VM do Azure na VNet (rede virtual emparelhada) 

Configure o [emparelhamento VNet](../virtual-network/tutorial-connect-virtual-networks-powershell.md) para estabelecer a conectividade com o banco de dados SQL de uma VM do Azure em uma VNet emparelhada.

## <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Conectando de uma VM do Azure no ambiente de VNet a VNet

Configure a [conexão de gateway VPN de vnet para vnet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) para estabelecer a conectividade com um banco de dados SQL de uma VM do Azure em uma região ou assinatura diferente.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Conectando de um ambiente local pela VPN

Para estabelecer a conectividade de um ambiente local para o banco de dados SQL, escolha e implemente uma das opções:
- [Conexão ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Conexão VPN site a site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [Circuito do ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-an-azure-sql-data-warehouse-to-azure-storage-using-polybase"></a>Conectando de um SQL Data Warehouse do Azure ao armazenamento do Azure usando o polybase

O polybase é comumente usado para carregar dados no Azure SQL Data Warehouse de contas de armazenamento do Azure. Se a conta de armazenamento do Azure para a qual você está carregando dados limita o acesso somente a um conjunto de sub-redes de VNet por meio de pontos de extremidade privados, pontos de extremidade de serviço ou firewalls baseados em IP, a conectividade do polybase com a conta será interrompida. Para habilitar os cenários de importação e exportação do polybase com o Azure SQL Data Warehouse se conectando ao armazenamento do Azure que é protegido para uma VNet, siga as etapas fornecidas [aqui](sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). 



## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral da segurança do banco de dados SQL do Azure, consulte [protegendo seu banco de dados](sql-database-security-overview.md)
- Para obter uma visão geral da conectividade do banco de dados SQL do Azure, consulte [arquitetura de conectividade do SQL do Azure](sql-database-connectivity-architecture.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/pe-connect-overview.png
[2]: ./media/sql-database-get-started-portal/telnet-result.png
[3]: ./media/sql-database-get-started-portal/pec-list-before.png
[4]: ./media/sql-database-get-started-portal/pec-approve.png
[5]: ./media/sql-database-get-started-portal/pec-list-after.png
[6]: ./media/sql-database-get-started-portal/pec-select.png
