---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dbc4d73ab5faee89895f5bd06349dfe6bc8bfd5d
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627723"
---
1. Na página do portal para o seu wan virtual, na secção **Conectividade,** selecione **sites VPN** para abrir a página de sites VPN.
2. Na página **Sites de VPN**, clique em **+Criar site**.

   ![A screenshot mostra o site para a janela do site com o painel do Site Create VPN aberto.](./media/virtual-wan-tutorial-site-include/basics.png "Informações básicas")
3. Na página **do Site Create VPN,** no separador **Básicos,** complete os seguintes campos:

    * **Região** - Anteriormente referida como localização. Esta é a localização em que pretende criar este recurso do site.
    * **Nome** - O nome pelo qual pretende consultar o seu site no local.
    * **Fornecedor de dispositivos** - O nome do fornecedor de dispositivos VPN (por exemplo: Citrix, Cisco, Barracuda). Adicionar o fornecedor de dispositivos pode ajudar a Equipa Azure a compreender melhor o seu ambiente de forma a adicionar possibilidades de otimização adicionais no futuro, ou para ajudá-lo a resolver problemas.
    * **Protocolo border gateway** - A definição **enable** implica que todas as ligações do site estarão ativadas no BGP. Irá eventualmente configurar as informações do BGP para cada link a partir do site da VPN na secção Links. Configurar o BGP num WAN virtual equivale a configurar o BGP numa VPN de gateway de rede virtual Azure. O seu endereço de pares BGP no local não deve ser o mesmo que o endereço IP público do seu VPN para dispositivo ou o espaço de endereço VNet do site VPN. Utilize um endereço IP diferente no dispositivo VPN para o seu IP de pares BGP. Pode ser um endereço atribuído à interface de loopback no dispositivo. Especifique este endereço no site VPN correspondente que representa a localização. Para pré-requisitos BGP, consulte [Sobre o BGP com o Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Pode sempre editar uma ligação VPN para atualizar os seus parâmetros BGP (Peering IP no link e o AS #) uma vez que a definição de BGP do site VPN esteja ativada.
    * **Espaço de endereço privado** - O espaço de endereço IP localizado no seu site no local. O tráfego destinado a este espaço de endereços é encaminhado para o site local. Isto é necessário quando o BGP não está ativado para o site.
    
      >[!NOTE]
      >Se editar o espaço do endereço após a criação do site (por exemplo, adicionar um espaço de endereço adicional) pode demorar 8-10 minutos a atualizar as rotas eficazes enquanto os componentes são recriados.
      >
    * **Hubs** - O hub ao que pretende que o seu Site se conecte. Um site só pode ser conectado aos centros que têm um Gateway VPN. Se não vir um hub, crie uma porta de entrada VPN naquele centro primeiro.
4. Selecione **Links** para adicionar informações sobre as ligações físicas no ramo. Se tiver um dispositivo CPE parceiro Virtual WAN, consulte com eles para ver se esta informação é trocada com o Azure como parte do upload de informações da sucursal configurados a partir dos seus sistemas.

   ![A screenshot mostra o painel do Site Create VPN com o separador Links selecionado.](./media/virtual-wan-tutorial-site-include/links.png "Ligações")

    * **Nome do link** - Um nome que pretende fornecer para o link físico no Site VPN. Exemplo: mylink1.
    * **Nome do Provedor** - O nome do link físico no Site VPN. Exemplo: ATT, Verizon.
    * **Velocidade** - Esta é a velocidade do dispositivo VPN na localização do ramo. Exemplo: 50, o que significa 50 Mbps é a velocidade do dispositivo VPN no local do ramo.
    * **Endereço IP/FQDN** - Endereço IP público do dispositivo no local utilizando este link. Opcionalmente, pode fornecer o endereço IP privado do seu dispositivo VPN no local que está por trás do ExpressRoute. Também pode incluir um nome de domínio totalmente qualificado. Por exemplo, *something.contoso.com.* O FQDN deve ser resolúvel a partir do portal VPN. Isto é possível se o servidor DNS que hospeda este FQDN for acessível através da internet. O endereço IP tem precedência quando tanto o endereço IP como o FQDN são especificados.

      >[!NOTE]
      >
      >* Suporta um endereço IPv4 por FQDN. Se o FQDN for resolvido para vários endereços IP, então o gateway VPN capta o primeiro endereço IP4 da lista. Os endereços IPv6 não são suportados neste momento.
      >
      >* O gateway VPN mantém uma cache DNS que é refrescada a cada 5 minutos. O portal tenta resolver fQDNs apenas para túneis desligados. Uma alteração de reset ou configuração de gateway também pode desencadear a resolução FQDN.
      >
5. Pode utilizar a caixa de verificação para eliminar ou adicionar mais links. Quatro links por VPN Site são suportados. Por exemplo, se tiver quatro ISPs (fornecedor de serviços de Internet) na localização da sucursal, pode criar quatro links, um por cada ISP, e fornecer as informações para cada link.
6. Uma vez terminado o preenchimento dos campos, selecione **Review + create** para verificar e criar o site.
7. Veja o estado na página dos sites VPN. O site irá para **a Conexão Necessária** porque o site ainda não foi ligado ao hub.
