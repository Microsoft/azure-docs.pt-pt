---
title: O que é o Azure Firewall?
description: Saiba mais sobre as funcionalidades do Azure Firewall.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 07/19/2019
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 1349e07662504564fdf48a53f24525c4a16aa477
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326909"
---
# <a name="what-is-azure-firewall"></a>O que é o Azure Firewall?

O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure. Trata-se de um firewall totalmente com estado como um serviço com alta disponibilidade interna e escalabilidade de nuvem irrestrita.

![Descrição geral das firewalls](media/overview/firewall-threat.png)

Pode criar, impor e registar centralmente políticas de conectividade de rede e aplicações entre subscrições e redes virtuais. O Azure Firewall utiliza um endereço IP público estático para os recursos de rede virtual que permite às firewalls externas identificar o tráfego com origem na sua rede virtual.  O serviço está totalmente integrado no Azure Monitor para fins de registo e análise.

O Azure Firewall oferece as seguintes funcionalidades:

## <a name="built-in-high-availability"></a>Elevada disponibilidade incorporada

A alta disponibilidade é interna, portanto, nenhum balanceador de carga adicional é necessário e não há nada que você precise configurar.

## <a name="availability-zones"></a>Zonas de Disponibilidade

O Firewall do Azure pode ser configurado durante a implantação para abranger vários Zonas de Disponibilidade para aumentar a disponibilidade. Com o Zonas de Disponibilidade, sua disponibilidade aumenta para 99,99% de tempo de atividade. Para obter mais informações, consulte o Contrato de Nível de Serviço de firewall do Azure [(SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). O SLA de tempo de atividade de 99,99% é oferecido quando duas ou mais Zonas de Disponibilidade são selecionadas.

Você também pode associar o Firewall do Azure a uma zona específica apenas por motivos de proximidade, usando o SLA do serviço Standard 99,95%.

Não há nenhum custo adicional para um firewall implantado em uma zona de disponibilidade. No entanto, há custos adicionais para transferências de dados de entrada e saída associadas a Zonas de Disponibilidade. Para obter mais informações, consulte [detalhes de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

Os Zonas de Disponibilidade de firewall do Azure estão disponíveis em regiões que dão suporte a Zonas de Disponibilidade. Para obter mais informações, consulte [o que são zonas de disponibilidade no Azure?](../availability-zones/az-overview.md#services-support-by-region)

> [!NOTE]
> Zonas de Disponibilidade só pode ser configurado durante a implantação. Você não pode configurar um firewall existente para incluir Zonas de Disponibilidade.

Para obter mais informações sobre Zonas de Disponibilidade, consulte [o que são zonas de disponibilidade no Azure?](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Escalabilidade da cloud sem restrições

O Azure Firewall pode aumentar verticalmente conforme as suas necessidades para acomodar fluxos de tráfego de rede em alteração, pelo que não precisa de orçamentar o tráfego de pico.

## <a name="application-fqdn-filtering-rules"></a>Regras de filtragem de FQDN de aplicação

Você pode limitar o tráfego de HTTP/S de saída ou o tráfego SQL do Azure (versão prévia) a uma lista especificada de FQDN (nomes de domínio totalmente qualificados), incluindo curingas. Esse recurso não requer terminação SSL.

## <a name="network-traffic-filtering-rules"></a>Regras de filtragem de tráfego de rede

Pode criar centralmente regras de filtragem de rede de *permissão* ou *negação* por endereço IP de origem e destino, porta e protocolo. O Azure Firewall tem total monitoração de estado, para conseguir distinguir pacotes legítimos para diferentes tipos de ligações. As regras são impostas e registadas em várias subscrições e redes virtuais.

## <a name="fqdn-tags"></a>Etiquetas FQDN

As etiquetas FQDN facilitam a permissão do tráfego de rede bem conhecido do serviço do Azure através da firewall. Por exemplo, digamos que deseja permitir tráfego de rede do Windows Update através da firewall. Crie uma regra de aplicação e inclua a etiqueta do Windows Update. Agora o tráfego de rede do Windows Update pode fluir através da firewall.

## <a name="service-tags"></a>Etiquetas de serviço

As etiquetas de serviço representam um grupo de prefixos de endereços IP, que ajudam a minimizar a complexidade da criação de regras de segurança. Você não pode criar sua própria marca de serviço nem especificar quais endereços IP estão incluídos em uma marca. A Microsoft gere os prefixos de endereços que as etiquetas abrangem e atualiza-as automaticamente à medida que os endereços são alterados.

## <a name="threat-intelligence"></a>Informações sobre ameaças

A filtragem com base em informações sobre ameaças pode ser ativada para que a sua firewall alerte e negue tráfego de/para endereços e domínios de IP maliciosos. Os endereços e domínios de IP são obtidos no feed de Informações sobre Ameaças da Microsoft.

## <a name="outbound-snat-support"></a>Suporte SNAT de saída

Todos os endereços IP de tráfego de rede virtual de saída são convertidos no IP público do Azure Firewall (Tradução de Endereços de Rede de Origem). Pode identificar e permitir tráfego com origem na sua rede virtual para destinos de Internet remotos. O Firewall do Azure não SNAT quando o IP de destino é um intervalo de IP privado por [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Se sua organização usa um intervalo de endereços IP públicos para redes privadas, o Firewall do Azure irá SNAT o tráfego para um dos endereços IP privados do firewall em AzureFirewallSubnet.

## <a name="inbound-dnat-support"></a>Suporte DNAT de entrada

O tráfego de rede de entrada para o seu endereço IP público do firewall é traduzido (Tradução de Endereços de Rede de Destino) e filtrado para os endereços IP privados nas suas redes virtuais.

## <a name="multiple-public-ip-addresses"></a>Vários endereços IP públicos

> [!IMPORTANT]
> O Firewall do Azure com vários endereços IP públicos está disponível por meio do portal do Azure, Azure PowerShell, CLI do Azure, REST e modelos.


Você pode associar vários endereços IP públicos (até 100) ao firewall.

Isso habilita os seguintes cenários:

- **DNAT** – você pode converter várias instâncias de porta padrão em seus servidores de back-end. Por exemplo, se você tiver dois endereços IP públicos, poderá converter a porta TCP 3389 (RDP) para ambos os endereços IP.
- **SNAT** -portas adicionais estão disponíveis para conexões SNAT de saída, reduzindo o potencial para esgotamento de porta SNAT. Neste momento, o Firewall do Azure seleciona aleatoriamente o endereço IP público de origem a ser usado para uma conexão. Se você tiver alguma filtragem downstream em sua rede, será necessário permitir todos os endereços IP públicos associados ao firewall.

## <a name="azure-monitor-logging"></a>Registo do Azure Monitor

Todos os eventos são integrados com o Azure Monitor, permitindo que você arquive logs em uma conta de armazenamento, transmita eventos para o Hub de eventos ou envie-os para Azure Monitor logs.

## <a name="known-issues"></a>Problemas conhecidos

O Azure Firewall tem os seguintes problemas conhecidos:

|Problema  |Descrição  |Mitigação  |
|---------|---------|---------|
As regras de filtragem de rede para protocolos não TCP/UDP (por exemplo, ICMP) não funcionam para o tráfego vinculado à Internet|As regras de filtragem de rede para protocolos não TCP/UDP não funcionam com SNAT para o seu endereço IP público. Os protocolos não TCP/UDP são suportados entre VNets e sub-redes spoke.|O Azure Firewall utiliza o Balanceador de Carga Standard [que não suporta atualmente SNAT para protocolos IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Estamos explorando opções para dar suporte a esse cenário em uma versão futura.|
|Suporte do PowerShell e CLI em falta para ICMP|O Azure PowerShell e a CLI não suportam o ICMP como um protocolo válido nas regras de rede.|Ainda é possível usar o ICMP como um protocolo por meio do portal e da API REST. Estamos trabalhando para adicionar o ICMP no PowerShell e na CLI em breve.|
|As etiquetas FQDN requerem um protocolo: porta a definir|As regras de aplicativo com marcas de FQDN exigem a definição de protocolo Port:.|Pode utilizar **https** como a porta: valor de protocolo. Estamos trabalhando para tornar esse campo opcional quando marcas de FQDN são usadas.|
|Não há suporte para a movimentação de um firewall para um grupo de recursos ou uma assinatura diferente|Não há suporte para a movimentação de um firewall para um grupo de recursos ou uma assinatura diferente.|O suporte a essa funcionalidade está em nosso mapa de estrada. Para mover uma firewall para um grupo de recursos ou uma subscrição diferente, tem de eliminar a instância atual e recriá-la no novo grupo de recursos ou subscrição.|
|Intervalo de portas em regras de aplicativos e de rede|As portas são limitadas a 64.000, pois as portas altas são reservadas para investigações de gerenciamento e integridade. |Estamos trabalhando para relaxar essa limitação.|
|Os alertas de inteligência contra ameaças podem ser mascarados|As regras de rede com o destino 80/443 para filtragem de saída mascaram alertas de inteligência contra ameaças quando configuradas para o modo somente alerta.|Criar filtragem de saída para 80/443 usando regras de aplicativo. Ou então, altere o modo de inteligência contra ameaças para **alertar e negar**.|
|O Firewall do Azure usa o DNS do Azure somente para resolução de nomes|O Firewall do Azure resolve FQDNs usando apenas o DNS do Azure. Não há suporte para um servidor DNS personalizado. Não há nenhum impacto na resolução de DNS em outras sub-redes.|Estamos trabalhando para relaxar essa limitação.|
|O Firewall do Azure SNAT/DNAT não funciona para destinos de IP privado|O suporte ao firewall do Azure SNAT/DNAT é limitado à saída/entrada na Internet. O SNAT/DNAT não funciona atualmente para destinos IP privados. Por exemplo, spoke para spoke.|Essa é uma limitação atual.|
|Não é possível remover a primeira configuração de IP público|Cada endereço IP público do firewall do Azure é atribuído a uma *configuração de IP*.  A primeira configuração de IP é atribuída durante a implantação do firewall e normalmente também contém uma referência à sub-rede do firewall (a menos que seja configurada explicitamente de forma diferente por meio de uma implantação de modelo). Não é possível excluir essa configuração de IP porque ela desalocaria o firewall. Você ainda poderá alterar ou remover o endereço IP público associado a essa configuração de IP se o firewall tiver pelo menos um outro endereço IP público disponível para uso.|Esta ação é propositada.|
|As zonas de disponibilidade só podem ser configuradas durante a implantação.|As zonas de disponibilidade só podem ser configuradas durante a implantação. Não é possível configurar Zonas de Disponibilidade após a implantação de um firewall.|Esta ação é propositada.|
|SNAT em conexões de entrada|Além de DNAT, as conexões por meio do endereço IP público do firewall (entrada) são no modo SNAT para um dos IPs privados do firewall. Esse requisito hoje (também para NVAs ativo/ativo) para garantir o roteamento simétrico.|Para preservar a fonte original para HTTP/S, considere o uso de cabeçalhos [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For) . Por exemplo, use um serviço como a [porta frontal do Azure](../frontdoor/front-door-http-headers-protocol.md#front-door-service-to-backend) na frente do firewall. Você também pode adicionar WAF como parte da porta frontal do Azure e cadeia ao firewall.
|Suporte à filtragem de FQDN do SQL somente no modo proxy (porta 1433)|Para o banco de dados SQL do Azure, o Azure SQL Data Warehouse e o Azure SQL Instância Gerenciada:<br><br>Durante a visualização, a filtragem de FQDN do SQL tem suporte somente no modo proxy (porta 1433).<br><br>Para IaaS do Azure SQL:<br><br>Se você estiver usando portas não padrão, poderá especificar essas portas nas regras de aplicativo.|Para SQL no modo de redirecionamento, que é o padrão se estiver se conectando de dentro do Azure, você pode filtrar o acesso usando a marca de serviço do SQL como parte das regras de rede do firewall do Azure.

## <a name="next-steps"></a>Passos Seguintes

- [Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure](tutorial-firewall-deploy-portal.md)
- [Implementar o Azure Firewall através de um modelo](deploy-template.md)
- [Criar um ambiente de teste do Azure Firewall](scripts/sample-create-firewall-test.md)
