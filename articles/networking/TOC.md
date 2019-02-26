# Descrição geral
## [Acerca das redes do Azure](networking-overview.md)
## Arquitetura
### [Datacenters Virtuais](/azure/architecture/vdc/networking-virtual-datacenter)
### [Encaminhamento assimétrico com vários caminhos de rede](../expressroute/expressroute-asymmetric-routing.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Designs de redes seguros](../best-practices-network-security.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Topologia hub-spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)
### [Melhores práticas de segurança de rede](../security/azure-security-network-security-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Aplicações virtuais de rede de elevada disponibilidade](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha )
### [Combinar métodos de balanceamento de carga](../traffic-manager/traffic-manager-load-balancing-azure.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Recuperação após desastre com o DNS do Azure e o Gestor de Tráfego](disaster-recovery-dns-traffic-manager.md)
## Planear e conceber
### [Redes virtuais](../virtual-network/virtual-network-vnet-plan-design-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Conectividade entre vários locais - VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Conectividade entre vários locais - privado dedicado](../expressroute/expressroute-workflows.md?toc=%2fazure%2fnetworking%2ftoc.json)
### Interoperabilidade de conectividade de back-end
#### [Prefácio e Configuração de Teste](connectivty-interoperability-preface.md?toc=%2fazure%2fnetworking%2ftoc.json)
#### [Configuração de Teste](connectivty-interoperability-configuration.md?toc=%2fazure%2fnetworking%2ftoc.json)
#### [Análise do Plano de Controlo](connectivty-interoperability-control-plane.md?toc=%2fazure%2fnetworking%2ftoc.json)
#### [Análise do Plano de Dados](connectivty-interoperability-data-plane.md?toc=%2fazure%2fnetworking%2ftoc.json)

##  Conceitos
### [Redes virtuais](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Balanceamento de carga de rede](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Balanceamento de carga de aplicação](../application-gateway/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [DNS](../dns/dns-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Distribuição de tráfego baseado em DNS](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Ligar no local - VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Ligar no local - dedicada](../expressroute/expressroute-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)


# Introdução
## [Crie a sua primeira rede virtual](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)

# Procedimento
## Conectividade Internet
### [Servidores públicos de balanceamento de carga de rede](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Servidores públicos de balanceamento de carga de aplicação](../application-gateway/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Proteger aplicações Web](../application-gateway/application-gateway-web-application-firewall-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Distribuir o tráfego por várias localizações](../traffic-manager/traffic-manager-configure-geographic-routing-method.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Conectividade interna
### [Servidores privados de balanceamento de carga de rede](../load-balancer/load-balancer-get-started-ilb-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Servidores privados de balanceamento de carga de aplicação](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Ligar redes virtuais (mesma localização)](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Ligar redes virtuais (localizações diferentes)](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Conectividade entre vários locais
### [Criar uma ligação S2S VPN (IPsec/IKE)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Criar uma ligação de VPN P2S (SSTP com certificados)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Criar uma ligação privada dedicada (ExpressRoute)](../expressroute/expressroute-howto-circuit-portal-resource-manager.md?toc=%2fazure%2fnetworking%2ftoc.json)

## Gestão
### [Descrição geral da monitorização de rede](network-monitoring-overview.md)
### [Verificar a utilização de recursos em relação aos limites do Azure](check-usage-against-limits.md)
### [Ver a topologia da rede](../network-watcher/network-watcher-topology-powershell.md?toc=%2fazure%2fnetworking%2ftoc.json)

## Scripts de exemplo
### [CLI do Azure](cli-samples.md)
### [Azure PowerShell](powershell-samples.md)

## Tutoriais
### [Balanceamento de carga de VMs](../virtual-machines/linux/tutorial-load-balancer.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Ligar um computador a uma rede virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)

# Referência
## [CLI do Azure](https://docs.microsoft.com/cli/azure/network)
## [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/?view=azurermps-3.8.0)
## [.Net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.network?view=azuremgmtnetwork-9.1.0-preview)
## [Node.js](https://azure.microsoft.com/develop/nodejs/#azure-sdk)
## [REST](https://msdn.microsoft.com/library/mt163658.aspx)

# Recursos
## [Modelos de autor](/azure/azure-resource-manager/resource-group-authoring-templates?toc=%2fazure%2fnetworking%2ftoc.json)
## [Mapa do Azure](https://azure.microsoft.com/roadmap/?category=networking)
## [Modelos da comunidade](https://azure.microsoft.com/resources/templates/)
## [Blogue das redes](https://azure.microsoft.com/blog/topics/networking)
## [Preços](https://azure.microsoft.com/pricing)
## [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/)
## [Disponibilidade regional](https://azure.microsoft.com/regions/services/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [Vídeos](https://azure.microsoft.com/resources/videos/index/?services=virtual-network)

