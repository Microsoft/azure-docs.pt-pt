---
title: 'Tutorial: Configurar o encaminhamento prioritário de tráfego com o Gestor de Tráfego da Azure'
description: Este tutorial explica como configurar o método de encaminhamento de tráfego prioritário no Gestor de Tráfego
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: duau
ms.openlocfilehash: 1835377f4690097c8390957bf7d897242ba7aace
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92208061"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Tutorial: Configurar método de encaminhamento prioritário de tráfego em Gerente de Tráfego

Este tutorial descreve como usar o Gestor de Tráfego Azure para encaminhar o tráfego do utilizador para pontos finais específicos, utilizando o método de encaminhamento prioritário. Neste método de encaminhamento, definirá a ordem de cada ponto final que vai para a configuração do perfil do Gestor de Tráfego. O tráfego dos utilizadores será encaminhado para o ponto final na ordem em que estão listados. Este método de encaminhamento é útil quando pretende configurar para o failover de serviço. O ponto final primário recebe um número prioritário de '1' e irá atender todos os pedidos de entrada. Enquanto os pontos finais de menor prioridade funcionarão como backups.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Crie um perfil de Gestor de Tráfego com encaminhamento prioritário.
> - Adicione pontos finais.
> - Configure prioridade dos pontos finais.
> - Utilize o perfil de Gestor de Tráfego.
> - Apague o perfil do Gestor de Tráfego.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="to-configure-the-priority-traffic-routing-method"></a>Para configurar o método prioritário de encaminhamento de tráfego
1. Num browser, inicie sessão no [portal do Azure](https://portal.azure.com).

1. **Selecione + Crie um recurso** no lado esquerdo. Procure o **perfil de Gestor de Tráfego** e selecione **Criar**.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-priority-profile.png" alt-text="Criar um perfil prioritário do Gestor de Tráfego":::

1. Na página de *perfil do Gestor de Tráfego,* defina as seguintes definições:

    | Definição         | Valor                                              |
    | ---             | ---                                                |
    | Nome            | Forneça um nome para o seu perfil. Este nome tem de ser único dentro da zona trafficmanager.net. Para aceder ao seu perfil de Gestor de Tráfego, utilize o nome DNS `<profilename>.trafficmanager.net` . |    
    | Método de encaminhamento  | Selecione **Prioridade**. |
    | Subscrição    | Selecione a sua subscrição. |
    | Grupo de recursos   | Utilize um grupo de recursos existente ou crie um novo grupo de recursos para colocar este perfil sob. Se optar por criar um novo grupo de recursos, utilize a *localização do Grupo de Recursos* para especificar a localização do grupo de recursos. Esta definição refere-se à localização do grupo de recursos, e não tem qualquer impacto no perfil do Gestor de Tráfego que é implantado globalmente. |

1. Selecione **Criar** para implementar o seu perfil de Gestor de Tráfego.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-profile-priority.png" alt-text="Criar uma prioridade de perfil do Gestor de Tráfego":::

## <a name="add-endpoints"></a>Adicionar pontos finais

1. Selecione o perfil de Gestor de Tráfego da lista.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-list.png" alt-text="Lista de perfis do Gestor de Tráfego":::

1. Selecione **pontos de final** em *Definições* e selecione **+ Adicione** para adicionar um novo ponto final.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-add-endpoints.png" alt-text="Gestor de tráfego adicionar pontos finais":::

1. Selecione ou introduza as seguintes definições: 

    | Definição                | Valor                                              |
    | ---                    | ---                                                |
    | Tipo                   | Selecione o tipo de ponto final. |    
    | Name                   | Dê um nome para identificar este ponto final. |
    | Tipo de recurso de destino   | Selecione o tipo de recurso para o alvo. |
    | Recurso de destino        | Selecione o recurso da lista. |
    | Prioridade               | Dê um número prioritário para este ponto final. 1 é a maior prioridade. |


1. **Selecione Adicionar** para adicionar o ponto final. Repita o passo 2 e 3 para adicionar pontos finais adicionais. Lembre-se de definir o número de prioridade apropriado.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/add-endpoint.png" alt-text="Adicionar prioridade 1 ponto final":::

1. Na página **Endpoints,** reveja a ordem prioritária para os seus pontos finais. Quando selecionar o método de encaminhamento de tráfego **prioritário,** a ordem dos pontos finais selecionados importa. Verifique a ordem prioritária dos pontos finais.  O ponto final principal está no topo. Verifique duas vezes a ordem que é exibida. Todos os pedidos serão encaminhados para o primeiro ponto final e se o Gestor de Tráfego detetar que não é saudável, o tráfego automaticamente falha para o próximo ponto final. 

    :::image type="content" source="./media/traffic-manager-priority-routing-method/endpoints-list.png" alt-text="Lista de pontos finais prioritários":::

1. Para alterar a ordem de prioridade do ponto final, selecione o ponto final, altere o valor prioritário e selecione **Guardar** para guardar as definições do ponto final.

## <a name="use-the-traffic-manager-profile"></a>Utilize o perfil do Gestor de Tráfego

1.  Na barra de pesquisa do portal, procure o nome de **perfil do Gestor de Tráfego** que criou na secção anterior e selecione o perfil do gestor de tráfego nos resultados que o apresentado.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/search-traffic-manager-profile.png" alt-text="Perfil do Gestor de Tráfego de Pesquisa":::

1.  A página de **visão** geral do gestor de tráfego apresenta o nome DNS do seu perfil de Gestor de Tráfego recém-criado. Isto pode ser usado por qualquer cliente (por exemplo, navegando para ele usando um navegador web) para ser encaminhado para o ponto final certo, conforme determinado pelo tipo de encaminhamento. Neste caso, todos os pedidos são encaminhados para o primeiro ponto final e se o Gestor de Tráfego detetar que não é saudável, o tráfego automaticamente falha para o próximo ponto final.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-dns-name.png" alt-text="Nome DNS do Gestor de Tráfego":::

1. Assim que o seu perfil de Gestor de Tráfego estiver a funcionar, edite o registo DNS no seu servidor DNS autoritário para apontar o nome de domínio da sua empresa para o nome de domínio do Gestor de Tráfego.

## <a name="clean-up-resources"></a>Limpar os recursos

Se já não necessitar do perfil do Gestor de Tráfego, localize o perfil e selecione **Excluir o perfil**.

:::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-delete-priority-profile.png" alt-text="Eliminar perfil prioritário do Gestor de Tráfego":::

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o método de encaminhamento prioritário, consulte:

> [!div class="nextstepaction"]
> [Método de encaminhamento prioritário](traffic-manager-routing-methods.md#priority-traffic-routing-method)
