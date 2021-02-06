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
ms.openlocfilehash: f469664c716ecef6b82de2befa40b33f253e229f
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627722"
---
1. Localize o WAN virtual que criou. Na página WAN virtual, na secção **Conectividade,** selecione **Hubs**.
2. Na página **Hubs,** selecione **+New Hub** para abrir a página **do hub virtual Create.**

    ![A screenshot mostra o painel de hub virtual Create com o separador Basics selecionado.](./media/virtual-wan-tutorial-hub-include/basics.png "Informações básicas")
3. No **separador 'Criar** página de centro virtual **Basics',** complete os seguintes campos:

   * **Região** (anteriormente designada por Localização)
   * **Nome**
   * **Espaço de endereço privado hub** - O espaço mínimo de endereço é /24 para criar um hub. Se utilizar alguma coisa na gama de /25 a /32, produzirá um erro durante a criação. Não é necessário planear explicitamente o espaço de endereço da sub-rede para os serviços no centro virtual. Como o Azure Virtual WAN é um serviço gerido, cria as sub-redes adequadas no centro virtual para os diferentes gateways/serviços (por exemplo, gateways VPN, gateways ExpressRoute, gateways ponto-a-local do utilizador, Firewall, encaminhamento e etc.).
4. Selecione **Seguinte: Site-to-site**.

    ![A screenshot mostra o painel de hub virtual Create com o Site para o site selecionado.](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Site a Site")

5. No **separador Site-to-site,** complete os seguintes campos:

   * Selecione **Sim** para criar uma VPN site-to-site.
   * O campo AS Number não pode ser editado.
   * Selecione o valor das **unidades** de escala Gateway a partir do dropdown. A unidade de escala permite-lhe escolher a produção agregada do gateway VPN que está a ser criado no centro virtual para ligar sites. Se escolher uma unidade de escala de 1 = 500 Mbps, implica que serão criados dois casos de redundância, cada um com uma potência máxima de 500 Mbps. Por exemplo, se tivesse cinco ramos, cada um fazendo 10 Mbps no ramo, precisaria de um agregado de 50 Mbps na extremidade da cabeça. O planeamento da capacidade agregada da gateway Azure VPN deve ser feito após a avaliação da capacidade necessária para suportar o número de balcões no centro.
6. Selecione **Review + Criar** para validar.
7. Selecione **Criar** para criar o hub. Após 30 minutos, **Refresque-se** para ver o hub na página **Hubs.** Selecione **Vá para o recurso** para navegar para o recurso.
