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
ms.date: 10/22/2020
ms.openlocfilehash: c91b0231271c6cbcf9ec92c7ad6d25f1bc0f9136
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960474"
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
- **Endereços IP suficientes:** A sub-rede SQL Managed Instance deve ter pelo menos 32 endereços IP. Para obter mais informações, consulte [determine o tamanho da sub-rede para sql Managed Instance](vnet-subnet-determine-size.md). Pode implementar casos geridos [na rede existente](vnet-existing-add-subnet.md) depois de a configurar para satisfazer os [requisitos de networking para a SQL Managed Instance](#network-requirements). Caso contrário, crie uma [nova rede e sub-rede](virtual-network-subnet-create-arm-template.md).

> [!IMPORTANT]
> Quando cria um caso gerido, é aplicada uma política de intenções de rede na sub-rede para evitar alterações não conformes na configuração da rede. Após a remoção da última instância da sub-rede, a política de intenções de rede também é removida. As regras abaixo são apenas para fins informativos, e não deve implantá-las usando o modelo ARM / PowerShell /CLI. Se quiser utilizar o modelo oficial mais recente, poderá sempre [recuperá-lo do portal](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Regras de segurança obrigatórias de entrada com configuração de sub-rede ajudada pelo serviço

| Name       |Porta                        |Protocolo|Origem           |Destino|Ação|
|------------|----------------------------|--------|-----------------|-----------|------|
|gestão  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |SUB-REDE MI  |Permitir |
|            |9000, 9003                  |TCP     |Serra Corpnet       |SUB-REDE MI  |Permitir |
|            |9000, 9003                  |TCP     |CorpnetPublic    |SUB-REDE MI  |Permitir |
|mi_subnet   |Qualquer                         |Qualquer     |SUB-REDE MI        |SUB-REDE MI  |Permitir |
|health_probe|Qualquer                         |Qualquer     |AzureLoadBalancer|SUB-REDE MI  |Permitir |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Regras de segurança obrigatórias de saída com configuração de sub-rede ajudada pelo serviço

| Name       |Porta          |Protocolo|Origem           |Destino|Ação|
|------------|--------------|--------|-----------------|-----------|------|
|gestão  |443, 12000    |TCP     |SUB-REDE MI        |AzureCloud |Permitir |
|mi_subnet   |Qualquer           |Qualquer     |SUB-REDE MI        |SUB-REDE MI  |Permitir |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Rotas definidas pelo utilizador com configuração de sub-rede ajudada pelo serviço

|Name|Prefixo de endereço|Próximo salto|
|----|--------------|-------|
|sub-rede-para-vnetlocal|SUB-REDE MI|Rede virtual|
|mi-azurecloud-REGION-internet|AzureCloud.REGION|Internet|
|mi-azurecloud-REGION_PAIR-internet|AzureCloud.REGION_PAIR|Internet|
|mi-azuremonitor-internet|AzureMonitor|Internet|
|mi-corpnetpublic-internet|CorpNetPublic|Internet|
|mi-corpnetsaw-internet|Serra CorpNet|Internet|
|mi-eventhub-REGION-internet|EventHub.REGION|Internet|
|mi-eventhub-REGION_PAIR-internet|EventHub.REGION_PAIR|Internet|
|mi-sqlmanagement-internet|SqlManagement|Internet|
|mi-armazenamento-internet|Armazenamento|Internet|
|mi-armazenamento-REGIÃO-internet|Armazenamento.REGIÃO|Internet|
|mi-storage-REGION_PAIR-internet|Storage.REGION_PAIR|Internet|
||||

\* MI SUBNET refere-se ao intervalo de endereço IP para a sub-rede no formulário x.x.x.x/y. Pode encontrar esta informação no portal Azure, em propriedades da sub-rede.

\** Se o endereço de destino for para um dos serviços da Azure, a Azure encaminha o tráfego diretamente para o serviço através da rede de espinha dorsal da Azure, em vez de encaminhar o tráfego para a Internet. O tráfego entre os serviços do Azure não atravessa a Internet, independentemente da região do Azure na qual a rede virtual existe ou da região em que uma instância do serviço Azure está implementada. Para mais detalhes consulte a [página de documentação da UDR](../../virtual-network/virtual-networks-udr-overview.md).

Além disso, pode adicionar entradas à tabela de rotas para o tráfego de rotas que tem no local gamas IP privadas como destino através do gateway de rede virtual ou do aparelho de rede virtual (NVA).

Se a rede virtual incluir um DNS personalizado, o servidor DNS personalizado deve ser capaz de resolver registos DNS públicos. A utilização de funcionalidades adicionais como a autenticação AD AZure pode requerer a resolução de FQDNs adicionais. Para obter mais informações, consulte [Configurar um DNS personalizado.](custom-dns-configure.md)

### <a name="networking-constraints"></a>Restrições de rede

**O TLS 1.2 é aplicado nas ligações de saída**: Em janeiro de 2020, a Microsoft impôs TLS 1.2 para tráfego intra-serviço em todos os serviços Azure. Para a Azure SQL Managed Instance, isto resultou na aplicação do TLS 1.2 nas ligações de saída utilizadas para replicação e ligações de servidores ligados ao SQL Server. Se estiver a utilizar versões do SQL Server com mais de 2016 com a SQL Managed Instance, certifique-se de que foram [aplicadas atualizações específicas TLS 1.2.](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)

As seguintes funcionalidades de rede virtual não são atualmente *suportadas* com a SQL Managed Instance:

- **Microsoft peering**: Habilitar [a Microsoft a espreitar](../../expressroute/expressroute-faqs.md#microsoft-peering) os circuitos ExpressRoute espreitei-o direta ou transitivamente com uma rede virtual onde o SQL Managed Instance reside afeta o fluxo de tráfego entre os componentes da SQL Managed Instance dentro da rede virtual e os serviços de que depende, causando problemas de disponibilidade. Espera-se que as implementações de SqL Managed Instance para rede virtual com o microsoft já ativado falhem.
- **Observação global da rede virtual**: A conectividade de rede virtual que espreita [as](../../virtual-network/virtual-network-peering-overview.md) regiões de Azure não funciona para as Instâncias Geridas SQL colocadas em sub-redes criadas antes de 22/9/2020.
- **AzurePlatformDNS**: A utilização da [etiqueta de serviço](../../virtual-network/service-tags-overview.md) AzurePlatformDNS para bloquear a resolução de DNS da plataforma tornaria a SQL Managed Instance indisponível. Embora a SQL Managed Instance suporte DNS definido pelo cliente para resolução de DNS dentro do motor, existe uma dependência da plataforma DNS para operações da plataforma.
- **Gateway NAT**: A utilização [da Rede Virtual Azure NAT](../../virtual-network/nat-overview.md) para controlar a conectividade de saída com um endereço IP público específico tornaria a SQL Managed Instance indisponível. O serviço SQL Managed Instance está atualmente limitado ao uso de balanceador de carga básico que não proporciona coexistência de fluxos de entrada e saída com a Rede Virtual NAT.
- **IPv6 para Azure Virtual Network**: Implantar a SQL Managed Instance para [dupla pilha redes virtuais IPv4/IPv6](../../virtual-network/ipv6-overview.md) deverá falhar. Associar o grupo de segurança da rede (NSG) ou a tabela de rotas (UDR) que contenha prefixos de endereço IPv6 na sub-rede SQL Managed Instance, ou adicionar prefixos de endereço IPv6 a NSG ou UDR que já esteja associado à sub-rede de instância gerida, tornaria indisponíveis as instâncias geridas sql. As implementações de Exemplos Geridos SQL para uma sub-rede com NSG e UDR que já têm prefixos IPv6 devem falhar.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral, veja [o que é Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md). .
- Saiba como [configurar uma nova rede virtual Azure](virtual-network-subnet-create-arm-template.md) ou uma [rede virtual Azure existente](vnet-existing-add-subnet.md) onde pode implementar a SQL Managed Instance.
- [Calcule o tamanho da sub-rede](vnet-subnet-determine-size.md) onde pretende implantar a SQL Managed Instance.
- Saiba como criar um caso gerido:
  - Do [portal Azure.](instance-create-quickstart.md)
  - Utilizando [o PowerShell](scripts/create-configure-managed-instance-powershell.md).
  - Utilizando [um modelo de Gestor de Recursos Azure](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Utilizando [um modelo de Gestor de Recursos Azure (utilizando o JumpBox, com SSMS incluído)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/).
