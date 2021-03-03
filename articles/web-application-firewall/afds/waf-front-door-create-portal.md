---
title: 'Tutorial: Criar política da WAF para Azure Front Door - Portal Azure'
description: Neste tutorial, aprende-se a criar uma política de Firewall de Aplicação Web (WAF) utilizando o portal Azure.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: victorh
ms.openlocfilehash: 8b1d1007e817bafe3d75f0f0d7c3fc6eb5470854
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729475"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Tutorial: Criar uma política de Firewall de aplicação web na porta frontal do Azure usando o portal Azure

Este tutorial mostra-lhe como criar uma política básica de Firewall de Aplicação Web (WAF) e aplicá-la a um anfitrião frontal na Azure Front Door.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma política de WAF
> * Associe-o a um anfitrião frontal
> * Regras de configuração da WAF

## <a name="prerequisites"></a>Pré-requisitos

Crie um perfil da porta frontal seguindo as instruções descritas no [Quickstart: Crie um perfil da porta da frente](../../frontdoor/quickstart-create-front-door.md). 

## <a name="create-a-web-application-firewall-policy"></a>Criar uma política de Firewall de Aplicação Web

Em primeiro lugar, crie uma política de WAF básica com o Conjunto de Regras Padrão (DRS) gerido utilizando o portal. 

1. No lado superior esquerdo do ecrã, selecione **Criar um recurso**>procurar **waf**>selecionar firewall de **aplicação Web (Pré-visualização)** > **selecionar Criar**.
2. No separador **Básico da** página de política **Da WAF,** introduza ou selecione as seguintes informações, aceite as predefinições para as definições restantes e, em seguida, selecione **Rever + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição            |Selecione o nome da subscrição da porta da frente.|
    | Grupo de recursos          |Selecione o nome do grupo de recursos da porta da frente.|
    | Nome da política             |Insira um nome único para a sua política WAF.|

   :::image type="content" source="../media/waf-front-door-create-portal/basic.png" alt-text="Screenshot da página de política Create a F, com um Review + criar caixas de botão e lista para a subscrição, grupo de recursos e nome de política." border="false":::

3. No separador **Associação** da página **política 'Criar uma política DA WAF',** selecione adicionar **frontend,** introduza as seguintes definições e, em seguida, selecione **Adicionar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Porta da frente              | Selecione o nome do perfil da porta da frente.|
    | Anfitrião frontend           | Selecione o nome do anfitrião da porta da frente e, em seguida, **selecione Adicionar**.|
    
    > [!NOTE]
    > Se o anfitrião frontal estiver associado a uma política de WAF, é mostrado como acinzentado. Primeiro, deve remover o anfitrião frontal da política associada e, em seguida, voltar a associar o anfitrião frontal a uma nova política da WAF.
1. Selecione **Rever + criar** e, em seguida, selecione **Criar**.

## <a name="configure-web-application-firewall-rules-optional"></a>Configure as regras de Firewall de Aplicação Web (opcional)

### <a name="change-mode"></a>Alterar o modo

Quando cria uma política WAF, pela política padrão de WAF está no modo **deteção.** No modo **deteção,** a WAF não bloqueia quaisquer pedidos, em vez disso, os pedidos correspondentes às regras WAF são registados nos registos WAF.
Para ver o WAF em ação, pode alterar as definições de modo de **Deteção** para **Prevenção**. No modo **prevenção,** os pedidos que correspondam às regras definidas no Conjunto de Regras Predefinidas (DRS) são bloqueados e registados nos registos WAF.

 :::image type="content" source="../media/waf-front-door-create-portal/policy.png" alt-text="Screenshot da secção de definições de Política. O alternador de modo está definido para Prevenção." border="false":::

### <a name="custom-rules"></a>Regras personalizadas

Pode criar uma regra personalizada selecionando a **regra personalizada De** acordo com a secção de **regras personalizadas.** Isto lança a página de configuração de regras personalizadas. Abaixo está um exemplo de configuração de uma regra personalizada para bloquear um pedido se a cadeia de consulta contiver **blockme**.

:::image type="content" source="../media/waf-front-door-create-portal/customquerystring2.png" alt-text="Screenshot da página de configuração de regras personalizadas mostrando definições para uma regra que verifica se a variável QueryString contém o bloco de valor." border="false":::

### <a name="default-rule-set-drs"></a>Conjunto de regras predefinidos (DRS)

O Conjunto de Regras Predefinidas geridos pelo Azure é ativado por padrão. A versão padrão atual é DefaultRuleSet_1.0. De **acordo com as regras** da WAF Managed , **Atribua,** o ruleset Microsoft_DefaultRuleSet_1.1 recentemente disponível está disponível na lista de drop down.

Para desativar uma regra individual dentro de um grupo de regras, expanda as regras dentro desse grupo de regras, selecione a caixa de **verificação** à frente do número de regras e selecione **Desativar** o separador acima. Para alterar tipos de ações para regras individuais dentro do conjunto de regras, selecione a caixa de verificação à frente do número de regra e, em seguida, selecione o separador **de ação Change** acima.

 :::image type="content" source="../media/waf-front-door-create-portal/managed2.png" alt-text="Screenshot da página de regras geridas mostrando um conjunto de regras, grupos de regras, regras e botões de Ativação, Desativação e Alteração de Ação. Uma regra é verificada." border="false":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, remova o grupo de recursos e todos os recursos relacionados.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre a Porta frontal Azure](../../frontdoor/front-door-overview.md)
