---
title: 'WAN virtual do Azure: criar conexões site a site'
description: Neste tutorial, vai aprender a utilizar uma WAN Virtual do Azure para criar uma ligação VPN de site a site ao Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: e17205af1ede845ea77b04f6f2b4c6babf3bc450
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482138"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Tutorial: Criar uma ligação site a site com a WAN Virtual do Azure

Este tutorial mostra-lhe como utilizar a WAN Virtual para se ligar aos seus recursos no Azure através de uma ligação VPN IPsec/IKE (IKEv1 e IKEv2). Este tipo de ligação requer um dispositivo VPN localizado no local que tenha um endereço IP público com acesso exterior atribuído ao mesmo. Para obter mais informações sobre a WAN Virtual, veja a [Descrição Geral da WAN Virtual](virtual-wan-about.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma WAN Virtual
> * Criar um hub
> * Criar um site
> * Conectar um site a um hub
> * Conectar um site VPN a um hub
> * Ligar uma VNet a um hub
> * Baixar um arquivo de configuração
> * Ver a WAN Virtual

> [!NOTE]
> Se tiver muitos sites, teria de utilizar, normalmente, um [parceiro de WAN Virtual](https://aka.ms/virtualwan) para criar esta configuração. Contudo, pode criá-la sozinho se se sentir à vontade com o trabalho em rede e souber configurar o seu próprio dispositivo VPN.
>

![Diagrama da WAN Virtual](./media/virtual-wan-about/virtualwan.png)

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* Você tem uma rede virtual à qual deseja se conectar. Verifique se nenhuma das sub-redes de suas redes locais se sobrepõe às redes virtuais às quais você deseja se conectar. Para criar uma rede virtual no portal do Azure, consulte o guia de [início rápido](../virtual-network/quick-create-portal.md).

* Sua rede virtual não tem nenhum gateway de rede virtual. Se sua rede virtual tiver um gateway (VPN ou ExpressRoute), você deverá remover todos os gateways. Essa configuração requer que as redes virtuais estejam conectadas ao gateway do Hub WAN virtual.

* Obtenha um intervalo de endereços IP para a região do seu hub. O Hub é uma rede virtual que é criada e usada pela WAN virtual. O intervalo de endereços especificado para o Hub não pode se sobrepor a nenhuma das redes virtuais existentes às quais você se conecta. Também não se pode sobrepor aos intervalos de endereços a que se ligue no local. Se você não estiver familiarizado com os intervalos de endereços IP localizados em sua configuração de rede local, coordene com alguém que possa fornecer esses detalhes para você.

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="openvwan"></a>Criar uma WAN virtual

Em um navegador, navegue até a portal do Azure e entre com sua conta do Azure.

1. Navegue até a página WAN virtual. No portal, clique em **Criar um recurso**. Digite **Wan virtual** na caixa de pesquisa e selecione Enter.
2. Selecione **Wan virtual** nos resultados. Na página WAN virtual, clique em **criar** para abrir a página Criar Wan.
3. Na página **criar Wan** , na guia **noções básicas** , preencha os seguintes campos:

   ![WAN Virtual](./media/virtual-wan-site-to-site-portal/vwan.png)

   * **Subscription** (subscrição) - selecione a subscrição que quer utilizar.
   * **Grupo de recursos** -criar novo ou usar existente.
   * **Local do grupo de recursos** -escolha um local de recurso no menu suspenso. Uma WAN é um recurso global e não reside numa região específica. Contudo, tem de selecionar uma região para poder gerir e localizar mais facilmente o recurso WAN que criou.
   * **Nome** -digite o nome que você deseja chamar para sua Wan.
   * **Tipo:** Básico ou Standard. Se você criar uma WAN básica, poderá criar apenas um hub básico. Os hubs básicos são capazes de somente conectividade VPN site a site.
4. Depois de concluir o preenchimento dos campos, selecione **revisar + criar**.
5. Depois que a validação for aprovada, selecione **criar** para criar a WAN virtual.

## <a name="hub"></a>Criar um hub

Um hub é uma rede virtual que pode conter gateways para a funcionalidade site a site, ExpressRoute ou ponto a site. Depois de criar o hub, vai ser cobrado pelo hub, mesmo que não anexe quaisquer sites. Leva 30 minutos para criar o gateway de VPN site a site no Hub virtual.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="site"></a>Criar um site

Agora você está pronto para criar os sites correspondentes aos seus locais físicos. Crie tantos sites qantos necessários para corresponder às suas localizações físicas. Por exemplo, se tiver uma sucursal em Nova Iorque, uma em Londres e outra em Lisboa, tem de criar três sites separados. Esses sites contêm os pontos finais dos seus dispositivos VPN no local. Você pode criar até 1000 sites por Hub virtual em uma WAN virtual. Se você tivesse vários hubs, poderá criar 1000 por cada um desses hubs. Se você tiver um dispositivo CPE de parceiro de WAN virtual (link INSERT), verifique com eles para saber mais sobre sua automação para o Azure. Normalmente, a automação implica uma experiência de clique simples para exportar informações de Branch em larga escala no Azure e configurar a conectividade do CPE para o gateway de VPN de WAN virtual do Azure (aqui está um link para as diretrizes de automação do Azure para parceiros de CPE).

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connectsites"></a>Conectar o site VPN ao Hub

Nesta etapa, você conecta o site de VPN ao Hub.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="vnet"></a>Conectar a VNet ao Hub

Nesta etapa, você cria a conexão entre o Hub e uma VNet. Repita estes passos para cada VNet que queira ligar.

1. Na página da WAN virtual, clique em **Ligações de rede virtual**.
2. Na página de ligação da rede virtual, clique em **+Add connection** (+Adicionar ligação).
3. Na página **Add connection** (Adicionar ligação), preencha os seguintes campos:

    * **Connection name** (Nome da ligação) - dê um nome à ligação.
    * **Hubs** - selecione o hub que pretende associar a esta ligação.
    * **Subscription** (Subscrição) - verifique a subscrição.
    * **Virtual network** (Rede virtual) - selecione a rede virtual que pretende ligar a este hub. A rede virtual não pode ter um gateway de rede virtual já existente.
4. Clique em **OK** para criar a conexão de rede virtual.

## <a name="device"></a>Baixar configuração de VPN

Utilize a configuração do dispositivo VPN para configurar o seu dispositivo VPN no local.

1. Na página da WAN virtual, clique em **Overview** (Descrição geral).
2. Na parte superior da página **Hub-> VPNSite** , clique em **baixar configuração de VPN**. O Azure cria uma conta de armazenamento no grupo de recursos ' Microsoft-Network-[Location] ', em que Location é o local da WAN. Depois de aplicar a configuração aos dispositivos VPN, pode eliminar esta conta de armazenamento.
3. Após a conclusão da criação do ficheiro, pode clicar na ligação para transferi-lo.
4. Aplique a configuração ao seu dispositivo VPN local.

### <a name="understanding-the-vpn-device-configuration-file"></a>Compreender o ficheiro de configuração do dispositivo VPN

O ficheiro de configuração do dispositivo contém as definições que vão ser utilizadas para configurar o dispositivo VPN no local. Quando vir este ficheiro, repare nas informações seguintes:

* **vpnSiteConfiguration -** esta secção mostra os detalhes do dispositivo configurados como site que se vai ligar à WAN virtual. Inclui o nome e o endereço IP público do dispositivo da sucursal.
* **vpnSiteConnections-** Esta seção fornece informações sobre as seguintes configurações:

    * **Espaço de endereços** da VNet do hub ou hubs virtuais.<br>Exemplo:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Espaço de endereços** das VNets que estão ligadas ao hub<br>Exemplo:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **Endereços IP** do vpngateway do hub virtual. Como cada conexão do vpngateway é composta de dois túneis na configuração ativa-ativa, você verá os dois endereços IP listados nesse arquivo. Neste exemplo, vê "Instance0" e "Instance1" para cada site.<br>Exemplo:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Detalhes de configuração de conexão Vpngateway** , como BGP, chave pré-compartilhada, etc. A PSK é a chave pré-compartilhada que é gerada automaticamente para você. Pode sempre editar a ligação na página Overview (Descrição geral) de um PSK personalizado.
  
### <a name="example-device-configuration-file"></a>Exemplo de ficheiro de configuração de dispositivo

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.30.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a>Configurar o dispositivo VPN

>[!NOTE]
> Se está a trabalhar com uma solução de parceiros do WAN Virtual, a configuração do dispositivo VPN acontece automaticamente. O controlador de dispositivo obtém o ficheiro de configuração do Azure e aplica o dispositivo para configurar a ligação ao Azure. Isto significa que não é preciso saber como configurar o dispositivo VPN manualmente.
>

Se precisar de instruções para configurar o dispositivo, pode utilizar as instruções na [página VPN device configuration scripts](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) (Scripts de configuração de dispositivos VPN), tendo em conta os seguintes avisos:

* As instruções a página de dispositivos VPN não foram escritas para a WAN Virtual, mas pode utilizar os valores desta a partir do ficheiro de configuração para configurar o seu dispositivo VPN manualmente. 
* Os scripts de configuração do dispositivo transferíveis que se destinam ao Gateway de VPN não funcionam para a WAN Virtual, uma vez que a configuração é diferente.
* Uma nova WAN virtual pode dar suporte A IKEv1 e IKEv2.
* A WAN virtual pode usar dispositivos VPN baseados em políticas e rotas e instruções de dispositivo.

## <a name="viewwan"></a>Exibir sua WAN virtual

1. Navegue para a WAN virtual.
2. Na página **visão geral** , cada ponto no mapa representa um Hub. Passe o mouse sobre qualquer ponto para exibir o resumo de integridade do Hub, o status da conexão e os bytes de entrada e saída.
3. Na seção hubs e conexões, você pode exibir o status do Hub, sites VPN, etc. Você pode clicar em um nome de Hub específico e navegar até o site VPN para obter detalhes adicionais.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).
