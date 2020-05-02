---
title: Ligação Privada
description: Visão geral da funcionalidade private endpoint
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.service: sql-database
ms.topic: overview
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: dd717d653e57fbb8c540e4ef023011c64778a3b0
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629002"
---
# <a name="private-link-for-azure-sql-database-and-azure-synapse-analytics"></a>Link privado para Base de Dados Azure SQL e Azure Synapse Analytics

O Private Link permite-lhe ligar-se a vários serviços PaaS em Azure através de um **ponto final privado.** Para obter uma lista de serviços PaaS que suportam a funcionalidade Private Link, vá à página de Documentação de [Link Privado.](../private-link/index.yml) Um ponto final privado é um endereço IP privado dentro de uma [VNet](../virtual-network/virtual-networks-overview.md) e Subnet específicas. 

> [!IMPORTANT]
> Este artigo aplica-se ao servidor Azure SQL e tanto às bases de dados SQL como à Azure Synapse Analytics que são criadas no servidor Azure SQL. Para a simplicidade, a Base de Dados SQL é usada quando se refere tanto à Base de Dados SQL como ao Azure Synapse Analytics. Este artigo *não* se aplica a uma implantação de **instânciagerida** na Base de Dados Azure SQL.

## <a name="data-exfiltration-prevention"></a>Prevenção da exfiltração de dados

A exfiltração de dados na Base de Dados Azure SQL é quando um utilizador autorizado, como uma administração de base de dados, é capaz de extrair dados de um sistema e movê-lo para outro local ou sistema fora da organização. Por exemplo, o utilizador transfere os dados para uma conta de armazenamento propriedade de terceiros.

Considere um cenário com um utilizador que executa o SQL Server Management Studio (SSMS) dentro de um Azure VM que liga a uma Base de Dados SQL. Esta Base de Dados SQL está no centro de dados dos EUA Ocidentais. O exemplo abaixo mostra como limitar o acesso com pontos finais públicos na Base de Dados SQL utilizando controlos de acesso à rede.

1. Desative todo o tráfego de serviço azure para a Base de Dados SQL através do ponto final, definindo os Serviços De acesso ao Azure **.** Certifique-se de que não são permitidos endereços IP nas regras de firewall do servidor e da base de dados. Para mais informações, consulte os controlos de acesso à [rede Azure SQL e Azure Synapse Analytics](sql-database-networkaccess-overview.md).
1. Apenas permita o tráfego na Base de Dados SQL utilizando o endereço IP privado do VM. Para mais informações, consulte os artigos sobre as regras de firewall [do Service Endpoint](sql-database-vnet-service-endpoint-rule-overview.md) e [VNet](sql-database-firewall-configure.md).
1. No VM Azure, reduza o âmbito de ligação de saída utilizando grupos de segurança de [rede (NSGs)](../virtual-network/manage-network-security-group.md) e etiquetas de serviço da seguinte forma
    - Especifique uma regra NSG para permitir o tráfego para etiqueta de serviço = SQL. WestUs - apenas permitindo a ligação à Base de Dados SQL nos EUA Ocidentais
    - Especificar uma regra NSG (com uma **prioridade maior)** para negar o tráfego para etiqueta de serviço = SQL - negando ligações à Base de Dados SQL em todas as regiões

No final desta configuração, o Azure VM só pode ligar-se às Bases de Dados SQL na região dos EUA Ocidentais. No entanto, a conectividade não se restringe a uma única base de dados SQL. O VM ainda pode ligar-se a quaisquer bases de dados SQL na região oeste dos EUA, incluindo as bases de dados que não fazem parte da subscrição. Embora tenhamos reduzido o âmbito da exfiltração de dados no cenário acima para uma região específica, não o eliminámos completamente.

Com o Private Link, os clientes podem agora configurar controlos de acesso à rede como NSGs para restringir o acesso ao ponto final privado. Os recursos individuais do Azure PaaS são então mapeados para pontos finais privados específicos. Um insider malicioso só pode aceder ao recurso PaaS mapeado (por exemplo, uma Base de Dados SQL) e nenhum outro recurso. 

## <a name="on-premises-connectivity-over-private-peering"></a>Conectividade no local sobre o peering privado

Quando os clientes se ligam ao ponto final público a partir de máquinas no local, o seu endereço IP precisa de ser adicionado à firewall baseada em IP utilizando uma [regra de firewall ao nível do Servidor](sql-database-server-level-firewall-rule.md). Embora este modelo funcione bem para permitir o acesso a máquinas individuais para dev ou trabalhos de trabalho de teste, é difícil de gerir em um ambiente de produção.

Com o Private Link, os clientes podem permitir o acesso transversal ao ponto final privado utilizando o [ExpressRoute,](../expressroute/expressroute-introduction.md)o peering privado ou o túnel VPN. Os clientes podem então desativar todos os acessos através do ponto final público e não utilizar a firewall baseada em IP para permitir quaisquer endereços IP.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Como configurar link privado para base de dados Azure SQL 

### <a name="creation-process"></a>Processo de Criação
Os pontos finais privados podem ser criados através do portal PowerShell ou Azure CLI:
- [Portal](../private-link/create-private-endpoint-portal.md)
- [PowerShell](../private-link/create-private-endpoint-powershell.md)
- [CLI](../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Processo de Aprovação
Uma vez que o administrador de rede cria o Ponto Final Privado (PE), o administrador SQL pode gerir a Ligação De Endpoint Privada (PEC) à Base de Dados SQL.

1. Navegue para o recurso do servidor SQL no portal Azure de acordo com os passos indicados na imagem abaixo

    - (1) Selecione as ligações de ponto final privado no painel esquerdo
    - (2) Apresenta uma lista de todas as ligações de ponto final privado (PECs)
    - (3) Correspondente ponto final privado ![(PE) criado Screenshot de todos os PECs][3]

1. Selecione um PEC individual da lista selecionando-o.
![Screenshot selecionado PEC][6]

1. O administrador da SQL pode optar por aprovar ou rejeitar um PEC e, opcionalmente, adicionar uma resposta de texto curta.
![Screenshot da aprovação pec][4]

1. Após aprovação ou rejeição, a lista refletirá o estado adequado, juntamente com o texto de resposta.
![Screenshot de todos os PECs após aprovação][5]

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Utilize casos de Link Privado para base de dados Azure SQL 

Os clientes podem ligar-se ao ponto final privado a partir do mesmo VNet, peered VNet na mesma região, ou através da ligação VNet-to-VNet através de regiões. Além disso, os clientes podem ligar-se a partir de instalações utilizando expressRoute, peering privado ou túnel VPN. Abaixo está um diagrama simplificado que mostra os casos de uso comuns.

 ![Diagrama de opções de conectividade][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network-vnet"></a>Testar a conectividade com a Base de Dados SQL a partir de um VM Azure na mesma Rede Virtual (VNet)

Para este cenário, assuma que criou uma Máquina Virtual Azure (VM) que executa o Windows Server 2016. 

1. [Inicie uma sessão de Ambiente de Trabalho Remoto (RDP) e ligue-se à máquina virtual](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. Em seguida, pode efetuar algumas verificações básicas de conectividade para garantir que o VM está a ligar-se à Base de Dados SQL através do ponto final privado utilizando as seguintes ferramentas:
    1. Telnet
    1. PSP
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Verifique a conectividade usando telnet

[O Cliente Telnet](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) é uma funcionalidade do Windows que pode ser usada para testar a conectividade. Dependendo da versão do Sistema Operativo Windows, poderá ser necessário ativar esta funcionalidade explicitamente. 

Abra uma janela De Comando Rápido depois de ter instalado telnet. Executar o comando Telnet e especificar o endereço IP e o ponto final privado da Base de Dados SQL.

```
>telnet 10.1.1.5 1433
```

Quando a Telnet se ligar com sucesso, verá um ecrã em branco na janela de comando como a imagem abaixo:

 ![Diagrama de telnet][2]

### <a name="check-connectivity-using-psping"></a>Verifique a conectividade usando a PSPing

[A PSPING](/sysinternals/downloads/psping) pode ser utilizada da seguinte forma para verificar se a ligação de ponto final Privado (PEC) está a ouvir ligações no porto 1433.

Executar a psping da seguinte forma, fornecendo o FQDN para o seu servidor de Base de Dados SQL e porta 1433:

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

A saída mostra que a PSPING poderia pingar o endereço IP privado associado ao PEC.

### <a name="check-connectivity-using-nmap"></a>Verifique a conectividade usando o Nmap

O Nmap (Mapper de Rede) é uma ferramenta gratuita e de código aberto utilizada para a descoberta da rede e auditoria de segurança. Para mais informações e https://nmap.orgo link de descarregamento, visite . Pode utilizar esta ferramenta para garantir que o ponto final privado está a ouvir ligações na porta 1433.

Executar o Nmap da seguinte forma, fornecendo a gama de endereços da subnet que acolhe o ponto final privado.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

O resultado mostra que um endereço IP está em cima; que corresponde ao endereço IP para o ponto final privado.

### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>Verifique a conectividade utilizando o Estúdio de Gestão de Servidores SQL (SSMS)
> [!NOTE]
> Utilize o Nome de **Domínio Totalmente Qualificado (FQDN)** do servidor nas cadeias de ligação para os seus clientes. Qualquer tentativa de login efetuada diretamente no endereço IP falhará. Este comportamento é por design, uma vez que o tráfego de pontos finais privados para o Portal SQL na região e o FQDN precisa de ser especificado para que os logins tenham sucesso.

Siga os passos aqui para utilizar [o SSMS para ligar à Base de Dados SQL](sql-database-connect-query-ssms.md). Depois de ligar à Base de Dados SQL utilizando SSMS, verifique se está a ligar-se a partir do endereço IP privado do Azure VM executando a seguinte consulta:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="limitations"></a>Limitações 
As ligações ao ponto final privado apenas apoiam a **Proxy** como política de [ligação](sql-database-connectivity-architecture.md#connection-policy)


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Ligação a partir de um VM Azure em Rede Virtual Peered (VNet) 

Configure o [vNet que pretende](../virtual-network/tutorial-connect-virtual-networks-powershell.md) estabelecer a conectividade com a Base de Dados SQL a partir de um VM Azure num VNet com um VNet com um vNet com um vNet.

## <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Ligação a partir de um VM Azure em ambiente VNet-to-VNet

Configure a ligação de [gateway VNet-to-VNet VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) para estabelecer conectividade com uma Base de Dados SQL a partir de um VM Azure numa região ou subscrição diferente.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Ligação a partir de um ambiente no local sobre VPN

Para estabelecer a conectividade de um ambiente no local até à Base de Dados SQL, escolha e implemente uma das opções:
- [Ligação ponto-a-local](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Ligação VPN Site a Site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [Circuito do ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-azure-synapse-analytics-to-azure-storage-using-polybase"></a>Ligação de Azure Synapse Analytics ao Armazenamento Azure usando a Polybase

A PolyBase é comumente usada para carregar dados em Azure Synapse Analytics a partir de contas de Armazenamento Azure. Se a conta de Armazenamento Azure que está a carregar dados dos limites aceder apenas a um conjunto de subredes VNet através de pontos finais privados, pontos finais de serviço ou firewalls baseados em IP, a conectividade da PolyBase para a conta quebrará. Para permitir tanto cenários de importação e exportação da PolyBase com a Azure Synapse Analytics a ligar-se ao Armazenamento Azure que está seguro a um VNet, siga os passos [aqui](sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)fornecidos . 

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral da segurança da Base de Dados Azure SQL, consulte [A segurança da sua base de dados](sql-database-security-overview.md)
- Para uma visão geral da conectividade Azure SQL Database, consulte [Azure SQL Connectivity Architecture](sql-database-connectivity-architecture.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/pe-connect-overview.png
[2]: ./media/sql-database-get-started-portal/telnet-result.png
[3]: ./media/sql-database-get-started-portal/pec-list-before.png
[4]: ./media/sql-database-get-started-portal/pec-approve.png
[5]: ./media/sql-database-get-started-portal/pec-list-after.png
[6]: ./media/sql-database-get-started-portal/pec-select.png
