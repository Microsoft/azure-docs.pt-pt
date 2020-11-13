---
title: Azure Virtual WAN parceiros de automação diretrizes de automação / Microsoft Docs
description: Crie um ambiente de automatização para ligar e configurar um VPN ou SD-WAN CPE, ou dispositivo de ramificação, para a Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 29fff3a6a430e3bc1a0b3a13876b55d22f7cb545
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566474"
---
# <a name="automation-guidelines-for-virtual-wan-partners"></a>Diretrizes de automatização para parceiros de WAN Virtual

Este artigo ajuda-o a entender como configurar o ambiente de automatização para conectar e configurar um dispositivo de filial (um dispositivo VPN ou SDWAN CPE) para Azure Virtual WAN. Se é um fornecedor que fornece dispositivos de ramificação que podem acomodar a conectividade VPN sobre iPsec/IKEv2 ou IPsec/IKEv1, este artigo é para si.

Um dispositivo de ramificação (um dispositivo VPN ou SDWAN CPE) normalmente utiliza um controlador/painel de instrumentos a ser a provisionado. Os administradores de soluções SD-WAN podem frequentemente utilizar uma consola de gestão para pré-fornecimento de um dispositivo antes de ser ligado à rede. Este dispositivo capaz de VPN obtém a sua lógica de plano de controlo a partir de um controlador. O dispositivo VPN ou controlador SD-WAN pode usar APIs Azure para automatizar a conectividade ao Azure Virtual WAN. Este tipo de ligação requer que o dispositivo no local tenha um endereço IP público virado para o exterior.

## <a name="before-you-begin-automating"></a><a name ="before"></a>Antes de começar a automatizar

* Verifique se o seu dispositivo suporta iPsec IKEv1/IKEv2. Consulte [as políticas predefinidos](#default).
* Consulte as [APIs REST](#additional) que utiliza para automatizar a conectividade ao Azure Virtual WAN.
* Teste a experiência do portal da Azure Virtual WAN.
* Em seguida, decida qual parte dos passos de conectividade que gostaria de automatizar. No mínimo, recomendamos automatizar:

  * Controlo de Acesso
  * Upload de informações de dispositivos de ramo em Azure Virtual WAN
  * Descarregar a configuração do Azure e configurar a conectividade do dispositivo de ramo para o Azure Virtual WAN

### <a name="additional-information"></a><a name ="additional"></a>Informação adicional

* [REST API](/rest/api/virtualwan/virtualhubs) para automatizar a criação do Virtual Hub
* [REST API](/rest/api/virtualwan/vpngateways) para automatizar gateway Azure VPN para WAN Virtual
* [REST API](/rest/api/virtualwan/vpnconnections) para ligar um VPNSite a um Hub Azure VPN
* [Políticas IPsec predefinidos](#default)

## <a name="customer-experience"></a><a name ="ae"></a>Experiência do cliente

Compreenda a experiência esperada do cliente em conjunto com a Azure Virtual WAN.

  1. Normalmente, um utilizador virtual WAN iniciará o processo criando um recurso WAN virtual.
  2. O utilizador irá criar um acesso de grupo de recursos baseado em serviço para o sistema de fornecimento de dispositivos no local (o seu controlador de filial ou software de fornecimento de dispositivos VPN) para escrever informações de filial em Azure Virtual WAN.
  3. O utilizador pode decidir neste momento iniciar sessão na sua UI e configurar as principais credenciais do serviço. Uma vez concluído, o seu controlador deverá ser capaz de carregar informações de filiais com a automatização que irá fornecer. O equivalente manual deste tipo no lado Azure é 'Criar Site'.
  4. Uma vez que a informação do Site (dispositivo de ramo) esteja disponível no Azure, o utilizador ligará o site a um hub. Um hub virtual é uma rede virtual gerida pela Microsoft. O hub contém vários pontos finais de serviço para ativar a conectividade da rede no local (vpnsite). O hub é o núcleo da sua rede numa região. Só pode haver um hub por região de Azure e o ponto final vpn (vpngateway) no seu interior é criado durante este processo. O gateway VPN é um gateway escalável que dimensiona adequadamente com base na largura de banda e necessidades de conexão. Pode optar por automatizar o hub virtual e a criação de vpngateway a partir do painel de controlo do dispositivo de ramificação.
  5. Uma vez que o Hub virtual está associado ao site, é gerado um ficheiro de configuração para o utilizador descarregar manualmente. É aqui que entra a sua automatização e torna a experiência do utilizador perfeita. Em vez de o utilizador ter de descarregar manualmente e configurar o dispositivo do ramo, pode definir a automatização e fornecer uma experiência mínima de clique na sua UI, aliviando assim problemas típicos de conectividade, tais como incompatibilidade de chave partilhada, desfasamento de parâmetros IPSec, legibilidade de ficheiros de configuração, etc.
  6. No final deste passo na sua solução, o utilizador terá uma ligação site-to-site sem emenda entre o dispositivo de ramo e o hub virtual. Também pode configurar ligações adicionais em outros centros. Cada ligação é um túnel ativo. O seu cliente pode optar por utilizar um ISP diferente para cada um dos links para o túnel.
  7. Considere fornecer capacidades de resolução e monitorização de problemas na interface de gestão cpe. Os cenários típicos incluem "Cliente não capaz de aceder aos recursos Azure devido a um problema de CPE", "Mostrar parâmetros IPsec no lado do CPE" etc.

## <a name="automation-details"></a><a name ="understand"></a>Detalhes da automatização

###  <a name="access-control"></a><a name="access"></a>Controlo de acesso

Os clientes devem ser capazes de configurar um controlo de acesso adequado para o WAN virtual na UI do dispositivo. Isto é recomendado usando um diretor de serviço Azure. O acesso principal do serviço fornece ao controlador do dispositivo a autenticação adequada para carregar informações sobre o ramo. Para mais informações, consulte [Criar o principal do serviço.](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) Embora esta funcionalidade esteja fora da oferta Azure Virtual WAN, listamos abaixo os passos típicos dados para configurar o acesso em Azure após o qual os detalhes relevantes são introduzidos no painel de gestão do dispositivo

* Crie uma aplicação Azure Ative Directory para o seu controlador de dispositivos no local.
* Obtenha iD de aplicação e chave de autenticação
* Obter o ID de inquilino
* Atribuir candidatura à função "Contribuinte"

###  <a name="upload-branch-device-information"></a><a name="branch"></a>Enviar informações sobre dispositivos de filial

Deve conceber a experiência do utilizador para enviar informações de sucursais (no local) para a Azure. Pode utilizar [APIs REST](/rest/api/virtualwan/vpnsites) para VPNSite para criar as informações do site em VIRTUAL WAN. Pode fornecer todos os dispositivos SDWAN/VPN de ramo ou selecionar personalizações de dispositivos conforme apropriado.

### <a name="device-configuration-download-and-connectivity"></a><a name="device"></a>Descarregamento de configuração do dispositivo e conectividade

Este passo envolve o download da configuração Azure e a configuração da conectividade do dispositivo de ramificação para a Azure Virtual WAN. Neste passo, um cliente que não utilize um fornecedor descarregaria manualmente a configuração Azure e aplicava-a no seu dispositivo SDWAN/VPN no local. Como provedor, deve automatizar este passo. Consulte as [APIs de](/rest/api/virtualwan/vpnsitesconfiguration/download) descarregamento para obter informações adicionais. O controlador do dispositivo pode chamar a API DE REST 'GetVpnConfiguration' para descarregar a configuração Azure.

**Notas de configuração**

  * Se os VNets Azure estiverem ligados ao centro virtual, aparecerão como ConnectedSubnets.
  * A conectividade VPN utiliza configuração baseada em rotas e suporta os protocolos IKEv1 e IKEv2.

## <a name="device-configuration-file"></a><a name="devicefile"></a>Ficheiro de configuração do dispositivo

O ficheiro de configuração do dispositivo contém as definições que vão ser utilizadas para configurar o dispositivo VPN no local. Quando vir este ficheiro, repare nas informações seguintes:

* **vpnSiteConfiguration -** esta secção mostra os detalhes do dispositivo configurados como site que se vai ligar à WAN virtual. Inclui o nome e o endereço IP público do dispositivo da sucursal.
* **vpnSiteConnections -** esta secção disponibiliza informações sobre o seguinte:

    * **Espaço** de endereço do(s) hub virtual(s) VNet.<br>Exemplo:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Espaço** de endereço dos VNets que estão ligados ao hub.<br>Exemplo:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **Endereços IP** do vpngateway do hub virtual. Uma vez que o vpngateway tem ambas as ligações compostas por dois túneis na configuração ativo-ativo, verá os dois endereços IP indicados neste ficheiro. Neste exemplo, vê "Instance0" e "Instance1" para cada site.<br>Exemplo:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Detalhes de configuração de ligação vpngateway,** tais como BGP, chave pré-partilhada, etc. O PSK é a chave pré-partilhada que é gerada automaticamente para si. Pode sempre editar a ligação na página Overview (Descrição geral) de um PSK personalizado.
  
**Exemplo de ficheiro de configuração de dispositivo**

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

## <a name="connectivity-details"></a><a name="default"></a>Detalhes da conectividade

O seu dispositivo SDWAN/VPN no local ou a configuração SD-WAN devem coincidir ou conter os seguintes algoritmos e parâmetros, que especifica na política Azure IPsec/IKE.

* Algoritmo de encriptação do IKE
* Algoritmo de integridade do IKE
* Grupo DH
* Algoritmo de encriptação do IPsec
* Algoritmo de integridade do IPsec
* Grupo PFS

### <a name="default-policies-for-ipsec-connectivity"></a><a name="default"></a>Políticas predefinidas para conectividade IPsec

[!INCLUDE [IPsec Default](../../includes/virtual-wan-ipsec-include.md)]

### <a name="custom-policies-for-ipsec-connectivity"></a><a name="custom"></a>Políticas personalizadas para conectividade IPsec

[!INCLUDE [IPsec Custom](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o WAN Virtual, consulte [About Azure Virtual WAN](virtual-wan-about.md) e o [Azure Virtual WAN FAQ](virtual-wan-faq.md).

Para mais informações, envie um e-mail para <azurevirtualwan@microsoft.com> . Inclua o nome da sua empresa em "[ ]" na linha de assunto.