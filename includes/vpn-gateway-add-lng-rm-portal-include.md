---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d9825ea41937dc9436fe8b465b48b378e13407c1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184131"
---
1. No portal, em **Todos os recursos**, clique em **+Adicionar**.
2. No **tudo** caixa de pesquisa de página, escreva **gateway de rede Local**, em seguida, clique aqui para retornar uma lista de recursos. Clique em **Gateway de rede Local** para abrir a página e, em seguida, clique em **Criar** para abrir a página **Criar gateway de rede local**.

   ![criar gateway de rede local](./media/vpn-gateway-add-lng-rm-portal-include/lng.png)
3. Na página **Criar gateway de rede local**, especifique os valores para o objeto do gateway de rede local.

   - **Nome:** Especifique um nome para o objeto de gateway de rede local. Se possível, utilize algo intuitivo, tal como **ClassicVNetLocal** ou **TestVNet1Local**. Isto torna mais fácil de identificar o gateway de rede local no portal.
   - **Endereço IP:** Especifique um público válido **endereço IP** para o dispositivo VPN ou gateway de rede virtual à qual pretende ligar.

     * **Se esta rede local representa uma localização no local:** Especifique o endereço IP público do dispositivo VPN que pretende ligar. Não pode estar protegido por NAT e tem de estar acessível ao Azure.
     * **Se esta rede local representar outra VNet:** Especifique o endereço IP público atribuído ao gateway de rede virtual para essa VNet.
     * **Se ainda não tiver o endereço IP:** Pode criar um endereço IP do marcador de posição válido e, em seguida, voltar atrás e modificar esta definição antes de se ligar.
   - O **Espaço de endereços** refere-se aos intervalos de endereços da rede que esta rede local representa. Pode adicionar vários intervalos de espaço de endereços. Certifique-se de que os intervalos que especificar aqui não se sobrepõem aos intervalos de outras redes às quais se liga.
   - **Configure definições de BGP:** Utilize apenas quando configurar o BGP. Caso contrário, não selecione esta opção.
   - **Subscrição:** Certifique-se de que é apresentada a subscrição correta.
   - **Grupo de recursos:** Selecione o grupo de recursos que pretende utilizar. Pode criar um novo grupo de recursos ou selecionar um que já tenha criado.
   - **Localização:** Selecione a localização que este objeto será criado. Poderá selecionar a mesma localização em que a VNet se encontra, mas não tem obrigatoriamente de o fazer.

4. Clique em **Criar** para criar o gateway da rede local.
