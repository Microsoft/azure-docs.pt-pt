---
title: Configure o itinerário de tráfego multivalorizado - Gestor de Tráfego Azure
description: Este artigo explica como configurar o Gestor de Tráfego para encaminhar o tráfego para os pontos finais A/AAAA.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: rohink
ms.openlocfilehash: daf7d09916d276130e337f7acea738228ee23707
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76938771"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Configure método de encaminhamento MultiValue no Gestor de Tráfego

Este artigo descreve como configurar o método de encaminhamento de tráfego MultiValue. O método de encaminhamento de tráfego **Multivalue** permite-lhe devolver vários pontos finais saudáveis e ajuda a aumentar a fiabilidade da sua aplicação, uma vez que os clientes têm mais opções para se retentar sem ter de fazer outra procura de DNS. O encaminhamento MultiValue está ativado apenas para perfis que tenham todos os seus pontos finais especificados utilizando endereços IPv4 ou IPv6. Quando uma consulta é recebida para este perfil, todos os pontos finais saudáveis são devolvidos com base na contagem máxima de devolução configurável especificada. 

>[!NOTE]
> Neste momento, a adição de pontos finais utilizando endereços IPv4 ou IPv6 é suportada apenas para pontos finais do tipo **Externo** e, portanto, o encaminhamento MultiValue também é suportado apenas para tais pontos finais.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em https://portal.azure.com.
## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos para o perfil do Gestor de Tráfego.
1. No painel esquerdo do portal Azure, selecione **Grupos de Recursos**.
2. Nos **grupos De recursos**, no topo da página, selecione **Adicionar**.
3. No nome do **grupo Recursos,** escreva um nome *myResourceGroupTM1*. Para obter a localização do **grupo Resource,** selecione **East US**, e, em seguida, selecione **OK**.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego
Crie um perfil de Gestor de Tráfego que direcione o tráfego do utilizador enviando-os para o ponto final com menor latência.

1. No lado superior esquerdo do ecrã, selecione **Criar um** > **perfil** > de Gestor de Tráfego de**Rede** > de recursos**Create**.
2. No **perfil Create Traffic Manager,** introduza ou selecione, as seguintes informações, aceite as predefinições para as definições restantes e, em seguida, selecione **Criar:**
    
    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Nome                   | Este nome tem de ser exclusivo na zona trafficmanager.net e dá origem ao nome DNS, trafficmanager.net, que é utilizado para aceder ao perfil do seu Gestor de Tráfego.                                   |
    | Método de encaminhamento          | Selecione o método de encaminhamento **Multivalue.**                                       |
    | Subscrição            | Selecione a sua subscrição.                          |
    | Grupo de recursos          | Selecione *myResourceGroupTM1*. |
    | Localização                | Esta definição refere-se à localização do grupo de recursos e não tem qualquer impacto no perfil do Gestor de Tráfego que vai ser implementado globalmente.                              |
   |        |           | 
  
   ![Criar um perfil do Gestor de Tráfego](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego

Adicione dois endereços IP como pontos finais externos ao perfil do Gestor de Tráfego MultiValue que criou na etapa anterior.

1. Na barra de pesquisa do portal, procure o nome do perfil do Gestor de Tráfego que criou na secção anterior e selecione-o nos resultados apresentados.
2. Em **Perfil do Gestor de Tráfego **, na secção **Definições**, clique em **Pontos Finais** e em **Adicionar**.
3. Introduza, ou selecione, as seguintes informações, aceite as predefinições para as definições restantes e, em seguida, selecione **OK:**

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Tipo                    | Ponto final externo                                   |
    | Nome           | myEndpoint1                                        |
    | Nome de domínio totalmente qualificado (FQDN) ou IP           | Digite o endereço IP público do ponto final que pretende adicionar a este perfil do Gestor de Tráfego                         |
    |        |           |

4. Repita os passos 2 e 3 para adicionar outro ponto final denominado *myEndpoint2,* para nome de **domínio totalmente qualificado (FQDN) ou IP**, introduza o endereço IP público do segundo ponto final.
5. Quando a adição de ambos os pontos finais estiver concluída, estes são apresentados em **Perfil do Gestor de Tráfego**, juntamente com o respetivo estado de monitorização como **Online**.

   ![Adicionar um ponto final do Gestor de Tráfego](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [método de encaminhamento de tráfego ponderado](traffic-manager-configure-weighted-routing-method.md).
- Saiba mais sobre o [método de encaminhamento prioritário](traffic-manager-configure-priority-routing-method.md).
- Saiba mais sobre o método de [encaminhamento de desempenho](traffic-manager-configure-performance-routing-method.md)
- Saiba mais sobre o [método de encaminhamento geográfico](traffic-manager-configure-geographic-routing-method.md).


