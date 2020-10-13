---
title: Arquitetura de conectividade
titleSuffix: Azure SQL Managed Instance
description: Saiba mais sobre a comunicação de instâncias geridas Azure SQL e arquitetura de conectividade, bem como como os componentes direcionam o tráfego para uma instância gerida.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 03/17/2020
ms.openlocfilehash: 81d0731f6ea77325b3f33f91bf8d5d1386dab2fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91283382"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Arquitetura de conectividade do Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo explica a comunicação em Azure SQL Managed Instance. Também descreve a arquitetura de conectividade e como os componentes direcionam o tráfego para uma instância gerida.  

SQL Managed Instance é colocado dentro da rede virtual Azure e a sub-rede que é dedicada a casos geridos. Esta implantação fornece:

- Um endereço IP privado seguro.
- A capacidade de ligar uma rede no local à SQL Managed Instance.
- A capacidade de ligar a SQL Managed Instance a um servidor ligado ou a outra loja de dados no local.
- A capacidade de ligar a SQL Managed Instance aos recursos do Azure.

## <a name="communication-overview"></a>Visão geral da comunicação

O diagrama seguinte mostra entidades que se ligam à SQL Managed Instance. Mostra também os recursos que precisam de comunicar com uma instância gerida. O processo de comunicação na parte inferior do diagrama representa aplicações e ferramentas do cliente que se conectam à SQL Managed Instance como fontes de dados.  

![Entidades em arquitetura de conectividade](./media/connectivity-architecture-overview/connectivityarch001.png)

SQL Managed Instance é uma plataforma como uma oferta de serviço (PaaS). A Azure utiliza agentes automatizados (gestão, implantação e manutenção) para gerir este serviço com base em fluxos de dados de telemetria. Como o Azure é responsável pela gestão, os clientes não podem aceder às máquinas de cluster virtual SQL Managed Instance através do Remote Desktop Protocol (RDP).

Algumas operações iniciadas por utilizadores finais ou aplicações podem exigir que a SQL Managed Instance interaja com a plataforma. Um dos casos é a criação de uma base de dados SQL Managed Instance. Este recurso é exposto através do portal Azure, PowerShell, Azure CLI e da API REST.

A SQL Managed Instance depende de serviços Azure como Azure Storage para backups, Azure Event Hubs para telemetria, Azure Ative Directory (Azure AD) para autenticação, Cofre de Chaves Azure para Encriptação de Dados Transparentes (TDE), e alguns serviços da plataforma Azure que fornecem funcionalidades de segurança e suporte. A SQL Managed Instance faz ligações a estes serviços.

Todas as comunicações são encriptadas e assinadas com certificados. Para verificar a fiabilidade das partes comunicantes, a SQL Managed Instance verifica constantemente estes certificados através de listas de revogação de certificados. Se os certificados forem revogados, a SQL Managed Instance fecha as ligações para proteger os dados.

## <a name="high-level-connectivity-architecture"></a>Arquitetura de conectividade de alto nível

A um nível elevado, SQL Managed Instance é um conjunto de componentes de serviço. Estes componentes estão alojados num conjunto dedicado de máquinas virtuais isoladas que funcionam dentro da sub-rede de rede virtual do cliente. Estas máquinas formam um aglomerado virtual.

Um cluster virtual pode acolher várias instâncias geridas. Se necessário, o cluster expande-se automaticamente ou contrata quando o cliente altera o número de instâncias a provisionadas na sub-rede.

As aplicações do cliente podem ligar-se à SQL Managed Instance e podem consultar e atualizar bases de dados dentro da rede virtual, rede virtual esprevada ou rede ligada pela VPN ou Azure ExpressRoute. Esta rede deve utilizar um ponto final e um endereço IP privado.  

![Diagrama de arquitetura de conectividade](./media/connectivity-architecture-overview/connectivityarch002.png)

Os serviços de gestão e implantação da Azure são executados fora da rede virtual. Os serviços SQL Managed Instance e Azure conectam-se sobre os pontos finais que têm endereços IP públicos. Quando a SQL Managed Instance cria uma ligação de saída, na tradução de endereço de rede recetora (NAT) faz com que a ligação pareça que vem deste endereço IP público.

O tráfego de gestão flui através da rede virtual do cliente. Isto significa que elementos da infraestrutura da rede virtual podem prejudicar o tráfego de gestão, fazendo com que o caso falhe e se torne indisponível.

> [!IMPORTANT]
> Para melhorar a experiência do cliente e a disponibilidade de serviço, o Azure aplica uma política de intenção de rede em elementos de infraestrutura de rede virtual Azure. A política pode afetar o funcionamento do SQL Managed Instance. Este mecanismo de plataforma comunica de forma transparente os requisitos de networking para os utilizadores. O principal objetivo da política é prevenir a má configuração da rede e assegurar operações normais de sql managed instance. Quando elimina uma instância gerida, a política de intenções de rede também é removida.

## <a name="virtual-cluster-connectivity-architecture"></a>Arquitetura de conectividade de cluster virtual

Vamos mergulhar mais profundamente na arquitetura de conectividade para o SQL Managed Instance. O diagrama seguinte mostra o layout conceptual do cluster virtual.

![Arquitetura de conectividade do cluster virtual](./media/connectivity-architecture-overview/connectivityarch003.png)

Os clientes conectam-se à SQL Managed Instance utilizando um nome de anfitrião que tem o formulário `<mi_name>.<dns_zone>.database.windows.net` . Este nome de anfitrião resolve-se para um endereço IP privado, embora esteja registado numa zona pública do Sistema de Nome de Domínio (DNS) e seja publicamente resolúvel. O `zone-id` é gerado automaticamente quando se cria o cluster. Se um cluster recém-criado hospedar um caso secundário gerido, partilha o seu ID de zona com o cluster primário. Para obter mais informações, consulte [utilize grupos de falha automática para permitir a falha transparente e coordenada de várias bases de dados](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Este endereço IP privado pertence ao balançador de carga interno para a SQL Managed Instance. O equilibrador de carga direciona o tráfego para o portal SQL Managed Instance. Como várias instâncias geridas podem ser executadas dentro do mesmo cluster, o gateway utiliza o nome de anfitrião SQL Managed Instance para redirecionar o tráfego para o serviço de motor SQL correto.

Os serviços de gestão e implantação conectam-se à SQL Managed Instance utilizando um [ponto final de gestão](#management-endpoint) que mapeia para um equilibrador de carga externo. O tráfego só é encaminhado para os nós se for recebido num conjunto de portas predefinido que apenas os componentes de gestão do SQL Managed Instance usam. Uma firewall incorporada nos nós é configurada para permitir o tráfego apenas a partir das gamas IP da Microsoft. Os certificados autenticam mutuamente toda a comunicação entre componentes de gestão e o plano de gestão.

## <a name="management-endpoint"></a>Ponto final de gestão

A Azure gere a SQL Managed Instance utilizando um ponto final de gestão. Este ponto final está dentro do aglomerado virtual de um caso. O ponto final de gestão está protegido por uma firewall incorporada ao nível da rede. No nível de candidatura, está protegido por verificação de certificado mútuo. Para encontrar o endereço IP do ponto final, consulte [determine o endereço IP do ponto final de gestão](management-endpoint-find-ip-address.md).

Quando as ligações começam dentro da SQL Managed Instance (como acontece com cópias de segurança e registos de auditoria), o tráfego parece começar a partir do endereço IP público do ponto de gestão. Pode limitar o acesso a serviços públicos a partir de SQL Managed Instance, definindo regras de firewall para permitir apenas o endereço IP para SQL Managed Instance. Para obter mais informações, consulte [verifique a firewall incorporada SQL.1M.](management-endpoint-verify-built-in-firewall.md)

> [!NOTE]
> O tráfego que vai para os serviços Azure que se encontram dentro da região sql Managed Instance está otimizado e por isso não é nated para o endereço IP público para o ponto final de gestão. Por essa razão, se precisar de utilizar regras de firewall baseadas em IP, mais comummente para armazenamento, o serviço precisa estar numa região diferente da SQL Managed Instance.

## <a name="service-aided-subnet-configuration"></a>Configuração de sub-rede com a ajuda de um serviço

Para responder aos requisitos de segurança e gestão do cliente, a SQL Managed Instance está a transitar de configuração manual para sub-rede ajudada pelo serviço.

Com a configuração da sub-rede ajudada pelo serviço, o utilizador está em total controlo do tráfego de dados (TDS), enquanto a SQL Managed Instance assume a responsabilidade de garantir um fluxo ininterrupto de tráfego de gestão de forma a cumprir um SLA.

A configuração da sub-rede apoiada pelo serviço baseia-se na funcionalidade de delegação de [sub-rede](../../virtual-network/subnet-delegation-overview.md) de rede virtual para fornecer uma gestão automática da configuração da rede e permitir pontos finais de serviço. 

Os pontos finais de serviço poderiam ser usados para configurar regras de firewall de rede virtuais em contas de armazenamento que mantêm cópias de segurança e registos de auditoria. Mesmo com os pontos finais de serviço ativados, os clientes são encorajados a usar [link privado](../../private-link/private-link-overview.md) que fornece segurança adicional sobre os pontos finais do serviço.

> [!IMPORTANT]
> Devido às especificidades da configuração do plano de controlo, a configuração da sub-rede ajudada pelo serviço não permitiria pontos finais de serviço em nuvens nacionais. 

### <a name="network-requirements"></a>Requisitos de rede

Implementar a SQL Managed Instance numa sub-rede dedicada dentro da rede virtual. A sub-rede deve ter estas características:

- **Sub-rede dedicada:** A sub-rede SQL Managed Instance não pode conter qualquer outro serviço de nuvem que esteja associado a ele, e não pode ser uma sub-rede de gateway. A sub-rede não pode conter nenhum recurso, mas SQL Managed Instance, e não pode adicionar mais tarde outros tipos de recursos na sub-rede.
- **Delegação da sub-rede:** A sub-rede SQL Managed Instance precisa de ser delegada no `Microsoft.Sql/managedInstances` fornecedor de recursos.
- **Grupo de segurança de rede (NSG):** Um NSG precisa de ser associado com a sub-rede SQL Managed Instance. Pode utilizar um NSG para controlar o acesso ao ponto final de dados da SQL Managed Instance filtrando o tráfego na porta 1433 e nas portas 11000-11999 quando o SQL Managed Instance está configurado para ligações de redirecionamento. O serviço irá automaticamente providenciar e manter [as regras](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) atuais necessárias para permitir um fluxo ininterrupto de tráfego de gestão.
- **Tabela de rota definida pelo utilizador (UDR):** Uma tabela UDR precisa de ser associada à sub-rede SQL Managed Instance. Pode adicionar entradas à tabela de rotas para encaminhar o tráfego que possui intervalos de IP privados no local como destino através do gateway da rede virtual ou da aplicação virtual de rede (NVA). O serviço será automaticamente aprovisionado e manterá as [entradas](#user-defined-routes-with-service-aided-subnet-configuration) atuais exigidas para permitir o fluxo do tráfego de gestão ininterrupto.
- **Endereços IP suficientes:** A sub-rede SQL Managed Instance deve ter pelo menos 16 endereços IP. O mínimo recomendado é de 32 endereços IP. Para obter mais informações, consulte [determine o tamanho da sub-rede para sql Managed Instance](vnet-subnet-determine-size.md). Pode implementar casos geridos [na rede existente](vnet-existing-add-subnet.md) depois de a configurar para satisfazer os [requisitos de networking para a SQL Managed Instance](#network-requirements). Caso contrário, crie uma [nova rede e sub-rede](virtual-network-subnet-create-arm-template.md).

> [!IMPORTANT]
> Quando cria um caso gerido, é aplicada uma política de intenções de rede na sub-rede para evitar alterações não conformes na configuração da rede. Após a remoção da última instância da sub-rede, a política de intenções de rede também é removida.

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Regras de segurança obrigatórias de entrada com configuração de sub-rede ajudada pelo serviço

| Nome       |Porta                        |Protocolo|Origem           |Destino|Ação|
|------------|----------------------------|--------|-----------------|-----------|------|
|gestão  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |SUB-REDE MI  |Permitir |
|            |9000, 9003                  |TCP     |Serra Corpnet       |SUB-REDE MI  |Permitir |
|            |9000, 9003                  |TCP     |CorpnetPublic    |SUB-REDE MI  |Permitir |
|mi_subnet   |Qualquer                         |Qualquer     |SUB-REDE MI        |SUB-REDE MI  |Permitir |
|health_probe|Qualquer                         |Qualquer     |AzureLoadBalancer|SUB-REDE MI  |Permitir |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Regras de segurança obrigatórias de saída com configuração de sub-rede ajudada pelo serviço

| Nome       |Porta          |Protocolo|Origem           |Destino|Ação|
|------------|--------------|--------|-----------------|-----------|------|
|gestão  |443, 12000    |TCP     |SUB-REDE MI        |AzureCloud |Permitir |
|mi_subnet   |Qualquer           |Qualquer     |SUB-REDE MI        |SUB-REDE MI  |Permitir |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Rotas definidas pelo utilizador com configuração de sub-rede ajudada pelo serviço

|Nome|Prefixo de endereço|Próximo salto|
|----|--------------|-------|
|sub-rede-para-vnetlocal|SUB-REDE MI|Rede virtual|
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

\* MI SUBNET refere-se ao intervalo de endereço IP para a sub-rede no formulário x.x.x.x/y. Pode encontrar esta informação no portal Azure, em propriedades da sub-rede.

Além disso, pode adicionar entradas à tabela de rotas para o tráfego de rotas que tem no local gamas IP privadas como destino através do gateway de rede virtual ou do aparelho de rede virtual (NVA).

Se a rede virtual incluir um DNS personalizado, o servidor DNS personalizado deve ser capaz de resolver registos DNS públicos. A utilização de funcionalidades adicionais como a autenticação AD AZure pode requerer a resolução de FQDNs adicionais. Para obter mais informações, consulte [Configurar um DNS personalizado.](custom-dns-configure.md)

### <a name="networking-constraints"></a>Restrições de rede

**O TLS 1.2 é aplicado nas ligações de saída**: Em janeiro de 2020, a Microsoft impôs TLS 1.2 para tráfego intra-serviço em todos os serviços Azure. Para a Azure SQL Managed Instance, isto resultou na aplicação do TLS 1.2 nas ligações de saída utilizadas para replicação e ligações de servidores ligados ao SQL Server. Se estiver a utilizar versões do SQL Server com mais de 2016 com a SQL Managed Instance, certifique-se de que foram [aplicadas atualizações específicas TLS 1.2.](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)

As seguintes funcionalidades de rede virtual não são atualmente suportadas com a SQL Managed Instance:

- **Microsoft peering**: Habilitar [a Microsoft a espreitar](../../expressroute/expressroute-faqs.md#microsoft-peering) os circuitos ExpressRoute espreitei-o direta ou transitivamente com uma rede virtual onde o SQL Managed Instance reside afeta o fluxo de tráfego entre os componentes da SQL Managed Instance dentro da rede virtual e os serviços de que depende, causando problemas de disponibilidade. Espera-se que as implementações de SqL Managed Instance para rede virtual com o microsoft já ativado falhem.
- **Observação global da rede virtual**: A conectividade de [observação de rede virtual](../../virtual-network/virtual-network-peering-overview.md) em todas as regiões do Azure não funciona para a SQL Managed Instance devido a [restrições documentadas do balançador de carga](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).
- **AzurePlatformDNS**: A utilização da [etiqueta de serviço](../../virtual-network/service-tags-overview.md) AzurePlatformDNS para bloquear a resolução de DNS da plataforma tornaria a SQL Managed Instance indisponível. Embora a SQL Managed Instance suporte DNS definido pelo cliente para resolução de DNS dentro do motor, existe uma dependência da plataforma DNS para operações da plataforma.
- **Gateway NAT**: A utilização [da Rede Virtual Azure NAT](../../virtual-network/nat-overview.md) para controlar a conectividade de saída com um endereço IP público específico tornaria a SQL Managed Instance indisponível. O serviço SQL Managed Instance está atualmente limitado ao uso de balanceador de carga básico que não proporciona coexistência de fluxos de entrada e saída com a Rede Virtual NAT.

### <a name="deprecated-network-requirements-without-service-aided-subnet-configuration"></a>[Precatado] Requisitos de rede sem configuração de sub-rede ajudada pelo serviço

Implementar a SQL Managed Instance numa sub-rede dedicada dentro da rede virtual. A sub-rede deve ter estas características:

- **Sub-rede dedicada:** A sub-rede SQL Managed Instance não pode conter qualquer outro serviço de nuvem que esteja associado a ele, e não pode ser uma sub-rede de gateway. A sub-rede não pode conter nenhum recurso, mas SQL Managed Instance, e não pode adicionar mais tarde outros tipos de recursos na sub-rede.
- **Grupo de segurança de rede (NSG):** Um NSG associado à rede virtual deve definir [regras de segurança de entrada](#mandatory-inbound-security-rules) e [regras de segurança de saída](#mandatory-outbound-security-rules) antes de quaisquer outras regras. Pode utilizar um NSG para controlar o acesso ao ponto final de dados da SQL Managed Instance filtrando o tráfego na porta 1433 e nas portas 11000-11999 quando o SQL Managed Instance está configurado para ligações de redirecionamento.
- **Tabela de rota definida pelo utilizador (UDR):** Uma tabela UDR associada à rede virtual deve incluir [entradas específicas.](#user-defined-routes)
- **Sem pontos finais de serviço:** Nenhum ponto final de serviço deve ser associado com a sub-rede SQL Managed Instance. Certifique-se de que a opção de pontos finais de serviço é desativada quando criar a rede virtual.
- **Endereços IP suficientes:** A sub-rede SQL Managed Instance deve ter pelo menos 16 endereços IP. O mínimo recomendado é de 32 endereços IP. Para obter mais informações, consulte [determine o tamanho da sub-rede para sql Managed Instance](vnet-subnet-determine-size.md). Pode implementar casos geridos [na rede existente](vnet-existing-add-subnet.md) depois de a configurar para satisfazer os [requisitos de networking para a SQL Managed Instance](#network-requirements). Caso contrário, crie uma [nova rede e sub-rede](virtual-network-subnet-create-arm-template.md).

> [!IMPORTANT]
> Não é possível implementar uma nova instância gerida se a sub-rede de destino não tiver estas características. Quando cria um caso gerido, é aplicada uma política de intenções de rede na sub-rede para evitar alterações não conformes na configuração da rede. Após a remoção da última instância da sub-rede, a política de intenções de rede também é removida.

### <a name="mandatory-inbound-security-rules"></a>Regras de segurança obrigatórias de entrada

| Nome       |Porta                        |Protocolo|Origem           |Destino|Ação|
|------------|----------------------------|--------|-----------------|-----------|------|
|gestão  |9000, 9003, 1438, 1440, 1452|TCP     |Qualquer              |SUB-REDE MI  |Permitir |
|mi_subnet   |Qualquer                         |Qualquer     |SUB-REDE MI        |SUB-REDE MI  |Permitir |
|health_probe|Qualquer                         |Qualquer     |AzureLoadBalancer|SUB-REDE MI  |Permitir |

### <a name="mandatory-outbound-security-rules"></a>Regras de segurança obrigatórias de saída

| Nome       |Porta          |Protocolo|Origem           |Destino|Ação|
|------------|--------------|--------|-----------------|-----------|------|
|gestão  |443, 12000    |TCP     |SUB-REDE MI        |AzureCloud |Permitir |
|mi_subnet   |Qualquer           |Qualquer     |SUB-REDE MI        |SUB-REDE MI  |Permitir |

> [!IMPORTANT]
> Assegurar que existe apenas uma regra de entrada para os portos 9000, 9003, 1438, 1440 e 1452, e uma regra de saída para os portos 443 e 12000. O fornecimento de exemplos geridos sql através de implementações do Azure Resource Manager falhará se as regras de entrada e saída forem configuradas separadamente para cada porta. Se estas portas estiverem em regras separadas, a implantação falhará com o código de erro `VnetSubnetConflictWithIntendedPolicy` .

\* MI SUBNET refere-se ao intervalo de endereço IP para a sub-rede no formulário x.x.x.x/y. Pode encontrar esta informação no portal Azure, em propriedades da sub-rede.

> [!IMPORTANT]
> Embora as regras de segurança de entrada necessárias permitam o tráfego de _qualquer_ fonte nos portos 9000, 9003, 1438, 1440 e 1452, estas portas estão protegidas por uma firewall incorporada. Para obter mais informações, consulte [determine o endereço final de gestão](management-endpoint-find-ip-address.md).

> [!NOTE]
> Se utilizar a replicação transacional em SQL Managed Instance, e se utilizar qualquer base de dados de instância como editor ou distribuidor, abra a porta 445 (saída TCP) nas regras de segurança da sub-rede. Esta porta permitirá o acesso à partilha de ficheiros Azure.

### <a name="user-defined-routes"></a>Rotas definidas pelo utilizador

|Nome|Prefixo de endereço|Próximo salto|
|----|--------------|-------|
|subnet_to_vnetlocal|SUB-REDE MI|Rede virtual|
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

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral, veja [o que é Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md). .
- Saiba como [configurar uma nova rede virtual Azure](virtual-network-subnet-create-arm-template.md) ou uma [rede virtual Azure existente](vnet-existing-add-subnet.md) onde pode implementar a SQL Managed Instance.
- [Calcule o tamanho da sub-rede](vnet-subnet-determine-size.md) onde pretende implantar a SQL Managed Instance.
- Saiba como criar um caso gerido:
  - Do [portal Azure.](instance-create-quickstart.md)
  - Utilizando [o PowerShell](scripts/create-configure-managed-instance-powershell.md).
  - Utilizando [um modelo de Gestor de Recursos Azure](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Utilizando [um modelo de Gestor de Recursos Azure (utilizando o JumpBox, com SSMS incluído)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/).
