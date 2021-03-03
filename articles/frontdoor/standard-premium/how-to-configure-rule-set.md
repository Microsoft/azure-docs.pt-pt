---
title: 'Porta frontal azul: configurar conjunto de regras da porta da frente'
description: Este artigo fornece orientações sobre como configurar um Conjunto de Regras.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 6863c492059ccee152ecf3d03a09e61793576bcb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715603"
---
# <a name="configure-a-rule-set-with-azure-front-door-standardpremium-preview"></a>Configure um conjunto de regras com Azure Front Door Standard/Premium (Pré-visualização)

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

Este artigo mostra como criar um Conjunto de Regras e o seu primeiro conjunto de regras no portal Azure. Em seguida, aprenderá a associar o Conjunto de Regras a uma rota a partir da página Rule set ou do Endpoint Manager.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

* Antes de configurar um Conjunto de Regras, tem primeiro de criar um Standard/Premium da Porta Frontal Azure. Para obter mais informações, consulte [Quickstart: Crie um perfil Standard/Premium da Porta Frontal Azure](create-front-door-portal.md).

## <a name="configure-rule-set-in-azure-portal"></a>Configurar regra definida no portal Azure

1. Dentro do perfil da porta da frente, selecione **Conjunto de Regras** localizado em **Definições**. **Selecione Adicionar** e dar-lhe um nome definido de regras.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set-1.png" alt-text="Screenshot da página de aterragem definida por regras.":::
    
1. Selecione **Adicionar Regra** para criar a sua primeira regra. Dê-lhe um nome de regra. Em seguida, **selecione Adicionar a condição** ou Adicionar **ação** para definir a sua regra. Pode adicionar até 10 condições e 5 ações para uma regra. Neste exemplo, usamos a variável do servidor para adicionar um cabeçalho de resposta 8Geo-country* para pedidos que incluam *contoso* no URL.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set.png" alt-text="Screenshot da página de configuração do conjunto de regras.":::
    
    > [!NOTE]
    > * Para eliminar uma condição ou ação de uma regra, utilize o caixote do lixo no lado direito da condição ou ação específicas.
    > * Para criar uma regra que se aplique a todo o tráfego de entrada, não especifique quaisquer condições.
    > * Para parar de avaliar as restantes regras se uma regra específica for cumprida, verifique **pare de avaliar a regra remanescente**. Se esta opção for verificada e todas as regras restantes no Conjunto de Regras não forem executadas independentemente de as condições de correspondência forem cumpridas.  

1. Pode determinar a prioridade das regras dentro do seu Conjunto de Regras utilizando os botões de seta para mover as regras mais ou menos na prioridade. A lista está em ordem ascendente, por isso a regra mais importante é listada em primeiro lugar.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-rule-set-change-orders.png" alt-text="Screenshot da prioridade definida pela regra." lightbox="../media/how-to-configure-rule-set/front-door-rule-set-change-orders-expanded.png":::

1. Uma vez criada uma ou mais regras, **selecione Guardar** para completar a criação do seu Conjunto de Regras.

1. Agora, associe o Conjunto de Regras a uma Rota para que possa produzir efeitos. Pode associar as regras definidas através da página Rule set ou pode ir ao Endpoint Manager para criar a associação.
 
    **Página de definição de regras**: 
    
    1. Selecione o Conjunto de Regras a ser associado.
    
    1. Selecione *o link não associado.*
     

    1. Em seguida, na página **de rota Do Associado,** selecione o ponto final e rota que pretende associar ao Conjunto de Regras. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set.png" alt-text="Screenshot de criar uma página de rota.":::    
        
    1. Selecione *Seguinte* para alterar ordens definidas regras se houver vários conjuntos de regras na rota selecionada. A regra definida será executada de cima para baixo. Pode alterar as ordens selecionando a regra definida e movê-la para cima ou para baixo. Em seguida, selecione *Associate*.
    
        > [!Note]
        > Só pode associar uma regra definida com uma única rota nesta página. Para associar um Conjunto de Regras a várias rotas, utilize o Gestor de Pontos Finais.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-2.png" alt-text="Screenshot das ordens definidas por regras.":::
    
    1. O conjunto de regras está agora associado a uma rota. Pode olhar para o cabeçalho de resposta e ver o Geo-país ser adicionado.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-3.png" alt-text="Screenshot da regra associada a uma rota.":::

   **Gestor de pontos finais:** 
    
    1. Vá ao gestor Endpoint, selecione o ponto final que pretende associar ao Conjunto de Regras.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1.png" alt-text="Screenshot de selecionar o ponto final no Endpoint Manager." lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1-expanded.png":::

    1. *Selecione Editar ponto final*.  
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2.png" alt-text="Screenshot de selecionar o ponto final de edição no Endpoint Manager." lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2-expanded.png":::

    1. Selecione a Rota. 
    
         :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-3.png" alt-text="Screenshot de selecionar uma rota.":::
    
    1. Na página *'Rota',* nas *Regras,* selecione as Regras que pretende associar à rota a partir do dropdown. Em seguida, pode alterar as ordens movendo a regra configurada para cima e para baixo. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-4.png" alt-text="Screenshot da atualização de uma página de rota.":::
    
    1. Em seguida, selecione *Update* ou *Add* para terminar a associação.

## <a name="delete-a-rule-set-from-your-azure-front-door-profile"></a>Elimine um conjunto de regras do seu perfil da porta frontal Azure

Nos passos anteriores, configura e associou uma Regra Definida à sua Rota. Se já não pretender que o Conjunto de Regras esteja associado à sua porta frontal, pode remover o Conjunto de Regras completando os seguintes passos:

1. Aceda à **página 'Conjunto de** **Regras'** em Definições para dissociar o Conjunto de Regras de todas as rotas associadas.

1. Expandir a Rota, selecionar os três pontos. Em seguida, *selecione Editar a rota*.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-1.png" alt-text="Screenshot da rota expandida em conjunto de regras.":::

1. Aceda à secção Regras na página 'Rota', selecione o conjunto de regras e selecione no botão *Eliminar.* 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2.png" alt-text="Screenshot da página de rota de atualização para eliminar um conjunto de regras." lightbox="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2-expanded.png":::

1. Selecione *Update* e o Conjunto de Regras dissocia-se-á do percurso.

1. Repita os passos 2-5 para dissociar outras rotas que estejam associadas a esta regra definida até ver o estado das rotas mostra *não associado*.

1. Para conjunto de regras que não esteja *subordinado,* pode eliminar o Conjunto de Regras clicando nos três pontos à direita e selecione *Eliminar*. 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-3.png" alt-text="Screenshot de como apagar um conjunto de regras.":::

1. A regra definida é agora eliminada.

## <a name="next-steps"></a>Passos seguintes

Saiba como adicionar [cabeçalhos de segurança com regras definidas](how-to-add-security-headers.md).
