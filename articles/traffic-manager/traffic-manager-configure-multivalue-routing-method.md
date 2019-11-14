---
title: Configurar o roteamento de tráfego de vários valores-Gerenciador de tráfego do Azure
description: Este artigo explica como configurar o Gerenciador de tráfego para rotear o tráfego para pontos de extremidade a/AAAA.
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: allensu
ms.openlocfilehash: 3e5e6cb55b86df8a48f96771fb2436afa8acaa18
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040373"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Configurar o método de roteamento de vários valores no Gerenciador de tráfego

Este artigo descreve como configurar o método de roteamento de tráfego de vários valores. O método de roteamento de tráfego de **múltiplos valores** permite que você retorne vários pontos de extremidade íntegros e ajuda a aumentar a confiabilidade do seu aplicativo, já que os clientes têm mais opções para tentar novamente sem precisar fazer outra pesquisa de DNS. O roteamento de vários valores é habilitado somente para perfis que têm todos os seus pontos de extremidade especificados usando endereços IPv4 ou IPv6. Quando uma consulta é recebida para esse perfil, todos os pontos de extremidade íntegros são retornados com base na contagem de retorno máxima configurável especificada. 

>[!NOTE]
> Neste momento, a adição de pontos de extremidade usando endereços IPv4 ou IPv6 tem suporte apenas para pontos de extremidade do tipo **externo** e, portanto, o roteamento de vários valores também tem suporte apenas para esses pontos de extremidade.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em https://portal.azure.com.
## <a name="create-a-resource-group"></a>Criar um grupo de recursos:
Crie um grupo de recursos para o perfil do Gerenciador de tráfego.
1. No painel esquerdo da portal do Azure, selecione grupos de **recursos**.
2. Em **grupos de recursos**, na parte superior da página, selecione **Adicionar**.
3. Em **nome do grupo de recursos**, digite um nome *myResourceGroupTM1*. Para **local do grupo de recursos**, selecione **leste dos EUA**e, em seguida, selecione **OK**.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego
Crie um perfil do Gerenciador de tráfego que direcione o tráfego do usuário enviando-o para o ponto de extremidade com menor latência.

1. No canto superior esquerdo do ecrã, selecione **Criar um recurso** > **Rede** > **Perfil do Gestor de Tráfego** > **Criar**.
2. Em **Criar perfil do Gerenciador de tráfego**, insira ou selecione as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **criar**:
    
    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Nome                   | Este nome tem de ser exclusivo na zona trafficmanager.net e dá origem ao nome DNS, trafficmanager.net, que é utilizado para aceder ao perfil do seu Gestor de Tráfego.                                   |
    | Método de encaminhamento          | Selecione o método de roteamento de vários **valores** .                                       |
    | Subscrição            | Selecione a sua subscrição.                          |
    | Grupo de recursos          | Selecione *myResourceGroupTM1*. |
    | Localização                | Esta definição refere-se à localização do grupo de recursos e não tem qualquer impacto no perfil do Gestor de Tráfego que vai ser implementado globalmente.                              |
   |        |           | 
  
   ![Criar um perfil do Gestor de Tráfego](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego

Adicione dois endereços IP como pontos de extremidade externos ao perfil do Gerenciador de tráfego de vários valores que você criou na etapa anterior.

1. Na barra de pesquisa do portal, procure o nome do perfil do Gestor de Tráfego que criou na secção anterior e selecione-o nos resultados apresentados.
2. Em **Perfil do Gestor de Tráfego** , na secção **Definições**, clique em **Pontos Finais** e em **Adicionar**.
3. Introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **OK**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Tipo                    | Ponto de extremidade externo                                   |
    | Nome           | myEndpoint1                                        |
    | FQDN (nome de domínio totalmente qualificado) ou IP           | Digite o endereço IP público do ponto de extremidade que você deseja adicionar a este perfil do Gerenciador de tráfego                         |
    |        |           |

4. Repita as etapas 2 e 3 para adicionar outro ponto de extremidade chamado *myEndpoint2*, para **FQDN (nome de domínio totalmente qualificado) ou IP**, insira o endereço IP público do segundo ponto de extremidade.
5. Quando a adição de ambos os pontos finais estiver concluída, estes são apresentados em **Perfil do Gestor de Tráfego**, juntamente com o respetivo estado de monitorização como **Online**.

   ![Adicionar um ponto final do Gestor de Tráfego](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [método de encaminhamento de tráfego ponderado](traffic-manager-configure-weighted-routing-method.md).
- Saiba mais sobre o [método de encaminhamento prioritário](traffic-manager-configure-priority-routing-method.md).
- Saiba mais sobre o [método de roteamento de desempenho](traffic-manager-configure-performance-routing-method.md)
- Saiba mais sobre o [método de encaminhamento geográfico](traffic-manager-configure-geographic-routing-method.md).


