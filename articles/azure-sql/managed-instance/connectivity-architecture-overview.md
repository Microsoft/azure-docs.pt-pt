---
title: Arquitetura de conectividade
titleSuffix: Azure SQL Managed Instance
description: Saiba mais sobre a arquitetura de comunicação e conectividade gerida azure SQL, bem como como os componentes direcionam o tráfego para a Instância Gerida sQL.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: e0a16ac8b52907f5ce27d0d186172725e8536423
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045187"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Arquitetura de conectividade para a instância gerida azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo explica a comunicação numa Instância Gerida Azure SQL. Também descreve a arquitetura de conectividade e como os componentes direcionam o tráfego para a Instância Gerida SQL.  

O SQL Managed Instance é colocado dentro da rede virtual Azure e da subnet que é dedicada a Instâncias Geridas SQL. Esta implantação fornece:

- Um endereço IP privado seguro.
- A capacidade de ligar uma rede no local a uma Instância Gerida SQL.
- A capacidade de ligar um SQL Managed Instance a um servidor ligado ou a outra loja de dados no local.
- A capacidade de ligar um SQL Managed Instance aos recursos Azure.

## <a name="communication-overview"></a>Visão geral da comunicação

O diagrama seguinte mostra entidades que se ligam a uma Instância Gerida SQL. Mostra também os recursos necessários para comunicar com a Instância Gerida SQL. O processo de comunicação na parte inferior do diagrama representa aplicações e ferramentas do cliente que se ligam à Instância Gerida SQL como fontes de dados.  

![Entidades em arquitetura de conectividade](./media/connectivity-architecture-overview/connectivityarch001.png)

A SQL Managed Instance é uma plataforma como uma oferta de serviço (PaaS). O Azure utiliza agentes automatizados (gestão, implantação e manutenção) para gerir este serviço com base em fluxos de dados de telemetria. Como o Azure é responsável pela gestão, os clientes não podem aceder às máquinas de cluster virtual SQL Managed Instance através do Protocolo de Ambiente de Trabalho Remoto (RDP).

Algumas operações iniciadas por utilizadores finais ou aplicações podem exigir que as Instâncias Geridas sQL interajam com a plataforma. Um dos casos é a criação de uma base de dados SQL Managed Instance. Este recurso é exposto através do portal Azure, PowerShell, Azure CLI e da REST API.

As Instâncias Geridas sQL dependem de serviços Azure como O Armazenamento Azure para backups, Hubs de eventos Azure para telemetria, Diretório Ativo Azure para autenticação, Cofre chave Azure para encriptação de dados transparente (TDE) e um par de serviços da plataforma Azure que fornecem funcionalidades de segurança e suporte. As Instâncias Geridas sQL fazem ligações a estes serviços.

Todas as comunicações são encriptadas e assinadas usando certificados. Para verificar a fiabilidade das partes comunicativas, as Instâncias Geridas sQL verificam constantemente estes certificados através de listas de revogação de certificados. Se os certificados forem revogados, o SQL Managed Instance fecha as ligações para proteger os dados.

## <a name="high-level-connectivity-architecture"></a>Arquitetura de conectividade de alto nível

A um nível elevado, um SQL Managed Instance é um conjunto de componentes de serviço. Estes componentes estão hospedados num conjunto dedicado de máquinas virtuais isoladas que funcionam dentro da subnet virtual da rede do cliente. Estas máquinas formam um aglomerado virtual.

Um cluster virtual pode acolher várias instâncias geridas pela SQL. Se necessário, o cluster expande-se automaticamente ou contrai-se quando o cliente altera o número de instâncias provisionadas na sub-rede.

As aplicações ao cliente podem ligar-se a Instâncias Geridas pela SQL e podem consultar e atualizar bases de dados dentro da rede virtual, rede virtual com par ou rede ligada por VPN ou Azure ExpressRoute. Esta rede deve utilizar um ponto final e um endereço IP privado.  

![Diagrama de arquitetura de conectividade](./media/connectivity-architecture-overview/connectivityarch002.png)

Os serviços de gestão e implantação da Azure funcionam fora da rede virtual. Um SQL Managed Instance e os serviços Azure ligam-se aos pontos finais que têm endereços IP públicos. Quando uma Instância Gerida SQL cria uma ligação de saída, ao receber a Tradução de Endereços de Rede (NAT) faz com que a ligação pareça que vem deste endereço IP público.

O tráfego de gestão flui através da rede virtual do cliente. Isto significa que elementos da infraestrutura da rede virtual podem prejudicar o tráfego de gestão fazendo com que a instância falhe e fique indisponível.

> [!IMPORTANT]
> Para melhorar a experiência do cliente e a disponibilidade de serviço, o Azure aplica uma política de intenção de rede em elementos de infraestrutura de rede virtual Azure. A política pode afetar o funcionamento do SQL Managed Instance. Este mecanismo de plataforma comunica de forma transparente os requisitos de networking aos utilizadores. O principal objetivo da política é prevenir a configuração errada da rede e garantir operações normais de Instância Gerida sQL. Ao eliminar uma Instância Gerida SQL, a política de intenções de rede também é removida.

## <a name="virtual-cluster-connectivity-architecture"></a>Arquitetura de conectividade de cluster virtual

Vamos dar um mergulho mais profundo na arquitetura de conectividade para Instâncias Geridas SQL. O diagrama seguinte mostra o layout conceptual do cluster virtual.

![Arquitetura de conectividade do cluster virtual](./media/connectivity-architecture-overview/connectivityarch003.png)

Os clientes ligam-se a uma Instância Gerida SQL utilizando um nome de anfitrião que tem o formulário `<mi_name>.<dns_zone>.database.windows.net` . Este nome de anfitrião resolve-se num endereço IP privado, embora esteja registado numa zona pública do Sistema de Nomede Domínio (DNS) e seja publicamente resolúvel. O `zone-id` é gerado automaticamente quando se cria o cluster. Se um cluster recém-criado acolhe uma instância gerida secundária, partilha o seu ID de zona com o cluster primário. Para obter mais informações, consulte [Utilize grupos de failover automático para permitir falhas transparentes e coordenadas de várias bases de dados](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Este endereço IP privado pertence ao equilíbrio interno de carga da SQL Managed Instance. O equilibrador de carga direciona o tráfego para o portal da Instância Gerida sQL. Como várias instâncias geridas pela SQL podem ser executadas dentro do mesmo cluster, o gateway usa o nome de anfitrião do SQL Managed Instance para redirecionar o tráfego para o serviço de motor SQL correto.

Os serviços de gestão e implementação ligam-se a um Caso Gerido SQL utilizando um ponto final de [gestão](#management-endpoint) que mapeia para um equilibrador de carga externo. O tráfego só é encaminhado para os nódosos se for recebido num conjunto predefinido de portas que apenas os componentes de gestão da SQL Managed Instance utilizam. Uma firewall incorporada nos nódosos é configurada para permitir o tráfego apenas a partir das gamas IP da Microsoft. Os certificados autenticam mutuamente toda a comunicação entre os componentes de gestão e o plano de gestão.

## <a name="management-endpoint"></a>Ponto final de gestão

O Azure gere o SQL Managed Instance utilizando um ponto final de gestão. Este ponto final está dentro do aglomerado virtual da instância. O ponto final de gestão é protegido por uma firewall incorporada no nível da rede. No nível de candidatura, está protegido por verificação de certificado mútuo. Para encontrar o endereço IP do ponto final, consulte [Determine o endereço IP do ponto final da gestão](management-endpoint-find-ip-address.md).

Quando as ligações começam dentro da Instância Gerida SQL (como em cópias de segurança e registos de auditoria), o tráfego parece começar a partir do endereço IP público do ponto final da gestão. Pode limitar o acesso aos serviços públicos a partir de uma Instância Gerida SQL, definindo regras de firewall para permitir apenas o endereço IP da Instância Gerida SQL. Para mais informações, [consulte Verifique a firewall incorporada da Instância Gerida SQL](management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> O tráfego que vai para os serviços Azure que estão dentro da região da Instância Gerida SQL está otimizado e, por isso, não nadou ao endereço IP público de ponto final de gestão. Por essa razão, se precisar de utilizar regras de firewall baseadas em IP, mais frequentemente para armazenamento, o serviço precisa estar numa região diferente da Instância Gerida sQL.

## <a name="service-aided-subnet-configuration"></a>Configuração de sub-rede com a ajuda de um serviço

Para responder aos requisitos de segurança e gestão do cliente, a SQL Managed Instance está a transitar de configuração manual para subnet ajudada ao serviço.

Com o utilizador de configuração de sub-rede assistido pelo serviço está em pleno controlo do tráfego de dados (TDS), enquanto a SQL Managed Instance assume a responsabilidade de garantir um fluxo ininterrupto de tráfego de gestão para cumprir o SLA.

A configuração da sub-rede com a ajuda de um serviço baseia-se na funcionalidade de [delegação de sub-rede](../../virtual-network/subnet-delegation-overview.md) da rede virtual para permitir a gestão da configuração de rede automática e ativar os pontos finais de serviço. Poderá utilizar os pontos finais de serviço para configurar as regras de firewall da rede virtual nas contas de armazenamento que armazenam os registos de auditoria/cópias de segurança.

### <a name="network-requirements"></a>Requisitos da rede

Implemente uma Instância Gerida SQL numa subnet dedicada dentro da rede virtual. A sub-rede deve ter estas características:

- **Sub-rede dedicada:** A subnet sql managed Instance não pode conter qualquer outro serviço de nuvem que esteja associado a ele, e não pode ser uma sub-rede gateway. A subnet não pode conter nenhum recurso, mas a Instância Gerida SQL, e não pode adicionar mais tarde outros tipos de recursos na sub-rede.
- **Delegação subnet:** A subnet sql Managed Instance precisa de ser delegada no fornecedor de `Microsoft.Sql/managedInstances` recursos.
- **Grupo de segurança de rede (NSG):** Um NSG precisa de ser associado à subnet da Instância Gerida SQL. Pode utilizar um NSG para controlar o acesso ao ponto final de dados da Instância Gerida SQL filtrando o tráfego na porta 1433 e nas portas 11000-11999 quando a Instância Gerida SQL estiver configurada para ligações redirecionais. O serviço será automaticamente aprovisionado e manterá as [regras](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) atuais exigidas para permitir o fluxo do tráfego de gestão ininterrupto.
- **Tabela de rota definida pelo utilizador (UDR):** Uma tabela UDR precisa de ser associada à subnet da Instância Gerida SQL. Pode adicionar entradas à tabela de rotas para encaminhar o tráfego que possui intervalos de IP privados no local como destino através do gateway da rede virtual ou da aplicação virtual de rede (NVA). O serviço será automaticamente aprovisionado e manterá as [entradas](#user-defined-routes-with-service-aided-subnet-configuration) atuais exigidas para permitir o fluxo do tráfego de gestão ininterrupto.
- **Endereços IP suficientes:** A sub-rede SQL Managed Instance deve ter pelo menos 16 endereços IP. O mínimo recomendado é de 32 endereços IP. Para obter mais informações, consulte [Determine o tamanho da subnet para Instâncias Geridas SQL](vnet-subnet-determine-size.md). Pode implementar Instâncias Geridas SQL na [rede existente](vnet-existing-add-subnet.md) depois de configurá-la para satisfazer os requisitos de [networking para Instâncias Geridas SQL](#network-requirements). Caso contrário, crie uma [nova rede e sub-rede](virtual-network-subnet-create-arm-template.md).

> [!IMPORTANT]
> Quando cria uma Instância Gerida SQL, aplica-se uma política de intenção de rede na subnet para evitar alterações não conformes à configuração da rede. Após a remoção da última instância da sub-rede, a política de intenção da rede também é removida.

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Regras de segurança obrigatórias de entrada com configuração de sub-rede ajudada pelo serviço

| Name       |Porta                        |Protocolo|Origem           |Destino|Ação|
|------------|----------------------------|--------|-----------------|-----------|------|
|gestão  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |MI SUBNET  |Permitir |
|            |9000, 9003                  |TCP     |CorpnetSaw       |MI SUBNET  |Permitir |
|            |9000, 9003                  |TCP     |CorpnetPublic    |MI SUBNET  |Permitir |
|mi_subnet   |Qualquer                         |Qualquer     |MI SUBNET        |MI SUBNET  |Permitir |
|health_probe|Qualquer                         |Qualquer     |AzureLoadBalancer|MI SUBNET  |Permitir |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Regras de segurança obrigatórias de saída com configuração de sub-rede ajudada pelo serviço

| Name       |Porta          |Protocolo|Origem           |Destino|Ação|
|------------|--------------|--------|-----------------|-----------|------|
|gestão  |443, 12000    |TCP     |MI SUBNET        |AzureCloud |Permitir |
|mi_subnet   |Qualquer           |Qualquer     |MI SUBNET        |MI SUBNET  |Permitir |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Vias definidas pelo utilizador com configuração de sub-rede ajudada pelo serviço

|Name|Prefixo de endereço|Próximo Hop|
|----|--------------|-------|
|subnet-to-vnetlocal|MI SUBNET|Rede virtual|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-internet|131.253.128.0/17|Internet|
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
|mi-167-105-16-nexthop-internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
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
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|Internet|
||||

\*MI SUBNET refere-se à gama de endereços IP para a sub-rede no formulário x.x.x.x/y. Pode encontrar esta informação no portal Azure, em propriedades de sub-rede.

Além disso, pode adicionar entradas à tabela de rotas para o tráfego de rotas que tem no local gamas IP privadas como destino através do gateway de rede virtual ou do aparelho de rede virtual (NVA).

Se a rede virtual incluir um DNS personalizado, o servidor DNS personalizado deve ser capaz de resolver registos públicos de DNS. A utilização de funcionalidades adicionais como a Autenticação AD Azure pode exigir a resolução de FQDNs adicionais. Para mais informações, consulte [Configurar um DNS personalizado](custom-dns-configure.md).

### <a name="networking-constraints"></a>Restrições de rede

**TLS 1.2 é aplicado em ligações de saída**: Em janeiro de 2020 a Microsoft impôs tLS 1.2 para tráfego intra-serviço em todos os serviços Azure. Para o Caso Gerido Azure SQL, isto resultou na aplicação do TLS 1.2 nas ligações de saída utilizadas para a replicação e ligações de servidores ligadas ao Servidor SQL. Se estiver a utilizar versões do SQL Server mais antigas e 2016 com a SQL Managed Instance, certifique-se de que foram [aplicadas atualizações específicas do TLS 1.2.](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)

As seguintes funcionalidades de rede virtual não são atualmente suportadas com a Instância Gerida sQL:

- **Microsoft peering**: Permitir que a [Microsoft peering](../../expressroute/expressroute-faqs.md#microsoft-peering) em circuitos de rota expressa soisse direta ou transitivamente com rede virtual onde a SQL Managed Instance reside afetam o fluxo de tráfego entre componentes de Instância Gerida SQL dentro da rede virtual e serviços depende de causar problemas de disponibilidade. Espera-se que as implementações de Instância gerida para a rede virtual com o peering da Microsoft já ativado falhem.
- **Peering global de rede virtual**: A conectividade da rede [virtual](../../virtual-network/virtual-network-peering-overview.md) em todas as regiões do Azure não funciona para a SQL Managed Instance devido a [restrições documentadas](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)de equilíbrio de carga.
- **AzurePlatformDNS**: Utilizar a [etiqueta](../../virtual-network/service-tags-overview.md) de serviço AzurePlatformDNS para bloquear a resolução dNS da plataforma tornaria a Instância Gerida sQL indisponível. Embora a SQL Managed Instance suporte o Cliente definido DNS para resolução DNS dentro do motor, existe uma dependência da plataforma DNS para operações de plataforma.
- **Gateway NAT**: Utilizar a [Rede Virtual NAT](../../virtual-network/nat-overview.md) para controlar a conectividade de saída com um endereço IP público específico tornaria a Instância Gerida sQL indisponível. O serviço SQL Managed Instance está atualmente limitado à utilização de um equilíbrio de carga básico que não proporciona a coexistência de fluxos de entrada e saída com o NAT da rede virtual.

### <a name="deprecated-network-requirements-without-service-aided-subnet-configuration"></a>[Depreciado] Requisitos de rede sem configuração de sub-rede ajudada pelo serviço

Implemente uma Instância Gerida SQL numa subnet dedicada dentro da rede virtual. A sub-rede deve ter estas características:

- **Sub-rede dedicada:** A subnet sql managed Instance não pode conter qualquer outro serviço de nuvem que esteja associado a ele, e não pode ser uma sub-rede gateway. A subnet não pode conter nenhum recurso, mas a Instância Gerida SQL, e não pode adicionar mais tarde outros tipos de recursos na sub-rede.
- **Grupo de segurança de rede (NSG):** Um NSG associado à rede virtual deve definir regras de segurança de [entrada](#mandatory-inbound-security-rules) e regras de segurança de [saída](#mandatory-outbound-security-rules) antes de quaisquer outras regras. Pode utilizar um NSG para controlar o acesso ao ponto final de dados da Instância Gerida SQL filtrando o tráfego na porta 1433 e nas portas 11000-11999 quando a Instância Gerida SQL estiver configurada para ligações redirecionais.
- **Tabela de rota definida pelo utilizador (UDR):** Uma tabela UDR associada à rede virtual deve incluir [entradas específicas.](#user-defined-routes)
- **Sem pontos finais de serviço:** Nenhum ponto final de serviço deve ser associado à sub-rede da Instância Gerida SQL. Certifique-se de que a opção de pontos finais do serviço está desativada quando criar a rede virtual.
- **Endereços IP suficientes:** A sub-rede SQL Managed Instance deve ter pelo menos 16 endereços IP. O mínimo recomendado é de 32 endereços IP. Para obter mais informações, consulte [Determine o tamanho da subnet para Instâncias Geridas SQL](vnet-subnet-determine-size.md). Pode implementar Instâncias Geridas SQL na [rede existente](vnet-existing-add-subnet.md) depois de configurá-la para satisfazer os requisitos de [networking para Instâncias Geridas SQL](#network-requirements). Caso contrário, crie uma [nova rede e sub-rede](virtual-network-subnet-create-arm-template.md).

> [!IMPORTANT]
> Não é possível implementar um novo SQL Managed Instance se a subnet de destino não tiver estas características. Quando cria uma Instância Gerida SQL, aplica-se uma política de intenção de rede na subnet para evitar alterações não conformes à configuração da rede. Após a remoção da última instância da sub-rede, a política de intenção da rede também é removida.

### <a name="mandatory-inbound-security-rules"></a>Regras de segurança obrigatórias

| Name       |Porta                        |Protocolo|Origem           |Destino|Ação|
|------------|----------------------------|--------|-----------------|-----------|------|
|gestão  |9000, 9003, 1438, 1440, 1452|TCP     |Qualquer              |MI SUBNET  |Permitir |
|mi_subnet   |Qualquer                         |Qualquer     |MI SUBNET        |MI SUBNET  |Permitir |
|health_probe|Qualquer                         |Qualquer     |AzureLoadBalancer|MI SUBNET  |Permitir |

### <a name="mandatory-outbound-security-rules"></a>Regras de segurança obrigatórias

| Name       |Porta          |Protocolo|Origem           |Destino|Ação|
|------------|--------------|--------|-----------------|-----------|------|
|gestão  |443, 12000    |TCP     |MI SUBNET        |AzureCloud |Permitir |
|mi_subnet   |Qualquer           |Qualquer     |MI SUBNET        |MI SUBNET  |Permitir |

> [!IMPORTANT]
> Certifique-se de que existe apenas uma regra de entrada para os portos 9000, 9003, 1438, 1440, 1452 e uma regra de saída para os portos 443, 12000. O fornecimento de instância gerida pela SQL através de implementações do Gestor de Recursos Do Azure falhará se as regras de entrada e saída forem configuradas separadamente para cada porta. Se estas portas estiverem em regras separadas, a implementação falhará com o código de erro`VnetSubnetConflictWithIntendedPolicy`

\*MI SUBNET refere-se à gama de endereços IP para a sub-rede no formulário x.x.x.x/y. Pode encontrar esta informação no portal Azure, em propriedades de sub-rede.

> [!IMPORTANT]
> Embora as regras de segurança de entrada exigidas permitam o tráfego de _qualquer_ fonte nos portos 9000, 9003, 1438, 1440 e 1452, estes portos estão protegidos por uma firewall incorporada. Para mais informações, consulte [Determine o endereço final da gestão](management-endpoint-find-ip-address.md).

> [!NOTE]
> Se utilizar a replicação transacional numa Instância Gerida SQL e se utilizar qualquer base de dados de instâncias como editor ou distribuidor, abra a porta 445 (saída de TCP) nas regras de segurança da subnet. Esta porta permitirá o acesso à partilha de ficheiros Azure.

### <a name="user-defined-routes"></a>Rotas definidas pelo utilizador

|Name|Prefixo de endereço|Próximo Hop|
|----|--------------|-------|
|subnet_to_vnetlocal|MI SUBNET|Rede virtual|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-internet|131.253.128.0/17|Internet|
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
|mi-167-105-16-nexthop-internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
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
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|Internet|
||||

## <a name="next-steps"></a>Próximos passos

- Para uma visão geral, veja [o que é azure SQL Managed Instance?](sql-managed-instance-paas-overview.md)
- Saiba como [criar uma nova rede virtual Azure](virtual-network-subnet-create-arm-template.md) ou uma rede virtual [Azure existente](vnet-existing-add-subnet.md) onde pode implementar Instâncias Geridas SQL.
- [Calcular o tamanho da sub-rede](vnet-subnet-determine-size.md) onde pretende implementar as Instâncias Geridas SQL.
- Saiba como criar uma Instância Gerida SQL:
  - Do [portal Azure.](instance-create-quickstart.md)
  - Utilizando [powerShell](scripts/create-configure-managed-instance-powershell.md).
  - Utilizando um modelo de Gestor de [Recursos Azure](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Utilizando um modelo de Gestor de [Recursos Azure (utilizando o JumpBox, com SSMS incluído)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/).
