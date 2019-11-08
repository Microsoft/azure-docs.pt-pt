---
title: Aplicativo de conexão de instância gerenciada
description: Este artigo discute como conectar seu aplicativo ao Instância Gerenciada do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab, vanto
ms.date: 11/09/2018
ms.openlocfilehash: 9f592c345b7cfcf5f21d816fde1fae6b8e6b98c7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823388"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Conectar seu aplicativo à instância gerenciada do banco de dados SQL do Azure

Hoje, você tem várias opções ao decidir como e onde você hospeda seu aplicativo.

Você pode optar por hospedar um aplicativo na nuvem usando Azure App serviço ou algumas das opções integradas de VNet (rede virtual) do Azure, como Ambiente do Serviço de Aplicativo do Azure, máquina virtual, conjunto de dimensionamento de máquinas virtuais. Você também pode usar a abordagem de nuvem híbrida e manter seus aplicativos locais.

Seja qual for a opção que você fez, você pode conectá-lo a um Instância Gerenciada.  

![alta disponibilidade](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>Conectar um aplicativo dentro da mesma VNet

Esse cenário é o mais simples. As máquinas virtuais dentro da VNet podem se conectar entre si diretamente, mesmo se estiverem dentro de sub-redes diferentes. Isso significa que tudo o que você precisa para conectar o aplicativo dentro de um ambiente Aplicativo Azure ou máquina virtual é definir a cadeia de conexão adequadamente.  

## <a name="connect-an-application-inside-a-different-vnet"></a>Conectar um aplicativo dentro de uma VNet diferente

Esse cenário é um pouco mais complexo porque Instância Gerenciada tem um endereço IP privado em sua própria VNet. Para se conectar, um aplicativo precisa de acesso à VNet onde Instância Gerenciada é implantado. Portanto, primeiro você precisa fazer uma conexão entre o aplicativo e a VNet Instância Gerenciada. O VNets não precisa estar na mesma assinatura para que esse cenário funcione.

Há duas opções para conectar VNets:

- [Emparelhamento de rede virtual do Azure](../virtual-network/virtual-network-peering-overview.md)
- Gateway de VPN de VNet para VNet ([portal do Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) [CLI do Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

A opção de emparelhamento é a preferível porque o emparelhamento usa a rede de backbone da Microsoft, portanto, da perspectiva de conectividade, não há nenhuma diferença perceptível na latência entre as máquinas virtuais na VNet emparelhada e na mesma VNet. O emparelhamento VNet é limitado às redes na mesma região.  

> [!IMPORTANT]
> O cenário de emparelhamento VNet para Instância Gerenciada é limitado às redes na mesma região devido às [restrições do emparelhamento de rede virtual global](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Consulte também a seção relevante do artigo [perguntas frequentes sobre redes virtuais do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) para obter mais detalhes. 

## <a name="connect-an-on-premises-application"></a>Conectar um aplicativo local

Instância Gerenciada só pode ser acessada por meio de um endereço IP privado. Para acessá-lo no local, você precisa fazer uma conexão site a site entre o aplicativo e a VNet Instância Gerenciada.

Há duas opções de como conectar-se localmente à VNet do Azure:

- Conexão VPN site a site ([portal do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) [CLI do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- Conexão do [ExpressRoute](../expressroute/expressroute-introduction.md)  

Se você tiver estabelecido a conexão local para o Azure com êxito e não puder estabelecer conexão com Instância Gerenciada, verifique se o firewall tem conexão de saída aberta na porta SQL 1433, bem como 11000-11999 intervalo de portas para redirecionamento.

## <a name="connect-an-application-on-the-developers-box"></a>Conectar um aplicativo na caixa desenvolvedores

Instância Gerenciada pode ser acessado somente por meio de um endereço IP privado para poder acessá-lo na caixa do desenvolvedor, primeiro você precisa fazer uma conexão entre sua caixa de desenvolvedor e a VNet Instância Gerenciada. Para fazer isso, configure uma conexão ponto a site com uma VNet usando a autenticação de certificado nativa do Azure. Para obter mais informações, consulte [Configurar uma conexão ponto a site para se conectar a um instância gerenciada do banco de dados SQL do Azure do computador local](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>Conectar-se do local com o emparelhamento VNet

Outro cenário implementado por clientes é o local em que o gateway de VPN é instalado em uma rede virtual separada e uma assinatura do Instância Gerenciada de hospedagem. As duas redes virtuais são emparelhadas. O diagrama de arquitetura de exemplo a seguir mostra como isso pode ser implementado.

![VNet peering](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

Depois que a infraestrutura básica estiver configurada, você precisará modificar algumas configurações para que o gateway de VPN possa ver os endereços IP na rede virtual que hospeda o Instância Gerenciada. Para fazer isso, faça as seguintes alterações muito específicas nas **configurações de emparelhamento**.

1. Na VNet que hospeda o gateway de VPN, acesse **emparelhamentos**e, em seguida, para a conexão de VNet emparelhada instância gerenciada e clique em **Permitir trânsito de gateway**.
2. Na VNet que hospeda o Instância Gerenciada, vá para **emparelhamentos**, depois para a conexão VNet emparelhada do gateway de VPN e clique em **usar gateways remotos**.

## <a name="connect-an-azure-app-service-hosted-application"></a>Conectar um aplicativo hospedado no serviço de Azure App

Instância Gerenciada pode ser acessado somente por meio de um endereço IP privado para poder acessá-lo do serviço de Azure App você precisa primeiro fazer uma conexão entre o aplicativo e a VNet Instância Gerenciada. Consulte [integrar seu aplicativo a uma rede virtual do Azure](../app-service/web-sites-integrate-with-vnet.md).  

Para solução de problemas, consulte [solução de problemas de VNets e aplicativos](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Se não for possível estabelecer uma conexão, tente [sincronizar a configuração de rede](sql-database-managed-instance-sync-network-configuration.md).

Um caso especial de conectar Azure App serviço ao Instância Gerenciada é quando você integrou o serviço Azure App a uma rede emparelhada para Instância Gerenciada VNet. Esse caso requer que a configuração a seguir seja configurada:

- A VNet Instância Gerenciada não deve ter um gateway  
- A VNet Instância Gerenciada deve ter o conjunto de opções usar gateways remotos
- VNet emparelhada deve ter o conjunto de opções Permitir trânsito de gateway

Esse cenário é ilustrado no diagrama a seguir:

![emparelhamento de aplicativo integrado](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>O recurso integração VNet não integra um aplicativo com uma VNet que tem um gateway de ExpressRoute. Mesmo que o gateway de ExpressRoute esteja configurado no modo de coexistência, a integração VNet não funciona. Se você precisar acessar recursos por meio de uma conexão do ExpressRoute, poderá usar um Ambiente do Serviço de Aplicativo, que é executado em sua VNet.

## <a name="troubleshooting-connectivity-issues"></a>Solucionando problemas de conectividade

Para solucionar problemas de conectividade, examine o seguinte:

- Se você não conseguir se conectar a Instância Gerenciada de uma máquina virtual do Azure na mesma VNet, mas em uma sub-rede diferente, verifique se você tem um grupo de segurança de rede definido na sub-rede VM que pode estar bloqueando o acesso. Além disso, observe que você precisa abrir a conexão de saída na porta SQL 1433, bem como portas no intervalo 11000-11999, já que elas são necessárias para se conectar via redirecionamento dentro do limite do Azure.
- Verifique se a propagação de BGP está definida como **habilitada** para a tabela de rotas associada à VNet.
- Se estiver usando a VPN P2S, verifique a configuração no portal do Azure para ver se você vê números de **entrada/saída** . Números diferentes de zero indicam que o Azure está roteando o tráfego para/do local.

   ![números de entrada/egresso](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Verifique se o computador cliente (que está executando o cliente VPN) tem entradas de rota para todos os VNets que você precisa acessar. As rotas são armazenadas em `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`.

   ![Route. txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Conforme mostrado nesta imagem, há duas entradas para cada VNet envolvida e uma terceira entrada para o ponto de extremidade de VPN que é configurada no Portal.

   Outra maneira de verificar as rotas é por meio do comando a seguir. A saída mostra as rotas para as várias sub-redes:

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================

   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- Se estiver usando o emparelhamento VNet, verifique se você seguiu as instruções para definir [Permitir trânsito de gateway e usar gateways remotos](#connect-from-on-premises-with-vnet-peering).

## <a name="required-versions-of-drivers-and-tools"></a>Versões necessárias de drivers e ferramentas

As seguintes versões mínimas das ferramentas e dos drivers serão recomendadas se você quiser se conectar ao Instância Gerenciada:

| Driver/ferramenta | Versão |
| --- | --- |
|.NET Framework | 4.6.1 (ou .NET Core) |
|Controlador ODBC| v17 |
|Controlador PHP| 5.2.0 |
|Controlador JDBC| 6.4.0 |
|Controlador Node.js| 2.1.1 |
|Controlador OLEDB| 18.0.2.0 |
|SSMS| 18,0 ou [superior](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) ou superior |

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre a Instância Gerida, veja [What is a Managed Instance](sql-database-managed-instance.md) (O que é uma Instância Gerida?).
- Para obter um tutorial mostrando como criar um novo Instância Gerenciada, consulte [criar um instância gerenciada](sql-database-managed-instance-get-started.md).
