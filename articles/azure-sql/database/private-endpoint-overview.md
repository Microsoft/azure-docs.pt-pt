---
title: Azure Private Link
description: Visão geral da função de ponto final privado.
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.service: sql-database
ms.topic: overview
ms.custom: sqldbrb=1
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 5109139c7168026c74a475128832fbb0733ce832
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447128"
---
# <a name="azure-private-link-for-azure-sql-database-and-azure-synapse-analytics"></a>Azure Private Link para Azure SQL Database e Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

O Private Link permite-lhe ligar-se a vários serviços PaaS em Azure através de um **ponto final privado.** Para obter uma lista de serviços PaaS que suportam a funcionalidade Private Link, aceda à página [de Documentação de Link Privado.](../../private-link/index.yml) Um ponto final privado é um endereço IP privado dentro de um [VNet](../../virtual-network/virtual-networks-overview.md) específico e sub-rede.

> [!IMPORTANT]
> Este artigo aplica-se tanto à Base de Dados Azure SQL como à Azure Synapse Analytics. Para simplificar, o termo "base de dados" refere-se a ambas as bases de dados na Base de Dados Azure SQL e na Azure Synapse Analytics. Da mesma forma, quaisquer referências ao 'servidor' referem-se ao [servidor lógico SQL](logical-servers.md) que acolhe a Base de Dados Azure SQL e a Azure Synapse Analytics. Este artigo *não* se aplica a **Azure SQL Managed Instance**.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Como configurar o Link Privado para a Base de Dados Azure SQL 

### <a name="creation-process"></a>Processo de Criação
Os pontos finais privados podem ser criados utilizando o portal Azure, PowerShell ou o Azure CLI:
- [O portal](../../private-link/create-private-endpoint-portal.md)
- [PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [CLI](../../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Processo de aprovação
Uma vez que o administrador de rede cria o Ponto Final Privado (PE), o administrador SQL pode gerir a Ligação De Pontos De Terminação Privada (PEC) para a Base de Dados SQL.

1. Navegue para o recurso do servidor no portal Azure de acordo com os passos mostrados na imagem abaixo

    - (1) Selecione as ligações de ponto final privado no painel esquerdo
    - (2) Apresenta uma lista de todas as ligações privadas de ponto final (PECs)
    - (3) Ponto final privado correspondente (PE) criado ![ Screenshot de todos os PECs][3]

1. Selecione um PEC individual da lista selecionando-o.
![Screenshot selecionado PEC][6]

1. O administrador sql pode optar por aprovar ou rejeitar um PEC e opcionalmente adicionar uma resposta de texto curta.
![Screenshot da aprovação do PEC][4]

1. Após aprovação ou rejeição, a lista refletirá o estado apropriado juntamente com o texto de resposta.
![Screenshot de todos os PECs após aprovação][5]

## <a name="on-premises-connectivity-over-private-peering"></a>Conectividade no local em peering privado

Quando os clientes se ligam ao ponto final público a partir de máquinas no local, o seu endereço IP precisa de ser adicionado à firewall baseada em IP utilizando uma [regra de firewall ao nível do Servidor](firewall-create-server-level-portal-quickstart.md). Embora este modelo funcione bem para permitir o acesso a máquinas individuais para dev ou testar cargas de trabalho, é difícil de gerir em ambiente de produção.

Com o Private Link, os clientes podem permitir o acesso transversal ao ponto final privado utilizando o [ExpressRoute,](../../expressroute/expressroute-introduction.md)o perspero privado ou o túnel VPN. Os clientes podem então desativar todos os acessos através do ponto final público e não utilizar a firewall baseada em IP para permitir quaisquer endereços IP.

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Utilizar casos de Link Privado para Base de Dados Azure SQL 

Os clientes podem ligar-se ao ponto final privado a partir da mesma rede virtual, rede virtual esprevada na mesma região, ou através de rede virtual para a ligação de rede virtual entre regiões. Além disso, os clientes podem conectar-se a partir de instalações usando expressRoute, peering privado ou túneis VPN. Abaixo está um diagrama simplificado mostrando os casos de uso comum.

 ![Diagrama de opções de conectividade][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network"></a>Testar conectividade à Base de Dados SQL a partir de um VM Azure na mesma rede virtual

Para este cenário, assuma que criou uma Máquina Virtual Azure (VM) com o Windows Server 2016. 

1. [Inicie uma sessão de desktop remoto (RDP) e ligue-se à máquina virtual](../../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. Em seguida, pode fazer algumas verificações básicas de conectividade para garantir que o VM está a ligar-se à Base de Dados SQL através do ponto final privado utilizando as seguintes ferramentas:
    1. Telnet
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Verifique a conectividade usando a Telnet

[O Cliente Telnet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) é uma funcionalidade do Windows que pode ser usada para testar a conectividade. Dependendo da versão do Sistema operativo Windows, poderá ser necessário ativar esta funcionalidade de forma explícita. 

Abra uma janela de pedido de comando depois de ter instalado a Telnet. Executar o comando Telnet e especificar o endereço IP e o ponto final privado da base de dados na Base de Dados SQL.

```
>telnet 10.1.1.5 1433
```

Quando a Telnet se ligar com sucesso, verá um ecrã em branco na janela de comando como a imagem abaixo:

 ![Diagrama de teleto][2]

### <a name="check-connectivity-using-psping"></a>Verifique a conectividade usando a Psping

[A psping](/sysinternals/downloads/psping) pode ser usada da seguinte forma para verificar se a ligação de ponto final privado (PEC) está a ouvir as ligações na porta 1433.

Executar a psping da seguinte forma, fornecendo o FQDN para o servidor lógico SQL e a porta 1433:

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

A saída mostra que a PSPing poderia localizar o endereço IP privado associado ao PEC.

### <a name="check-connectivity-using-nmap"></a>Verifique a conectividade usando o Nmap

O Nmap (Network Mapper) é uma ferramenta de código aberto e gratuito utilizada para a descoberta de redes e auditoria de segurança. Para mais informações e o link de descarregamento, https://nmap.org visite. Pode utilizar esta ferramenta para garantir que o ponto final privado está a ouvir ligações na porta 1433.

Executar Nmap da seguinte forma, fornecendo o intervalo de endereço da sub-rede que hospeda o ponto final privado.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

O resultado mostra que um endereço IP está em cima; que corresponde ao endereço IP para o ponto final privado.

### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>Verifique a conectividade utilizando o SQL Server Management Studio (SSMS)
> [!NOTE]
> Utilize o **Nome de Domínio Totalmente Qualificado (FQDN)** do servidor em cadeias de ligação para os seus clientes `<server>.database.windows.net` (). Quaisquer tentativas de login efetuadas diretamente para o endereço IP ou utilizando o link privado FQDN `<server>.privatelink.database.windows.net` () falharão. Este comportamento é por design, uma vez que o ponto final privado encaminha o tráfego para o SQL Gateway na região e o FQDN correto precisa de ser especificado para que os logins tenham sucesso.

Siga os passos aqui para utilizar [o SSMS para ligar à Base de Dados SQL.](connect-query-ssms.md) Depois de ligar à Base de Dados SQL utilizando SSMS, verifique se está a ligar a partir do endereço IP privado do Azure VM executando a seguinte consulta:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="data-exfiltration-prevention"></a>Data exfiltration prevention (Prevenção da transferência de dados não autorizada)

A exfiltração de dados na Base de Dados Azure SQL é quando um utilizador autorizado, como um administrador de base de dados é capaz de extrair dados de um sistema e movê-lo para fora da organização. Por exemplo, o utilizador desloca os dados para uma conta de armazenamento detida por terceiros.

Considere um cenário com um utilizador que executa o SQL Server Management Studio (SSMS) dentro de uma máquina virtual Azure conectando-se a uma base de dados na Base de Dados SQL. Esta base de dados está no centro de dados dos EUA. O exemplo abaixo mostra como limitar o acesso com pontos finais públicos na Base de Dados SQL utilizando controlos de acesso à rede.

1. Desative todo o tráfego de serviço Azure para a Base de Dados SQL através do ponto final público, definindo Allow Azure Services to **OFF**. Certifique-se de que não são permitidos endereços IP nas regras de firewall de nível de servidor e base de dados. Para obter mais informações, consulte a [Base de Dados Azure SQL e os controlos de acesso à rede Azure Synapse Analytics](network-access-controls-overview.md).
1. Apenas permita o tráfego na base de dados na Base de Dados SQL utilizando o endereço IP privado do VM. Para obter mais informações, consulte os artigos sobre [as regras](firewall-configure.md)de Service [Endpoint](vnet-service-endpoint-rule-overview.md) e de firewall de rede virtual .
1. No Azure VM, reduza o âmbito de ligação de saída utilizando grupos de segurança de [rede (NSGs)](../../virtual-network/manage-network-security-group.md) e tags de serviço da seguinte forma
    - Especifique uma regra NSG para permitir o tráfego para tag de serviço = SQL. WestUs - apenas permitindo a ligação à SQL Database nos EUA
    - Especifique uma regra NSG (com uma **prioridade maior)** para negar o tráfego para tag de serviço = SQL - negando ligações à Base de Dados SQL em todas as regiões

No final desta configuração, o Azure VM só pode ligar-se a uma base de dados na Base de Dados SQL na região oeste dos EUA. No entanto, a conectividade não se restringe a uma única base de dados na Base de Dados SQL. O VM ainda pode ligar-se a qualquer base de dados na região oeste dos EUA, incluindo as bases de dados que não fazem parte da subscrição. Embora tenhamos reduzido o âmbito da exfiltração de dados no cenário acima para uma região específica, não o eliminámos completamente.

Com o Private Link, os clientes podem agora configurar controlos de acesso à rede como os NSGs para restringir o acesso ao ponto final privado. Os recursos individuais do Azure PaaS são então mapeados para pontos finais privados específicos. Um insider malicioso só pode aceder ao recurso PaaS mapeado (por exemplo, uma base de dados na Base de Dados SQL) e nenhum outro recurso. 

## <a name="limitations"></a>Limitações 
As ligações ao ponto final privado apenas **suportam o Proxy** como a política de [ligação](connectivity-architecture.md#connection-policy)


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network"></a>Ligação a partir de um VM Azure em Rede Virtual Peered 

Configure [a rede virtual que observa](../../virtual-network/tutorial-connect-virtual-networks-powershell.md) para estabelecer conectividade com a Base de Dados SQL a partir de um Azure VM numa rede virtual espreitada.

## <a name="connecting-from-an-azure-vm-in-virtual-network-to-virtual-network-environment"></a>Ligação de um Azure VM em rede virtual ao ambiente de rede virtual

Configure [a rede virtual para a ligação de gateway de rede virtual VPN](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) para estabelecer conectividade a uma base de dados na Base de Dados SQL a partir de um VM Azure numa região ou subscrição diferente.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Ligação a partir de um ambiente no local sobre VPN

Para estabelecer conectividade de um ambiente no local até à base de dados na Base de Dados SQL, escolha e implemente uma das opções:
- [Ligação ponto-a-local](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Ligação VPN Site a Site](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [Circuito ExpressRoute](../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-azure-synapse-analytics-to-azure-storage-using-polybase-and-the-copy-statement"></a>Ligação do Azure Synapse Analytics ao Azure Storage utilizando a Base Polibase e a declaração COPY

PolyBase e a declaração COPY são geralmente usados para carregar dados em Azure Synapse Analytics a partir de contas de Armazenamento Azure. Se a conta de Armazenamento Azure que está a carregar dados de limites de acesso apenas a um conjunto de sub-redes de rede virtuais através de Pontos Finais Privados, Endpoints de Serviço ou firewalls baseados em IP, a conectividade da PolyBase e a declaração COPY para a conta quebrarão. Para permitir tanto cenários de importação como de exportação com a Azure Synapse Analytics conectando-se ao Azure Storage que está seguro a uma rede virtual, siga os passos fornecidos [aqui.](vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) 

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral da segurança da Base de Dados Azure SQL, consulte [a sua base de dados](security-overview.md)
- Para uma visão geral da conectividade da Base de Dados [Azure SQL, consulte Azure SQL Connectivity Architecture](connectivity-architecture.md)

<!--Image references-->
[1]: media/quickstart-create-single-database/pe-connect-overview.png
[2]: media/quickstart-create-single-database/telnet-result.png
[3]: media/quickstart-create-single-database/pec-list-before.png
[4]: media/quickstart-create-single-database/pec-approve.png
[5]: media/quickstart-create-single-database/pec-list-after.png
[6]: media/quickstart-create-single-database/pec-select.png