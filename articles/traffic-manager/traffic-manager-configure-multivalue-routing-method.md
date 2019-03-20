---
title: Configurar o método de encaminhamento de tráfego de valores múltiplos no Gestor de tráfego do Azure
description: Este artigo explica como configurar o Gestor de tráfego para encaminhar o tráfego para pontos finais de A/AAAA.
services: traffic-manager
documentationcenter: ''
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: kumud
ms.openlocfilehash: de7288e2062fdfab363c46749b34c7afcacbb6e1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094808"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Configurar o método de encaminhamento de valores múltiplos no Gestor de tráfego

Este artigo descreve como configurar o método de encaminhamento de tráfego de vários valores. O **Multivalue** tráfego permite-lhe devolver vários pontos de extremidade de bom estado de funcionamento e ajuda a aumentar a fiabilidade da sua aplicação, uma vez que os clientes têm mais opções para tentar novamente sem ter de fazer outra pesquisa de DNS para o método de encaminhamento. Encaminhamento de valores múltiplos está ativada apenas para perfis de que tem todos os seus pontos de extremidade especificados com endereços IPv4 ou IPv6. Quando uma consulta é recebida para este perfil, todos os pontos finais de bom estado de funcionamento são devolvidos com base na contagem de retorna máxima configurável especificada. 

>[!NOTE]
> Neste momento, adicionar pontos finais a utilizar endereços IPv4 ou IPv6 só é suportada para pontos finais do tipo **externo** e, por conseguinte, encaminhamento de valores múltiplos também só é suportada para esses pontos de extremidade.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em https://portal.azure.com.
## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos para o perfil do Gestor de tráfego.
1. No painel esquerdo do portal do Azure, selecione **grupos de recursos**.
2. Na **grupos de recursos**, no topo da página, selecione **Add**.
3. Na **nome do grupo de recursos**, escreva um nome *myResourceGroupTM1*. Para **localização do grupo de recursos**, selecione **E.U.A. Leste**e, em seguida, selecione **OK**.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego
Crie um perfil do Gestor de tráfego que direciona o tráfego de utilizadores, enviando-as para o ponto final com latência mais baixa.

1. No canto superior esquerdo do ecrã, selecione **Criar um recurso** > **Rede** > **Perfil do Gestor de Tráfego** > **Criar**.
2. Na **Gestor de tráfego criar perfil**, introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **criar**:
    
    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Name                   | Este nome tem de ser exclusivo na zona trafficmanager.net e dá origem ao nome DNS, trafficmanager.net, que é utilizado para aceder ao perfil do seu Gestor de Tráfego.                                   |
    | Método de encaminhamento          | Selecione o **Multivalue** método de encaminhamento.                                       |
    | Subscrição            | Selecione a sua subscrição.                          |
    | Grupo de recursos          | Select *myResourceGroupTM1*. |
    | Localização                | Esta definição refere-se à localização do grupo de recursos e não tem qualquer impacto no perfil do Gestor de Tráfego que vai ser implementado globalmente.                              |
   |        |           | 
  
   ![Criar um perfil do Gestor de Tráfego](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego

Adicione dois endereços IP como pontos finais externos para o perfil do Gestor de tráfego de MultiValue que criou no passo anterior.

1. Na barra de pesquisa do portal, procure o nome do perfil do Gestor de Tráfego que criou na secção anterior e selecione-o nos resultados apresentados.
2. Em **Perfil do Gestor de Tráfego** , na secção **Definições**, clique em **Pontos Finais** e em **Adicionar**.
3. Introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **OK**:

    | Definição                 | Value                                              |
    | ---                     | ---                                                |
    | Type                    | Ponto final externo                                   |
    | Name           | myEndpoint1                                        |
    | Nome de domínio completamente qualificado (FQDN) ou IP           | Escreva o endereço IP público do ponto final que pretende adicionar a este perfil do Gestor de tráfego                         |
    |        |           |

4. Repita os passos 2 e 3 para adicionar outro ponto final com o nome *myEndpoint2*, para **nome de domínio completamente qualificado (FQDN) ou IP**, introduza o endereço IP público do segundo ponto de extremidade.
5. Quando a adição de ambos os pontos finais estiver concluída, estes são apresentados em **Perfil do Gestor de Tráfego**, juntamente com o respetivo estado de monitorização como **Online**.

   ![Adicionar um ponto final do Gestor de Tráfego](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [método de encaminhamento de tráfego ponderado](traffic-manager-configure-weighted-routing-method.md).
- Saiba mais sobre o [método de encaminhamento prioritário](traffic-manager-configure-priority-routing-method.md).
- Saiba mais sobre [método de encaminhamento de desempenho](traffic-manager-configure-performance-routing-method.md)
- Saiba mais sobre o [método de encaminhamento geográfico](traffic-manager-configure-geographic-routing-method.md).


