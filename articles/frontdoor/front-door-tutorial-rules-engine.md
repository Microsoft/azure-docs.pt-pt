---
title: Azure Front Door
description: Este artigo fornece um tutorial sobre como configurar o Motor de Regras tanto no portal Azure como no CLI.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: a931a12889cec67baf6ef2db09091c8ec581ef08
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85321567"
---
# <a name="configure-your-rules-engine"></a>Configure o seu motor de regras

Este artigo fornece passos para criar uma configuração do Motor de Regras e a sua primeira regra tanto no portal Azure como no CLI. 

## <a name="configure-rules-engine-in-azure-portal"></a>Configure o motor de regras no portal Azure
1. Antes de criar uma configuração do motor Rules, [crie uma porta frontal](quickstart-create-front-door.md).

2. Dentro do recurso da porta frontal, vá a **Definições** e selecione **a configuração do Motor de Regra**. Clique **em Adicionar,** dê um nome à sua configuração e comece a criar a sua primeira configuração do Motor de Regras.

    ![encontrar motor de regras](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

3. Clique **em Adicionar Regra** para criar a sua primeira regra. Em seguida, clicando **em Adicionar condição** ou adicionar **ação** pode definir a sua regra.
    
    > [!NOTE]
    >- Para eliminar uma condição ou ação da regra, utilize o caixote do lixo no lado direito da condição ou ação específicas.
    > - Para criar uma regra que se aplique a todo o tráfego de entrada, não especifique quaisquer condições.
    > - Para parar de avaliar as regras uma vez cumprida a primeira condição de jogo, verifique **pare de avaliar a regra restante**. Se isto for verificado e todas as condições de jogo de uma determinada regra forem cumpridas, então as restantes regras na configuração não serão executadas.  

    ![encontrar motor de regras](./media/front-door-rules-engine/rules-engine-tutorial-4.png) 

4. Determine a prioridade das regras dentro da sua configuração utilizando o Move up, Move down e Move para os botões superiores. A prioridade está na ordem ascendente, o que significa que a regra listada pela primeira vez é a regra mais importante.

5. Depois de criar uma ou mais regras, prima **Save**. Esta ação cria a configuração do Motor de Regras.

6. Uma vez criada uma ou mais configurações, associe uma configuração do Motor de Regras com uma Regra de Rota. Embora uma única configuração possa ser aplicada a muitas regras de rota, uma regra de rota pode conter apenas uma configuração do Motor de Regras. Para fazer a associação, vá às regras da Rota **do Designer da Porta da Frente.**  >  **Route rules** Selecione a regra rota a que pretende adicionar a configuração do motor Rules para, ir para **os detalhes da Rota**  >  **Configuração**do motor e selecione a configuração que gostaria de associar.

    ![encontrar motor de regras](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Configure o motor das regras em Azure CLI

1. Se ainda não o fez, instale [o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Adicione a extensão "porta da frente":- extensão az adicionar --nome porta-frente. Em seguida, faça login e mude para a sua conta az de subscrição -- subscrição <name_or_Id>.

2. Comece por criar um Motor de Regras - este exemplo mostra uma regra com uma ação baseada no cabeçalho e uma condição de correspondência. 

```azurecli-interactive
az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
```

3. Enumerar todas as regras. 

```azurecli-interactive
az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
```

4. Adicione uma rota de encaminhamento sobreposição. 

```azurecli-interactive
az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
```

5. Enumerar todas as ações numa regra. 

```azurecli-interactive
az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
```

6. Ligue uma configuração do motor de regras a uma regra de encaminhamento.  

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
```

7. Desvincular o motor das regras. 

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
```

Para mais informações, uma lista completa dos comandos do MOTOR de Regras AFD pode ser consultada [aqui.](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest)   

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o MOTOR DE Regras DA AFD](front-door-rules-engine.md). 
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
- Confira mais na referência [CLI](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest)do motor de regras da AFD. 
- Confira mais na referência AFD Rules Engine [PowerShell](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0). 
