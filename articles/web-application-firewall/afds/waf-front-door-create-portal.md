---
title: 'Tutorial: Criar a política de firewall de aplicação web (WAF) para a Porta da Frente Azure - portal Azure'
description: Neste tutorial, aprende-se a criar uma política waf utilizando o portal Azure.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/10/2020
ms.author: victorh
ms.openlocfilehash: 2e4987273d0ecdc258a3134b89ffc3406e25e97c
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137582"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Tutorial: Criar uma política de firewall de aplicação web na porta frontal azure usando o portal Azure

Este tutorial mostra-lhe como criar uma política básica de Firewall de Aplicação Web Azure (WAF) e aplicar a política a um anfitrião frontal na Porta frontal do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma política waf
> * Associe-o a um anfitrião frontend
> * Configure as regras waf

## <a name="prerequisites"></a>Pré-requisitos

Crie um perfil porta da frente seguindo as instruções descritas em [Quickstart: Crie um perfil porta da frente](../../frontdoor/quickstart-create-front-door.md).

## <a name="create-a-web-application-firewall-policy"></a>Criar uma política de firewall de aplicação web

Em primeiro lugar, crie uma política básica de WAF com o Conjunto de Regras padrão gerido (DRS) utilizando o portal.

1. No lado superior esquerdo do ecrã, selecione **Criar um recurso**>procurar **WAF**>selecione **Web application firewall ** > selecione **Create**.
2. No separador **Basics** da página **de política Criar uma WAF,** introduzir ou selecionar as seguintes informações, aceitar as predefinições para as definições restantes e, em seguida, selecionar Rever + **criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Política para            |Selecione Global WAF (Porta da Frente).|
    | Subscrição            |Selecione o nome de subscrição da Porta Da Frente.|
    | Grupo de recursos          |Selecione o nome do grupo de recursos da Porta Da Frente.|
    | Nome da política             |Introduza um nome único para a sua política waf.|

   ![Criar uma política waf](../media/waf-front-door-create-portal/basic.png)

3. No separador **Associação** da página **de política Create a WAF,** selecione **Adicionar o anfitrião frontend,** introduza as seguintes definições e, em seguida, selecione **Adicionar:**

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Porta da frente              | Selecione o nome do perfil da Porta da Frente.|
    | Anfitrião frontend           | Selecione o nome do seu anfitrião da porta da frente e, em seguida, selecione **Adicionar**.|
    
    > [!NOTE]
    > Se o anfitrião frontal estiver associado a uma política de WAF, é mostrado como cinza. Primeiro deve remover o anfitrião frontal da política associada e, em seguida, reassociar o anfitrião frontal a uma nova política de WAF.
1. Selecione **Rever + criar**e, em seguida, selecione **Criar**.

## <a name="configure-web-application-firewall-rules-optional"></a>Configure regras de firewall de aplicações web (opcional)

### <a name="change-mode"></a>Alterar modo

Quando cria uma política waf, pela política padrão de WAF está no modo **deteção.** No modo de **deteção,** a WAF não bloqueia quaisquer pedidos, pelo contrário, os pedidos que correspondam às regras waf são registados em registos WAF.
Para ver o WAF em ação, pode alterar as definições de modo de **Deteção** para **Prevenção**. No modo **de Prevenção,** os pedidos que correspondem às regras definidas no Predefinido Definido remado (DRS) são bloqueados e registados em registos WAF.

 ![Alterar o modo de política waf](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Regras personalizadas

Pode criar uma regra personalizada selecionando a **regra personalizada adicionar** sob a secção de regras **personalizadas.** Isto lança a página de configuração de regras personalizadas. Abaixo está um exemplo de configurar uma regra personalizada para bloquear um pedido se a corda de consulta contiver **bloqueio**.

![Alterar o modo de política waf](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Conjunto de regras padrão (DRS)

O conjunto de regras predefinidas gerido pelo Azure é ativado por defeito. Para desativar uma regra individual dentro de um grupo de regras, expanda as regras dentro desse grupo de regras, selecione a caixa de **verificação** em frente ao número da regra e selecione **Desativar** no separador acima. Para alterar os tipos de ações para regras individuais dentro do conjunto de regras, selecione a caixa de verificação em frente ao número da regra e, em seguida, selecione o separador de **ação Change** acima.

 ![Alterar conjunto de regras waf](../media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> Saiba mais sobre a Firewall de [Aplicação Web Azure](../overview.md)
> [Saiba mais sobre a Porta da Frente Azure](../../frontdoor/front-door-overview.md)