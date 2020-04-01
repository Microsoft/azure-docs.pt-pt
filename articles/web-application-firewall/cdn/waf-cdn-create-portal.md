---
title: 'Tutorial: Criar política WAF para Azure CDN - Portal Azure'
description: Neste tutorial, aprende-se a criar uma política de Firewall de Aplicação Web (WAF) sobre o Azure CDN utilizando o portal Azure.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 738be1361bfbd944575abceb08781b241336f6e8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79485595"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>Tutorial: Criar uma política waf no Azure CDN usando o portal Azure

Este tutorial mostra-lhe como criar uma política básica de Firewall de Aplicação Web Azure (WAF) e aplicá-la num ponto final na Rede de Entrega de Conteúdos Azure (CDN).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma política waf
> * Associe-o a um ponto final da CDN
> * Configure as regras waf

## <a name="prerequisites"></a>Pré-requisitos

Crie um perfil e ponto final azure CDN seguindo as instruções em [Quickstart: Crie um perfil e ponto final azure CDN](../../cdn/cdn-create-new-endpoint.md). 

## <a name="create-a-web-application-firewall-policy"></a>Criar uma política de firewall de aplicação web

Em primeiro lugar, crie uma política básica de WAF com um conjunto de regras de padrão gerido (DRS) utilizando o portal.

1. No lado superior esquerdo do ecrã, selecione **Criar um recurso**>procurar POR **WAF**>selecione firewall de **aplicação Web** > selecione **Criar**.
2. No separador **Basics** da página **de política Criar uma WAF,** introduzir ou selecionar as seguintes informações, aceitar as predefinições para as definições restantes e, em seguida, selecionar Rever + **criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Política para            |Selecione Azure CDN (Pré-visualização).|
    | Subscrição            |Selecione o nome de subscrição da Porta Da Frente.|
    | Grupo de recursos          |Selecione o nome do grupo de recursos da Porta Da Frente.|
    | Nome da política             |Introduza um nome único para a sua política waf.|

   ![Criar uma política waf](../media/waf-cdn-create-portal/basic.png)

3. No separador **Associação** da página **de política Create a WAF,** selecione **Add CDN Endpoint,** introduza as seguintes definições e, em seguida, selecione **Adicionar:**

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Perfil CDN              | Selecione o nome do perfil da CDN.|
    | Ponto Final           | Selecione o nome do seu ponto final e, em seguida, selecione **Adicionar**.|
    
    > [!NOTE]
    > Se o ponto final estiver associado a uma política de WAF, é mostrado cinza. Primeiro, deve retirar o Ponto Final da política associada e, em seguida, reassociar o ponto final a uma nova política de WAF.
1. Selecione **Rever + criar**e, em seguida, selecione **Criar**.

## <a name="configure-web-application-firewall-policy-optional"></a>Configurar a política de firewall de aplicações web (opcional)

### <a name="change-mode"></a>Alterar o modo

Por padrão, a política waf está no modo *deteção* quando cria uma política WAF. No modo *deteção,* a WAF não bloqueia quaisquer pedidos. Em vez disso, os pedidos que correspondam às regras waf são registados em registos WAF.

Para ver o WAF em ação, pode alterar as definições de modo de *Deteção* para *Prevenção*. No modo *de Prevenção,* os pedidos que correspondem às regras definidas no Predefinido Definido remado (DRS) são bloqueados e registados em registos WAF.

 ![Alterar o modo de política waf](../media/waf-cdn-create-portal/policy.png)

### <a name="custom-rules"></a>Regras personalizadas

Para criar uma regra personalizada, selecione **Adicionar regra personalizada** sob a secção de regras **personalizadas.** Isto abre a página de configuração de regras personalizadas. Existem dois tipos de regras personalizadas: regra de **correspondência** e regra **limite de taxa.**

A imagem seguinte mostra uma regra de correspondência personalizada para bloquear um pedido se a cadeia de consulta contiver o **bloqueio**de valor .

![Alterar o modo de política waf](../media/waf-cdn-create-portal/custommatch.png)

As regras limite de taxas requerem dois campos adicionais: **duração do limite de taxas** e limite de taxa **(pedidos),** tal como demonstrado no seguinte exemplo:

![Alterar o modo de política waf](../media/waf-cdn-create-portal/customrate.png)

### <a name="default-rule-set-drs"></a>Conjunto de regras padrão (DRS)

O Conjunto de Regras predefinidas gerido pelo Azure é ativado por defeito. Para desativar uma regra individual dentro de um grupo de regras, expanda as regras dentro desse grupo de regras, selecione a caixa de verificação em frente ao número da regra e selecione **Desativar** no separador acima. Para alterar os tipos de ações para regras individuais dentro do conjunto de regras, selecione a caixa de verificação em frente ao número da regra e, em seguida, selecione o separador de **ação Change** acima.

 ![Alterar conjunto de regras waf](../media/waf-cdn-create-portal/managed2.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o Firewall de Aplicação Web Azure](../overview.md)
