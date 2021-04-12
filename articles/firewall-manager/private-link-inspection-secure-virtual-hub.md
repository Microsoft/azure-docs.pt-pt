---
title: Tráfego seguro destinado a pontos finais privados em Azure Virtual WAN
description: Saiba como utilizar as regras de rede e as regras de aplicação para garantir o tráfego destinado a pontos finais privados em Azure Virtual WAN
services: firewall-manager
author: jocortems
ms.service: firewall-manager
ms.topic: how-to
ms.date: 04/02/2021
ms.author: jocorte
ms.openlocfilehash: a1237077b9e0a1af67d34c85d8ea8d526bd71372
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282017"
---
# <a name="secure-traffic-destined-to-private-endpoints-in-azure-virtual-wan"></a>Tráfego seguro destinado a pontos finais privados em Azure Virtual WAN

[Azure Private Endpoint](../private-link/private-endpoint-overview.md) é o bloco de construção fundamental para [a Azure Private Link](../private-link/private-link-overview.md). Os pontos finais privados permitem que os recursos Azure implantados numa rede virtual comuniquem privadamente com recursos de ligação privada.

Os pontos finais privados permitem o acesso dos recursos ao serviço de ligação privada implantado numa rede virtual. O acesso ao ponto final privado através de ligações de rede de observação de rede virtuais e de rede no local alarga a conectividade.

Poderá ser necessário filtrar o tráfego de clientes nas instalações ou no Azure destinados a serviços expostos através de pontos finais privados numa rede virtual ligada à WAN. Este artigo acompanha-o através desta tarefa usando [o hub virtual seguro](../firewall-manager/secured-virtual-hub.md) com a [Azure Firewall](../firewall/overview.md) como provedor de segurança.

A Azure Firewall filtra o tráfego utilizando qualquer um dos seguintes métodos:

* [FQDN nas regras de rede](../firewall/fqdn-filtering-network-rules.md) para protocolos TCP e UDP
* [FQDN nas regras de aplicação](../firewall/features.md#application-fqdn-filtering-rules) para HTTP, HTTPS e MSSQL.
* Endereços IP de origem e destino, porta e protocolo usando [regras de rede](../firewall/features.md#network-traffic-filtering-rules)

Utilize regras de aplicação sobre as regras de rede para inspecionar o tráfego destinado a pontos finais privados.
Um hub virtual seguro é gerido pela Microsoft e não pode ser ligado a uma [Zona Privada de DNS](../dns/private-dns-privatednszone.md). Isto é necessário para resolver um [recurso de ligação privada](../private-link/private-endpoint-overview.md#private-link-resource) FQDN para o seu endereço IP de ponto final privado correspondente.

A filtragem SQL FQDN é suportada apenas em [modo proxy](../azure-sql/database/connectivity-architecture.md#connection-policy) (porta 1433). O modo *proxy* pode resultar em mais latência em comparação com *o redirecionamento*. Se pretender continuar a utilizar o modo de redirecionamento, que é o padrão para os clientes que se ligam dentro do Azure, pode filtrar o acesso usando o FQDN nas regras da rede de firewall.

## <a name="filter-traffic-using-fqdn-in-network-and-application-rules"></a>Filtrar o tráfego utilizando o FQDN nas regras de rede e aplicação

Os seguintes passos permitem ao Azure Firewall filtrar o tráfego utilizando o FQDN nas regras de rede e aplicação:

1. Implementar uma máquina virtual [de reencaminhador DENS](../private-link/private-endpoint-dns.md#virtual-network-and-on-premises-workloads-using-a-dns-forwarder) numa rede virtual ligada ao centro virtual seguro e ligada às Zonas Privadas de DNS que hospedam os tipos de registo A para os pontos finais privados.

2. Configure [as definições de DNS personalizadas](../firewall/dns-settings.md#configure-custom-dns-servers---azure-portal) para apontar para o endereço IP da máquina virtual de encaminhar o DNS e ativar o proxy DNS na política de firewall associada à Firewall Azure implantada no hub virtual seguro.

3. Configure [servidores DNS personalizados](../virtual-network/manage-virtual-network.md#change-dns-servers) para as redes virtuais ligadas ao centro virtual seguro para apontar para o endereço IP privado associado ao Azure Firewall implantado no centro virtual seguro.

4. Configurar nas instalações servidores DNS para encaminhar consultas dns para as zonas públicas de DNS de pontos finais privados para o endereço IP privado associado à Firewall Azure implantado no centro virtual seguro.

5. Configure uma [regra de aplicação](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule) ou [regra de rede,](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule) conforme necessário na política de firewall associada à Firewall Azure implantada no centro virtual seguro com *o Destination Type* FQDN e o recurso de ligação privada público FQDN como *Destino*.

6. Navegue para *centros virtuais seguros* na política de firewall associada à Firewall Azure implantada no centro virtual seguro e selecione o centro virtual seguro onde a filtragem de tráfego destinada a pontos finais privados será configurada.

7. Navegue para **configuração de Segurança**, selecione **Enviar através de Azure Firewall** sob tráfego **privado**.

8. Selecione **prefixos de tráfego privados** para editar os prefixos CIDR que serão inspecionados através do Azure Firewall no hub virtual seguro e adicione um prefixo /32 para cada ponto final privado da seguinte forma:

   > [!IMPORTANT]
   > Se estes prefixos /32 não estiverem configurados, o tráfego destinado a pontos finais privados passará por Azure Firewall.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Configuração de segurança do gestor de firewall" border="true":::

Estes passos só funcionam quando os clientes e os pontos finais privados são implantados em diferentes redes virtuais ligadas ao mesmo hub virtual seguro e para clientes de instalações. Se os clientes e os pontos finais privados forem implantados na mesma rede virtual, deve ser criado um UDR com /32 rotas para os pontos finais privados. Configure estas rotas com **o próximo tipo de lúpulo**  definido para o aparelho **virtual** e próximo endereço **de lúpulo** definido para o endereço IP privado da Firewall Azure implantado no hub virtual seguro. **As rotas de propagação** devem ser definidas para **Sim.**

O diagrama que se segue ilustra os fluxos de tráfego de DNS e dados para que os diferentes clientes se conectem a um ponto final privado implantado na WAN virtual Azure:

:::image type="content" source="./media/private-link-inspection-secure-virtual-hub/private-link-inspection-virtual-wan-architecture.png" alt-text="Fluxos de Tráfego" border="true":::

## <a name="troubleshooting"></a>Resolução de problemas

Os principais problemas que pode ter quando tenta filtrar o tráfego destinado a pontos finais privados através de um centro virtual seguro são:

- Os clientes não conseguem ligar-se a pontos finais privados.

- A Azure Firewall é contornada. Este sintoma pode ser validado pela ausência de entradas de registo de regras de rede ou de aplicação no Azure Firewall.

Na maioria dos casos, estes problemas são causados por uma das seguintes questões:

- Resolução incorreta do nome DNS

- Configuração de encaminhamento incorreta

### <a name="incorrect-dns-name-resolution"></a>Resolução incorreta do nome DNS

1. Verifique se os servidores DNS de rede virtual estão definidos para *Personalizado* e o endereço IP é o endereço IP privado do Azure Firewall no centro virtual seguro.

   Azure CLI:

   ```azurecli-interactive
   az network vnet show --name <VNET Name> --resource-group <Resource Group Name> --query "dhcpOptions.dnsServers"
   ```
2. Verifique se os clientes na mesma rede virtual que a máquina virtual de reencaminhamento DENS pode resolver o ponto final privado FQDN para o seu endereço IP privado correspondente consultando diretamente a máquina virtual configurada como reencaminhador DNS.

   Linux:

   ```bash
   dig @<DNS forwarder VM IP address> <Private endpoint public FQDN>
   ```
3. Inspecione as entradas de registo *AzureFirewallDNSProxy* Azure Firewall e valide-a pode receber e resolver consultas dns dos clientes.

   ```kusto
   AzureDiagnostics
   | where Category contains "DNS"
   | where msg_s contains "database.windows.net"
   ```
4. Verifique se o *proxy DNS* foi ativado e um servidor DNS *personalizado* que aponta para o endereço IP do endereço IP da máquina virtual de encaminhamento DNS foi configurado na política de firewall associada à Firewall Azure firewall no centro virtual seguro.

   Azure CLI:

   ```azurecli-interactive
   az network firewall policy show --name <Firewall Policy> --resource-group <Resource Group Name> --query dnsSettings
   ```

### <a name="incorrect-routing-configuration"></a>Configuração de encaminhamento incorreta

1. Verifique a *configuração de segurança* na política de firewall associada à Firewall Azure implantada no centro virtual seguro. Certifique-se de que, por baixo da coluna **PRIVATE TRAFFIC,** mostra como **Protegido pelo Azure Firewall** para todas as ligações de rede e ramos virtuais para as quais pretende filtrar o tráfego.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-policy-private-traffic-configuration.png" alt-text="Tráfego Privado Protegido por Azure Firewall" border="true":::

2. Verifique a **configuração de segurança** na política de firewall associada à Firewall Azure implantada no centro virtual seguro. Certifique-se de que existe uma entrada /32 para cada endereço IP privado de ponto final privado que pretende filtrar o tráfego sob **prefixos de tráfego privados**.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Configuração de segurança do gestor de firewall - Prefixos de tráfego privado" border="true":::

3. No centro virtual seguro sob WAN virtual, inspecione as rotas eficazes para as tabelas de rotas associadas às redes virtuais e às ligações de ramos para as quais pretende filtrar o tráfego. Certifique-se de que existem /32 entradas para cada endereço IP privado de ponto final privado para o qual pretende filtrar o tráfego.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/secured-virtual-hub-effective-routes.png" alt-text="Rotas eficazes do hub virtual protegido" border="true":::

4. Inspecione as rotas eficazes nos NICs ligados às máquinas virtuais implantadas nas redes virtuais para as quais pretende filtrar o tráfego. Certifique-se de que existem /32 entradas para cada endereço IP privado de ponto final privado para o qual pretende filtrar o tráfego.
 
   Azure CLI:

   ```azurecli-interactive
   az network nic show-effective-route-table --name <Network Interface Name> --resource-group <Resource Group Name> -o table
   ```
5. Inspecione as tabelas de encaminhamento dos seus dispositivos de encaminhamento no local. Certifique-se de que está a aprender os espaços de endereço das redes virtuais onde os pontos finais privados são implantados.

   Azure virtual WAN não anuncia os prefixos configurados sob **prefixos de tráfego privado** na **configuração** de segurança da política de firewall para as instalações. Espera-se que as entradas /32 não apareçam nas tabelas de encaminhamento dos seus dispositivos de encaminhamento no local.

6. Inspecione os registos **AzureFirewallApplicationRule** e **AzureFirewallNetworkRule** Azure Firewall. Certifique-se de que o tráfego destinado aos pontos finais privados está a ser registado.

   As entradas de registo **AzureFirewallNetworkRule** não incluem informações de FQDN. Filtrar por endereço IP e porta ao inspecionar as regras da rede.

   Ao filtrar o tráfego destinado aos pontos finais privados [do Azure Files,](../storage/files/storage-files-introduction.md) as entradas de registo **AzureFirewallNetworkRule** só serão geradas quando um cliente montar ou ligar à partilha de ficheiros. O Azure Firewall não gerará registos para operações [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) para ficheiros na partilha de ficheiros. Isto porque as operações crud são realizadas sobre o persistente canal TCP aberto quando o cliente conecta ou monta pela primeira vez a partilha de ficheiros.

   Exemplo de consulta de registo de regras de aplicação:

   ```kusto
   AzureDiagnostics
   | where msg_s contains "database.windows.net"
   | where Category contains "ApplicationRule"
   ```
## <a name="next-steps"></a>Passos seguintes

- [Utilizar o Azure Firewall para inspecionar o tráfego destinado a um ponto final privado](../private-link/inspect-traffic-with-azure-firewall.md)
