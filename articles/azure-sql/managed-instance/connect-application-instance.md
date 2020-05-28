---
title: Ligar aplicação a instância gerida
titleSuffix: Azure SQL Managed Instance
description: Este artigo discute como ligar a sua aplicação à Instância Gerida Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab, vanto
ms.date: 11/09/2018
ms.openlocfilehash: 051d589ec13c1fa8642701fe94a361e1dfbe4aab
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84044389"
---
# <a name="connect-your-application-to-azure-sql-managed-instance"></a>Ligue a sua aplicação à Instância Gerida Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Hoje tem várias opções ao decidir como e onde acolhe a sua candidatura.

Pode optar por hospedar aplicações na nuvem, quer utilizando o Azure App Service, quer algumas das opções integradas da rede virtual (rede virtual) do Azure, como o Azure App Service Environment, a Virtual Machine, o Virtual Machine Scale set. Também pode ter a abordagem híbrida da nuvem e manter as suas aplicações no local.

Qualquer que seja a escolha que fizer, pode ligá-la a uma Instância Gerida SQL. 

![alta disponibilidade](./media/connect-application-instance/application-deployment-topologies.png)

Este artigo descreve como ligar uma aplicação ao Azure SQL Managed Instance em vários cenários de aplicação diferentes. 

## <a name="connect-inside-the-same-vnet"></a>Ligar dentro do mesmo VNet

Ligar uma aplicação dentro da mesma rede virtual que o SQL Managed Instance é o cenário mais simples. As máquinas virtuais dentro da rede virtual podem ligar-se diretamente, mesmo que estejam dentro de diferentes subredes. Isto significa que tudo o que precisa para ligar a aplicação dentro de um Ambiente de Aplicação Azure ou Máquina Virtual é definir a cadeia de ligação adequadamente.  

## <a name="connect-inside-a-different-vnet"></a>Conecte-se dentro de uma VNet diferente

Ligar uma aplicação quando reside numa rede virtual diferente, uma vez que a Instância Gerida sQL é um pouco mais complexa porque a SQL Managed Instance tem endereços IP privados na sua própria rede virtual. Para se ligar, uma aplicação precisa de acesso à rede virtual onde a SQL Managed Instance é implementada. Por isso, tem de fazer uma ligação entre a aplicação e a rede virtual SQL Managed Instance. As redes virtuais não têm de estar na mesma subscrição para que este cenário funcione.

Existem duas opções para ligar redes virtuais:

- [Peering Azure VPN](../../virtual-network/virtual-network-peering-overview.md)
- Gateway VNet-to-VNet VPN: ([Portal Azure,](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) [PowerShell,](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) [Azure CLI](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

O peering é preferível porque o peering utiliza a rede de espinha dorsal da Microsoft para que, do ponto de vista da conectividade, não haja uma diferença notável na latência entre máquinas virtuais na rede virtual e na mesma rede virtual. O epeering de rede virtual está limitado às redes da mesma região.  

> [!IMPORTANT]
> O cenário de peering de rede virtual para A Instância Gerida sQL está limitado às redes da mesma região devido aos [constrangimentos do peering da Rede Virtual Global.](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) Consulte também a secção relevante do artigo de [Perguntas Virtuais Azure frequentemente feitas](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) para obter mais detalhes. 

## <a name="connect-from-on-premises"></a>Ligar a partir do local 

Também pode ligar a sua aplicação no local à sua Instância Gerida SQL. A Instância Gerida sQL só pode ser acedida através de um endereço IP privado. Para aceder a partir do local, é necessário efazer uma ligação Site-a-Site entre a aplicação e a rede virtual SQL Managed Instance.

Existem duas opções como ligar as instalações à rede virtual Azure:

- Ligação VPN site-to-site[(portal Azure,](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) [PowerShell,](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) [Azure CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [Conexão ExpressRoute](../../expressroute/expressroute-introduction.md)  

Se estabeleceu no local a ligação Azure com sucesso e não consegue estabelecer a ligação à SQL Managed Instance, verifique se a sua firewall tem ligação de saída aberta na porta SQL 1433, bem como 11000-11999 portas para reorientação.

## <a name="connect-the-developers-box"></a>Ligar a caixa de desenvolvedores

Também é possível ligar a sua caixa de desenvolvedores à Instância Gerida SQL. A Instância Gerida sQL só pode ser acedida através de um endereço IP privado para aceder a partir da sua caixa de desenvolvimento, primeiro precisa de fazer uma ligação entre a sua caixa de desenvolvimento e a rede virtual SQL Managed Instance. Para tal, configure uma ligação Ponto-a-Local a uma rede virtual utilizando a autenticação de certificado saqueado nativo azure. Para mais informações, consulte [configurar uma ligação ponto-a-local para ligar ao Azure SQL Managed Instance a partir do computador no local](point-to-site-p2s-configure.md).

## <a name="connect-with-vnet-peering"></a>Conecte-se com o peering VNet

Outro cenário implementado pelos clientes é onde um gateway VPN é instalado numa rede virtual separada e subscrição a partir daquele que acolhe a Instância Gerida SQL. As duas redes virtuais são então espumosas. O diagrama de arquitetura da amostra seguinte mostra como isto pode ser implementado.

![virtual rede peering](./media/connect-application-instance/vnet-peering.png)

Uma vez configurada a infraestrutura básica, é necessário modificar alguma definição para que o Gateway VPN possa ver os endereços IP na rede virtual que acolhe a Instância Gerida SQL. Para isso, faça as seguintes alterações muito específicas sob as definições de **Peering**.

1. Na rede virtual que acolhe o gateway VPN, vá a **Peerings,** depois ao SQL Managed Instance especiou a ligação virtual da rede e, em seguida, clique em Permitir o **Gateway Transit**.
2. Na rede virtual que acolhe o SQL Managed Instance, vá a **Peerings,** depois ao VPN Gateway peered conexão de rede virtual, e depois clique **em Utilizar gateways remotos**.

## <a name="connect-azure-app-service"></a>Serviço de Aplicações Connect Azure 

Também pode ligar uma aplicação que é hospedada pelo Serviço de Aplicações Azure. A Instância Gerida sQL só pode ser acedida através de um endereço IP privado, pelo que, para aceder ao Serviço de Aplicações Azure, primeiro precisa de fazer uma ligação entre a aplicação e a rede virtual SQL Managed Instance. Ver [Integrar a sua aplicação com uma Rede Virtual Azure.](../../app-service/web-sites-integrate-with-vnet.md)  

Para resolução de problemas, consulte as redes virtuais e aplicações de resolução de [problemas.](../../app-service/web-sites-integrate-with-vnet.md#troubleshooting) Se não for possível estabelecer uma ligação, tente [sincronizar a configuração de rede](azure-app-sync-network-configuration.md).

Um caso especial de ligação do Serviço de Aplicações Azure à SQL Managed Instance é quando integra o Azure App Service a uma rede com o SQL Managed Instance. Este caso requer a seguinte configuração a ser configurada:

- Rede virtual SQL Managed Instance NÃO deve ter gateway  
- Rede virtual SQL Managed Instance deve ter `Use remote gateways` conjunto de opções
- Rede virtual peered deve ter permitir opção de trânsito gateway definido

Este cenário é ilustrado no seguinte diagrama:

![peering de aplicativo integrado](./media/connect-application-instance/integrated-app-peering.png)

>[!NOTE]
>A funcionalidade de Integração de rede virtual não integra uma aplicação com uma rede virtual que tem um Gateway ExpressRoute. Mesmo que o ExpressRoute Gateway esteja configurado no modo de coexistência, a integração da rede virtual não funciona. Se precisar de aceder a recursos através de uma ligação ExpressRoute, então pode utilizar um App Service Environment, que funciona na sua rede virtual.

## <a name="troubleshooting-connectivity-issues"></a>Troubleshooting connectivity issues (Resolver problemas de conectividade)

Para problemas de resolução de problemas de conectividade, reveja o seguinte:

- Se não conseguir ligar-se ao SQL Managed Instance a partir de uma máquina virtual Azure dentro da mesma rede virtual, mas subnet diferente, verifique se tem um Grupo de Segurança de Rede definido na subnet VM que pode estar a bloquear o acesso. Adicionalmente, abra a ligação de saída na porta SQL 1433, bem como portas na faixa 11000-11999, uma vez que estas são necessárias para a ligação através da redirecionamento dentro da fronteira Azure.
- Certifique-se de que a Propagação do BGP está definida para **ativada** para a tabela de rotas associada à rede virtual.
- Se utilizar o P2S VPN, verifique a configuração no portal Azure para ver se vê os **números Ingress/Egress.** Números não-zero indicam que o Azure está a encaminhar o tráfego de/para o local.

   ![números de entrada/saída](./media/connect-application-instance/ingress-egress-numbers.png)

- Verifique se a máquina cliente (que está a executar o cliente VPN) tem entradas de rotas para todas as redes virtuais a que precisa de aceder. As rotas estão armazenadas em `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt` .

   ![route.txt](./media/connect-application-instance/route-txt.png)

   Como mostra esta imagem, existem duas entradas para cada rede virtual envolvida e uma terceira entrada para o ponto final VPN que está configurada no Portal.

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

- Se utilizar o especte de rede virtual, certifique-se de que seguiu as instruções para a definição [Permitir o trânsito de gateway e utilizar gateways remotos](#connect-from-on-premises).

- Se utilizar uma rede virtual que pretende ligar uma aplicação hospedada ao Serviço de Aplicações Azure, e a rede virtual SQL Managed Instance tiver uma gama pública de endereços IP, certifique-se de que as definições de aplicação hospedadas permitem que o seu tráfego de saída seja encaminhado para redes IP públicas. Siga as instruções na Integração da [rede virtual Regional.](../../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)

## <a name="required-versions-of-drivers-and-tools"></a>Versões necessárias de condutores e ferramentas

Recomenda-se as seguintes versões mínimas das ferramentas e controladores se pretender ligar-se à Instância Gerida sQL:

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

## <a name="next-steps"></a>Próximos passos

- Para obter informações sobre a Instância Gerida sQL, consulte o que é a Instância Gerida pela [SQL?](sql-managed-instance-paas-overview.md)
- Para um tutorial que lhe mostre como criar uma nova Instância Gerida SQL, consulte [Create a SQL Managed Instance](instance-create-quickstart.md).
