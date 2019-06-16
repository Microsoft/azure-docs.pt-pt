---
title: Arquitetura de conectividade para uma instância gerida na SQL Database do Azure | Documentos da Microsoft
description: Saiba mais sobre a comunicação de instância de base de dados do SQL do Azure gerida e também a arquitetura de conectividade como a forma como os componentes de direcionar o tráfego para a instância gerida.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: dbb5ee122e715aeaa66d786f02966beedd2447c3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522321"
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

Instâncias geridas dependem de serviços do Azure como armazenamento do Azure para cópias de segurança, para a telemetria dos Hubs de eventos do Azure, Azure Active Directory para autenticação, Azure Key Vault para encriptação de dados transparente (TDE) e alguns dos serviços de plataforma do Azure que fornecem recursos de segurança e capacidade de suporte. As instâncias geridas faz ligações para estes serviços.

Todas as comunicações são criptografadas e assinada utilizando certificados. Para verificar a fidedignidade de comunicar as partes, geridas instâncias constantemente verificar estes certificados através de listas de revogação de certificado. Se os certificados são revogados, a instância gerida fecha as ligações para proteger os dados.

## <a name="high-level-connectivity-architecture"></a>Arquitetura de alto nível de conectividade

Num alto nível, uma instância gerida é um conjunto de componentes do serviço. Estes componentes estão alojados num conjunto dedicado de máquinas virtuais isoladas que são executados dentro da sub-rede da rede virtual do cliente. Estas máquinas formam um cluster virtual.

Um cluster virtual pode alojar várias instâncias geridas. Se for necessário, o cluster é automaticamente se expande ou contratos quando o cliente é alterado o número de instâncias aprovisionados na sub-rede.

Aplicações de cliente podem ligar a instâncias geridas e pode consultar e atualizar bancos de dados dentro da rede virtual, em modo de peering de rede virtual, ou de rede ligados por VPN ou Azure ExpressRoute. Esta rede tem de utilizar um ponto de extremidade e endereços IP privados.  

![Diagrama de arquitetura de conectividade](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Serviços de gerenciamento e a implantação de Microsoft são executados fora da rede virtual. Uma instância gerida e serviços da Microsoft estabelecem ligação através de pontos de extremidade que tenham endereços IP públicos. Quando uma instância gerida cria uma ligação de saída, na extremidade de recebimento faz de tradução de endereços de rede (NAT), o aspeto de ligação é proveniente deste endereço IP público.

Fluxos de tráfego de gestão através da rede virtual do cliente. Isso significa que os elementos da infra-estrutura da rede virtual podem afetar o tráfego de gestão, fazendo a instância que falham e fique indisponível.

> [!IMPORTANT]
> Para melhorar a experiência do cliente e a disponibilidade do serviço, a Microsoft aplica uma política de intenção de rede em elementos de infraestrutura de rede virtual do Azure. A política pode afetar como funciona a instância gerida. Esse mecanismo de plataforma comunica forma transparente os requisitos de rede aos utilizadores. Principal objetivo a política é impedir que a configuração incorreta de rede e para assegurar as operações normais de instância gerida. Quando elimina uma instância gerida, a política de intenção de rede também é removida.

## <a name="virtual-cluster-connectivity-architecture"></a>Arquitetura de conectividade do virtual cluster

Vamos aprofundar na arquitetura de conectividade para instâncias geridas. O diagrama seguinte mostra o esquema conceitual do virtual cluster.

![Arquitetura de conectividade do virtual cluster](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Os clientes ligam a uma instância gerida por um nome de anfitrião com o formato `<mi_name>.<dns_zone>.database.windows.net`. Este nome de anfitrião é resolvido para um endereço IP privado, embora ele está registado numa zona de sistema de nomes (DNS) de domínio público e é resolvível publicamente. O `zone-id` é gerado automaticamente quando criar o cluster. Se um cluster recém-criado aloja uma instância secundária, compartilha sua identificação de zona com o cluster principal. Para obter mais informações, consulte [utilizar grupos de ativação pós-falha automática para ativar a ativação pós-falha transparente e coordenada de várias bases de dados](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets).

Este endereço IP privado pertence ao balanceador de carga interno a instância gerida. O Balanceador de carga direciona o tráfego para o gateway a instância gerida. Uma vez que várias instâncias geridas podem ser executados dentro do mesmo cluster, o gateway utiliza o nome de anfitrião a instância gerida para redirecionar o tráfego para o serviço de motor SQL correto.

Serviços de gerenciamento e implantação de ligar a uma instância gerida com um [ponto final de gestão](#management-endpoint) que é mapeado para um externo Balanceador de carga. Tráfego é encaminhado para os nós apenas se recebido num conjunto predefinido de portas que utilizam componentes de gerenciamento apenas a instância gerida. Um firewall interno em nós é configurado para permitir tráfego apenas a partir de intervalos de IP da Microsoft. Certificados autenticar mutuamente todas as comunicações entre os componentes de gestão e o plano de gestão.

## <a name="management-endpoint"></a>Ponto final de gestão

Microsoft gerencia a instância gerida utilizando um ponto de final de gestão. Este ponto final é dentro do cluster virtual da instância. O ponto final de gestão está protegido por um firewall interno no nível da rede. No nível da aplicação, é protegida pela verificação de certificados mútuos. Para obter o endereço IP do ponto de extremidade, veja [determinar o endereço IP do ponto final de gestão](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Quando as ligações começam dentro da instância gerida (tal como acontece com as cópias de segurança e registos de auditoria), o tráfego é apresentado iniciar a partir endereço IP do ponto final de gestão. Pode limitar o acesso aos serviços do público de uma instância gerida através da definição de regras de firewall para permitir apenas o endereço IP da instância gerida. Para obter mais informações, consulte [Verifique se o firewall interno da instância gerida](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> O tráfego que vai para serviços do Azure que estão dentro de região a instância gerida está otimizado e por esse motivo, não NATed para geridos gestão ponto final público endereço IP da instância. Por esse motivo se precisar de utilizar regras de firewall baseadas em IP, mais comumente para armazenamento, o serviço precisa ser numa região diferente da instância gerida.

## <a name="network-requirements"></a>Requisitos da rede

Implemente uma instância gerida numa sub-rede dedicada dentro da rede virtual. A sub-rede tem de ter as seguintes características:

- **Sub-rede dedicado:** Sub-rede da instância gerida não pode conter qualquer outro serviço de cloud que está associado e não pode ser uma sub-rede de gateway. A sub-rede não pode conter qualquer recurso, mas a instância gerida e, mais tarde não é possível adicionar outros tipos de recursos na sub-rede.
- **Grupo de segurança de rede (NSG):** Um NSG associado a rede virtual tem de definir [regras de segurança de entrada](#mandatory-inbound-security-rules) e [regras de segurança de saída](#mandatory-outbound-security-rules) antes de quaisquer outras regras. Pode utilizar um NSG para controlar o acesso ao ponto final de dados a instância gerida ao filtrar o tráfego na porta 1433 e portas 11999 11000 quando a instância gerida está configurada para redirecionar as ligações.
- **Tabela de rota definida pelo (UDR) de utilizador:** Uma tabela UDR que está associada a rede virtual tem de incluir específico [entradas](#user-defined-routes).
- **Não existem pontos finais de serviço:** Nenhum ponto de final de serviço deve ser associado a sub-rede a instância gerida. Certifique-se de que a opção de pontos finais de serviço é desabilitada quando criar a rede virtual.
- **Endereços IP suficientes:** A sub-rede de instância gerida tem de ter, pelo menos, 16 endereços IP. O mínimo recomendado é 32 endereços IP. Para obter mais informações, consulte [determinar o tamanho da sub-rede para instâncias geridas](sql-database-managed-instance-determine-size-vnet-subnet.md). Pode implementar instâncias geridas na [da rede existente](sql-database-managed-instance-configure-vnet-subnet.md) depois de configurá-lo para satisfazer [os requisitos de rede para instâncias geridas](#network-requirements). Caso contrário, crie uma [nova rede e sub-rede](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Não é possível implementar uma nova instância gerida, se a sub-rede de destino não tiver estas características. Quando cria uma instância gerida, é aplicada uma política de intenção de rede na sub-rede para impedir alterações não conformes a configuração do funcionamento em rede. Após a última instância é removida a partir da sub-rede, a política de intenção de rede também é removida.

### <a name="mandatory-inbound-security-rules"></a>Regras de segurança de entrada obrigatório

| Name       |Port                        |Protocol|Origem           |Destino|Ação|
|------------|----------------------------|--------|-----------------|-----------|------|
|móveis  |9000, 9003, 1438, 1440, 1452|TCP     |Qualquer              |SUB-REDE DE MI  |Permitir |
|mi_subnet   |Qualquer                         |Qualquer     |SUB-REDE DE MI        |SUB-REDE DE MI  |Permitir |
|health_probe|Qualquer                         |Qualquer     |AzureLoadBalancer|SUB-REDE DE MI  |Permitir |

### <a name="mandatory-outbound-security-rules"></a>Regras de segurança de saída obrigatórios

| Name       |Port          |Protocol|Origem           |Destino|Ação|
|------------|--------------|--------|-----------------|-----------|------|
|móveis  |80, 443, 12000|TCP     |SUB-REDE DE MI        |AzureCloud |Permitir |
|mi_subnet   |Qualquer           |Qualquer     |SUB-REDE DE MI        |SUB-REDE DE MI  |Permitir |

> [!IMPORTANT]
> Certifique-se de que existe apenas uma regra de entrada para portas 9000, 9003, 1438, 1440, 1452 e uma regra de saída para as portas 80, 443, 12000. Gerido através do Azure Resource Manager as implementações irão falhar se as regras de entrada e saídas estão configuradas em separado para cada porta de aprovisionamento da instância. Se estas portas são nas regras separadas, a implementação irá falhar com o código de erro `VnetSubnetConflictWithIntendedPolicy`

\* Sub-rede de MI refere-se para o intervalo de endereços IP para a sub-rede na 10.x.x.x/y formulário. Pode encontrar estas informações no portal do Azure, nas propriedades de sub-rede.

> [!IMPORTANT]
> Embora as regras de segurança de entrada necessário permitem tráfego a partir _qualquer_ nas portas de origem 9000, 9003, 1438, 1440 e 1452, estas portas são protegidas por uma firewall interna. Para obter mais informações, consulte [determinar o endereço de ponto final de gestão](sql-database-managed-instance-find-management-endpoint-ip-address.md).
> [!NOTE]
> Se utilizar a replicação transacional numa instância gerida e, se utilizar qualquer base de dados de instância como um publicador ou distribuidor, abra a porta 445 (saída de TCP) nas regras de segurança da sub-rede. Esta porta permitirá o acesso à partilha de ficheiros do Azure.

### <a name="user-defined-routes"></a>Rotas definidas pelo utilizador

|Name|Prefixo de endereço|Salto seguinte|
|----|--------------|-------|
|subnet_to_vnetlocal|SUB-REDE DE MI|Rede virtual|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-96-13-nexthop-internet|13.96.0.0/13|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-8-nexthop-internet|20.0.0.0/8|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-8-nexthop-internet|51.0.0.0/8|Internet|
|mi-52-8-nexthop-internet|52.0.0.0/8|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-22-nexthop-internet|103.25.156.0/22|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-253-16-nexthop-internet|131.253.0.0/16|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-100-102-24-nexthop-internet|192.100.102.0/24|Internet|
|mi-192-100-103-24-nexthop-internet|192.100.103.0/24|Internet|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|Internet|
||||

Além disso, pode adicionar entradas à tabela de rotas para encaminhar o tráfego que tem intervalos de IP privados no local como um destino através do gateway de rede virtual ou a aplicação de rede virtual (NVA).

Se a rede virtual inclui um DNS personalizado, o servidor DNS personalizado tem de ser capaz de resolver os nomes de anfitrião no \*. core.windows.net zona. Usando recursos adicionais, como a autenticação do Azure AD pode exigir a resolução de FQDNs adicionais. Para obter mais informações, consulte [configurar um DNS personalizado](sql-database-managed-instance-custom-dns.md).

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral, consulte [base de dados SQL avançadas de segurança dos dados](sql-database-managed-instance.md).
- Saiba como [configurar uma nova rede virtual do Azure](sql-database-managed-instance-create-vnet-subnet.md) ou uma [rede virtual do Azure existente](sql-database-managed-instance-configure-vnet-subnet.md) onde pode implementar instâncias geridas.
- [Calcular o tamanho da sub-rede](sql-database-managed-instance-determine-size-vnet-subnet.md) onde pretende implementar as instâncias geridas.
- Saiba como criar uma instância gerida:
  - Partir do [portal do Azure](sql-database-managed-instance-get-started.md).
  - Usando [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Usando [um modelo Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Usando [um modelo do Azure Resource Manager (usando a JumpBox, com o SSMS incluídos)](https://portal.azure.com/). 
