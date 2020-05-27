---
title: O que é o Azure Firewall?
description: O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 05/22/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 2f466c71673c9239f6f984f838d050af8bf52182
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816068"
---
# <a name="what-is-azure-firewall"></a>O que é o Azure Firewall?

![Certificação ICSA](media/overview/icsa-cert-firewall-small.png)

O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure. É uma firewall como um serviço com monitorização de estado com alta disponibilidade integrada e escalabilidade da cloud irrestrita.

![Descrição geral das firewalls](media/overview/firewall-threat.png)

Pode criar, impor e registar centralmente políticas de conectividade de rede e aplicações entre subscrições e redes virtuais. O Azure Firewall utiliza um endereço IP público estático para os recursos de rede virtual que permite às firewalls externas identificar o tráfego com origem na sua rede virtual.  O serviço está totalmente integrado no Azure Monitor para fins de registo e análise.

O Azure Firewall oferece as seguintes funcionalidades:

## <a name="built-in-high-availability"></a>Elevada disponibilidade incorporada

A alta disponibilidade está incorporada, por isso não são necessários equilibradores de carga adicionais e não há nada que precises de configurar.

## <a name="availability-zones"></a>Zonas de Disponibilidade

O Firewall Azure pode ser configurado durante a implementação para abranger várias Zonas de Disponibilidade para maior disponibilidade. Com As Zonas de Disponibilidade, a sua disponibilidade aumenta para 99,99% de tempo de uptime. Para mais informações, consulte o Acordo de Nível de Serviço de Firewall Azure [(SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). O SLA de 99,99% é oferecido quando duas ou mais Zonas de Disponibilidade são selecionadas.

Também pode associar o Azure Firewall a uma zona específica apenas por razões de proximidade, utilizando o padrão de serviço 99.95% SLA.

Não há custos adicionais para uma firewall implantada numa Zona de Disponibilidade. No entanto, existem custos adicionais para transferências de dados de entrada e saída associadas às Zonas de Disponibilidade. Para mais informações, consulte os detalhes dos [preços da Largura de Banda](https://azure.microsoft.com/pricing/details/bandwidth/).

As Zonas de Disponibilidade de Firewall Azure estão disponíveis em regiões que suportam zonas de disponibilidade. Para mais informações, consulte [Regiões que apoiam Zonas de Disponibilidade em Azure](../availability-zones/az-region.md)

> [!NOTE]
> As Zonas de Disponibilidade só podem ser configuradas durante a implantação. Não é possível configurar uma firewall existente para incluir Zonas de Disponibilidade.

Para mais informações sobre Zonas de Disponibilidade, consulte [Regiões e Zonas de Disponibilidade em Azure](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Escalabilidade da cloud sem restrições

O Azure Firewall pode aumentar verticalmente conforme as suas necessidades para acomodar fluxos de tráfego de rede em alteração, pelo que não precisa de orçamentar o tráfego de pico.

## <a name="application-fqdn-filtering-rules"></a>Regras de filtragem de FQDN de aplicação

Pode limitar o tráfego DE saída HTTP/S ou o tráfego Azure SQL (pré-visualização) a uma lista especificada de nomes de domínio totalmente qualificados (FQDN), incluindo cartões selvagens. Esta funcionalidade não requer a rescisão de TLS.

## <a name="network-traffic-filtering-rules"></a>Regras de filtragem de tráfego de rede

Pode criar centralmente regras de filtragem de rede de *permissão* ou *negação* por endereço IP de origem e destino, porta e protocolo. O Azure Firewall tem total monitoração de estado, para conseguir distinguir pacotes legítimos para diferentes tipos de ligações. As regras são impostas e registadas em várias subscrições e redes virtuais.

## <a name="fqdn-tags"></a>Etiquetas FQDN

[As tags FQDN](fqdn-tags.md) facilitam-lhe a vida a permitir o conhecido tráfego da rede de serviços Azure através da sua firewall. Por exemplo, digamos que deseja permitir tráfego de rede do Windows Update através da firewall. Crie uma regra de aplicação e inclua a etiqueta do Windows Update. Agora o tráfego de rede do Windows Update pode fluir através da firewall.

## <a name="service-tags"></a>Etiquetas de serviço

Uma etiqueta de [serviço](service-tags.md) representa um grupo de prefixos de endereço IP para ajudar a minimizar a complexidade para a criação de regras de segurança. Não pode criar a sua própria etiqueta de serviço, nem especificar quais os endereços IP incluídos dentro de uma etiqueta. A Microsoft gere os prefixos de endereços que as etiquetas abrangem e atualiza-as automaticamente à medida que os endereços são alterados.

## <a name="threat-intelligence"></a>Informações sobre ameaças

A filtragem baseada em [informações](threat-intel.md)de ameaça pode ser ativada para que a sua firewall alerte e negue tráfego de/para endereços e domínios IP maliciosos conhecidos. Os endereços e domínios IP são obtidos a partir do feed da Microsoft Threat Intelligence.

## <a name="outbound-snat-support"></a>Suporte SNAT de saída

Todos os endereços IP de tráfego de rede virtual de saída são convertidos no IP público do Azure Firewall (Tradução de Endereços de Rede de Origem). Pode identificar e permitir tráfego com origem na sua rede virtual para destinos de Internet remotos. O Azure Firewall não snaT quando o IP de destino é uma gama ip privada por [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Se a sua organização utilizar uma gama pública de endereços IP para redes privadas, o Azure Firewall irá snat o tráfego para um dos endereços IP privados firewall em AzureFirewallSubnet. Pode configurar o Azure Firewall para **não** sNAT a sua gama pública de endereços IP. Para mais informações, consulte as gamas de [endereços IP privados Azure Firewall SNAT](snat-private-range.md).

## <a name="inbound-dnat-support"></a>Suporte DNAT de entrada

O tráfego de rede de Internet de entrada para o seu endereço IP público firewall é traduzido (Tradução de endereço de rede de destino) e filtrado para os endereços IP privados nas suas redes virtuais.

## <a name="multiple-public-ip-addresses"></a>Múltiplos endereços IP públicos

Pode associar [vários endereços IP públicos](deploy-multi-public-ip-powershell.md) (até 250) com a sua firewall.

Isto permite os seguintes cenários:

- **DNAT** - Pode traduzir várias instâncias de porta padrão para os seus servidores de backend. Por exemplo, se tiver dois endereços IP públicos, pode traduzir a porta TCP 3389 (RDP) para ambos os endereços IP.
- **SNAT** - Portos adicionais estão disponíveis para ligações SNAT de saída, reduzindo o potencial de exaustão da porta SNAT. Neste momento, o Azure Firewall seleciona aleatoriamente o endereço IP público de origem para utilizar para uma ligação. Se tiver alguma filtragem a jusante na sua rede, tem de permitir todos os endereços IP públicos associados à sua firewall. Considere utilizar um [prefixo de endereço IP público](../virtual-network/public-ip-address-prefix.md) para simplificar esta configuração.

## <a name="azure-monitor-logging"></a>Registo do Azure Monitor

Todos os eventos estão integrados com o Monitor Azure, permitindo-lhe arquivar registos numa conta de armazenamento, transmitir eventos para o seu Hub de Eventos ou enviá-los para registos do Monitor Azure. Para mais informações, consulte [Tutorial: Monitor Azure Firewall registos e métricas](tutorial-diagnostics.md).

## <a name="forced-tunneling"></a>Túnel forçado

Pode configurar o Azure Firewall para encaminhar todo o tráfego ligado à Internet para um próximo salto designado em vez de ir diretamente para a Internet. Por exemplo, pode ter uma firewall de borda no local ou outro aparelho virtual de rede (NVA) para processar o tráfego da rede antes de ser passado para a Internet. Para mais informações, consulte [o Azure Firewall forçado a fazer túneis.](forced-tunneling.md)

## <a name="certifications"></a>Certificações

O Azure Firewall é a Indústria de Cartões de Pagamento (PCI), os Controlos da Organização de Serviços (SOC), a Organização Internacional para a Normalização (ISO) e o ICSA Labs em conformidade. Para mais informações, consulte as certificações de [conformidade da Firewall Azure.](compliance-certifications.md)


## <a name="known-issues"></a>Problemas conhecidos

O Azure Firewall tem os seguintes problemas conhecidos:

|Problema  |Descrição  |Mitigação  |
|---------|---------|---------|
As regras de filtragem de rede para protocolos não TCP/UDP (por exemplo, ICMP) não funcionam para o tráfego vinculado à Internet|As regras de filtragem da rede para protocolos não TCP/UDP não funcionam com o SNAT no seu endereço IP público. Os protocolos não TCP/UDP são suportados entre VNets e sub-redes spoke.|O Azure Firewall utiliza o Balanceador de Carga Standard [que não suporta atualmente SNAT para protocolos IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). Estamos a explorar opções para apoiar este cenário num futuro lançamento.|
|Suporte do PowerShell e CLI em falta para ICMP|A Azure PowerShell e a CLI não suportam o ICMP como um protocolo válido nas regras da rede.|Ainda é possível usar o ICMP como protocolo através do portal e da API REST. Estamos a trabalhar para adicionar iCMP em PowerShell e CLI em breve.|
|As etiquetas FQDN requerem um protocolo: porta a definir|As regras de aplicação com etiquetas FQDN requerem a definição de porta: protocolo.|Pode utilizar **https** como a porta: valor de protocolo. Estamos a trabalhar para tornar este campo opcional quando as etiquetas FQDN são usadas.|
|Mover uma firewall para um grupo de recursos diferente ou subscrição não é suportado|Mover uma firewall para um grupo de recursos diferente ou subscrição não é suportado.|O suporte a esta funcionalidade está no nosso roteiro. Para mover uma firewall para um grupo de recursos ou uma subscrição diferente, tem de eliminar a instância atual e recriá-la no novo grupo de recursos ou subscrição.|
|Alertas de inteligência de ameaça podem ser mascarados|Regras de rede com o destino 80/443 para filtrar máscaras de saída alertas de inteligência ameaça quando configurados para alertar apenas o modo.|Crie filtragem de saída para 80/443 utilizando as regras de aplicação. Ou mudar o modo de inteligência de ameaça para **Alertar e Negar.**|
|Azure Firewall usa DNS Azure apenas para resolução de nomes|A Firewall Azure resolve os FQDNs usando apenas dNS azure. Um servidor DNS personalizado não é suportado. Não há impacto na resolução do DNS noutras subredes.|Estamos a trabalhar para relaxar esta limitação.|
|DNAT azure Firewall não funciona para destinos IP privados|O suporte ao Adn da Firewall Azure está limitado à saída/entrada da Internet. O DNAT não funciona atualmente para destinos IP privados. Por exemplo, falei com a fala.|Esta é uma limitação atual.|
|Não pode remover a primeira configuração ip pública|Cada endereço IP público da Firewall Azure é atribuído a uma *configuração IP*.  A primeira configuração IP é atribuída durante a implementação da firewall, e normalmente também contém uma referência à sub-rede de firewall (a menos que configurada explicitamente de forma diferente através de uma implementação do modelo). Não é possível eliminar esta configuração IP porque iria desalocar a firewall. Ainda pode alterar ou remover o endereço IP público associado a esta configuração IP se a firewall tiver pelo menos um outro endereço IP público disponível para usar.|Esta ação é propositada.|
|As zonas de disponibilidade só podem ser configuradas durante a implantação.|As zonas de disponibilidade só podem ser configuradas durante a implantação. Não é possível configurar zonas de disponibilidade depois de uma firewall ter sido implantada.|Esta ação é propositada.|
|SNAT em ligações de entrada|Além do DNAT, as ligações através do endereço IP público firewall (entrada) são SNATed para um dos IPs privados firewall. Este requisito hoje (também para NVAs Ativos/Ativos) para garantir o encaminhamento simétrico.|Para preservar a fonte original para HTTP/S, considere usar cabeçalhos [XFF.](https://en.wikipedia.org/wiki/X-Forwarded-For) Por exemplo, utilize um serviço como [a Porta Frontal Azure](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) ou o Portal de [Aplicações Azure](../application-gateway/rewrite-http-headers.md) em frente à firewall. Também pode adicionar WAF como parte da Porta Frontal Azure e corrente à firewall.
|Suporte de filtragem SQL FQDN apenas em modo proxy (porta 1433)|Para base de dados Azure SQL, Azure SQL Data Warehouse e Azure SQL Managed Instance:<br><br>Durante a pré-visualização, a filtragem SQL FQDN é suportada apenas no modo proxy (porta 1433).<br><br>Para Azure SQL IaaS:<br><br>Se estiver a utilizar portas não standard, pode especificar essas portas nas regras de aplicação.|Para o SQL no modo de redirecionamento (o predefinido se ligar a partir do Azure), pode, em vez disso, filtrar o acesso utilizando a etiqueta de serviço SQL como parte das regras de rede Azure Firewall.
|Tráfego de saída na porta 25 da TCP não é permitido| As ligações SMTP de saída que utilizam a porta TCP 25 estão bloqueadas. A porta 25 é usada principalmente para entrega de e-mail não autenticada. Este é o comportamento padrão da plataforma para máquinas virtuais. Para mais informações, consulte mais problemas de [conectividade SMTP em Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). No entanto, ao contrário das máquinas virtuais, não é atualmente possível ativar esta funcionalidade no Azure Firewall. Nota: para permitir a autenticação de SMTP (porta 587) ou SMTP sobre uma porta que não seja 25, certifique-se de configurar uma regra de rede e não uma regra de aplicação, uma vez que a inspeção SMTP não é suportada neste momento.|Siga o método recomendado para enviar e-mail, conforme documentado no artigo de resolução de problemas da SMTP. Ou excluir a máquina virtual que necessita de acesso SMTP de saída da sua rota padrão para a firewall. Em vez disso, configure o acesso de saída diretamente à internet.
|FtP ativo não é suportado|O FTP ativo é desativado no Azure Firewall para proteger contra ataques ftp bounce usando o comando FTP PORT.|Em vez disso, pode utilizar ftp passivo. Ainda deve abrir explicitamente as portas TCP 20 e 21 na firewall.
|Métrica de utilização da porta SNAT mostra 0%|A métrica de utilização da porta Azure Firewall SNAT pode mostrar uma utilização de 0%, mesmo quando as portas SNAT são utilizadas. Neste caso, a utilização da métrica como parte da métrica de saúde da firewall proporciona um resultado incorreto.|Esta questão foi corrigida e a produção está direcionada para maio de 2020. Em alguns casos, a redistribuição da firewall resolve o problema, mas não é consistente. Como uma suposição intermédia, utilize apenas o estado de saúde da firewall para procurar *o estado=degradado,* não para *o status=insalubre*. A exaustão portuária mostrar-se-á *degradada.* *Não é saudável* é reservado para uso futuro quando são mais métricas para impactar a saúde da firewall.
|O DNAT não é suportado com túnel forçado habilitado|As firewalls implantadas com túneis forçados ativados não suportam o acesso à entrada da Internet devido ao encaminhamento assimétrico.|Isto é por design por causa do encaminhamento assimétrico. O caminho de retorno para ligações de entrada passa pela firewall no local, que não viu a ligação estabelecida.
|FTP passivo de saída não funciona para Firewalls com vários endereços IP públicos|O FTP passivo estabelece diferentes ligações para os canais de controlo e dados. Quando um Firewall com vários endereços IP públicos envia dados de saída, ele seleciona aleatoriamente um dos seus endereços IP públicos para o endereço IP fonte. O FTP falha quando os canais de dados e de controlo utilizam diferentes endereços IP de origem.|Está prevista uma configuração sNAT explícita. Entretanto, considere utilizar um único endereço IP nesta situação.|
|A métrica NetworkRuleHit está a perder uma dimensão de protocolo|A métrica ApplicationRuleHit permite filtrar o protocolo baseado, mas esta capacidade está em falta na métrica correspondente do NetworkRuleHit.|Uma correção está a ser investigada.|
|As regras do NAT com portos entre 64000 e 65535 não são apoiadas|O Azure Firewall permite qualquer porta na gama 1-65535 nas regras de rede e aplicação, no entanto as regras na NAT apenas suportam portas na gama 1-63999.|Esta é uma limitação atual.
|As atualizações de configuração podem demorar cinco minutos, em média,|Uma atualização de configuração de Firewall Azure pode demorar entre 3 a 5 minutos, em média, e as atualizações paralelas não são suportadas.|Uma correção está a ser investigada.|
|Azure Firewall usa cabeçalhos SNI TLS para filtrar tráfego HTTPS e MSSQL|Se o software do navegador ou servidor não suportar a extensão do Indicador de Nome do Servidor (SNI), não poderá ligar através do Firewall Do Azure.|Se o software do navegador ou servidor não suportar o SNI, poderá controlar a ligação utilizando uma regra de rede em vez de uma regra de aplicação. Consulte a [indicação do nome](https://wikipedia.org/wiki/Server_Name_Indication) do servidor para software que suporta o SNI.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Implementar e configurar o Azure Firewall com o portal do Azure](tutorial-firewall-deploy-portal.md)
- [Implementar o Azure Firewall através de um modelo](deploy-template.md)
- [Criar um ambiente de teste do Azure Firewall](scripts/sample-create-firewall-test.md)
