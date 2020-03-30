---
title: Aplicação de ligação de instância gerida
description: Este artigo discute como ligar a sua aplicação à Instância Gerida pela Base de Dados Azure SQL.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823388"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Ligar a aplicação à instância gerida de Base de Dados SQL do Azure

Hoje tem várias opções ao decidir como e onde acolhe a sua candidatura.

Pode optar por hospedar aplicações na nuvem, quer utilizando o Azure App Service, quer algumas das opções integradas da rede virtual (VNet) do Azure, como o Azure App Service Environment, a Virtual Machine, o Virtual Machine Scale set. Também pode ter a abordagem híbrida da nuvem e manter as suas aplicações no local.

Qualquer que seja a escolha que tenha feito, pode ligá-la a uma Instância Gerida.  

![alta disponibilidade](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>Ligue uma aplicação dentro do mesmo VNet

Este cenário é o mais simples. As máquinas virtuais dentro do VNet podem ligar-se diretamente, mesmo que estejam dentro de diferentes subredes. Isto significa que tudo o que precisa para ligar a aplicação dentro de um Ambiente de Aplicação Azure ou Máquina Virtual é definir a cadeia de ligação adequadamente.  

## <a name="connect-an-application-inside-a-different-vnet"></a>Ligue uma aplicação dentro de um VNet diferente

Este cenário é um pouco mais complexo porque a Managed Instance tem endereço IP privado no seu próprio VNet. Para se ligar, uma aplicação precisa de acesso ao VNet onde a Instância Gerida é implementada. Então, primeiro precisa fazer uma ligação entre a aplicação e o VNet de Instância Gerida. Os VNets não têm de estar na mesma subscrição para que este cenário funcione.

Existem duas opções para ligar VNets:

- [Peering da Rede Virtual Azure](../virtual-network/virtual-network-peering-overview.md)
- Gateway VNet-to-VNet VPN ([portal Azure,](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) [PowerShell,](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) [Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

A opção de peering é a preferível porque o peering utiliza a rede de espinha dorsal da Microsoft para que, do ponto de vista da conectividade, não haja uma diferença notável na latência entre máquinas virtuais em VNet e no mesmo VNet. O peering VNet está limitado às redes da mesma região.  

> [!IMPORTANT]
> O cenário de observação da VNet para A Instância Gerida limita-se às redes da mesma região devido a [restrições do peering da Rede Virtual Global.](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) Consulte também a secção relevante do artigo de [Perguntas Virtuais Azure frequentemente feitas](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) para obter mais detalhes. 

## <a name="connect-an-on-premises-application"></a>Ligar a sua própria aplicação no local

A Instância Gerida só pode ser acedida através de um endereço IP privado. Para aceder a partir do local, é necessário efazer uma ligação Site-a-Local entre a aplicação e a VNet de Instância Gerida.

Existem duas opções como ligar as instalações ao Azure VNet:

- Ligação VPN site-to-site[(portal Azure,](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) [PowerShell,](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) [Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [Conexão ExpressRoute](../expressroute/expressroute-introduction.md)  

Se estabeleceu no local a ligação Azure com sucesso e não consegue estabelecer a ligação à Instância Gerida, verifique se a sua firewall tem ligação de saída aberta na porta SQL 1433, bem como a gama 11000-11999 portas para reorientação.

## <a name="connect-an-application-on-the-developers-box"></a>Ligar uma aplicação na caixa de programadores

A Instância Gerida só pode ser acedida através de um endereço IP privado para aceder a partir da sua caixa de desenvolvimento, primeiro precisa de fazer uma ligação entre a sua caixa de desenvolvimento e o VNet de Instância Gerida. Para tal, configure uma ligação Ponto-a-Local a uma VNet utilizando a autenticação do certificado Azure nativo. Para mais informações, consulte [Configurar uma ligação ponto-a-local para se ligar a uma base de dados Azure SQL Managed Instance a partir de um computador no local](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>Ligue-se a partir de instalações com o peering VNet

Outro cenário implementado pelos clientes é onde o gateway VPN é instalado numa rede virtual separada e uma subscrição do único anfitrião Managed Instance. As duas redes virtuais são então espumosas. O diagrama de arquitetura da amostra seguinte mostra como isto pode ser implementado.

![VNet peering](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

Uma vez configurada a infraestrutura básica, é necessário modificar alguma definição para que o Gateway VPN possa ver os endereços IP na rede virtual que acolhe a Instância Gerida. Para isso, faça as seguintes alterações muito específicas sob as definições de **Peering**.

1. Na VNet que acolhe o gateway VPN, vá a **Peerings,** depois à conexão VNet de instância gerida, e clique em Permitir o **Gateway Transit**.
2. No VNet que acolhe a Instância Gerida, vá a **Peerings,** depois à ligação VpN Gateway peered VNet, e, em seguida, clique **em Utilizar gateways remotos**.

## <a name="connect-an-azure-app-service-hosted-application"></a>Ligue uma aplicação do Azure App Service

A Instância Gerida só pode ser acedida através de um endereço IP privado, pelo que, para aceder ao Serviço de Aplicações Azure, primeiro precisa de fazer uma ligação entre a aplicação e a VNet de Instância Gerida. Ver [Integrar a sua aplicação com uma Rede Virtual Azure.](../app-service/web-sites-integrate-with-vnet.md)  

Para resolução de problemas, consulte [A resolução de Problemas VNets e Aplicações](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Se não for possível estabelecer uma ligação, tente [sincronizar a configuração de rede](sql-database-managed-instance-sync-network-configuration.md).

Um caso especial de ligação do Serviço de Aplicações Azure à Instância Gerida é quando integrou o Azure App Service a uma rede com o par de VNet de Instância Gerida. Este caso requer a seguinte configuração a ser configurada:

- Instância gerida VNet NÃO deve ter gateway  
- Caso gerido VNet deve ter utilização de gateways remotos definido
- VNet peered deve ter permitir opção de trânsito de gateway definido

Este cenário é ilustrado no seguinte diagrama:

![peering de aplicativo integrado](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>A funcionalidade de Integração VNet não integra uma aplicação com um VNet que tem um Gateway ExpressRoute. Mesmo que o ExpressRoute Gateway esteja configurado no modo de coexistência, a Integração VNet não funciona. Se precisar de aceder a recursos através de uma ligação ExpressRoute, então pode utilizar um Ambiente de Serviço de Aplicação, que corre no seu VNet.

## <a name="troubleshooting-connectivity-issues"></a>Troubleshooting connectivity issues (Resolver problemas de conectividade)

Para problemas de resolução de problemas de conectividade, reveja o seguinte:

- Se não conseguir ligar-se à Instância Gerida a partir de uma máquina virtual Azure dentro da mesma VNet, mas subnet diferente, verifique se tem um Grupo de Segurança de Rede definido na subnet VM que pode estar a bloquear o acesso. Note ainda que precisa de abrir a ligação de saída na porta SQL 1433, bem como portas no intervalo 11000-11999, uma vez que estas são necessárias para a ligação através da redirecionamento dentro da fronteira Azure.
- Certifique-se de que a propagação do BGP está definida para **ativada** para a tabela de rotas associada à VNet.
- Se utilizar o P2S VPN, verifique a configuração no portal Azure para ver se vê os **números Ingress/Egress.** Números não-zero indicam que o Azure está a encaminhar o tráfego de/para o local.

   ![números de entrada/saída](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Verifique se a máquina cliente (que está a executar o cliente VPN) tem entradas de rotas para todos os VNets a que precisa de aceder. As rotas estão `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`armazenadas em .

   ![route.txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Como mostrado nesta imagem, existem duas entradas para cada VNet envolvida seletiva e uma terceira entrada para o ponto final VPN que está configurada no Portal.

   Outra forma de verificar as rotas é através do seguinte comando. A saída mostra as rotas para as várias subredes:

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

- Se utilizar o peering VNet, certifique-se de que seguiu as instruções para a definição [Permitir o trânsito de gateway e utilizar gateways remotos](#connect-from-on-premises-with-vnet-peering).

## <a name="required-versions-of-drivers-and-tools"></a>Versões necessárias de condutores e ferramentas

Recomenda-se as seguintes versões mínimas das ferramentas e controladores se pretender ligar-se à Instância Gerida:

| Condutor/ferramenta | Versão |
| --- | --- |
|.NET Framework | 4.6.1 (ou .NET Core) |
|Controlador ODBC| v17 |
|Controlador PHP| 5.2.0 |
|Controlador JDBC| 6.4.0 |
|Controlador Node.js| 2.1.1 |
|Controlador OLEDB| 18.0.2.0 |
|SSMS| 18.0 ou [superior](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) ou mais |

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre a Instância Gerida, consulte [O que é uma Instância Gerida](sql-database-managed-instance.md).
- Para um tutorial que lhe mostre como criar uma nova Instância Gerida, consulte [Create a Managed Instance](sql-database-managed-instance-get-started.md).
