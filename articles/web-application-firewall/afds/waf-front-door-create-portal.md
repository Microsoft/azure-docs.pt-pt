---
title: 'Tutorial: Criar política da WAF para Azure Front Door - Portal Azure'
description: Neste tutorial, aprende-se a criar uma política de Firewall de Aplicação Web (WAF) utilizando o portal Azure.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: victorh
ms.openlocfilehash: 7c7ea5297276ed9a1d1f2ca8f4190997dcab57c3
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602222"
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

   ![Criar uma política de WAF](../media/waf-front-door-create-portal/basic.png)

3. No separador **Associação** da página **política 'Criar uma política DA WAF',** selecione adicionar **frontend,** introduza as seguintes definições e, em seguida, selecione **Adicionar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Porta da frente              | Selecione o nome do perfil da porta da frente.|
    | Anfitrião frontend           | Selecione o nome do anfitrião da porta da frente e, em seguida, **selecione Adicionar**.|
    
    > [!NOTE]
    > Se o anfitrião frontal estiver associado a uma política de WAF, é mostrado como acinzentado. Primeiro, deve remover o anfitrião frontal da política associada e, em seguida, voltar a associar o anfitrião frontal a uma nova política da WAF.
1. Selecione **'Rever + criar'** e, em seguida, selecione **Criar**.

## <a name="configure-web-application-firewall-rules-optional"></a>Configure as regras de Firewall de Aplicação Web (opcional)

### <a name="change-mode"></a>Alterar o modo

Quando cria uma política WAF, pela política padrão de WAF está no modo **deteção.** No modo **deteção,** a WAF não bloqueia quaisquer pedidos, em vez disso, os pedidos correspondentes às regras WAF são registados nos registos WAF.
Para ver o WAF em ação, pode alterar as definições de modo de **Deteção** para **Prevenção**. No modo **prevenção,** os pedidos que correspondam às regras definidas no Conjunto de Regras Predefinidas (DRS) são bloqueados e registados nos registos WAF.

 ![Alterar o modo de política DAA](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Regras personalizadas

Pode criar uma regra personalizada selecionando a **regra personalizada De** acordo com a secção de **regras personalizadas.** Isto lança a página de configuração de regras personalizadas. Abaixo está um exemplo de configuração de uma regra personalizada para bloquear um pedido se a cadeia de consulta contiver **blockme**.

![Regras personalizadas](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Conjunto de regras predefinidos (DRS)

O Conjunto de Regras Predefinidas geridos pelo Azure é ativado por padrão. Para desativar uma regra individual dentro de um grupo de regras, expanda as regras dentro desse grupo de regras, selecione a caixa de **verificação** à frente do número de regras e selecione **Desativar** o separador acima. Para alterar tipos de ações para regras individuais dentro do conjunto de regras, selecione a caixa de verificação à frente do número de regra e, em seguida, selecione o separador **de ação Change** acima.

 ![Alterar conjunto de regras WAF](../media/waf-front-door-create-portal/managed2.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, remova o grupo de recursos e todos os recursos relacionados.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre a Porta frontal Azure](../../frontdoor/front-door-overview.md)