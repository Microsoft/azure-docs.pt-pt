---
title: Arquitetura de conectividade para uma instância gerida na SQL Database do Azure | Documentos da Microsoft
description: Saiba mais sobre a comunicação de instância de base de dados do SQL do Azure gerida e também a arquitetura de conectividade como a forma como os componentes de direcionar o tráfego para a instância gerida.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 02/26/2019
ms.openlocfilehash: 6ef020ff1054416e2b9af5af824b9aa27f0b1e64
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247244"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Arquitetura de conectividade para uma instância gerida na base de dados do Azure SQL 

Este artigo explica a comunicação numa instância gerida da base de dados do Azure SQL. Também descreve a arquitetura de conectividade e a forma como os componentes de direcionar o tráfego para a instância gerida.  

A instância gerida da base de dados SQL é colocada dentro da rede virtual do Azure e a sub-rede que dedica-se a instâncias geridas. Esta implementação fornece:

- Um endereço IP privado seguro.
- A capacidade de ligar uma rede no local a uma instância gerida.
- A capacidade de ligar uma instância gerida a um servidor vinculado ou outro arquivo de dados no local.
- A capacidade de ligar uma instância gerida nos recursos do Azure.

## <a name="communication-overview"></a>Descrição geral da comunicação

O diagrama seguinte mostra as entidades que se ligam a uma instância gerida. Ela também mostra os recursos que precisam se comunicar com a instância gerida. O processo de comunicação na parte inferior do diagrama representa aplicações de cliente e ferramentas que se ligam para a instância gerida como fontes de dados.  

![Entidades na arquitetura de conectividade](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Uma instância gerida é uma plataforma como oferta de serviço (PaaS). A Microsoft utiliza automatizadas de agentes (gestão, implementação e manutenção) para gerir este serviço com base nos fluxos de dados de telemetria. Como a Microsoft é responsável pela gestão, os clientes não podem acessar as máquinas de virtuais de cluster de instância gerida através do protocolo RDP (Remote Desktop).

Algumas operações iniciadas por aplicações ou os utilizadores finais podem exigir do SQL Server gerido instâncias para interagir com a plataforma. Um caso é a criação de uma base de dados de instância gerida. Este recurso é exposto através do portal do Azure, PowerShell, CLI do Azure e a API REST.

Instâncias geridas dependem de serviços do Azure como armazenamento do Azure para cópias de segurança, o Azure Service Bus para telemetria, o Azure Active Directory para autenticação e o Azure Key Vault para encriptação de dados transparente (TDE). As instâncias geridas efetuar ligações para estes serviços.

Todas as comunicações utilizam certificados de criptografia e assinatura. Para verificar a fidedignidade de comunicar as partes, geridas instâncias constantemente Verifique se estes certificados, entrar em contato com uma autoridade de certificação. Se os certificados são revogados ou não não possível verificar, a instância gerida fecha as ligações para proteger os dados.

## <a name="high-level-connectivity-architecture"></a>Arquitetura de alto nível de conectividade

Num alto nível, uma instância gerida é um conjunto de componentes do serviço. Estes componentes estão alojados num conjunto dedicado de máquinas virtuais isoladas que são executados dentro da sub-rede da rede virtual do cliente. Estas máquinas formam um cluster virtual.

Um cluster virtual pode alojar várias instâncias geridas. Se for necessário, o cluster é automaticamente se expande ou contratos quando o cliente é alterado o número de instâncias aprovisionados na sub-rede.

Aplicações de cliente podem ligar a instâncias geridas e podem consultar e atualizar bancos de dados apenas se eles são executados dentro da rede virtual, em modo de peering virtual rede ou a rede ligados por VPN ou Azure ExpressRoute. Esta rede tem de utilizar um ponto de extremidade e endereços IP privados.  

![Diagrama de arquitetura de conectividade](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Serviços de gerenciamento e a implantação de Microsoft são executados fora da rede virtual. Uma instância gerida e serviços da Microsoft estabelecem ligação através de pontos de extremidade que tenham endereços IP públicos. Quando uma instância gerida cria uma ligação de saída, na extremidade de recebimento faz de tradução de endereços de rede (NAT), o aspeto de ligação é proveniente deste endereço IP público.

Fluxos de tráfego de gestão através da rede virtual do cliente. Isso significa que os elementos da infra-estrutura da rede virtual podem afetar o tráfego de gestão, fazendo a instância que falham e fique indisponível.

> [!IMPORTANT]
> Para melhorar a experiência do cliente e a disponibilidade do serviço, a Microsoft aplica uma política de intenção de rede em elementos de infraestrutura de rede virtual do Azure. A política pode afetar como funciona a instância gerida. Esse mecanismo de plataforma comunica forma transparente os requisitos de rede aos utilizadores. Principal objetivo a política é impedir que a configuração incorreta de rede e para assegurar as operações normais de instância gerida. Quando elimina uma instância gerida, a política de intenção de rede também é removida.

## <a name="virtual-cluster-connectivity-architecture"></a>Arquitetura de conectividade do virtual cluster

Vamos aprofundar na arquitetura de conectividade para instâncias geridas. O diagrama seguinte mostra o esquema conceitual do virtual cluster.

![Arquitetura de conectividade do virtual cluster](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Os clientes ligam a uma instância gerida por um nome de anfitrião com o formato `<mi_name>.<dns_zone>.database.windows.net`. Este nome de anfitrião é resolvido para um endereço IP privado, embora ele está registado numa zona de sistema de nomes (DNS) de domínio público e é resolvível publicamente. O `zone-id` é gerado automaticamente quando criar o cluster. Se um cluster recém-criado aloja uma instância secundária, compartilha sua identificação de zona com o cluster principal. Para obter mais informações, consulte [utilizar grupos de autofailover para ativar a ativação pós-falha transparente e coordenada de várias bases de dados](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets).

Este endereço IP privado pertence ao balanceador de carga interno a instância gerida. O Balanceador de carga direciona o tráfego para o gateway a instância gerida. Uma vez que várias instâncias geridas podem ser executados dentro do mesmo cluster, o gateway utiliza o nome de anfitrião a instância gerida para redirecionar o tráfego para o serviço de motor SQL correto.

Serviços de gerenciamento e implantação de ligar a uma instância gerida com um [ponto final de gestão](#management-endpoint) que é mapeado para um externo Balanceador de carga. Tráfego é encaminhado para os nós apenas se recebido num conjunto predefinido de portas que utilizam componentes de gerenciamento apenas a instância gerida. Um firewall interno em nós é configurado para permitir tráfego apenas a partir de intervalos de IP da Microsoft. Certificados autenticar mutuamente todas as comunicações entre os componentes de gestão e o plano de gestão.

## <a name="management-endpoint"></a>Ponto final de gestão

Microsoft gerencia a instância gerida utilizando um ponto de final de gestão. Este ponto final é dentro do cluster virtual da instância. O ponto final de gestão está protegido por um firewall interno no nível da rede. No nível da aplicação, é protegida pela verificação de certificados mútuos. Para obter o endereço IP do ponto de extremidade, veja [determinar o endereço IP do ponto final de gestão](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Quando as ligações começam dentro da instância gerida (tal como acontece com as cópias de segurança e registos de auditoria), o tráfego é apresentado iniciar a partir endereço IP do ponto final de gestão. Pode limitar o acesso aos serviços do público de uma instância gerida através da definição de regras de firewall para permitir apenas o endereço IP da instância gerida. Para obter mais informações, consulte [Verifique se o firewall interno da instância gerida](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Ao contrário da firewall para ligações que começam dentro da instância gerida, os serviços do Azure que estão dentro de região a instância gerida tem uma firewall que está otimizada para o tráfego que passa entre estes serviços.

## <a name="network-requirements"></a>Requisitos da rede

Implemente uma instância gerida numa sub-rede dedicada dentro da rede virtual. A sub-rede tem de ter as seguintes características:

- **Sub-rede dedicado:** Sub-rede da instância gerida não pode conter qualquer outro serviço de cloud que está associado e não pode ser uma sub-rede de gateway. A sub-rede não pode conter qualquer recurso, mas a instância gerida e, mais tarde não é possível adicionar recursos na sub-rede.
- **Grupo de segurança de rede (NSG):** Um NSG associado a rede virtual tem de definir [regras de segurança de entrada](#mandatory-inbound-security-rules) e [regras de segurança de saída](#mandatory-outbound-security-rules) antes de quaisquer outras regras. Pode utilizar um NSG para controlar o acesso ao ponto final de dados a instância gerida ao filtrar o tráfego na porta 1433.
- **Tabela de rota definida pelo (UDR) de utilizador:** Uma tabela UDR que está associada a rede virtual tem de incluir específico [entradas](#user-defined-routes).
- **Não existem pontos finais de serviço:** Nenhum ponto de final de serviço deve ser associado a sub-rede a instância gerida. Certifique-se de que a opção de pontos finais de serviço é desabilitada quando criar a rede virtual.
- **Endereços IP suficientes:** A sub-rede de instância gerida tem de ter, pelo menos, 16 endereços IP. O mínimo recomendado é 32 endereços IP. Para obter mais informações, consulte [determinar o tamanho da sub-rede para instâncias geridas](sql-database-managed-instance-determine-size-vnet-subnet.md). Pode implementar instâncias geridas na [da rede existente](sql-database-managed-instance-configure-vnet-subnet.md) depois de configurá-lo para satisfazer [os requisitos de rede para instâncias geridas](#network-requirements). Caso contrário, crie uma [nova rede e sub-rede](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Não é possível implementar uma nova instância gerida, se a sub-rede de destino não tiver estas características. Quando cria uma instância gerida, é aplicada uma política de intenção de rede na sub-rede para impedir alterações não conformes a configuração do funcionamento em rede. Após a última instância é removida a partir da sub-rede, a política de intenção de rede também é removida.

### <a name="mandatory-inbound-security-rules"></a>Regras de segurança de entrada obrigatório

| Name       |Porta                        |Protocolo|Origem           |Destino|Ação|
|------------|----------------------------|--------|-----------------|-----------|------|
|móveis  |9000, 9003, 1438, 1440, 1452|TCP     |Qualquer              |Qualquer        |Permitir |
|mi_subnet   |Qualquer                         |Qualquer     |SUB-REDE DE MI        |Qualquer        |Permitir |
|health_probe|Qualquer                         |Qualquer     |AzureLoadBalancer|Qualquer        |Permitir |

### <a name="mandatory-outbound-security-rules"></a>Regras de segurança de saída obrigatórios

| Name       |Porta          |Protocolo|Origem           |Destino|Ação|
|------------|--------------|--------|-----------------|-----------|------|
|móveis  |80, 443, 12000|TCP     |Qualquer              |Internet   |Permitir |
|mi_subnet   |Qualquer           |Qualquer     |Qualquer              |SUB-REDE DE MI *  |Permitir |

\* Sub-rede de MI refere-se para o intervalo de endereços IP para a sub-rede na 10.x.x.x/y formulário. Pode encontrar estas informações no portal do Azure, nas propriedades de sub-rede.

> [!IMPORTANT]
> Embora as regras de segurança de entrada necessário permitem tráfego a partir _qualquer_ nas portas de origem 9000, 9003, 1438, 1440 e 1452, estas portas são protegidas por uma firewall interna. Para obter mais informações, consulte [determinar o endereço de ponto final de gestão](sql-database-managed-instance-find-management-endpoint-ip-address.md).

> [!NOTE]
> Se utilizar a replicação transacional numa instância gerida e, se utilizar qualquer base de dados de instância como um publicador ou distribuidor, abra a porta 445 (saída de TCP) nas regras de segurança da sub-rede. Esta porta permitirá o acesso à partilha de ficheiros do Azure.

### <a name="user-defined-routes"></a>Rotas definidas pelo utilizador

|Name|Prefixo de endereço|Salto seguinte|
|----|--------------|-------|
|subnet_to_vnetlocal|[mi_subnet]|Rede virtual|
|mi-0-5-next-hop-internet|0.0.0.0/5|Internet|
|mi-11-8-nexthop-internet|11.0.0.0/8|Internet|
|mi-12-6-nexthop-internet|12.0.0.0/6|Internet|
|mi-128-3-nexthop-internet|128.0.0.0/3|Internet|
|mi-16-4-nexthop-internet|16.0.0.0/4|Internet|
|mi-160-5-nexthop-internet|160.0.0.0/5|Internet|
|mi-168-6-nexthop-internet|168.0.0.0/6|Internet|
|mi-172-12-nexthop-internet|172.0.0.0/12|Internet|
|mi-172-128-9-nexthop-internet|172.128.0.0/9|Internet|
|mi-172-32-11-nexthop-internet|172.32.0.0/11|Internet|
|mi-172-64-10-nexthop-internet|172.64.0.0/10|Internet|
|mi-173-8-nexthop-internet|173.0.0.0/8|Internet|
|mi-174-7-nexthop-internet|174.0.0.0/7|Internet|
|mi-176-4-nexthop-internet|176.0.0.0/4|Internet|
|mi-192-128-11-nexthop-internet|192.128.0.0/11|Internet|
|mi-192-160-13-nexthop-internet|192.160.0.0/13|Internet|
|mi-192-169-16-nexthop-internet|192.169.0.0/16|Internet|
|mi-192-170-15-nexthop-internet|192.170.0.0/15|Internet|
|mi-192-172-14-nexthop-internet|192.172.0.0/14|Internet|
|mi-192-176-12-nexthop-internet|192.176.0.0/12|Internet|
|mi-192-192-10-nexthop-internet|192.192.0.0/10|Internet|
|mi-192-9-nexthop-internet|192.0.0.0/9|Internet|
|mi-193-8-nexthop-internet|193.0.0.0/8|Internet|
|mi-194-7-nexthop-internet|194.0.0.0/7|Internet|
|mi-196-6-nexthop-internet|196.0.0.0/6|Internet|
|mi-200-5-nexthop-internet|200.0.0.0/5|Internet|
|mi-208-4-nexthop-internet|208.0.0.0/4|Internet|
|mi-224-3-nexthop-internet|224.0.0.0/3|Internet|
|mi-32-3-nexthop-internet|32.0.0.0/3|Internet|
|mi-64-2-nexthop-internet|64.0.0.0/2|Internet|
|mi-8-7-nexthop-internet|8.0.0.0/7|Internet|
||||

Além disso, pode adicionar entradas à tabela de rotas para encaminhar o tráfego que tem intervalos de IP privados no local como um destino através do gateway de rede virtual ou a aplicação de rede virtual (NVA).

Se a rede virtual inclui um DNS personalizado, adicione uma entrada para o endereço IP de resolução recursiva do Azure (por exemplo, 168.63.129.16). Para obter mais informações, consulte [configurar um DNS personalizado](sql-database-managed-instance-custom-dns.md). O servidor DNS personalizado tem de ser capaz de resolver os nomes de anfitrião nesses domínios e os respetivos subdomínios: *microsoft.com*, *windows.net*, *windows.com*,  *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com*, e *microsoftonline-p.com*.

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral, consulte [base de dados SQL avançadas de segurança dos dados](sql-database-managed-instance.md).
- Saiba como [configurar uma nova rede virtual do Azure](sql-database-managed-instance-create-vnet-subnet.md) ou uma [rede virtual do Azure existente](sql-database-managed-instance-configure-vnet-subnet.md) onde pode implementar instâncias geridas.
- [Calcular o tamanho da sub-rede](sql-database-managed-instance-determine-size-vnet-subnet.md) onde pretende implementar as instâncias geridas.
- Saiba como criar uma instância gerida:
  - Partir do [portal do Azure](sql-database-managed-instance-get-started.md).
  - Usando [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/).
  - Usando [um modelo Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Usando [um modelo do Azure Resource Manager (usando a JumpBox, com o SSMS incluídos)](https://portal.azure.com/).
