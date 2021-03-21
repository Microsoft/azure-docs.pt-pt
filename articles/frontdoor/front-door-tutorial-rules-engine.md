---
title: 'Tutorial: Configurar o motor das regras - Porta frontal azul'
description: Este artigo fornece um tutorial sobre como configurar o Motor de Regras tanto no portal Azure como no CLI.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/09/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5d26d36c9f4ecb4aa0c7114b1ebef066e104f175
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198868"
---
# <a name="tutorial-configure-your-rules-engine"></a>Tutorial: Configurar o seu motor de regras

Este tutorial mostra como criar uma configuração do Motor de Regras e a sua primeira regra tanto no portal Azure como no CLI. 

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Configurar o motor de regras utilizando o portal.
> - Configure Regras Motor usando Azure CLI

## <a name="prerequisites"></a>Pré-requisitos

* Para concluir os passos neste tutorial, tem primeiro de criar um Front Door. Para obter mais informações, veja [Quickstart: Create a Front Door](quickstart-create-front-door.md) (Início Rápido: Criar um Front Door).

## <a name="configure-rules-engine-in-azure-portal"></a>Configure o motor de regras no portal Azure
1. Dentro do recurso da porta frontal, vá a **Definições** e selecione **a configuração do Motor de Regra**. Clique **em Adicionar,** dê um nome à sua configuração e comece a criar a sua primeira configuração do Motor de Regras.

    ![Menu de configurações da porta da frente](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

1. Clique **em Adicionar Regra** para criar a sua primeira regra. Em seguida, clicando **em Adicionar condição** ou adicionar **ação** pode definir a sua regra.
    
    > [!NOTE]
    >- Para eliminar uma condição ou ação da regra, utilize o caixote do lixo no lado direito da condição ou ação específicas.
    > - Para criar uma regra que se aplique a todo o tráfego de entrada, não especifique quaisquer condições.
    > - Para parar de avaliar as regras uma vez cumprida a primeira condição de jogo, verifique **pare de avaliar a regra restante**. Se isto for verificado e todas as condições de jogo de uma determinada regra forem cumpridas, então as restantes regras na configuração não serão executadas.  

    ![Configuração do motor de regras](./media/front-door-rules-engine/rules-engine-tutorial-4.png) 

1. Determine a prioridade das regras dentro da sua configuração utilizando o Move up, Move down e Move para os botões superiores. A prioridade está na ordem ascendente, o que significa que a regra listada pela primeira vez é a regra mais importante.

1. Depois de criar uma ou mais regras, prima **Save**. Esta ação cria a configuração do Motor de Regras.

1. Uma vez criada uma ou mais configurações, associe uma configuração do Motor de Regras com uma Regra de Rota. Embora uma única configuração possa ser aplicada a muitas regras de rota, uma regra de rota pode conter apenas uma configuração do Motor de Regras. Para fazer a associação, vá às regras da Rota **do Designer da Porta da Frente.**  >   Selecione a regra rota a que pretende adicionar a configuração do motor Rules para, ir para **os detalhes da Rota**  >  **Configuração** do motor e selecione a configuração que gostaria de associar.

    ![Configurar uma regra de encaminhamento](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Configure o motor das regras em Azure CLI

1. Se ainda não o fez, instale [o Azure CLI](/cli/azure/install-azure-cli). Adicione a extensão "porta da frente":- extensão az adicionar --nome porta-frente. Em seguida, faça login e mude para a sua conta az de subscrição -- subscrição <name_or_Id>.

1. Comece por criar um Motor de Regras - este exemplo mostra uma regra com uma ação baseada no cabeçalho e uma condição de correspondência. 

    ```azurecli-interactive
    az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
    ```

1. Enumerar todas as regras. 

    ```azurecli-interactive
    az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
    ```

1. Adicione uma rota de encaminhamento sobreposição. 

    ```azurecli-interactive
    az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
    ```

1. Enumerar todas as ações numa regra. 

    ```azurecli-interactive
    az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
    ```

1. Ligue uma configuração do motor de regras a uma regra de encaminhamento.  

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
    ```

1. Desvincular o motor das regras. 

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
    ```

Para mais informações, uma lista completa dos comandos do MOTOR de Regras AFD pode ser consultada [aqui.](/cli/azure/ext/front-door/network/front-door/rules-engine)   

## <a name="clean-up-resources"></a>Limpar os recursos

Nos passos anteriores, configura a configuração do motor de regras e associou às suas regras de encaminhamento. Se já não pretender a configuração do Motor das Regras associada à porta da frente, pode remover a configuração executando os seguintes passos:

1. Desassociam quaisquer regras de encaminhamento da configuração do Motor de Regra clicando nos três pontos ao lado do nome do Motor de Regra.

    :::image type="content" source="./media/front-door-rules-engine/front-door-rule-engine-routing-association.png" alt-text="Regras de encaminhamento associado":::

1. Desmarque todas as regras de encaminhamento esta configuração do Motor de Regra está associada e clique em guardar.

    :::image type="content" source="./media/front-door-rules-engine/front-door-routing-rule-association.png" alt-text="Associação de regras de encaminhamento":::

1. Agora pode eliminar a configuração do Motor de Regra da porta da frente.

    :::image type="content" source="./media/front-door-rules-engine/front-door-delete-rule-engine-configuration.png" alt-text="Eliminar configuração do motor de regra":::

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

* Criar uma configuração do motor de regra
* Configuração de associado às suas regras de encaminhamento da porta da frente.

Para aprender a adicionar cabeçalhos de segurança com o Motor de Regra, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Cabeçalhos de segurança com motor de regras](front-door-security-headers.md)