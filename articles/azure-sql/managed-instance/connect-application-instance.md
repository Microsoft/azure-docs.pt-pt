---
title: Ligue a sua aplicação a SQL Managed Instance
titleSuffix: Azure SQL Managed Instance
description: Este artigo discute como ligar a sua aplicação à Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, vanto
ms.date: 11/09/2018
ms.openlocfilehash: 3106ec1a45b307207624e5cd077a222acccc83d1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692832"
---
# <a name="connect-your-application-to-azure-sql-managed-instance"></a>Ligar a aplicação ao Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Hoje tem múltiplas opções ao decidir como e onde hospeda a sua aplicação.

Pode optar por hospedar a aplicação na nuvem utilizando o Azure App Service ou algumas das opções integradas da rede virtual da Azure, como o Azure App Service Environment, as Máquinas Virtuais Azure e os conjuntos de escala de máquinas virtuais. Também pode fazer uma abordagem híbrida em nuvem e manter as suas aplicações no local.

Seja qual for a escolha que fizer, pode ligá-la ao Azure SQL Managed Instance. 

![Elevada disponibilidade](./media/connect-application-instance/application-deployment-topologies.png)

Este artigo descreve como ligar uma aplicação ao Azure SQL Managed Instance em vários cenários de aplicação diferentes. 

## <a name="connect-inside-the-same-vnet"></a>Ligar dentro do mesmo VNet

Ligar uma aplicação dentro da mesma rede virtual que o SQL Managed Instance é o cenário mais simples. As máquinas virtuais dentro da rede virtual podem ligar-se umas às outras diretamente, mesmo que estejam dentro de diferentes sub-redes. Isto significa que tudo o que precisa para ligar uma aplicação dentro do App Service Environment ou uma máquina virtual é definir o fio de ligação adequadamente.  

## <a name="connect-inside-a-different-vnet"></a>Conecte-se dentro de um VNet diferente

Ligar uma aplicação quando reside numa rede virtual diferente do SQL Managed Instance é um pouco mais complexo porque o SQL Managed Instance tem endereços IP privados na sua própria rede virtual. Para se conectar, uma aplicação precisa de acesso à rede virtual onde o SQL Managed Instance é implantado. Por isso, é necessário estabelecer uma ligação entre a aplicação e a rede virtual SQL Managed Instance. As redes virtuais não têm de estar na mesma subscrição para que este cenário funcione.

Existem duas opções para ligar redes virtuais:

- [Azure VNet olhando](../../virtual-network/virtual-network-peering-overview.md)
- Gateway VNet-to-VNet VPN[(portal Azure,](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) [PowerShell,](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) [Azure CLI](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

O peering é preferível porque utiliza a rede de espinha dorsal da Microsoft, por isso, do ponto de vista da conectividade, não há uma diferença notável na latência entre máquinas virtuais numa rede virtual esprevada e na mesma rede virtual. O espreitamento de rede virtual é para suportar entre as redes da mesma região. O espreitamento global da rede virtual também é suportado com a limitação descrita na nota abaixo.  

> [!IMPORTANT]
> [Em 22/9/2020 anunciamos a rede virtual global que procura clusters virtuais recém-criados.](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/) Isto significa que o espreitamento global da rede virtual é suportado para sql Managed Instances criados em sub-redes vazias após a data de anúncio, bem como para todos os casos geridos subsequentes criados nessas sub-redes. Para todos os outros SQL, o suporte de observação está limitado às redes da mesma região devido aos [constrangimentos da rede virtual global de observação.](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) Consulte também a secção relevante das [Redes Virtuais Azure frequentemente fez perguntas](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) a artigo para mais detalhes. 

## <a name="connect-from-on-premises"></a>Ligar a partir de instalações 

Também pode ligar a sua aplicação no local à SQL Managed Instance. Sql Managed Instance só pode ser acedido através de um endereço IP privado. Para aceder a partir do local, é necessário fazer uma ligação site-to-site entre a aplicação e a rede virtual SQL Managed Instance.

Existem duas opções para como ligar no local a uma rede virtual Azure:

- Ligação VPN local-a-local[(portal Azure,](../../vpn-gateway/tutorial-site-to-site-portal.md) [PowerShell,](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) [Azure CLI)](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)
- [Ligação Azure ExpressRoute](../../expressroute/expressroute-introduction.md)  

Se estabeleceu uma ligação no local para a Azure com sucesso e não pode estabelecer uma ligação com a SQL Managed Instance, verifique se a sua firewall tem uma ligação de saída aberta na porta SQL 1433, bem como a gama de portas 11000-11999 para reorientação.

## <a name="connect-the-developer-box"></a>Ligue a caixa de desenvolvedores

Também é possível ligar a sua caixa de desenvolvedor a SQL Managed Instance. A SQL Managed Instance só pode ser acedida através de um endereço IP privado, pelo que, para aceder a partir da sua caixa de desenvolvedores, primeiro tem de fazer uma ligação entre a sua caixa de desenvolvedores e a rede virtual SQL Managed Instance. Para tal, configurar uma ligação ponto-a-local a uma rede virtual utilizando a autenticação de certificado azure nativo. Para obter mais informações, consulte  [configurar uma ligação ponto-a-local para ligar à Azure SQL Managed Instance a partir de um computador no local](point-to-site-p2s-configure.md).

## <a name="connect-with-vnet-peering"></a>Conecte-se com o espremiamento VNet

Outro cenário implementado pelos clientes é onde um gateway VPN é instalado numa rede virtual separada e subscrição do que hospeda a SQL Managed Instance. As duas redes virtuais são então espreitadas. O seguinte diagrama de arquitetura de amostra mostra como isto pode ser implementado.

![Peering de rede virtual](./media/connect-application-instance/vnet-peering.png)

Uma vez configurada a infraestrutura básica, precisa modificar algumas definições para que o gateway VPN possa ver os endereços IP na rede virtual que acolhe a SQL Managed Instance. Para tal, faça as seguintes alterações muito específicas nas **definições de Peering**.

1. Na rede virtual que acolhe o gateway VPN, vá a **Peerings,** vá à ligação de rede virtual esprevada para a SQL Managed Instance e, em seguida, clique em **Permitir o Trânsito de Gateway**.
2. Na rede virtual que acolhe a SQL Managed Instance, vá a **Peerings,** vá à ligação de rede virtual esprevada para o gateway VPN e, em seguida, clique em **Utilizar gateways remotos**.

## <a name="connect-azure-app-service"></a>Ligue o Serviço de Aplicações Azure 

Também pode ligar uma aplicação que é hospedada pelo Azure App Service. A SQL Managed Instance só pode ser acedida através de um endereço IP privado, pelo que, para aceder ao mesmo a partir do Azure App Service, primeiro precisa de fazer uma ligação entre a aplicação e a rede virtual SQL Managed Instance. Ver [Integrar a sua aplicação com uma rede virtual Azure.](../../app-service/web-sites-integrate-with-vnet.md)  

Para a resolução de problemas, consulte [redes virtuais e aplicações de resolução de problemas.](../../app-service/web-sites-integrate-with-vnet.md#troubleshooting) Se não for possível estabelecer uma [ligação, tente sincronizar a configuração de rede](azure-app-sync-network-configuration.md).

Um caso especial de ligação do Azure App Service à SQL Managed Instance é quando integra o Azure App Service a uma rede espreguiçadinha de uma rede virtual SQL Managed Instance. Este caso requer a configuração seguinte:

- A rede virtual SQL Managed Instance NÃO deve ter um portal  
- A rede virtual SQL Managed Instance deve ter o `Use remote gateways` conjunto de opções
- A rede virtual esprevada deve ter o `Allow gateway transit` conjunto de opções

Este cenário é ilustrado no seguinte diagrama:

![peering de aplicativo integrado](./media/connect-application-instance/integrated-app-peering.png)

>[!NOTE]
>A funcionalidade de integração de rede virtual não integra uma aplicação com uma rede virtual que tenha um gateway ExpressRoute. Mesmo que o gateway ExpressRoute esteja configurado em modo de coexistência, a integração de rede virtual não funciona. Se precisar de aceder a recursos através de uma ligação ExpressRoute, então pode utilizar o App Service Environment, que funciona na sua rede virtual.

## <a name="troubleshooting-connectivity-issues"></a>Troubleshooting connectivity issues (Resolver problemas de conectividade)

Para resolver problemas de conectividade, reveja o seguinte:

- Se não conseguir ligar-se à SQL Managed Instance a partir de uma máquina virtual Azure dentro da mesma rede virtual, mas de uma sub-rede diferente, verifique se tem um Grupo de Segurança de Rede definido na sub-rede VM que pode estar a bloquear o acesso. Adicionalmente, a ligação de saída aberta na porta SQL 1433, bem como as portas da gama 11000-11999, uma vez que são necessárias para a ligação através da reorientação dentro do limite Azure.
- Certifique-se de que a propagação do BGP está definida para **ativar** a tabela de rotas associada à rede virtual.
- Se utilizar o P2S VPN, verifique a configuração no portal Azure para ver se vê os **números de Ingress/Egress.** Números não nulos indicam que o Azure está a encaminhar o tráfego de/para o local.

   ![números de entradas/saídas](./media/connect-application-instance/ingress-egress-numbers.png)

- Verifique se a máquina do cliente (que está a executar o cliente VPN) tem entradas de rota para todas as redes virtuais a que necessita de aceder. As rotas estão armazenadas em `%AppData%\Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt` .

   ![route.txt](./media/connect-application-instance/route-txt.png)

   Como mostrado nesta imagem, existem duas entradas para cada rede virtual envolvida e uma terceira entrada para o ponto final VPN que está configurado no portal.

   Outra forma de verificar as rotas é através do seguinte comando. A saída mostra as rotas para as várias sub-redes:

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

- Se estiver a utilizar o espreitamento de rede virtual, certifique-se de que seguiu as instruções para definir [Allow Gateway Transit and Use Remote Gateways](#connect-from-on-premises).

- Se estiver a utilizar a rede virtual para ligar uma aplicação hospedada do Azure App Service e a rede virtual SQL Managed Instance tiver uma gama de endereços IP público, certifique-se de que as definições de aplicação hospedadas permitem que o tráfego de saída seja encaminhado para redes IP públicas. Siga as instruções de [integração regional da rede virtual.](../../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)

## <a name="required-versions-of-drivers-and-tools"></a>Versões necessárias de motoristas e ferramentas

Recomenda-se as seguintes versões mínimas das ferramentas e dos condutores se pretender ligar-se à SQL Managed Instance:

| Condutor/ferramenta | Versão |
| --- | --- |
|.NET Framework | 4.6.1 (ou.NET Core) |
|Controlador ODBC| v17 |
|Controlador PHP| 5.2.0 |
|Controlador JDBC| 6.4.0 |
|Controlador Node.js| 2.1.1 |
|Controlador OLEDB| 18.0.2.0 |
|SSMS| 18.0 ou [superior](/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) ou mais |

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre a SQL Managed Instance, consulte [o que é a Sql Managed Instance?](sql-managed-instance-paas-overview.md)
- Para um tutorial que lhe mostre como criar um novo exemplo gerido, consulte [Criar um caso gerido](instance-create-quickstart.md).