---
title: Ligar a aplicação do Azure SQL Database Managed Instance | Documentos da Microsoft
description: Este artigo descreve como ligar a sua aplicação para a instância gerida da base de dados SQL do Azure.
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
ms.date: 11/09/2018
ms.openlocfilehash: 9d47e3fb821e9ad64beecd62c274a33e7fa14041
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57887328"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Ligar a sua aplicação à Instância Gerida de Base de Dados SQL do Azure

Hoje em dia tem várias opções ao decidir como e onde hospedar seu aplicativo. 
 
Pode optar alojar aplicações na cloud utilizando o serviço de aplicações do Azure ou algumas das opções de rede virtual (VNet), integrado do Azure, como o ambiente de serviço de aplicações do Azure, máquinas virtuais, conjunto de dimensionamento da Máquina Virtual. Também pode levar a abordagem de cloud híbrida e manter as suas aplicações no local. 
 
Independentemente da sua escolha que criou, pode ligá-la para uma instância gerida.  

![elevada disponibilidade](./media/sql-database-managed-instance/application-deployment-topologies.png)  
## <a name="connect-an-application-inside-the-same-vnet"></a>Ligar uma aplicação dentro da mesma VNet 

Este cenário é a forma mais simples. Máquinas virtuais dentro da VNet pode ligar diretamente entre si mesmo que estejam em sub-redes diferentes. Isso significa que tudo o que precisa para ligar a aplicação dentro de um ambiente de aplicação do Azure ou a Máquina Virtual é definir a cadeia de ligação corretamente.  
 
## <a name="connect-an-application-inside-a-different-vnet"></a>Ligar uma aplicação dentro de uma VNet diferente 

Este cenário é um pouco mais complexo, porque a instância gerida tem o endereço IP privado na sua própria VNet. Para ligar, um aplicativo precisa de acesso para a VNet onde a instância gerida está implementada. Por isso, primeiro tem de estabelecer uma ligação entre o aplicativo e a VNet de instância gerida. As VNets não tem de estar na mesma subscrição, para que este cenário funcione. 
 
Existem duas opções para ligar VNets: 
- [Peering de rede Virtual do Azure](../virtual-network/virtual-network-peering-overview.md) 
- Gateway de VPN de VNet a VNet ([portal do Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [da CLI do Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)) 
 
A opção de peering é aquele preferível porque o peering utiliza a rede de backbone do Microsoft assim, da perspectiva de conectividade, não existe nenhuma diferença notável numa latência entre máquinas virtuais na VNet em modo de peering e na mesma VNet. O VNet peering é limitado às redes na mesma região.  
 
> [!IMPORTANT]
> Cenário de peering de VNet para a instância gerida está limitado às redes na mesma região devido a [restrições do peering de redes virtuais Global](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). 

## <a name="connect-an-on-premises-application"></a>Ligar uma aplicação no local 

Instância gerida só pode ser acedida através de um endereço IP privado. Para aceder a partir do local, terá de estabelecer uma ligação Site a Site entre o aplicativo e a VNet de instância gerida. 
 
Existem duas opções como ligar no local à VNet do Azure: 
- Ligação de VPN de site a Site ([portal do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [da CLI do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) 
- [ExpressRoute](../expressroute/expressroute-introduction.md) ligação  
 
Se criada com êxito no local para a ligação do Azure e não é possível estabelecer ligação à instância gerida, verifique se a firewall tem de abrir ligação de saída na porta 1433 do SQL, bem como o intervalo de 11000 12000 de portas para o redirecionamento. 

## <a name="connect-an-application-on-the-developers-box"></a>Ligar uma aplicação na caixa de desenvolvedores

A instância gerida pode ser acedida apenas por meio de um endereço IP privado por isso, para poder acessá-lo de sua caixa de desenvolvedor, primeiro tem de estabelecer uma ligação entre a sua caixa de desenvolvedor e a VNet de instância gerida. Para fazer isso, configure uma ligação ponto a Site a uma VNet com a autenticação de certificados nativa do Azure. Para obter mais informações, consulte [configurar uma ligação de ponto a site para ligar a uma instância de gerida de base de dados do Azure SQL a partir do computador no local](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>Ligar no local com o peering de VNet
Outro cenário implementado por parte dos clientes é onde o gateway de VPN está instalado numa subscrição de uma instância gerida alojamento e de uma rede virtual separada. As duas redes virtuais são, em seguida, em modo de peering. O diagrama de arquitetura de exemplo seguinte mostra como isso pode ser implementado.

![VNet peering](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

Assim que tiver a infraestrutura básica configurada, terá de modificar algumas definição para que o Gateway de VPN, pode ver os endereços IP na rede virtual que aloja a instância gerida. Para fazer isso, efetue as seguintes alterações muito específicas sob o **definições de Peering**.
1.  Na VNet que aloja o gateway de VPN, aceda a **Peerings**, em seguida, para a instância gerida em modo de peering ligação de VNet e, em seguida, clique em **permitir que o trânsito de Gateway**.
2.  Na VNet que aloja a instância gerida, aceda a **Peerings**, em seguida, para o Gateway de VPN em modo de peering ligação de VNet e, em seguida, clique em **utilizar gateways remotos**.

## <a name="connect-an-azure-app-service-hosted-application"></a>Ligar uma aplicação de serviço de aplicações do Azure alojada 

A instância gerida pode ser acessada apenas por meio de um endereço IP privado, portanto, para poder aceder a partir do serviço de aplicações do Azure tem primeiro de estabelecer uma ligação entre o aplicativo e a VNet de instância gerida. Ver [integrar a sua aplicação com uma Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).  
 
Para resolução de problemas, consulte [VNets de resolução de problemas e aplicativos](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Se não for possível estabelecer uma ligação, tente [a sincronizar a configuração de rede](sql-database-managed-instance-sync-network-configuration.md). 
 
Um caso especial de conexão do serviço de aplicações do Azure para a instância gerida é quando integrado o que serviço de aplicações do Azure para uma rede em modo de peering de VNet de instância gerida. Esse caso requer a seguinte configuração para configurar a: 

- VNet de instância gerida não pode ter o gateway  
- VNet de instância gerida precisa de conjunto de opções do utilizar gateways remotos 
- VNet peering tem de ter a opção de trânsito de gateway de permitir definido 
 
Este cenário é ilustrado no diagrama seguinte:

![peering de aplicação integrada](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>A funcionalidade de integração de VNet não integra a uma aplicação com uma VNet com um Gateway do ExpressRoute. Mesmo que o Gateway do ExpressRoute estiver configurado no modo de coexistência a integração de VNet não funciona. Se precisar de aceder aos recursos através de uma ligação ExpressRoute, em seguida, pode utilizar um ambiente de serviço de aplicações, o que é executado na sua VNet.
>
 
## <a name="troubleshooting-connectivity-issues"></a>Resolução de problemas de conectividade

Para resolução de problemas de conectividade, reveja o seguinte:
- Se não é possível ligar à instância gerida a partir de uma máquina virtual do Azure dentro da mesma VNet, sub-rede diferente, mas, verifique se tem um grupo de segurança de rede definida na sub-rede VM que poderão estar a bloquear o acesso. Além disso tenha em atenção que tem de abrir a ligação de saída na porta 1433 do SQL, bem como portas no intervalo de 11000 12000, uma vez que os são necessários para ligar através do redirecionamento de dentro do limite do Azure. 
- Certifique-se de que a propagação do BGP está definida como **ativado** para a tabela de rotas associada à VNet.
- Se utilizar a P2S VPN, verifique a configuração no portal do Azure para ver se verá **entrada/saída** números. Números diferentes de zero indicam que Azure está a encaminhar o tráfego de/para no local.

   ![números de entrada/saída](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Verifique se a máquina de cliente (o que está a executar o cliente VPN) tem entradas de rota para todas as VNets que precisa acessar. As rotas são armazenadas no `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`.


   ![route.txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Como é mostrado nesta imagem, há duas entradas para cada VNet envolvido e uma terceira entrada para o ponto final VPN que está configurado no Portal.

   Outra forma de verificar as rotas é através do seguinte comando. O resultado mostra as rotas para as várias sub-redes: 

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

- Se utilizar o VNet peering, certifique-se de que seguiu as instruções para a definição [permitir que o trânsito de Gateway e utilizar Gateways remotos](#connect-from-on-premises-with-vnet-peering). 

## <a name="required-versions-of-drivers-and-tools"></a>Versões necessárias do controlador e as ferramentas

As seguintes versões mínimas a ferramentas e os controladores são recomendadas para ligar à instância gerida:

| Ferramenta de Driver / | Versão |
| --- | --- |
|.NET Framework | 4.6.1 (ou .NET Core) | 
|Controlador ODBC    | v17 |
|Controladores PHP | 5.2.0 |
|JDBC driver    | 6.4.0 |
|Controlador node. js | 2.1.1 |
|Controlador OLEDB   | 18.0.2.0 |
|SSMS   | 17.8.1 ou [superior](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações sobre a Instância Gerida, veja [What is a Managed Instance](sql-database-managed-instance.md) (O que é uma Instância Gerida?).
- Para obter um tutorial que mostra como criar uma nova instância gerida, veja [criar uma instância gerida](sql-database-managed-instance-get-started.md).
