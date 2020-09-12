---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 059d15090204c2fce0fddd4b80f4954755ea8f65
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89449661"
---
1. A partir do menu [do portal Azure,](https://portal.azure.com) selecione **Criar um recurso**. 

   ![Criar um recurso no portal do Azure](./media/vpn-gateway-add-local-network-gateway-portal-include/azure-portal-create-resource.png)
2. No campo **'Pesquisar no mercado',** **digite gateway de rede local**e, em seguida, prima **Enter** para pesquisar. Isto irá devolver uma lista de resultados. Clique em **Gateway de rede Local** e, em seguida, clique no botão **Criar** para abrir a página **Criar gateway de rede local**.

   ![Criar o portal de rede local](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "Criar o gateway de rede local")

3. Na página **Criar gateway de rede local**, especifique os valores para o objeto do gateway de rede local.

   - **Nome:** Especifique um nome para seu objeto de gateway de rede local.
   - **Endereço IP:** Este é o endereço IP público do dispositivo VPN a que pretende estabelecer ligação. Especifique um endereço IP público válido. Se não tiver o endereço IP no momento, pode usar os valores mostrados no exemplo, mas precisará voltar atrás e substituir o endereço IP do marcador de posição pelo endereço IP público do seu dispositivo VPN. Caso contrário, o Azure não será capaz de se ligar.
   - **Address Space** refere-se aos intervalos de endereços para a rede que o objeto de rede local representa (a sua rede no local). Adicione os espaços de endereço que pretende encaminhar para a sua rede no local. Pode adicionar vários intervalos de espaço de endereços. Certifique-se de que os intervalos que especificar aqui não se sobrepõem aos intervalos de outras redes às quais pretende ligar. O Azure irá encaminhar o intervalo de endereços que especificou no endereço IP do dispositivo VPN no local. *Se pretender ligar ao seu site no local utilize os seus próprios valores, não os valores mostrados no exemplo*.
   - **Configurar definições de BGP:** utilize apenas quando configurar o BGP. Caso contrário, não selecione esta opção.
   - **Subscrição:** Verifique se é apresentada a subscrição correta.
   - **Grupo de Recursos:** Selecione o grupo de recursos que pretende utilizar. Pode criar um novo grupo de recursos ou selecionar um que já tenha criado.
   - **Localização:** A localização é a mesma **da Região** em outros cenários. Selecione a localização em que este objeto será criado. Poderá selecionar a mesma localização em que a VNet se encontra, mas não tem obrigatoriamente de o fazer.

4. Quando tiver terminado de especificar os valores, clique no botão **Criar** na parte inferior da página para criar o gateway de rede local.
