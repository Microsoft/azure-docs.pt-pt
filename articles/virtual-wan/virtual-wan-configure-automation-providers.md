---
title: Parceiros de WAN virtual do Azure | Microsoft Docs
description: Este artigo ajuda os parceiros a configurar a automação de WAN virtual do Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: cherylmc
ms.openlocfilehash: 2f847d8db983303d46b465f4f80bff65eeff632f
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168496"
---
# <a name="virtual-wan-partners"></a>Parceiros de WAN virtual

Este artigo ajuda você a entender como configurar o ambiente de automação para se conectar e configurar um dispositivo de ramificação (um dispositivo VPN local do cliente ou SDWAN CPE) para a WAN virtual do Azure. Se você for um provedor que fornece dispositivos de ramificação que podem acomodar a conectividade VPN por IPsec/IKEv2 ou IPsec/IKEv1, este artigo é para você.

Um dispositivo de ramificação (um dispositivo VPN local do cliente ou SDWAN CPE) normalmente usa um painel do controlador/dispositivo a ser provisionado. Os administradores da solução SD-WAN geralmente podem usar um console de gerenciamento para pré-configurar um dispositivo antes que ele seja conectado à rede. Esse dispositivo compatível com VPN Obtém a lógica do plano de controle de um controlador. O dispositivo VPN ou o controlador SD-WAN pode usar as APIs do Azure para automatizar a conectividade com a WAN virtual do Azure. Esse tipo de conexão requer que o dispositivo local tenha um endereço IP público voltado para o lado externamente atribuído a ele.

## <a name ="before"></a>Antes de começar a automatizar

* Verifique se o dispositivo dá suporte a IPsec IKEv1/IKEv2. Consulte [políticas padrão](#default).
* Exiba as [APIs REST](#additional) que você usa para automatizar a conectividade com a WAN virtual do Azure.
* Teste a experiência do portal da WAN virtual do Azure.
* Em seguida, decida qual parte das etapas de conectividade você gostaria de automatizar. No mínimo, recomendamos automatizar:

  * Controlo de Acesso
  * Carregamento de informações do dispositivo de ramificação na WAN virtual do Azure
  * Baixando a configuração do Azure e configurando a conectividade do dispositivo de ramificação na WAN virtual do Azure

### <a name ="additional"></a>Informações adicionais

* [API REST](https://docs.microsoft.com/rest/api/virtualwan/virtualhubs) para automatizar a criação do Hub virtual
* [API REST](https://docs.microsoft.com/rest/api/virtualwan/vpngateways) para automatizar o gateway de VPN do Azure para WAN virtual
* [API REST](https://docs.microsoft.com/rest/api/virtualwan/vpnconnections) para conectar um VPNSite a um hub de VPN do Azure
* [Políticas IPsec padrão](#default)

## <a name ="ae"></a>Experiência do cliente

Entenda a experiência do cliente esperada em conjunto com a WAN virtual do Azure.

  1. Normalmente, um usuário de WAN virtual iniciará o processo criando um recurso de WAN virtual.
  2. O usuário configurará um acesso de grupo de recursos baseado na entidade de serviço para o sistema local (o software de provisionamento de dispositivos VPN ou controlador de Branch) para gravar informações de ramificação na WAN virtual do Azure.
  3. O usuário pode decidir no momento para fazer logon em sua interface do usuário e configurar as credenciais da entidade de serviço. Quando isso for concluído, o controlador deverá ser capaz de carregar informações de Branch com a automação que você fornecerá. O equivalente manual disso no lado do Azure é ' criar site '.
  4. Depois que as informações de site (dispositivo de ramificação) estiverem disponíveis no Azure, o usuário irá conectar o site a um Hub. Um hub virtual é uma rede virtual gerenciada pela Microsoft. O hub contém vários pontos finais de serviço para ativar a conectividade da rede no local (vpnsite). O hub é o núcleo da sua rede numa região. Só pode haver um hub por região do Azure e o ponto de extremidade de VPN (vpngateway) dentro dele é criado durante esse processo. O gateway de VPN é um gateway escalonável que dimensiona adequadamente com base nas necessidades de largura de banda e conexão. Você pode optar por automatizar o Hub virtual e a criação de vpngateway do painel do controlador de dispositivo da filial.
  5. Depois que o Hub virtual estiver associado ao site, um arquivo de configuração será gerado para que o usuário faça o download manualmente. É aí que entra a automação e torna a experiência do usuário direta. Em vez de o usuário ter que baixar e configurar manualmente o dispositivo de ramificação, você pode definir a automação e fornecer uma experiência de clique mínima em sua interface de usuário, aliviando, assim, problemas típicos de conectividade, como incompatibilidade de chave compartilhada, parâmetro IPSec incompatibilidade, legibilidade do arquivo de configuração, etc.
  6. No final desta etapa em sua solução, o usuário terá uma conexão direta site a site entre o dispositivo de ramificação e o Hub virtual. Você também pode configurar conexões adicionais em outros hubs. Cada conexão é um túnel ativo-ativo. O cliente pode optar por usar um ISP diferente para cada um dos links para o túnel.
  7. Considere fornecer recursos de monitoramento e solução de problemas na interface de gerenciamento do CPE. Os cenários típicos incluem "o cliente não pode acessar os recursos do Azure devido a um problema de CPE", "mostrar parâmetros de IPsec no lado da CPE" etc.

## <a name ="understand"></a>Detalhes da automação

###  <a name="access"></a>Controle de acesso

Os clientes devem ser capazes de configurar o controle de acesso apropriado para a WAN virtual na interface do usuário do dispositivo. Isso é recomendado usando uma entidade de serviço do Azure. O acesso baseado na entidade de serviço fornece a autenticação apropriada do controlador do dispositivo para carregar informações de Branch. Para obter mais informações, consulte [criar entidade de serviço](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Embora essa funcionalidade esteja fora da oferta de WAN virtual do Azure, listamos abaixo as etapas típicas seguidas para configurar o acesso no Azure após o qual os detalhes relevantes são inseridos no painel de gerenciamento de dispositivos

* Crie um aplicativo Azure Active Directory para o controlador de dispositivo local.
* Obter a ID do aplicativo e a chave de autenticação
* Obter o ID de inquilino
* Atribuir aplicativo à função "colaborador"

###  <a name="branch"></a>Carregar informações do dispositivo de ramificação

Você deve projetar a experiência do usuário para carregar informações de Branch (site local) no Azure. Você pode usar [APIs REST](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) para VPNSite para criar as informações do site na WAN virtual. Você pode fornecer todos os dispositivos SDWAN/VPN de ramificação ou selecionar as personalizações de dispositivo conforme apropriado.

### <a name="device"></a>Download e conectividade da configuração do dispositivo

Esta etapa envolve o download da configuração do Azure e a configuração da conectividade do dispositivo de ramificação na WAN virtual do Azure. Nesta etapa, um cliente que não está usando um provedor baixaria manualmente a configuração do Azure e a aplicaria ao dispositivo SDWAN/VPN local. Como um provedor, você deve automatizar esta etapa. Exiba as [APIs REST](https://docs.microsoft.com/rest/api/virtualwan/vpnsitesconfiguration/download) de download para obter informações adicionais. O controlador de dispositivo pode chamar a API REST ' GetVpnConfiguration ' para baixar a configuração do Azure.

**Observações de configuração**

  * Se os VNets do Azure forem anexados ao Hub virtual, eles aparecerão como ConnectedSubnets.
  * A conectividade VPN usa a configuração baseada em rota e dá suporte aos protocolos IKEv1 e IKEv2.

## <a name="devicefile"></a>Arquivo de configuração do dispositivo

O ficheiro de configuração do dispositivo contém as definições que vão ser utilizadas para configurar o dispositivo VPN no local. Quando vir este ficheiro, repare nas informações seguintes:

* **vpnSiteConfiguration -** esta secção mostra os detalhes do dispositivo configurados como site que se vai ligar à WAN virtual. Inclui o nome e o endereço IP público do dispositivo da sucursal.
* **vpnSiteConnections -** esta secção disponibiliza informações sobre o seguinte:

    * **Espaço de endereço** da VNet de hubs virtuais.<br>Exemplo:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Espaço de endereço** do VNets que estão conectados ao Hub.<br>Exemplo:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **Endereços IP** do vpngateway do hub virtual. Uma vez que o vpngateway tem ambas as ligações compostas por dois túneis na configuração ativo-ativo, verá os dois endereços IP indicados neste ficheiro. Neste exemplo, vê "Instance0" e "Instance1" para cada site.<br>Exemplo:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Detalhes de configuração de conexão Vpngateway** , como BGP, chave pré-compartilhada, etc. A PSK é a chave pré-compartilhada que é gerada automaticamente para você. Pode sempre editar a ligação na página Overview (Descrição geral) de um PSK personalizado.
  
**Exemplo de arquivo de configuração de dispositivo**

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

## <a name="default"></a>Detalhes de conectividade

Seu dispositivo SDWAN/VPN local ou a configuração de SD-WAN deve corresponder ou conter os seguintes algoritmos e parâmetros, que você especifica na política de IPsec/IKE do Azure.

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

Para obter mais informações sobre WAN virtual, consulte [sobre a WAN virtual do Azure](virtual-wan-about.md) e as [perguntas frequentes sobre a WAN virtual do Azure](virtual-wan-faq.md).

Para obter informações adicionais, envie um email para <azurevirtualwan@microsoft.com>. Inclua o nome da sua empresa em "[ ]" na linha de assunto.