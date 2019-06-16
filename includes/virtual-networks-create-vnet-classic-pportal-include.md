---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: aa88bf3bd6c5037b41c09e9ffe47921f1b9dc9be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171974"
---
## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Como criar uma VNet clássica no portal do Azure
Para criar um clássico que vnet com base no cenário anterior, siga estes passos.

1. Num browser, navegue para http://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique em **Criar um recurso** > **Rede** > **Rede virtual**. Tenha em atenção que o **selecionar um modelo de implementação** lista já mostra **clássico**. 3. Clique em **criar** conforme mostrado na figura a seguir.
   
    ![Criar a VNet no Portal do Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. Sobre o **rede Virtual** painel, escreva o **nome** da VNet e, em seguida, clique **espaço de endereços**. Configure as definições de espaço de endereço para a VNet e a sua primeira sub-rede, em seguida, clique em **OK**. A figura seguinte mostra as definições de bloco CIDR para o nosso cenário.
   
    ![Painel de espaço de endereço](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Clique em **grupo de recursos** e selecione um grupo de recursos para adicionar a VNet ou clique em **criar novo grupo de recursos** para adicionar a VNet a um grupo de recursos. A figura seguinte mostra o recurso de definições de grupo para um novo grupo de recursos chamado **TestRG**. Para obter mais informações sobre os grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
    ![Criar o painel do grupo de recursos](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. Se necessário, altere as definições da **Subscrição** e da **Localização** para a sua VNet. 
7. Se não pretender ver a VNet como um mosaico no **Startboard**, desative **Afixar ao Startboard**. 
8. Clique em **Create** e repare no mosaico com o nome **criar rede Virtual** conforme mostrado na figura a seguir.
   
    ![Criar a VNet no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Aguarde que a VNet seja criada e, quando é apresentado o mosaico, clique na opção para adicionar mais sub-redes.
   
    ![Criar a VNet no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. Deverá ver o **configuração** para a sua VNet, conforme mostrado. 
   
    ![Criar a VNet no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Clique em **sub-redes** > **Add**, em seguida, escreva um **nome** e especifique uma **intervalo de endereços (bloco CIDR)** para a sua sub-rede e, em seguida, Clique em **OK**. A figura seguinte mostra as definições para o nosso cenário atual.
    
    ![Criar a VNet no Portal do Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

