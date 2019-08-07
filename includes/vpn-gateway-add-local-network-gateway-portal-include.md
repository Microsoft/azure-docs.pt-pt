---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dbfa7ff6e30b64fe1025114590c1b1a8824e6cdf
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780152"
---
1. No portal, clique em **Criar um recurso**.
2. Na caixa de pesquisa, escreva **Gateway de rede Local** e, em seguida, prima **Enter** para procurar. Isto irá devolver uma lista de resultados. Clique em **Gateway de rede Local** e, em seguida, clique no botão **Criar** para abrir a página **Criar gateway de rede local**.

   ![Criar o gateway de rede local](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "Criar o gateway de rede local")

3. Na página **Criar gateway de rede local**, especifique os valores para o objeto do gateway de rede local.

   - **Nomes** Especifique um nome para o objeto de gateway de rede local.
   - **Endereço IP:** Esse é o endereço IP público do dispositivo VPN ao qual você deseja que o Azure se conecte. Especifique um endereço IP público válido. Se não tiver o endereço IP no momento, pode usar os valores mostrados no exemplo, mas precisará voltar atrás e substituir o endereço IP do marcador de posição pelo endereço IP público do seu dispositivo VPN. Caso contrário, o Azure não será capaz de se ligar.
   - O **Espaço de endereços** refere-se aos intervalos de endereços da rede que esta rede local representa. Pode adicionar vários intervalos de espaço de endereços. Certifique-se de que os intervalos que especificar aqui não se sobrepõem aos intervalos de outras redes às quais pretende ligar. O Azure irá encaminhar o intervalo de endereços que especificou no endereço IP do dispositivo VPN no local. *Se pretender ligar ao seu site no local utilize os seus próprios valores, não os valores mostrados no exemplo*.
   - **Definir configurações de BGP:** Use somente ao configurar o BGP. Caso contrário, não selecione esta opção.
   - **Scriçõe** Verifique se a assinatura correta está sendo exibida.
   - **Grupo de recursos:** Selecione o grupo de recursos que você deseja usar. Pode criar um novo grupo de recursos ou selecionar um que já tenha criado.
   - **Local** Selecione o local em que esse objeto será criado. Poderá selecionar a mesma localização em que a VNet se encontra, mas não tem obrigatoriamente de o fazer.

4. Quando tiver terminado de especificar os valores, clique no botão **Criar** na parte inferior da página para criar o gateway de rede local.
