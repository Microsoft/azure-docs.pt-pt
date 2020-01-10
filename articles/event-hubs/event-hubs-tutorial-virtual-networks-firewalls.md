---
title: Hubs de eventos do Azure – habilitar integração de redes virtuais e firewalls
description: Neste tutorial, você aprende a integrar os hubs de eventos com redes virtuais e firewalls para habilitar o acesso seguro.
services: event-hubs
author: axisc
manager: darosa
ms.author: aschhab
ms.date: 12/20/2019
ms.topic: tutorial
ms.service: event-hubs
ms.custom: mvc
ms.openlocfilehash: f911a1513c6f89180ea51cc0de96dc8a475c7fc8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437109"
---
# <a name="tutorial-enable-virtual-networks-integration-and-firewalls-on-event-hubs-namespace"></a>Tutorial: habilitar a integração de redes virtuais e firewalls no namespace de hubs de eventos

Os [pontos de extremidade de serviço de rede virtual (VNet)](../virtual-network/virtual-network-service-endpoints-overview.md) estendem seu espaço de endereço privado de rede virtual e a identidade de sua VNet para os serviços do Azure, em uma conexão direta. Os pontos finais permitem-lhe obter os seus recursos críticos de serviço do Azure apenas para as suas redes virtuais. O tráfego da sua VNet para o serviço do Azure permanece sempre na rede backbone do Microsoft Azure.

Os firewalls permitem que você limite o acesso ao namespace de hubs de eventos de endereços IP específicos (ou intervalos de endereços IP)

Este tutorial mostra como integrar pontos de extremidade de serviço de redes virtuais e configurar firewalls (filtragem de IP) com seu namespace existente de hubs de eventos do Azure, usando o Portal.

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> * Como integrar pontos de extremidade de serviço de redes virtuais com seu namespace de hubs de eventos.
> * Como configurar o firewall (filtragem de IP) com seu namespace de hubs de eventos.

>[!WARNING]
> A implementação da integração de redes virtuais pode impedir que outros serviços do Azure interajam com os hubs de eventos.
>
> Não há suporte para integrações de primeira parte quando as redes virtuais estão habilitadas.
> Cenários comuns do Azure que não funcionam com redes virtuais-
> * Diagnóstico do Azure e registro em log
> * Azure Stream Analytics
> * Integração da grade de eventos
> * Os aplicativos Web & funções precisam estar em uma rede virtual.
> * Rotas do Hub IoT
> * Device Explorer de IoT


> [!IMPORTANT]
> Redes virtuais são suportadas no **padrão** e **dedicado** escalões de Hubs de eventos. Não é suportada no escalão básico.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita] [] antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Aproveitaremos um namespace de hubs de eventos existente, portanto, verifique se você tem um namespace de hubs de eventos disponível. Se você não fizer isso, consulte [este tutorial](./event-hubs-create.md)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Primeiro, vá para a [portal do Azure][Azure portal] e entre usando sua assinatura do Azure.

## <a name="select-event-hubs-namespace"></a>Selecionar namespace de hubs de eventos

Para fins deste tutorial, criamos um namespace de hubs de eventos e navegaremos até isso.

## <a name="navigate-to-firewalls-and-virtual-networks-experience"></a>Navegue até firewalls e experiência de redes virtuais

Use o menu de navegação no painel esquerdo no portal para selecionar a opção **' firewalls e redes virtuais '** .

  ![Navegar para o menu](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-landing-page.png)

  Na primeira vez que você visitar esta página, o botão de opção **todas as redes** deverá ser selecionado. Isso implica que o namespace de hubs de eventos permite todas as conexões de entrada.

## <a name="add-virtual-network-service-endpoint"></a>Adicionar ponto de extremidade de serviço de rede virtual

Para limitar o acesso, você precisa integrar o ponto de extremidade de serviço de rede virtual para este namespace de hubs de eventos.

1. Clique no botão de opção **redes selecionadas** na parte superior da página para habilitar o restante da página com opções de menu.
  ![redes selecionadas](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. Na seção rede virtual da página, selecione a opção para ***+ Adicionar rede virtual existente***. Isso deslizará no painel que lhe permitirá selecionar uma rede virtual já criada.
  ![adicionar](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane.png) de rede virtual existente
3. Selecione a rede virtual na lista e escolha a sub-rede.
   ![selecionar](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-with-subnet-query.png) de sub-rede
4. Você precisa habilitar o ponto de extremidade de serviço antes de adicionar a rede virtual à lista. Se o ponto de extremidade de serviço não estiver habilitado, o portal solicitará que você o habilite.
  ![selecionar sub-rede e habilitar ponto de extremidade](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-enabling.png)
    > [!NOTE]
    > Se não for possível habilitar o ponto de extremidade de serviço, você poderá ignorar o ponto de extremidade de serviço de rede virtual ausente usando o modelo ARM. Essa funcionalidade não está disponível no Portal.

5. Depois de habilitar o ponto de extremidade de serviço na sub-rede selecionada, você pode continuar a adicioná-lo à lista de redes virtuais permitidas.
  ![adicionar sub-rede depois de habilitar o ponto de extremidade](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-adding.png)

6. Continue a pressionar o botão **salvar** na faixa de para cima para salvar a configuração de rede virtual no serviço. Aguarde alguns minutos para que a confirmação seja exibida nas notificações do Portal.

## <a name="add-firewall-for-specified-ip"></a>Adicionar firewall para o IP especificado

Podemos limitar o acesso ao namespace de hubs de eventos para um intervalo limitado de endereços IP ou um endereço IP específico usando regras de firewall.

1. Clique no botão de opção **redes selecionadas** na parte superior da página para habilitar o restante da página com opções de menu.
  ![redes selecionadas](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. Na seção **Firewall** , na grade ***intervalo de endereços*** , você pode adicionar um ou vários endereços IP específicos ou intervalos de endereços IP.
  ![adicionar endereços IP](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall.png)
3. Depois de adicionar vários endereços IP (ou intervalos de endereços IP), clique em **salvar** na faixa de faixas superior para garantir que a configuração seja salva no lado do serviço. Aguarde alguns minutos para que a confirmação seja exibida nas notificações do Portal.
  ![adicionar endereços IP e clicar em salvar](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall-hitting-save.png)

## <a name="adding-your-current-ip-address-to-the-firewall-rules"></a>Adicionando seu endereço IP atual às regras de firewall

1. Você também pode adicionar seu endereço IP atual rapidamente marcando a caixa de seleção ***Adicionar seu endereço IP do cliente (seu endereço IP atual)*** logo acima da grade do ***intervalo de endereços*** .
  ![adicionar o endereço IP atual](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save.png)
2. Depois de adicionar o endereço IP atual às regras de firewall, clique em **salvar** na faixa de faixas superior para garantir que a configuração seja salva no lado do serviço. Aguarde alguns minutos para que a confirmação seja exibida nas notificações do Portal.
  ![adicionar o endereço IP atual e clicar em salvar](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save-after-saving.png)

## <a name="conclusion"></a>Conclusão

Neste tutorial, você integrou os pontos de extremidade de rede virtual e as regras de firewall com um namespace existente de hubs de eventos. Você aprende como:
> [!div class="checklist"]
> * Como integrar pontos de extremidade de serviço de redes virtuais com seu namespace de hubs de eventos.
> * Como configurar o firewall (filtragem de IP) com seu namespace de hubs de eventos.


[Azure portal]: https://portal.azure.com/
