---
title: Azure Virtual WAN parceiros diretrizes de automação / Microsoft Docs
description: Este artigo ajuda os parceiros a criar a automação Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: cherylmc
ms.openlocfilehash: 7848dda09b39f446dd218b7ce1eb2a07664bcaa6
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190423"
---
# <a name="automation-guidelines-for-virtual-wan-partners"></a>Diretrizes de automação para parceiros Virtuais WAN

Este artigo ajuda-o a compreender como configurar o ambiente de automação para ligar e configurar um dispositivo de ramificação (um dispositivo VPN do cliente no local ou cpE SDWAN) para o Azure Virtual WAN. Se é um fornecedor que fornece dispositivos de sucursais que podem acomodar a conectividade VPN através de IPsec/IKEv2 ou IPsec/IKEv1, este artigo é para si.

Um dispositivo de sucursal (um dispositivo VPN do cliente no local ou CPE SDWAN) normalmente utiliza um painel de instrumentos de controlador/dispositivo para ser provisionado. Os administradores de soluções SD-WAN podem frequentemente utilizar uma consola de gestão para pré-fornecer um dispositivo antes de ser ligado à rede. Este dispositivo capaz de VPN obtém a sua lógica de plano de controlo a partir de um controlador. O dispositivo VPN ou o controlador SD-WAN podem utilizar APIs Azure para automatizar a conectividade com o Bluee Virtual WAN. Este tipo de ligação requer que o dispositivo no local tenha um endereço IP público virado para o exterior.

## <a name ="before"></a>Antes de começar a automatizar

* Verifique se o seu dispositivo suporta IPsec IKEv1/IKEv2. Consulte [as políticas predefinidas](#default).
* Veja as [APIs REST](#additional) que utiliza para automatizar a conectividade com o Azure Virtual WAN.
* Teste a experiência do portal do Azure Virtual WAN.
* Então, decida qual a parte dos passos de conectividade que gostaria de automatizar. No mínimo, recomendamos automatizar:

  * Controlo de Acesso
  * Upload de informações do dispositivo de sucursal para O WAN Virtual Azure
  * Descarregar a configuração do Azure e estabelecer conectividade do dispositivo de ramificação para o Azure Virtual WAN

### <a name ="additional"></a>Informações adicionais

* [REST API](https://docs.microsoft.com/rest/api/virtualwan/virtualhubs) para automatizar criação do Virtual Hub
* [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpngateways) para automatizar porta de entrada Azure VPN para Wan Virtual
* [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpnconnections) para ligar um VPNSite a um Hub VPN Azure
* [Políticas de IPsec padrão](#default)

## <a name ="ae"></a>Experiência do cliente

Compreenda a experiência esperada do cliente em conjunto com o Azure Virtual WAN.

  1. Normalmente, um utilizador virtual de WAN iniciará o processo criando um recurso Virtual WAN.
  2. O utilizador irá criar um serviço principal de acesso ao grupo de recursos para o sistema no local (o seu controlador de sucursal ou software de provisionamento de dispositivos VPN) para escrever informações de sucursais em Azure Virtual WAN.
  3. O utilizador pode decidir neste momento iniciar sessão na Sua UI e configurar as credenciais principais do serviço. Uma vez concluído, o seu controlador deverá ser capaz de carregar informações do ramo com a automatização que irá fornecer. O equivalente manual deste no lado Azure é 'Create Site'.
  4. Assim que a informação do Site (dispositivo de ramificação) estiver disponível no Azure, o utilizador ligará o site a um hub. Um hub virtual é uma rede virtual gerida pela Microsoft. O hub contém vários pontos finais de serviço para ativar a conectividade da rede no local (vpnsite). O hub é o núcleo da sua rede numa região. Só pode haver um hub por região de Azure e o ponto final vpn (vpngateway) no seu interior é criado durante este processo. O gateway VPN é um gateway escalável que dimensiona adequadamente com base na largura de banda e necessidades de ligação. Pode optar por automatizar o hub virtual e a criação de vpngateway a partir do painel de controlador do seu dispositivo de ramificação.
  5. Uma vez que o Hub virtual está associado ao site, um ficheiro de configuração é gerado para o utilizador descarregar manualmente. É aqui que entra a sua automatização e torna a experiência do utilizador perfeita. Em vez de o utilizador ter de descarregar manualmente e configurar o dispositivo de ramificação, pode definir a automatização e fornecer uma experiência mínima de clique no seu UI, aliviando assim problemas típicos de conectividade, tais como desfasamento de chave partilhada, parâmetro IPSec incompatibilidade, capacidade de configuração de ficheiros, etc.
  6. No final deste passo na sua solução, o utilizador terá uma ligação local-local sem emenda entre o dispositivo do ramo e o hub virtual. Também pode configurar ligações adicionais em outros centros. Cada ligação é um túnel ativo. O seu cliente pode optar por utilizar um ISP diferente para cada uma das ligações para o túnel.
  7. Considere fornecer capacidades de resolução e monitorização de problemas na interface de gestão do CPE. Os cenários típicos incluem "Cliente incapaz de aceder aos recursos do Azure devido a um problema cpe", "Mostrar parâmetros IPsec no lado do CPE" etc.

## <a name ="understand"></a>Detalhes da automação

###  <a name="access"></a>Controlo de acessos

Os clientes devem ser capazes de criar um controlo de acesso adequado para o Wan Virtual no UI do dispositivo. Isto é recomendado usando um Diretor de Serviço Azure. O acesso baseado no principal do serviço fornece ao controlador do dispositivo a autenticação adequada para carregar informações do ramo. Para mais informações, consulte [Criar o principal de serviço.](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) Embora esta funcionalidade esteja fora da oferta Azure Virtual WAN, listamos abaixo dos passos típicos dados para configurar o acesso em Azure após os quais os detalhes relevantes são incorporados no painel de gestão do dispositivo

* Crie uma aplicação Azure Ative Directory para o seu controlador de dispositivos no local.
* Obtenha id de aplicação e chave de autenticação
* Obter o ID de inquilino
* Atribuir candidatura ao papel "Contribuinte"

###  <a name="branch"></a>Enviar informações sobre o dispositivo do ramo

Deve projetar a experiência do utilizador para carregar informações do ramo (no local) para o Azure. Pode utilizar [APIs REST](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) para VPNSite para criar as informações do site em Wan Virtual. Pode fornecer todos os dispositivos SDWAN/VPN ou selecionar personalizações de dispositivos conforme apropriado.

### <a name="device"></a>Download e conectividade de configuração do dispositivo

Este passo envolve o download da configuração do Azure e a configuração da conectividade do dispositivo de ramificação para o Azure Virtual WAN. Neste passo, um cliente que não esteja a utilizar um fornecedor descarregaria manualmente a configuração Do Azure e aplicava-a ao seu dispositivo SDWAN/VPN no local. Como provedor, deve automatizar este passo. Consulte as [APIs](https://docs.microsoft.com/rest/api/virtualwan/vpnsitesconfiguration/download) de descarregamento rest para obter informações adicionais. O controlador do dispositivo pode chamar 'GetVpnConfiguration' REST API para descarregar a configuração Azure.

**Notas de configuração**

  * Se os VNets Azure estiverem ligados ao centro virtual, aparecerão como ConnectedSubnets.
  * A conectividade VPN utiliza a configuração baseada em rotas e suporta os protocolos IKEv1 e IKEv2.

## <a name="devicefile"></a>Ficheiro de configuração do dispositivo

O ficheiro de configuração do dispositivo contém as definições que vão ser utilizadas para configurar o dispositivo VPN no local. Quando vir este ficheiro, repare nas informações seguintes:

* **vpnSiteConfiguration -** esta secção mostra os detalhes do dispositivo configurados como site que se vai ligar à WAN virtual. Inclui o nome e o endereço IP público do dispositivo da sucursal.
* **vpnSiteConnections -** esta secção disponibiliza informações sobre o seguinte:

    * **Espaço** de endereço do(s) VNet do centro virtual.<br>Exemplo:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Espaço de endereço** dos VNets que estão ligados ao centro.<br>Exemplo:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **Endereços IP** do vpngateway do hub virtual. Uma vez que o vpngateway tem ambas as ligações compostas por dois túneis na configuração ativo-ativo, verá os dois endereços IP indicados neste ficheiro. Neste exemplo, vê "Instance0" e "Instance1" para cada site.<br>Exemplo:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * Detalhes de configuração de **ligação vpngateway** tais como BGP, chave pré-partilhada, etc. O PSK é a chave pré-partilhada que é gerada automaticamente para si. Pode sempre editar a ligação na página Overview (Descrição geral) de um PSK personalizado.
  
**Ficheiro de configuração do dispositivo de exemplo**

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
                  "10.3.0.0/16"
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

## <a name="default"></a>Detalhes da conectividade

O seu dispositivo SDWAN/VPN ou a configuração SD-WAN devem coincidir ou conter os seguintes algoritmos e parâmetros, que especifica na política Azure IPsec/IKE.

* Algoritmo de encriptação do IKE
* Algoritmo de integridade do IKE
* Grupo DH
* Algoritmo de encriptação do IPsec
* Algoritmo de integridade do IPsec
* Grupo PFS

### <a name="default"></a>Políticas padrão para conectividade IPsec

[!INCLUDE [IPsec Default](../../includes/virtual-wan-ipsec-include.md)]

### <a name="custom"></a>Políticas personalizadas para conectividade IPsec

[!INCLUDE [IPsec Custom](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Wan Virtual, consulte [o Azure Virtual WAN](virtual-wan-about.md) e o [Azure Virtual WAN FAQ](virtual-wan-faq.md).

Para mais informações, por favor envie um e-mail para <azurevirtualwan@microsoft.com>. Inclua o nome da sua empresa em "[ ]" na linha de assunto.
