---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9648f4c16e8d266bbdd504d4a7599b67a5c846b4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523574"
---
1. No menu [portal do Azure](https://portal.azure.com) , selecione **criar um recurso**. 

   ![Criar um recurso no portal do Azure](./media/vpn-gateway-add-local-network-gateway-portal-include/azure-portal-create-resource.png)
2. No campo **Pesquisar no Marketplace** , digite **Gateway de rede local**e pressione **Enter** para pesquisar. Isto irá devolver uma lista de resultados. Clique em **Gateway de rede Local** e, em seguida, clique no botão **Criar** para abrir a página **Criar gateway de rede local**.

   ![Criar o gateway de rede local](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "Criar o gateway de rede local")

3. Na página **Criar gateway de rede local**, especifique os valores para o objeto do gateway de rede local.

   - **Nome:** Especifique um nome para seu objeto de gateway de rede local.
   - **Endereço IP:** Este é o endereço IP público do dispositivo VPN a que pretende estabelecer ligação. Especifique um endereço IP público válido. Se não tiver o endereço IP no momento, pode usar os valores mostrados no exemplo, mas precisará voltar atrás e substituir o endereço IP do marcador de posição pelo endereço IP público do seu dispositivo VPN. Caso contrário, o Azure não será capaz de se ligar.
   - O **Espaço de endereços** refere-se aos intervalos de endereços da rede que esta rede local representa. Pode adicionar vários intervalos de espaço de endereços. Certifique-se de que os intervalos que especificar aqui não se sobrepõem aos intervalos de outras redes às quais pretende ligar. O Azure irá encaminhar o intervalo de endereços que especificou no endereço IP do dispositivo VPN no local. *Se pretender ligar ao seu site no local utilize os seus próprios valores, não os valores mostrados no exemplo*.
   - **Configurar definições de BGP:** utilize apenas quando configurar o BGP. Caso contrário, não selecione esta opção.
   - **Subscrição:** Verifique se é apresentada a subscrição correta.
   - **Grupo de Recursos:** Selecione o grupo de recursos que pretende utilizar. Pode criar um novo grupo de recursos ou selecionar um que já tenha criado.
   - **Local:** O local é o mesmo que a **região** em outras configurações. Selecione o local em que esse objeto será criado. Poderá selecionar a mesma localização em que a VNet se encontra, mas não tem obrigatoriamente de o fazer.

4. Quando tiver terminado de especificar os valores, clique no botão **Criar** na parte inferior da página para criar o gateway de rede local.
