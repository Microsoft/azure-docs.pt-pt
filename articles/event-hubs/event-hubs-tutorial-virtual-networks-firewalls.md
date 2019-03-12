---
title: Tutorial - ativar Virtual redes integração e Firewalls em Hubs de eventos | Documentos da Microsoft
description: Neste tutorial, saiba como integrar os Hubs de eventos com redes virtuais e as Firewalls para permitir o acesso seguro.
services: event-hubs
author: axisc
manager: darosa
ms.author: aschhab
ms.date: 11/28/2018
ms.topic: tutorial
ms.service: event-hubs-messaging
ms.custom: mvc
ms.openlocfilehash: 9eea40a8ad2f08099b2662a0e7539c326d4d143e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779050"
---
# <a name="tutorial-enable-virtual-networks-integration-and-firewalls-on-event-hubs-namespace"></a>Tutorial: Ativar a integração de redes virtuais e as Firewalls no espaço de nomes de Hubs de eventos

[Pontos finais de serviço de rede (VNet) virtual](../virtual-network/virtual-network-service-endpoints-overview.md) expandir o seu espaço de endereços privados da rede virtual e a identidade da sua VNet para os serviços do Azure, através de uma ligação direta. Os pontos finais permitem-lhe obter os seus recursos críticos de serviço do Azure apenas para as suas redes virtuais. O tráfego da sua VNet para o serviço do Azure permanece sempre na rede backbone do Microsoft Azure.

Firewalls permitem-lhe limitar o acesso ao espaço de nomes dos Hubs de eventos a partir de endereços IP específicos (ou intervalos de endereços IP)

Este tutorial mostra como integrar a pontos finais de serviço de redes virtuais e configurar Firewalls (filtragem de IP) com o seu espaço de nomes dos Hubs de eventos do Azure existente, com o portal.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Como integrar os pontos finais de serviço de redes virtuais com o seu espaço de nomes de Hubs de eventos.
> * Como configurar o Firewall (filtragem de IP) com o seu espaço de nomes de Hubs de eventos.

>[!WARNING]
> Implementar a integração de redes virtuais pode impedir que outros serviços do Azure a interagir com os Hubs de eventos.
>
> Primeira integrações de terceiros não são suportadas quando redes virtuais ativadas.
> Cenários comuns do Azure que não funcionam com redes virtuais-
> * Registo e diagnóstico do Azure
> * Azure Stream Analytics
> * Integração do Event Grid
> * Aplicações Web e as funções são necessários para estar numa rede Virtual.
> * Rotas de Hub IoT
> * IoT Device Explorer


> [!IMPORTANT]
> Redes virtuais são suportadas no **padrão** e **dedicado** escalões de Hubs de eventos. Não é suportada no escalão básico.

Se não tiver uma subscrição do Azure, crie uma [[conta gratuita]] antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Iremos tirar partido um espaço de nomes de Hubs de eventos existente, por isso, certifique-se de que tem um espaço de nomes de Hubs de eventos disponível. Se não o fizer, consulte [neste tutorial](./event-hubs-create.md)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Primeiro, entre no [Portal do Azure][Azure portal] e inicie sessão com a sua subscrição do Azure.

## <a name="select-event-hubs-namespace"></a>Selecionar espaço de nomes de Hubs de eventos

Para efeitos deste tutorial, criou um espaço de nomes de Hubs de eventos e navegará para que.

## <a name="navigate-to-firewalls-and-virtual-networks-experience"></a>Navegue para a experiência de Firewalls e redes virtuais

Utilize o menu de navegação no painel do lado esquerdo no portal para escolher a **"Firewalls e redes virtuais"** opção.

  ![Navegue para o menu](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-landing-page.png)

  Na primeira vez que visite esta página, o **todas as redes** tem de selecionar o botão de opção. Isso implica que o espaço de nomes de Hubs de eventos permite que todas as ligações recebidas.

## <a name="add-virtual-network-service-endpoint"></a>Adicionar ponto final de serviço de rede Virtual

Para limitar o acesso, tem de integrar o ponto de final de serviço de rede Virtual para este espaço de nomes de Hubs de eventos.

1. Clique nas **redes selecionadas** botão de opção no topo da página para ativar o resto da página com as opções de menu.
  ![redes selecionadas](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. Na secção de rede Virtual da página, selecione a opção para ***+ adicionar rede virtual existente***. Isto irá deslize no painel que irá permitir-lhe selecionar uma rede Virtual já criada.
  ![Adicionar rede virtual existente](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane.png)
3. Selecione a rede Virtual a partir da lista e escolha a sub-rede.
   ![Selecione a sub-rede](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-with-subnet-query.png)
4. Tem de ativar o ponto final de serviço antes de adicionar a rede Virtual à lista. Se o ponto final de serviço não estiver ativado, o portal irá solicitar-lhe para ativá-la.
  ![Selecione a sub-rede e ativar o ponto final](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-enabling.png)
    > [!NOTE]
    > Se não é possível ativar o ponto final de serviço, pode ignorar o em falta Virtual rede ponto final de serviço utilizando o modelo ARM. Esta funcionalidade não está disponível no portal.

5. Depois de ativar o ponto final de serviço na sub-rede selecionada, pode avançar para o adicionar à lista de redes virtuais permitidas.
  ![adicionar a sub-rede depois de ativar o ponto final](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-adding.png)

6. Avance para atingir a **guardar** botão da faixa de opções principais para guardar a configuração de rede virtual no serviço. Aguarde alguns minutos para que a confirmação a aparecer nas notificações do portal.

## <a name="add-firewall-for-specified-ip"></a>Adicionar Firewall para o IP especificado

Podemos pode limitar o acesso ao espaço de nomes dos Hubs de eventos para um limitado intervalo de endereços IP ou um endereço IP específico ao utilizar regras de Firewall.

1. Clique nas **redes selecionadas** botão de opção no topo da página para ativar o resto da página com as opções de menu.
  ![redes selecionadas](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. Na **Firewall** secção no ***intervalo de endereços*** grade, pode adicionar um ou muitos específico endereço IP ou intervalos de endereços IP.
  ![adicionar endereços ip](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall.png)
3. Depois de adicionar vários endereços IP (ou intervalos de endereços IP), pressionar **guardar** da faixa de opções principais para se certificar de que a configuração é guardada no lado do serviço. Aguarde alguns minutos para que a confirmação a aparecer nas notificações do portal.
  ![adicionar endereços ip e clique em Guardar](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall-hitting-save.png)

## <a name="adding-your-current-ip-address-to-the-firewall-rules"></a>Adicionar o seu endereço IP atual para as regras de Firewall

1. Também pode adicionar seu endereço IP atual rapidamente, verificando a ***adicionar endereço IP do cliente (o seu atual endereço IP)*** caixa de verificação imediatamente acima do ***intervalo de endereços*** grid.
  ![Adicionar endereço IP atual](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save.png)
2. Depois de adicionar seu endereço IP atual para as regras de firewall, pressione **guardar** da faixa de opções principais para se certificar de que a configuração é guardada no lado do serviço. Aguarde alguns minutos para que a confirmação a aparecer nas notificações do portal.
  ![Adicionar endereço IP atual e clique em Guardar](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save-after-saving.png)

## <a name="conclusion"></a>Conclusão

Neste tutorial, integrado pontos finais de rede Virtual e regras de Firewall com um espaço de nomes de Hubs de eventos existente. Com aprendizagem como para:
> [!div class="checklist"]
> * Como integrar os pontos finais de serviço de redes virtuais com o seu espaço de nomes de Hubs de eventos.
> * Como configurar o Firewall (filtragem de IP) com o seu espaço de nomes de Hubs de eventos.


[Azure portal]: https://portal.azure.com/
