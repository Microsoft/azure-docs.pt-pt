---
title: 'Tutorial: Criar política waf para Azure CDN - portal Azure'
description: Neste tutorial, aprende-se a criar uma política de Firewall de Aplicação Web (WAF) sobre o Azure CDN utilizando o portal Azure.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/16/2020
ms.author: victorh
ms.openlocfilehash: 9579d0da3347bdd4ecc627662cee42f909cbfaf7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92132776"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>Tutorial: Criar uma política de WAF no Azure CDN usando o portal Azure

Este tutorial mostra-lhe como criar uma política básica de Firewall de Aplicação Web (WAF) e aplicá-la a um ponto final na Rede de Entrega de Conteúdos Azure (CDN).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma política de WAF
> * Associe-o a um ponto final da CDN. Só pode associar uma política WAF a pontos finais que estejam hospedados no **Azure CDN Standard da Microsoft** SKU.
> * Regras de configuração da WAF

## <a name="prerequisites"></a>Pré-requisitos

Crie um perfil e ponto final Azure CDN seguindo as instruções em [Quickstart: Criar um perfil e ponto final Azure CDN](../../cdn/cdn-create-new-endpoint.md). 

## <a name="create-a-web-application-firewall-policy"></a>Criar uma política de Firewall de Aplicação Web

Em primeiro lugar, crie uma política de WAF básica com um Conjunto de Regras Padrão (DRS) gerido utilizando o portal.

1. No lado superior esquerdo do ecrã, selecione **Criar um recurso**>procurar **WAF**>selecionar firewall **de aplicação Web** > **selecionar Criar**.
2. No separador **Básico da** página de política **Da WAF,** introduza ou selecione as seguintes informações, aceite as predefinições para as definições restantes e, em seguida, selecione **Rever + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Política para            |Selecione Azure CDN (pré-visualização).|
    | Subscrição            |Selecione o nome da subscrição da porta da frente.|
    | Grupo de recursos          |Selecione o nome do grupo de recursos da porta da frente.|
    | Nome da política             |Insira um nome único para a sua política WAF.|

   :::image type="content" source="../media/waf-cdn-create-portal/basic.png" alt-text="Screenshot da página de política Criar uma política W A F, com um botão Review + criar e valores introduzidos para várias definições." border="false":::

3. No separador **Associação** da página **política 'Criar uma política DA WAF',** selecione Adicionar o Ponto final **CDN,** introduza as seguintes definições e, em seguida, selecione **Adicionar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Perfil CDN              | Selecione o nome do seu perfil CDN.|
    | Ponto final           | Selecione o nome do seu ponto final e, em seguida, **selecione Adicionar**.|
    
    > [!NOTE]
    > Se o ponto final estiver associado a uma política de WAF, é mostrado acinzentado. Primeiro, deve remover o Ponto Final da política associada e, em seguida, voltar a associar o ponto final a uma nova política da WAF.
1. Selecione **Rever + criar** e, em seguida, selecione **Criar**.

## <a name="configure-web-application-firewall-policy-optional"></a>Configure a política de Firewall de aplicação web (opcional)

### <a name="change-mode"></a>Alterar o modo

Por defeito, a política waf está no modo *deteção* quando cria uma política WAF. No modo *deteção,* a WAF não bloqueia quaisquer pedidos. Em vez disso, os pedidos correspondentes às regras da WAF são registados nos registos da WAF.

Para ver o WAF em ação, pode alterar as definições de modo de *Deteção* para *Prevenção*. No modo *prevenção,* os pedidos que correspondam às regras definidas no Conjunto de Regras Predefinidas (DRS) são bloqueados e registados nos registos WAF.

 :::image type="content" source="../media/waf-cdn-create-portal/policy.png" alt-text="Screenshot da secção de definições de Política. O alternador de modo está definido para Prevenção." border="false":::

### <a name="custom-rules"></a>Regras personalizadas

Para criar uma regra personalizada, **selecione Adicionar regra personalizada** na secção de **regras personalizadas.** Isto abre a página de configuração de regras personalizadas. Existem dois tipos de regras personalizadas: **regra de correspondência** e regra limite de **taxa.**

A imagem que se segue mostra uma regra de correspondência personalizada para bloquear um pedido se a cadeia de consulta contiver o **bloco de** valor .

:::image type="content" source="../media/waf-cdn-create-portal/custommatch.png" alt-text="Screenshot da página de configuração de regras personalizadas mostrando definições para uma regra que verifica se a variável QueryString contém o bloco de valor." border="false":::

As regras limite de taxa requerem dois campos adicionais: **Duração limite de taxa** e **limiar de limite de taxa (pedidos),** como indicado no exemplo seguinte:

:::image type="content" source="../media/waf-cdn-create-portal/customrate.png" alt-text="Screenshot da página de configuração da regra de limite de taxa. Uma caixa de lista de limite de taxa e uma caixa limite de taxa (pedidos) são visíveis." border="false":::

### <a name="default-rule-set-drs"></a>Conjunto de regras predefinidos (DRS)

O Conjunto de Regras Padrão gerido por Azure é ativado por padrão. Para desativar uma regra individual dentro de um grupo de regras, expanda as regras dentro desse grupo de regras, selecione a caixa de verificação à frente do número de regras e selecione **Desativar** o separador acima. Para alterar tipos de ações para regras individuais dentro do conjunto de regras, selecione a caixa de verificação à frente do número de regra e, em seguida, selecione o separador **de ação Change** acima.

 :::image type="content" source="../media/waf-cdn-create-portal/managed2.png" alt-text="Screenshot da página de regras geridas mostrando um conjunto de regras, grupos de regras, regras e botões de Ativação, Desativação e Alteração de Ação. Uma regra é verificada." border="false":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, remova o grupo de recursos e todos os recursos relacionados.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre a firewall de aplicação web Azure](../overview.md)
