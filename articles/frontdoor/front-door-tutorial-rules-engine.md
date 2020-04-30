---
title: Porta da frente azure [ Microsoft Docs
description: Este artigo apresenta uma descrição geral do Azure Front Door. Descubra se é a escolha certa para equilibrar o tráfego de utilizadores para a sua aplicação.
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
ms.openlocfilehash: 4574597c0b93f2985953bfbc815cca220ecc4f28
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515602"
---
# <a name="configure-your-rules-engine"></a>Configure o seu Motor de Regras 

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="configure-rules-engine-in-azure-portal"></a>Configure Rules Engine no portal Azure 
1. Antes de criar uma configuração do motor Regras, [crie uma porta frontal](quickstart-create-front-door.md).

2. Dentro do recurso da porta da frente, vá a **Definições** e selecione **configuração do Motor de Regra**. Clique em **Adicionar,** dê um nome à sua configuração e comece a criar a sua primeira configuração do Motor de Regras. 

![encontrar regras motor](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

3. Clique em **Adicionar Regra** para criar a sua primeira regra. Em seguida, clicando em **Adicionar condição** ou **adicionar ação** pode definir a sua regra. 
    
    *Notas:*
    - Para eliminar uma condição ou ação por regra, utilize o caixote do lixo do lado direito da condição ou ação específica.
    - Para criar uma regra que se aplique a todo o tráfego de entrada, não especifique quaisquer condições. 
    - Para parar de avaliar as regras uma vez cumprida a primeira condição do jogo, verifique **Parar de avaliar a regra**. 

![encontrar regras motor](./media/front-door-rules-engine/rules-engine-tutorial-4.png)

4. Determine a prioridade das regras dentro da sua configuração utilizando o Movimento para cima, mova-se para baixo e mova-se para os botões superiores. A prioridade está na ordem ascendente, o que significa que a regra primeira listada é a regra mais importante. 

5. Depois de ter criado uma ou mais regras, prima **Save**. Esta ação cria a configuração do Motor de Regras. 

6. Depois de ter criado uma ou mais configurações, associe uma configuração do Motor de Regras com uma Regra de Rota. Embora uma única configuração possa ser aplicada a muitas regras de rota, uma regra de Rota só pode conter uma configuração do Motor de Regras. Para fazer a associação, vá às regras da > **Rota**do **Designer da Porta Da Frente.** Selecione a regra Rota a que pretende adicionar a configuração do motor Regras, vá aos **detalhes** > da Rota Configuração do**motor,** e selecione a configuração que deseja associar. 

![encontrar regras motor](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Configure Rules Engine em Azure CLI 

1. Se ainda não o fez, [instale o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Adicione a extensão "porta da frente":- extensão az adicionar --nome porta da frente. Em seguida, inicie sessão e altere para o conjunto de conta az de subscrição -- subscrição <name_or_Id>. 

2. Comece por criar um Motor de Regras - este exemplo mostra uma regra com uma ação baseada em cabeçalho e uma condição de correspondência. 

```azurecli-interactive
az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
```

2.  Enumera todas as regras. 

```azurecli-interactive
az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
```

3.  Adicione uma rota de reencaminhamento de ação. 

```azurecli-interactive
az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
```

4.  Enumera todas as ações numa regra. 

```azurecli-interactive
az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
```

5. Ligue uma configuração do motor de regras a uma regra de encaminhamento.  

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
```

6. Desvincular o motor. 

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
```

Para mais informações, uma lista completa de comandos afd regras motor pode ser encontrada [aqui](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest).   

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Motor de Regras Da AFD](front-door-rules-engine.md). 
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
- Confira mais na [referência CLI](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest)do motor de regras DaAD . 
- Confira mais na referência a Motor [PowerShell](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0)das Regras AFD . 
