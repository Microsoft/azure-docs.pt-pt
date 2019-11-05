---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4bcee1097010bb8746b11185a470ca2584485c3f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488974"
---
1. Na página do portal para sua Wan virtual, na seção **conectividade** , selecione **sites VPN** para abrir a página sites VPN.
2. Na página **Sites de VPN**, clique em **+Criar site**.

   ![Noções básicas](./media/virtual-wan-tutorial-site-include/basics.png "Noções básicas")
3. Na página **criar site VPN** , na guia **noções básicas** , preencha os seguintes campos:

    * **Região** – anteriormente conhecido como local. Esse é o local em que você deseja criar esse recurso de site.
    * **Nome** -o nome pelo qual você deseja se referir ao site local.
    * **Fornecedor do dispositivo** -o nome do fornecedor do dispositivo VPN (por exemplo: Citrix, Cisco, Barracuda). Isso pode ajudar a equipe do Azure a entender melhor seu ambiente para adicionar outras possibilidades de otimização no futuro ou para ajudá-lo a solucionar problemas.
    * **Border Gateway Protocol** -Enable implica que todas as conexões do site serão habilitadas para BGP. Eventualmente, você configurará as informações de BGP para cada link do site VPN na seção links. Configurar o BGP em uma WAN virtual é equivalente a configurar o BGP em uma VPN de gateway de rede virtual do Azure. Seu endereço de par de BGP local não deve ser o mesmo que o endereço IP público de sua VPN para o dispositivo ou o espaço de endereço de VNet do site de VPN. Use um endereço IP diferente no dispositivo VPN para o IP do par de BGP. Pode ser um endereço atribuído à interface de loopback no dispositivo. No entanto, ele não pode ser um endereço APIPA (169.254. x. x). Especifique esse endereço no site de VPN correspondente que representa o local. Para pré-requisitos de BGP, consulte [sobre o BGP com o gateway de VPN do Azure](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Você sempre pode editar uma conexão VPN para atualizar seus parâmetros BGP (IP de emparelhamento no link e o as #) depois que a configuração de BGP do site VPN estiver habilitada.
    * **Espaço de endereço privado** – o espaço de endereço IP que está localizado no site local. O tráfego destinado a este espaço de endereços é encaminhado para o site local. Isso é necessário quando o BGP não está habilitado para o site.
    * **Hubs** -o Hub ao qual você deseja que o site se conecte. Um site só pode ser conectado aos hubs que têm um gateway de VPN. Se você não vir um Hub, crie primeiro um gateway de VPN nesse Hub.
4. Selecione **links** para adicionar informações sobre os links físicos na ramificação. Se você tiver um dispositivo CPE de parceiro de WAN virtual, verifique com eles para ver se essas informações são trocadas com o Azure como parte do upload de informações do Branch configurado em seus sistemas.

   ![Vincule](./media/virtual-wan-tutorial-site-include/links.png "Ligações")

    * **Nome do link** -um nome que você deseja fornecer para o link físico no site de VPN. Exemplo: Mylink1.
    * **Nome do provedor** -o nome do link físico no site VPN. Exemplo: ATT, Verizon.
    * **Velocidade** – essa é a velocidade do dispositivo VPN no local da filial. Exemplo: 50, que significa 50 Mbps é a velocidade do dispositivo VPN no site da filial.
    * **Endereço IP** -endereço IP público do dispositivo local usando este link. Opcionalmente, você pode fornecer o endereço IP privado do dispositivo VPN local que está atrás do ExpressRoute.
5. Você pode usar a caixa de seleção para excluir ou adicionar links adicionais. Há suporte para quatro links por site VPN. Por exemplo, se você tiver quatro ISPs (provedor de serviços de Internet) no local da filial, poderá criar quatro links. um por cada ISP e fornece as informações para cada link.
6. Depois de terminar de preencher os campos, selecione **examinar + criar** para verificar e criar o site.
7. Exiba o status na página sites VPN. O site vai para a **conexão necessária** porque o site ainda não foi conectado ao Hub.