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
ms.openlocfilehash: 8b585a47d3950d232eb3e8047c12ee8949030c95
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780270"
---
Para criar uma VNet no modelo de implementação do Gestor de Recursos com o Portal do Azure, siga os passos abaixo. Use os **valores de exemplo** se você estiver usando estas etapas como um tutorial. Se não estiver a seguir estes passos como um tutorial, certifique-se de substitui os valores pelos seus próprios. Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição Geral da Virtual Network](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Para esta VNet ligar a uma localização no local, terá de se coordenar com o administrador da rede no local para extrair um intervalo de endereços IP que possa utilizar especificamente para esta rede virtual. Se existir um intervalo de endereços duplicados em ambos os lados da ligação VPN, o tráfego não será encaminhado da forma esperada. Além disso, se pretender ligar esta VNet a outra VNet, o espaço de endereços não pode sobrepor-se a outra VNet. Tenha o cuidado de planear a configuração da rede em conformidade.
>

1. Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.
2. Clique em **Criar um recurso**. No campo **Procurar no Marketplace**, escreva "rede virtual". Localize a **Rede virtual** na lista devolvida e clique para abrir a página **Rede Virtual**.
3. Clique em **Criar**. Isso abre a página **criar rede virtual** .
4. Na página **Criar rede virtual**, configure as definições da VNet. Depois de preencher os campos, o sinal de exclamação vermelho muda para uma marca de verificação verde se os caracteres introduzidos nos campos forem válidos. Utilize os seguintes valores:

   - **Nome**: VNet1
   - **Espaço de endereço**: 10.1.0.0/16
   - **Assinatura**: Verifique se a assinatura listada é aquela que você deseja usar. Pode utilizar o menu pendente para mudar de subscrição.
   - **Grupo de recursos**: TestRG1 (clique em **criar novo** para criar um novo grupo)
   - **Local**: East US
   - **Sub-rede**: Front-end
   - **Intervalo de endereços**: 10.1.0.0/24

   ![Página Criar rede virtual](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network1.png)
5. Deixe DDoS como básico, pontos de extremidade de serviço como desabilitado e firewall como desabilitado.
6. Clique em **Criar** para criar a VNet.