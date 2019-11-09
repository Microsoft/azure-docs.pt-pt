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
ms.openlocfilehash: ac5599a38c3fcbc4a6c331d2b14c6b6c55c31d82
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73850457"
---
## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Como criar uma VNet clássica no portal do Azure
Para criar uma VNet clássica com base no cenário anterior, siga estas etapas.

1. Num browser, navegue para https://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.
2. Clique em **Criar um recurso** > **Rede** > **Rede virtual**. Observe que a lista **selecionar um modelo de implantação** já mostra o **clássico**. 3. Clique em **criar** , conforme mostrado na figura a seguir.
   
    ![Criar a VNet no Portal do Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. No painel **rede virtual** , digite o **nome** da VNet e clique em **espaço de endereço**. Defina as configurações de espaço de endereço para a VNet e sua primeira sub-rede e clique em **OK**. A figura a seguir mostra as configurações de bloco CIDR para nosso cenário.
   
    ![Painel de espaço de endereço](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Clique em **grupo de recursos** e selecione um grupo de recursos ao qual adicionar a vnet ou clique em **criar novo grupo de recursos** para adicionar a vnet a um novo grupo de recursos. A figura a seguir mostra as configurações do grupo de recursos para um novo grupo de recursos chamado **TestRG**. Para obter mais informações sobre os grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
    ![Painel Criar grupo de recursos](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. Se necessário, altere as definições da **Subscrição** e da **Localização** para a sua VNet. 
7. Se não pretender ver a VNet como um mosaico no **Startboard**, desative **Afixar ao Startboard**. 
8. Clique em **criar** e observe o bloco chamado **criando rede virtual** , conforme mostrado na figura a seguir.
   
    ![Criar VNet no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Aguarde até que a VNet seja criada e, quando você vir o bloco, clique nele para adicionar mais sub-redes.
   
    ![Criar VNet no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. Você deve ver a **configuração** de sua VNet, conforme mostrado. 
   
    ![Criar VNet no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Clique **em sub-redes** > **Adicionar**, digite um **nome** e especifique um **intervalo de endereços (bloco CIDR)** para sua sub-rede e clique em **OK**. A figura a seguir mostra as configurações para nosso cenário atual.
    
    ![Criar a VNet no Portal do Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

