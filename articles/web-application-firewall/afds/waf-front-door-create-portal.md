---
title: 'Tutorial: Criar política da WAF para Azure Front Door - Portal Azure'
description: Neste tutorial, aprende-se a criar uma política de Firewall de Aplicação Web (WAF) utilizando o portal Azure.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: victorh
ms.openlocfilehash: e7b4544530dc9c0c894ae7a0f2b1d2830f895928
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122261"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Tutorial: Criar uma política de Firewall de aplicação web na porta frontal do Azure usando o portal Azure

Este tutorial mostra-lhe como criar uma política básica de Firewall de Aplicação Web (WAF) e aplicá-la a um anfitrião frontal na Azure Front Door.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma política de WAF
> * Associe-o a um anfitrião frontal
> * Regras de configuração da WAF

## <a name="prerequisites"></a>Pré-requisitos

Crie uma [porta frontal](../../frontdoor/quickstart-create-front-door.md) ou um perfil [Standard/Premium da porta da frente.](../../frontdoor/standard-premium/create-front-door-portal.md) 

## <a name="create-a-web-application-firewall-policy"></a>Criar uma política de Firewall de Aplicação Web

Em primeiro lugar, crie uma política de WAF básica com o Conjunto de Regras Padrão (DRS) gerido utilizando o portal. 

1. No lado superior esquerdo do ecrã, selecione **Criar um recurso** > procurar **WAF** > selecione Web Application **Firewall (WAF)** > **selecionar Criar**.

1. No separador **Básico da** página de política **Da WAF,** introduza ou selecione as seguintes informações, aceite as predefinições para as definições restantes e, em seguida, selecione **Rever + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Política para              | Selecione **GLOBAL WAF (Porta da frente)**. |
    | Porta da frente SKU          | Selecione entre SKU básico, padrão e premium. |
    | Subscrição            | Selecione o nome da subscrição da porta da frente.|
    | Grupo de recursos          | Selecione o nome do grupo de recursos da porta da frente.|
    | Nome da política             | Insira um nome único para a sua política WAF.|
    | Estado político            | Definido como **Ativado**. | 

   :::image type="content" source="../media/waf-front-door-create-portal/basic.png" alt-text="Screenshot da página de política Create a F, com um Review + criar caixas de botão e lista para a subscrição, grupo de recursos e nome de política.":::

1. No separador **Associação** da página **de política Da WAF,** selecione + Associar um perfil da porta da **frente,** introduzir as seguintes definições e, em seguida, selecionar **Adicionar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Perfil da porta da frente              | Selecione o nome do perfil da porta da frente. |
    | Domínios          | Selecione os domínios a que pretende associar a política de WAF e, em seguida, selecione **Adicionar**. |

    :::image type="content" source="../media/waf-front-door-create-portal/associate-profile.png" alt-text="Screenshot do associado uma página de perfil da Porta da Frente.":::
    
    > [!NOTE]
    > Se o domínio estiver associado a uma política de WAF, é mostrado como acinzentado. Primeiro, deve remover o domínio da política associada e, em seguida, voltar a associar o domínio a uma nova política da WAF.

1. Selecione **Rever + criar** e, em seguida, selecione **Criar**.

## <a name="configure-web-application-firewall-rules-optional"></a>Configure as regras de Firewall de Aplicação Web (opcional)

### <a name="change-mode"></a>Alterar o modo

Quando cria uma política WAF, pela política padrão de WAF está no modo **deteção.** No modo **deteção,** a WAF não bloqueia quaisquer pedidos, em vez disso, os pedidos correspondentes às regras WAF são registados nos registos WAF.
Para ver o WAF em ação, pode alterar as definições de modo de **Deteção** para **Prevenção**. No modo **prevenção,** os pedidos que correspondam às regras definidas no Conjunto de Regras Predefinidas (DRS) são bloqueados e registados nos registos WAF.

 :::image type="content" source="../media/waf-front-door-create-portal/policy.png" alt-text="Screenshot da secção de definições de Política. O alternador de modo está definido para Prevenção.":::

### <a name="custom-rules"></a>Regras personalizadas

Pode criar uma regra personalizada selecionando a **regra personalizada De** acordo com a secção de **regras personalizadas.** Isto lança a página de configuração de regras personalizadas. 

:::image type="content" source="../media/waf-front-door-create-portal/custom-rules.png" alt-text="Screenshot da página de regras personalizadas.":::

Abaixo está um exemplo de configuração de uma regra personalizada para bloquear um pedido se a cadeia de consulta contiver **blockme**.

:::image type="content" source="../media/waf-front-door-create-portal/customquerystring2.png" alt-text="Screenshot da página de configuração de regras personalizadas mostrando definições para uma regra que verifica se a variável QueryString contém o bloco de valor.":::

### <a name="default-rule-set-drs"></a>Conjunto de regras predefinidos (DRS)

O Conjunto de Regras Predefinidas geridos pelo Azure é ativado por padrão. A versão padrão atual é DefaultRuleSet_1.0. De **acordo com as regras** da WAF Managed , **Atribua,** o ruleset Microsoft_DefaultRuleSet_1.1 recentemente disponível está disponível na lista de drop down.

Para desativar uma regra individual, selecione a caixa de **verificação** à frente do número de regra e selecione **Desativar** no topo da página. Para alterar tipos de ações para regras individuais dentro do conjunto de regras, selecione a caixa de verificação à frente do número de regras e, em seguida, selecione a **ação 'Alterar'** no topo da página.

:::image type="content" source="../media/waf-front-door-create-portal/managed-rules.png" alt-text="Screenshot da página de regras geridas mostrando um conjunto de regras, grupos de regras, regras e botões de Ativação, Desativação e Alteração de Ação." lightbox="../media/waf-front-door-create-portal/managed-rules-expanded.png":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, remova o grupo de recursos e todos os recursos relacionados.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre a Porta](../../frontdoor/front-door-overview.md) 
>  frontal Azure [Saiba mais sobre o Azure Front Door Standard/Premium](../../frontdoor/standard-premium/overview.md)
