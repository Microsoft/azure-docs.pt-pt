---
title: Arquitetura de conectividade de instância de gerida de base de dados SQL do Azure | Documentos da Microsoft
description: Este artigo fornece a base de dados do SQL do Azure gerido descrição geral da comunicação de instância e explica a arquitetura de conectividade, bem como a forma como os diferentes componentes de função para direcionar o tráfego para a instância gerida.
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
ms.openlocfilehash: 64e0444c85440a017872aa32017e7d1c47e44e89
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889783"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Arquitetura de conectividade de instância de base de dados SQL geridas do Azure

Este artigo fornece a base de dados do SQL do Azure gerido descrição geral da comunicação de instância e explica a arquitetura de conectividade, bem como a forma como os diferentes componentes de função para direcionar o tráfego para a instância gerida.  

A instância gerida da base de dados do Azure SQL é colocada dentro da VNet do Azure e a sub-rede dedicada a instâncias geridas. Esta implementação permite que os seguintes cenários:

- Proteger o endereço IP privado.
- Ligar a uma instância gerida diretamente a partir de uma rede no local.
- Ligar uma instância gerida para o servidor ligado ou de outra no local o arquivo de dados.
- A ligar uma instância gerida nos recursos do Azure.

## <a name="communication-overview"></a>Descrição geral da comunicação

O diagrama seguinte mostra a entidades que se ligam a instância gerida, bem como os recursos que a instância gerida tem de entrar em contacto para funcionar corretamente.

![entidades de arquitetura de conectividade](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Comunicação que é descrita na parte inferior do diagrama representa os aplicativos de cliente e ferramentas de ligar à instância gerida como origem de dados.  

Como a instância gerida é uma plataforma-como-um-serviços (PaaS de oferta), a Microsoft gere este serviço usando agentes automatizados (gestão, implementação e manutenção) com base nos fluxos de dados de telemetria. Como gerido gerenciamento de instância é unicamente responsabilidade da Microsoft, os clientes não são capazes de acessar as máquinas de virtuais de cluster de instância gerida através de RDP.

Algumas operações iniciadas pelas aplicações ou os utilizadores finais podem exigir do SQL Server gerido instâncias para interagir com a plataforma. Um caso é a criação de uma instância gerida da base de dados - um recurso que é exposto por meio do portal do Azure, PowerShell, CLI do Azure e a API REST.

Instância gerida depende de outros serviços do Azure para o seu funcionamento adequado (como o armazenamento do Azure para cópias de segurança, Service bus do Azure para a telemetria, Azure AD para autenticação, o Azure Key Vault para TDE e assim por diante) e inicia ligações aos mesmos em conformidade.

Todas as comunicações, indicadas acima, são criptografadas e assinados com certificados. A certificar-se de que as partes da comunicação são fidedignas, a instância gerida constantemente verifica se estes certificados ao contactar a autoridade de certificação. Se os certificados são revogados ou instância gerida não foi possível verificá-las, ele fecha as ligações para proteger os dados.

## <a name="high-level-connectivity-architecture"></a>Arquitetura de alto nível de conectividade

Num alto nível, uma instância gerida é um conjunto de componentes do serviço, hospedado num conjunto dedicado de isolado máquinas virtuais que são executados dentro da sub-rede de rede virtual de cliente e formar um cluster virtual.

Várias instâncias geridas podem ser alojadas no cluster virtual único. O cluster é automaticamente expandido ou contratado se for necessário quando o cliente é alterado o número de instâncias aprovisionados na sub-rede.

Aplicações de cliente podem ligar a instâncias geridas, consulta e atualização de bases de dados apenas se eles são executados dentro da rede virtual ou em modo de peering rede virtual ou VPN / Express Route ligado através do ponto final com o endereço IP privado de rede.  

![Diagrama de arquitetura de conectividade](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Serviços de gerenciamento e a implantação da Microsoft executam fora da rede virtual, para ligações entre uma instância gerida e serviços da Microsoft são enviados pelos pontos de extremidade com endereços IP públicos. Quando a instância gerida cria a ligação de saída, na receção final parece que ele está vindo este IP público devido a tradução de endereços de rede (NAT).

Fluxos de tráfego de gestão através da rede virtual do cliente. Isso significa que afetam a elementos da infraestrutura de rede virtual e pode potencialmente o tráfego de gestão de danos a causar a instância entrar em estado com falhas e fique indisponível.

> [!IMPORTANT]
> Para melhorar a experiência do cliente e a disponibilidade do serviço, a Microsoft aplica políticas de intenção de rede em elementos de infraestrutura de rede virtual do Azure que podem afetar o funcionamento da instância gerida. Esse é um mecanismo de plataforma para comunicar os requisitos de rede de forma transparente para os utilizadores finais, com o objetivo principal para impedir a configuração incorreta de rede e assegurar as operações normais de instância gerida. Após a eliminação da instância gerida política de intenção de rede são também removida.

## <a name="virtual-cluster-connectivity-architecture"></a>Arquitetura de conectividade do virtual cluster

Vamos aprofundar na arquitetura de conectividade de instância gerida. O diagrama seguinte mostra o esquema conceitual do virtual cluster.

![cluster virtual do diagrama de arquitetura de conectividade](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Os clientes ligam a instância gerida com o nome de anfitrião que tenha um formulário `<mi_name>.<dns_zone>.database.windows.net`. Este nome de anfitrião é resolvido para o endereço IP privado, embora ele está registado na zona DNS pública e é resolvível publicamente. O `zone-id` é gerado automaticamente quando o cluster é criado. Se um cluster recém-criado está a alojar uma instância secundária, compartilha sua identificação de zona com o cluster principal. Para obter mais informações, consulte [grupos de ativação pós-falha automática](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets)

Pertence este endereço IP privado para o Balanceador de carga interno (ILB) de instância gerida que direciona o tráfego para o gateway de instância gerida (GW). Como várias instâncias geridas poderiam potencialmente executado dentro do mesmo cluster, o GW utiliza o nome de anfitrião da instância gerida para redirecionar o tráfego para o serviço de motor de SQL correto.

Serviços de gerenciamento e implantação de ligar a uma instância gerida utilizando um [ponto final de gestão](#management-endpoint) que é mapeado para um externo Balanceador de carga. O tráfego é encaminhado para os nós apenas se tiver recebido num conjunto predefinido de portas que são utilizados exclusivamente pelos componentes de gerenciamento de instância gerida. Firewall interno em nós está configurado para permitir tráfego apenas a partir de intervalos de IP específicos de Microsoft. Todas as comunicações entre os componentes de gestão e plano de gestão é mutuamente autenticado de certificado.

## <a name="management-endpoint"></a>Ponto final de gestão

O cluster virtual de instância gerida contém um ponto final de gestão que a Microsoft utiliza para gerir a instância gerida. O ponto final de gestão está protegido com firewall interno na verificação de certificado mútua e nível de rede no nível de aplicativo. Pode [encontrar o endereço de ip do ponto final de gestão](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Quando as ligações sejam iniciadas a partir de dentro da instância gerida (cópia de segurança, registo de auditoria), parece que origina o tráfego do endereço IP de público de ponto final de gestão. Pode limitar o acesso à serviços públicos de uma instância gerida através da definição de regras de firewall para permitir apenas o endereço IP de instância gerida. Encontrar mais informações sobre o método que pode [Verifique se o firewall interno da instância gerida](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Isso não se aplica à configuração de regras de firewall para serviços do Azure que estão na mesma região que a instância gerida, como a plataforma do Azure tem uma otimização para o tráfego que passa entre os serviços que sejam colocados.

## <a name="network-requirements"></a>Requisitos da rede

Implementar uma instância gerida numa sub-rede dedicada (a sub-rede de instância gerida) dentro da rede virtual que está em conformidade com os seguintes requisitos:

- **Dedicado sub-rede**: A sub-rede de instância gerida não pode conter qualquer outro serviço de cloud associado à mesma, e não tem de ser uma sub-rede de Gateway. Não será possível criar uma instância gerida numa sub-rede que contém os recursos que não seja a instância gerida e, posteriormente, pode não adicionar outros recursos na sub-rede.
- **O grupo de segurança de rede (NSG)**: Um NSG associado com a rede virtual tem de conter estes obrigatório definido [regras de segurança de entrada](#mandatory-inbound-security-rules) e [regras de segurança de saída](#mandatory-outbound-security-rules) à frente de quaisquer outras regras. Pode utilizar um NSG para controlar totalmente o acesso para o ponto final de dados de instância gerida ao filtrar o tráfego na porta 1433.
- **Tabela de rotas definidas pelo utilizador (UDR)**: Uma tabela de rotas definidas pelo utilizador associada à rede virtual tem de ter estes [entradas](#user-defined-routes) numa tabela de rota definida pelo utilizador.
- **Não existem pontos finais de serviço**: A sub-rede de instância gerida não pode ter um ponto de extremidade de serviço associado a si. Certifique-se de que a opção de pontos finais de serviço é desativada quando criar a rede virtual.
- **Endereços IP suficientes**: A sub-rede de instância gerida tem de ter o mínimo de 16 endereços IP (recomendado mínimo é de 32 endereços IP). Para obter mais informações, consulte [determinar o tamanho da sub-rede para instâncias geridas](sql-database-managed-instance-determine-size-vnet-subnet.md). Pode implementar instâncias geridas na [da rede existente](sql-database-managed-instance-configure-vnet-subnet.md) depois de configurá-lo para satisfazer [geridos os requisitos de rede de instância](#network-requirements), ou criar um [nova rede e sub-rede](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Não será capaz de implantar uma nova instância gerida, se a sub-rede de destino não é compatível com todos esses requisitos. Quando é criada uma instância gerida, uma *política de intenção de rede* é aplicado na sub-rede para impedir alterações não conformes para configuração de rede. Após a última instância é removida a partir da sub-rede, o *política de intenção de rede* é removido também

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

\* Sub-rede de MI refere-se para o intervalo de endereços IP para a sub-rede na 10.x.x.x/y formulário. Estas informações podem ser encontradas no portal do Azure (por meio de propriedades de sub-rede).

> [!IMPORTANT]
> Embora as regras de segurança de entrada obrigatório permitem tráfego a partir _qualquer_ origem nas portas 9000, 9003, 1438, 1440, 1452 estas portas são protegidas pelo firewall interno. Isso [artigo](sql-database-managed-instance-find-management-endpoint-ip-address.md) mostra como pode descobrir o endereço IP do ponto final de gestão e verifique se as regras de firewall.
> [!NOTE]
> Se estiver a utilizar replicação transacional numa instância gerida e qualquer base de dados de instância é utilizado como um publicador ou distribuidor, porta 445 (saída de TCP) também tem de estar abertas nas regras de segurança da sub-rede para aceder à partilha de ficheiros do Azure.

### <a name="user-defined-routes"></a>Rotas definidas pelo utilizador

|Name|Prefixo de endereço|Salto NET|
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

- **DNS personalizado opcional**: Se não for especificado um DNS personalizado na rede virtual, endereço IP de resolução de recursiva do Azure (por exemplo, 168.63.129.16) tem de ser adicionado à lista. Para obter mais informações, consulte [configurar o DNS de personalizado](sql-database-managed-instance-custom-dns.md). O servidor DNS personalizado tem de ser capaz de resolver nomes de anfitriões para os seguintes domínios e os respetivos subdomínios: *microsoft.com*, *windows.net*, *windows.com*, *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com*, e *microsoftonline-p.com*.

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral, consulte [o que é uma instância gerida](sql-database-managed-instance.md)
- Saiba como [configurar a nova VNet](sql-database-managed-instance-create-vnet-subnet.md) ou [configurar VNet existente](sql-database-managed-instance-configure-vnet-subnet.md) onde pode implementar instâncias geridas.
- [Calcular o tamanho da sub-rede](sql-database-managed-instance-determine-size-vnet-subnet.md) onde irá implementar as instâncias geridas.
- Para guias de introdução, consulte como criar a instância gerida:
  - Do [portal do Azure](sql-database-managed-instance-get-started.md)
  - usando [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)
  - usando [modelo Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)
  - usando [modelo Azure Resource Manager (jumpbox com o SSMS incluídos)](https://portal.azure.com/)
