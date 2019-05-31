---
title: Parceiros de Virtual WAN do Azure | Documentos da Microsoft
description: Este artigo ajuda os parceiros de configurar a automatização de WAN Virtual do Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: cherylmc
ms.openlocfilehash: f286c02e0eb6e801f62d4f2e16f1197a1e9d44ce
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304556"
---
# <a name="virtual-wan-partners"></a>Parceiros WAN virtual

Este artigo ajuda-o a compreender como configurar o ambiente de automatização para se ligar e configurar um dispositivo de ramificação (um dispositivo VPN do cliente no local ou SDWAN CPE) para o WAN Virtual do Azure. Se for um fornecedor que fornece a dispositivos de ramificação que podem acomodar a conectividade VPN através de IPsec/IKEv2 ou IPsec/IKEv1, este artigo é para.

Um dispositivo de ramificação (um dispositivo VPN no local de cliente ou SDWAN CPE) normalmente usa um dashboard de controlador/dispositivo a ser aprovisionado. Os administradores de solução SD-WAN, muitas vezes, podem utilizar uma consola de gestão pré-aprovisionar um dispositivo antes de ele obtém ligado à rede. Este dispositivo com capacidade de VPN obtém sua lógica de plano de controlo de um controlador. O dispositivo VPN ou SD-WAN controlador pode utilizar os APIs do Azure para automatizar a conectividade WAN Virtual do Azure. Este tipo de ligação requer o dispositivo no local para ter um endereço IP público com acesso exterior atribuído ao mesmo.

## <a name ="before"></a>Antes de começar a automatizar

* Certifique-se de que o dispositivo oferece suporte a IPsec IKEv1/IKEv2. Ver [predefinido políticas](#default).
* Consulte a [REST APIs](https://docs.microsoft.com/rest/api/azure/) que irá utilizar para automatizar a conectividade WAN Virtual do Azure.
* Testar a experiência do portal de WAN Virtual do Azure.
* Em seguida, decida que parte dos passos de conectividade que pretende automatizar. No mínimo, recomendamos automatizar:

  * Controlo de Acesso
  * Carregamento de informações de dispositivos de ramo para o WAN Virtual do Azure
  * Transferir configuração do Azure e a configuração de conectividade do dispositivo de ramo em WAN Virtual do Azure

* Compreenda a experiência do cliente esperado em conjunto com o WAN Virtual do Azure.

  1. Normalmente, um utilizador WAN virtual iniciará o processo através da criação de um recurso de Virtual WAN.
  2. O utilizador irá configurar um acesso de grupo de recursos baseada em principais de serviço para o sistema no local (seu controlador de ramo ou software de aprovisionamento de dispositivos VPN) para gravar informações do ramo no WAN Virtual do Azure.
  3. O utilizador pode decidir neste momento para iniciar sessão na sua interface do Usuário e configurar as credenciais do principal de serviço. Assim que estiver concluído, seu controlador deve ser capaz de carregar informações do ramo com a automação, deve fornecer. Seu equivalente manual do lado do Azure é "Criar Site".
  4. Assim que as informações do Site (dispositivo de filial) estão disponíveis no Azure, o utilizador irá associar o site para um hub. Um hub virtual é uma rede virtual gerida pela Microsoft. O hub contém vários pontos finais de serviço para ativar a conectividade da rede no local (vpnsite). O hub é o núcleo da sua rede numa região. Só pode existir um hub por região do Azure e o ponto final de vpn (vpngateway) de no interior do mesmo é criado durante este processo. O gateway de VPN é um gateway dimensionável que os tamanhos adequadamente, com base na largura de banda e ligação necessidades. Pode optar automatizar o virtual hub e a criação de vpngateway do seu dashboard de controlador de dispositivo do ramo.
  5. Assim que o Hub virtual está associado ao site, um ficheiro de configuração é gerado para o utilizador transfira manualmente. É onde sua automação entra em ação e torna o experiência de utilizador totalmente integrada. Em vez do utilizador ter de transferir e configurar o dispositivo de ramo manualmente, pode definir a automação e fornecer a experiência de início de Clickthrough mínima em sua interface do Usuário, deste modo, resolvendo problemas problemas de conectividade típicas, tais como a incompatibilidade de chave partilhada, o parâmetro de IPSec Erro de correspondência, legibilidade de ficheiro de configuração etc.
  6. No final deste passo na sua solução, o usuário terá uma ligação site a site totalmente integrada entre o dispositivo de filial e o virtual hub. Também pode configurar ligações adicionais em outros hubs. Cada ligação é um túnel de ativo-ativo. Seu cliente pode optar por utilizar um ISP diferente para cada uma das ligações para o túnel.

## <a name ="understand"></a>Compreender os detalhes de automatização


###  <a name="access"></a>Controlo de acesso

Os clientes tem de ser capazes de configurar o controlo de acesso apropriados para o Virtual WAN na IU do dispositivo. Isto é recomendado utilizar um Principal de serviço do Azure. O acesso de baseada em principais de serviço fornece a autenticação adequada de controlador de dispositivo para carregar as informações do ramo. Para obter mais informações, consulte [criar principal de serviço](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Enquanto esta funcionalidade está fora da oferta de WAN Virtual do Azure, listados abaixo as etapas típicas direcionadas para configurar o acesso no Azure após o qual os detalhes relevantes são introduzidos para o dashboard de gestão de dispositivos

* Crie uma aplicação do Azure Active Directory para seu controlador de dispositivo no local.
* Obter a chave de ID e a autenticação da aplicação
* Obter o ID de inquilino
* Atribua a aplicação à função "Contribuinte"

###  <a name="branch"></a>Carregar informações de dispositivo do ramo

Crie a experiência do usuário para carregar informações do ramo (site no local) para o Azure. [REST APIs](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) para VPNSite pode ser utilizado para criar as informações do site na Virtual WAN. Pode fornecer todos os dispositivos SDWAN/VPN ramo ou selecionar personalizações de dispositivo conforme apropriado.


### <a name="device"></a>A transferência de configuração do dispositivo e conectividade

Este passo envolve transferir configuração do Azure e a configuração de conectividade do dispositivo ramo em WAN Virtual do Azure. Neste passo, faria um cliente que não está a utilizar um fornecedor manualmente transferir a configuração do Azure e aplicá-la para o respetivo dispositivo SDWAN/VPN no local. Como um fornecedor, pode automatizar este passo. O controlador de dispositivo pode chamar a API de REST de "GetVpnConfiguration" para transferir a configuração do Azure, que normalmente será semelhante ao seguinte ficheiro.

**Notas de configuração**

  * Se as VNets do Azure estão ligados ao concentrador virtual, serão apresentados como ConnectedSubnets.
  * Conectividade VPN utiliza a configuração baseada em rota e IKEv2/IKEv1.

#### <a name="understanding-the-device-configuration-file"></a>Noções básicas sobre o ficheiro de configuração do dispositivo

O ficheiro de configuração do dispositivo contém as definições que vão ser utilizadas para configurar o dispositivo VPN no local. Quando vir este ficheiro, repare nas informações seguintes:

* **vpnSiteConfiguration -** esta secção mostra os detalhes do dispositivo configurados como site que se vai ligar à WAN virtual. Inclui o nome e o endereço IP público do dispositivo da sucursal.
* **vpnSiteConnections -** esta secção disponibiliza informações sobre o seguinte:

    * **Espaço de endereços** do virtual no hub ou hubs VNet.<br>Exemplo:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Espaço de endereços** das VNets que estão ligadas ao hub.<br>Exemplo:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **Endereços IP** do vpngateway do hub virtual. Uma vez que o vpngateway tem ambas as ligações compostas por dois túneis na configuração ativo-ativo, verá os dois endereços IP indicados neste ficheiro. Neste exemplo, vê "Instance0" e "Instance1" para cada site.<br>Exemplo:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Detalhes da configuração da ligação Vpngateway**, como BGP, chave pré-partilhada, etc. O PSK é a chave pré-partilhada que é gerada automaticamente para si. Pode sempre editar a ligação na página Overview (Descrição geral) de um PSK personalizado.
  
#### <a name="example-device-configuration-file"></a>Exemplo de ficheiro de configuração de dispositivo

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

## <a name="default"></a>Políticas predefinidas para conectividade IPsec

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-include.md)]

### <a name="does-everything-need-to-match-between-the-virtual-hub-vpngateway-policy-and-my-on-premises-sdwanvpn-device-or-sd-wan-configuration"></a>Tudo o que precisa corresponder entre a política do virtual hub vpngateway e meu dispositivo SDWAN/VPN no local ou de configuração SD-WAN?

O dispositivo SDWAN/VPN no local ou SD-WAN configuração tem de corresponder ou conter os seguintes algoritmos e parâmetros, que pode especificar na política de IPsec/IKE do Azure.

* Algoritmo de encriptação do IKE
* Algoritmo de integridade do IKE
* Grupo DH
* Algoritmo de encriptação do IPsec
* Algoritmo de integridade do IPsec
* Grupo PFS

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Virtual WAN, consulte [sobre a Azure Virtual WAN](virtual-wan-about.md) e o [FAQ de WAN Virtual do Azure](virtual-wan-faq.md).

Para informações adicionais, envie um e-mail para <azurevirtualwan@microsoft.com>. Inclua o nome da sua empresa em "[ ]" na linha de assunto.